# インフラ／コスト耐性方針 v1 — 裁定記録（罠の地図）

- 日付: 2026-09-06 JST
- revision: MYRIG-20260906-062（この文書は 062 で追加）
- 状態: **PROPOSAL（イタヤ裁定待ち）**。裁定後に本文冒頭を DECISION へ書き換え、docs/ 側へ反映する
- 経緯: Claude / GPT / Gemini が同一依頼で独立調査（2026-09-05）→ GPT が3案を統合（2026-09-06）→ Claude が整形
- 原資料: `_proposals/2026-09-05_infra-cost-resilience_claude-v1.md`（試算・単価・出典の全文）／`_proposals/2026-09-06_infra-cost-resilience_gpt-final.md`（GPT 最終統合案・原文）
- 拘束力: **L2**。料金・アダプタ・閾値は変動するため、本書の具体値は「2026-09-05 時点の観測」であり恒久固定しない

---

## 1. 何を決めたか（案）

| # | 決定（案） |
|---|---|
| 1 | MVP 構成 = **Vercel Pro + Supabase Pro(Tokyo) + Cloudflare R2 + Upstash + Cloudflare Turnstile + Custom SMTP** |
| 2 | 画像の正本を **Cloudflare Images Hosted から R2 へ**変更。バリアントはアップロード時に固定種を一度だけ生成し、以後は CDN 配信のみ |
| 3 | DB には画像の完全 URL ではなく **`storage_key`** を保存し、描画時に配信ドメインと合成 |
| 4 | Cloudflare を Vercel の **reverse proxy 前段に置かない**。Cloudflare は DNS / `media.myrigrc.com`(R2) / Turnstile に使う |
| 5 | Supabase Auth を MVP で維持。**Total MAU と Authenticated MAU を分離**して試算・監視する。Auth quota の 80% で再審査 |
| 6 | app 層を将来 Cloudflare Workers へ移せる状態を MVP から維持（可搬性原則）。アダプタ名・移行金額は固定しない |
| 7 | 非機能要件として **多段 degraded mode**（normal / economy / restricted / readonly / frozen）を持つ。緊急状態の権威は Supabase に置かない |
| 8 | DB 負荷の是正（`view_count` バッファ化・counter 列・Feed の N+1 禁止）を**ベンダー変更より先に**行う |
| 9 | 財務ガードレール: インフラ予算は「実現済み収益」と「イタヤが明示した自己負担上限」の**小さい方**に拘束される |
| 10 | 非採用（恒久 REJECT ではない）: Cloudflare D1 / Neon / 自前 VPS / Cloudflare Images Hosted / Vercel Hobby / MAU 課金型 Auth SaaS / 外部 Search SaaS / Realtime 中心 Feed |

---

## 2. なぜそうしたか（踏むと壊れる罠）

### 2.1 Cloudflare Images Hosted をやめた理由

- 課金は **配信 $1 / 10万回**（公式: "Every image requested by the browser counts as one billable request"）＋保存 $5 / 10万枚。**CDN キャッシュヒットも1配信**
- MyRIG は Feed / Garage / Search / RIG 詳細で画像を大量表示する。配信数 ≒ PV × 画像枚数 で線形に伸び、**上限機能が無い**（Cloudflare の Budget alert は通知のみ）
- 試算（Claude 案・PV 40/MAU・画像 8/PV）: 100k MAU で $392/月、1M MAU で **$3,920/月**。同条件の R2 事前生成は $23 / $227
- **罠**: R2 で「リクエスト時に動的変換」を使うと、ユニーク変換が月ごとにリセットされるため、閲覧される蓄積画像 × バリアント数に比例して **Hosted より高くなり得る**（試算 1M MAU で $5,758）。**アップロード時の事前生成が必須**。動的変換は MVP の 5,000 unique/月 無料枠内に限る
- 移行難易度: Hosted は原本を API で1枚ずつ取得し URL 全行更新が要る（高）。R2 は S3 互換で `rclone` 可（低）

### 2.2 `storage_key` にした理由

- 現行 schema v1_6 は `images.url` / `thumbnail_url` / `profiles.avatar_url` / `cover_image_url` に Cloudflare Images の完全 URL を想定
- プロバイダ固有 URL を DB に焼くと、配信基盤の変更が **DB 全行更新** になる。`storage_key`（例: `users/<uid>/rigs/<rid>/<imgid>`）＋描画時合成なら配信ドメインの差し替えだけで済む
- バリアントは `key` にサフィックス（`_card.webp` 等）で表現し、DB は原本 key のみ持つ

### 2.3 Vercel を MVP で採用し、永久固定しない理由

- 採用: Tokyo(hnd1) あり、Next.js 15 が無改造で動く、**Spend Management の自動 pause が「真のハードキャップ」として存在**（100% 到達で本番全停止、チェックは数分おき）
- **Hobby は商用不可**（Fair Use: 広告・アフィリエイト・寄付も商用扱い）。公開時点で Pro $20/月
- 固定しない理由: 単価が高い。Edge Requests $2.60/M（hnd1）、Fast Data Transfer $0.16/GB。**DDoS 緩和で遮断した分は非課金だが、「DDoS と判定されないボット・クローラ」は課金される**。1M 不正ページリクエストあたりの露出は約 $28（Workers なら約 $0.90）
- 同じ Next.js を Workers に載せた試算: 1M MAU で Vercel $1,895 vs Workers $41（app 層のみ）
- **罠1**: Cloudflare を Vercel の前段に置いて守ろうとする構成。Vercel 公式が非推奨（Firewall の可視性・Bot 判定・キャッシュが劣化）。
- **罠2**: 移行先アダプタを正典に名指しすること。2026-09 時点で Cloudflare は新規 Next.js に vinext（beta）を推奨、OpenNext は「既存アプリ維持用」に位置づけを変えた。名指しすると次に触る AI が古い方式を守る。**「移行時点の Cloudflare 公式推奨方式」とだけ書く**
- **罠3**: 「Vercel $300/月で移行」のような金額固定。料金体系が変わると嘘になる。判断基準は「Vercel 従量費が全体予算に対して無視できない割合になり、年間削減額が移行工数を上回るとき」

### 2.4 Supabase を維持した理由

- compute は**固定段階料金**（Micro $10 → Small $15 → Large $110 → 2XL $410 → 4XL $960）。トラフィックで請求が滑らかに増えない。増えるのは自分で段階を上げたとき
- **Spend Cap（Pro 既定 ON）** は egress / MAU / Storage / Edge Functions / Realtime を対象に、超過分を課金ではなく停止にする。**対象外は compute / Read Replica / PITR / Disk IOPS**（これらは自分で選ぶ固定費）
- 東京 `ap-northeast-1` あり。pg_trgm / PGroonga 同梱で日本語検索が DB 内で完結
- 現行正典の RLS 設計（L1）と `profiles.id → auth.users.id` をそのまま使える
- **罠**: Spend Cap ON のまま Auth quota（Pro 10万 MAU）を超えると、超過分の利用は次サイクルまで不可、猶予期間後は **org 全体に 402 / read-only 等の制限**が及ぶ（公式 FAQ）。「成功したらログインが止まる」構造。→ quota の 80% で再審査する運用ゲートを置く。開発用プロジェクトは同じ org に置かない（制限が org 単位で及ぶ）
- MAU の定義（公式）: 請求期間内に sign in または token refresh した distinct user。**未ログイン閲覧者は含まれない**。試算で Total MAU をそのまま Auth MAU に置くと、1M MAU で $2,925 と $975（Auth 率 40%）の差が出る。**Auth 率は正典で固定せず、公開後は実測へ置き換える**

### 2.5 D1 を採らない理由

- 有料でも **1 DB 10GB 上限・増枠不可**。SQLite。各 DB は単一スレッド処理。Cloudflare 自身が「多数の小 DB へ水平分割」を想定
- MyRIG は profiles → rigs → logs → images → likes → comments → follows → feed のユーザー横断関係が強く、分割すると「フォロー中100人の最新 LOG を Feed に出す」が難しくなる
- Postgres RLS（L1）を持ち込めない。PostgreSQL → D1 は SQLite 互換への変換が要る
- **罠**: 「インフラを安くするためにアプリを D1 向けに作り替える」逆転。月額差より設計変更コストが大きい

### 2.6 Neon を MVP で採らない理由

- **東京リージョンが無い**（AWS 8 リージョン、最寄りシンガポール）。日本中心の MVP で SSR × DB 往復すべてに +60〜80ms
- **ハードキャップが無い**。Spending notification は 80/100% のメールのみ、自動 compute 停止は「Coming Soon」（2026-09-05 時点）。autoscaling の最大 CU は compute を抑えるが storage / egress は抑えない
- compute が従量（Launch $0.106/CU-h、最大 16CU ≒ $2,600/月相当）。Supabase の固定段階と逆の性質
- Neon Auth の MAU 課金条件は Claude 調査で未確認（GPT は「100万 MAU まで込み」としたが公式ページから取得できず）
- Hyperdrive（Workers の接続プール・キャッシュ、Paid で追加費用なし）は **Supabase にも使える**。Hyperdrive のために Neon へ行く必要はない
- 恒久 REJECT ではない。Tokyo 追加・hard cap 実装で再評価

### 2.7 VPS を MVP で採らない理由

- Hetzner 等は固定費で「成功しても請求が増えない」性質を持つ（例: CCX23 4vCPU/16GB SIN €108.99/月）。ただし**東京なし**（SIN で +60〜80ms）
- OS 更新・セキュリティ・Postgres 保守・バックアップ・PITR・HA・監視・障害対応をすべてソロ開発者が背負う。副業規模では「月 $100 節約のために 24 時間 DB 障害対応を抱える」方が危険
- 将来の脱出路として残す。収益化後・運用委託可能な段階で再評価

### 2.8 緊急状態の権威を Supabase に置かない理由

- Supabase が原因（障害・Spend Cap 発動）で止めたいとき、Supabase へ問い合わせないと現在モードが分からない構造は本末転倒
- Vercel 期の権威は Upstash。ただし **Upstash も max budget で止まり得る**。取得不能時のフェイルセーフは:
  - Public READ → economy 相当で継続
  - Signup / 画像 upload / 高コスト WRITE → restricted 相当で **fail closed**
  - 「設定ストアが壊れたから normal へ戻る」は禁止
- frozen だけは Upstash と独立し、Cloudflare DNS 切替（→ Workers 静的 survival page、静的アセット配信は無料・無制限）を別レバーとして持つ
- Workers 移行後は権威を Workers KV 等の Edge 側へ移すことを検討

### 2.9 DB 負荷是正をベンダー変更より先にする理由

- 現行 schema v1_6: `view_count` を表示ごとに行へ直接 UPDATE、like / favorite は表示ごとに `COUNT(*)`
- 1M MAU × 40 PV = 月 4,000万 write。DB compute の段階アップ（$110 → $410 → $960）を**トラフィックではなく設計が**強制する
- 対策: `view_count` は Redis INCR に溜めて数分ごと batch 反映、件数は trigger 等で counter 列を維持、Feed は cursor ページング・1 クエリで必要情報を取得・N+1 禁止・無限バックグラウンド polling 禁止
- **罠**: 「MAU が増えたから DB を大きくする」前に、この4点を直す。アプリ設計で 10 倍差が付く領域

### 2.10 事例から学んだ爆発経路（2023〜2026・21 件）

- 爆発するのは 帯域/egress（Netlify $104k・simmer.io $98k）、関数呼出（Cara $96k）、DB read / KV write のバグループ（Firestore $72k・Cloudflare KV $36k）、画像変換（LLM クローラ）。**Auth MAU の巨額事例は無い**
- 真のハードキャップを持つのは Vercel（pause）/ Netlify Free / Supabase Spend Cap（対象項目のみ）/ Upstash（max budget）/ MongoDB Flex。**Cloudflare / Neon / PlanetScale / DO / Render は通知のみ**
- **罠**: 「プロバイダが止めてくれる」を Cloudflare に期待すること。Workers の `limits.cpu_ms`、WAF、rate limit、kill switch はアプリ側の責務

---

## 3. 2026-09-05 時点の公式単価（観測値・恒久固定しない）

| 項目 | 値 |
|---|---|
| Vercel Pro | $20/seat。Edge Requests 10M 込 → hnd1 $2.60/M。FDT 1TB 込 → hnd1 $0.16/GB。Invocations $0.60/M。Active CPU hnd1 $0.202/h。Spend Management 既定 $200・pause 既定 ON |
| Vercel Hobby | 商用不可。超過課金なし（機能 30 日停止） |
| Supabase Pro | $25。Micro 相当 $10 クレジット込。100k Auth MAU 込 → $0.00325/MAU。egress 250GB 込 → $0.09/GB。disk 8GB 込 → $0.125/GB。PITR 7 日 ≒ $100/月。Spend Cap 既定 ON |
| Supabase compute | Micro $10 / Small $15 / Medium $60 / Large $110 / XL $210 / 2XL $410 / 4XL $960（月換算） |
| Cloudflare R2 | $0.015/GB-月、Class A $4.50/M、Class B $0.36/M、egress $0。無料枠 10GB |
| Cloudflare Images | 配信 $1/10万、保存 $5/10万枚、変換 $0.50/1,000 unique（5,000/月 無料） |
| Cloudflare Workers | $5/月、10M req 込 → $0.30/M、CPU 30M ms 込 → $0.02/M ms。静的アセット無料・無制限。**ハードキャップなし** |
| Upstash Redis | PAYG $0.20/10万 cmd。max monthly budget 設定可（到達で制限、超過請求なし） |
| Turnstile | 無料 |
| Resend | Free 3,000 通/月・100 通/日。Pro $20/月 |
| Supabase 内蔵 SMTP | 2 通/時（本番不可） |

## 4. 試算（Claude 案 Base Case・計画用概算）

前提: PV 40/MAU/月、Auth 率 40%、画像 8/PV、アップロード 1.2 枚/MAU/月、12 か月蓄積、動画なし、匿名ページはキャッシュ。

| Total MAU | A: 現行（CF Images） | B: 現行＋R2 事前生成 | C: Workers＋Supabase＋R2 |
|---|---|---|---|
| 1,000 | $49 | $45 | $30 |
| 10,000 | $120 | $83 | $68 |
| 100,000 | $773 | $403 | $317 |
| 1,000,000 | $8,012 | $4,319 | $2,465 |

感度（1M MAU）: 軽量（PV 20・画像 5）A $4,700 / C $2,400。重量（PV 80・画像 12・Auth 率 60%）A $17,300 / C $3,300。**C は入力を 2〜3 倍にしても $3,000 台に留まる。**

1M MAU の C の内訳: DB compute $820（2XL＋replica、自分で選ぶ固定費）、Auth MAU $975（ユーザー数比例＝収益相関）、Workers $41、R2 $227、その他 $400。

## 5. 未確認（実装前に実測すること）

| 項目 | 影響 |
|---|---|
| Vercel / Supabase の**使用量取得 API** | 取れなければ degraded mode の判定は自動ではなく、50/75/100% のプロバイダ通知を受けた手動運用。**「自動縮退」と正典に書く前に確認** |
| Supabase Read Replica の対応リージョン（東京可否） | 世界展開時の遅延対策 |
| Supabase 新規プロジェクトの既定 Postgres バージョン | PGroonga 等の可用性 |
| Cloudflare WAF で遮断した分が Workers 課金に乗らないことの明文 | Workers 移行後の露出見積り |
| Neon Auth の MAU 課金条件 | Neon 再評価時 |
| ブラウザ側でバリアント生成する場合の信頼性 | クライアント生成だと「card と public が別画像」の差し替えが可能。サーバ検証か再生成が要る |

## 6. 3AI の主張と収束

| 論点 | GPT 初案 | Gemini | Claude | 統合 |
|---|---|---|---|---|
| MVP Web | Vercel | Workers 寄り | Vercel | **Vercel** |
| MVP DB | Supabase | Neon / D1 | Supabase Tokyo | **Supabase** |
| Auth | Supabase → 将来 Neon | Neon Auth | Supabase 維持 | **Supabase 維持・quota 80% で再審査** |
| 画像 | R2 | R2 | R2 事前生成 | **R2 事前生成** |
| D1 | 非推奨 | 一時推奨 | 非推奨 | **非採用** |
| 成長後 Web | Workers | Workers | Workers | **Workers（方式は移行時点の公式推奨）** |
| 多段縮退 | 5 段 | 提案 | 4 段＋具体設計 | **normal / economy / restricted / readonly / frozen** |
| 緊急状態の権威 | Cloudflare Edge 側 | — | DB＋Redis → 修正 | **Vercel 期 = Upstash（fail closed）、frozen = DNS 独立レバー** |
| Auth-MAU 定義 | Total = Auth | Total = Auth | Auth 率 40% | **分離して扱う。率は固定しない** |
| Gemini の誤り | — | 「Vercel は DDoS も課金で青天井」「Supabase 5 万 MAU」 | — | 公式で否定（DDoS 遮断分は非課金・Pro は 10 万） |

## 7. 影響先（裁定後に反映する docs/）

| 文書 | 変更 |
|---|---|
| `docs/schema/myrig_db_schema_v1_6.md` | `images.url` / `thumbnail_url` / `profiles.avatar_url` / `cover_image_url` → `storage_key` 方式。「統計カウントの方針」を counter 列・buffer 方式へ |
| `docs/support/App_Ready_Design_Rules.md` | Rule 5（画像）を R2 ＋ `storage_key` へ。可搬性原則を追加 |
| `docs/support/implementation_checklist.md` | §0-3（開発環境）、§1-4（rate limit store 抽象化）、Phase 3（R2 presigned upload ＋ 事前生成）、§5（Spend Management / Spend Cap / max budget / degraded mode） |
| `docs/support/`（新規） | degraded mode 仕様（5 状態・権威・フェイルセーフ・レバー） |
