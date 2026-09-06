<!-- 原資料: Claude 独立検証 報告書（2026-09-05）。正典ではない。裁定は _decisions/2026-09-06_infra-cost-resilience-v1.md -->

# MyRIG インフラ／コスト耐性設計 — 独立検証 v1

- 作成: 2026-09-05 JST（Cowork / Claude）
- CURRENT: **MYRIG-20260905-061**（revision.txt / CURRENT.md 冒頭 / git HEAD `aa35d74` の3点一致）
- 位置づけ: 独立案（GPT / Gemini 未参照）。正典 L2 への差分提案を含む。採用はイタヤ裁定
- 料金は 2026-09-05 に各社公式ページで確認。**「公式」= 公式ページの値、「推定」= 本書の仮定**

---

## 0. 結論（先に）

1. **DB を Supabase(Tokyo) に置く判断は正しい。** compute は固定段階料金で「伸びたから請求が爆発」しない。Spend Cap（Pro 既定 ON）で egress / MAU / Storage は課金より先に止まる
2. **現行案の最大の罠は Cloudflare Images の「配信課金」**（$1 / 10万配信、**キャッシュヒットも1配信**）。画像は PV に線形で増え、上限機能が無い。1M MAU で **月 $3,900**。→ **R2 ＋ アップロード時にバリアント事前生成**へ差し替える（同規模で月 $230）
3. **Vercel は MVP では正解、成長後は最大の変動費。** 1M MAU で月 $1,900（Edge Requests ＋ Fast Data Transfer）。Spend Management の自動 pause が唯一の「真のハードキャップ」だが、pause = 全停止。**同じ Next.js を Cloudflare Workers(OpenNext) に載せると月 $41**。移行可能な書き方を MVP から守り、Vercel 請求が月 $300 を超えた時点で移す
4. **Supabase Auth の MAU 課金（10万超 $0.00325/MAU）が「成功で止まる」唯一の Supabase 側の罠。** Spend Cap ON のまま 10万 Auth-MAU を超えると、超過分の利用は次サイクルまで「不可」となり、猶予期間後は **org 全体に 402 / read-only 等の制限**が及ぶ（公式 FAQ）。8万 Auth-MAU 到達を「Spend Cap OFF ＋ 自前監視」へ切り替える裁定点にする
5. **最終推奨: MVP = Vercel Pro ＋ Supabase Pro ＋ Cloudflare(R2 / Turnstile / DNS) ＋ Upstash（月 $45〜85）。** 成長期に app 層だけ Workers へ移す（1M MAU で月 $2,500 前後、うち固定段階費が大半）。VPS 化はソロ運用では推奨しない

---

## 1. 前提（正典から確認した MyRIG の要件）

| 項目 | 正典の値 | 出典 |
|---|---|---|
| スタック | Next.js 15 + Supabase(Tokyo) + Vercel + Cloudflare Images（Supabase Storage 直保存は禁止） | implementation_checklist / App_Ready_Design_Rules |
| 画像上限 | RIG: Cover1＋Sub8=9（暫定値 10）/ PARTS: 5 / LOG: 3。10MB・8000px | CURRENT 044・048 / checklist 3-2 |
| 投稿数 | RIG・パーツ・LOG は無制限。画像枚数のみ制限 | memory（確定） |
| 統計 | `view_count` は行へ直接インクリメント／like・favorite は都度 `COUNT(*)` | schema v1_6 |
| Rate limit | Vercel Edge Middleware ＋ Upstash（暫定値。正典に裁定根拠なし） | checklist 1-4 |
| Auth | Supabase Auth。方式 HOLD（L1） | checklist 1-1 |
| 環境 | 本番＋開発の Supabase 2プロジェクト | checklist 0-3 |
| 収益 | アフィリエイト優先 → AdSense | memory（確定） |
| 国判定 | `x-vercel-ip-country` | memory |

**正典との矛盾・要注意（公式確認済み）**
- **Vercel Hobby は商用不可**（アフィリエイト・広告・寄付も商用扱い）。公開時点で **Pro $20/月が必須**
- **Vercel は Cloudflare 等のリバースプロキシ前段を公式に非推奨**（Firewall / DDoS 検知が劣化）。「Cloudflare を Vercel の前に置いて守る」構成は取れない。Cloudflare は画像ドメインと DNS のみに使う

---

## 2. 現行候補構成の評価

| 構成要素 | 評価 | 理由 |
|---|---|---|
| Supabase Pro (Tokyo) | ◎ 維持 | compute は固定段階（Micro $10 → 2XL $410 → 4XL $960）。Spend Cap 既定 ON。東京リージョンあり。pg_trgm / PGroonga 同梱で日本語検索が DB 内で完結 |
| Vercel Pro | ○ MVP 限定 | Tokyo(hnd1) あり。Spend Management で自動 pause 可（既定 $200 予算、pause 既定 ON）。ただし単価が高い（Edge Requests $2.60/M、FDT $0.16/GB、**ボット・クローラも課金**）。Hobby 不可 |
| Cloudflare Images | ✕ 差し替え | 配信 $1/10万（キャッシュヒット込み）＋保存 $5/10万枚。上限機能なし。画像は PV に線形 → 成長時の最大変動費 |
| 本番＋開発の Supabase 2プロジェクト | △ | $35/月。開発は **Supabase CLI ローカル（無料）** か **別 Free org**（Free と Pro は同一 org 内に混在不可・別 org なら可）で $0 にできる |
| `x-vercel-ip-country` 依存 | △ | Vercel ロックイン。`getCountry(req)` に抽象化し `cf-ipcountry` も受ける |
| `images.url` に Cloudflare Images URL を保存 | △ | プロバイダ URL を DB に焼くと移行時に全行更新。**`storage_key` を保存し URL は描画時に合成**する（L2 差分提案） |

---

## 3. 代替構成案

| 案 | 構成 | 向き | 不採用理由 / 条件 |
|---|---|---|---|
| **A 現行** | Vercel + Supabase + CF Images | — | 画像配信課金が成長時に最大費目 |
| **B 現行＋画像差替（MVP 推奨）** | Vercel + Supabase + **R2 事前生成** | MVP〜10万 MAU | Vercel 費が伸びたら C へ |
| **C Cloudflare 中心（成長期推奨）** | **Workers(OpenNext)** + Supabase + R2 | 10万〜100万 MAU | app 層のみ移行。DB は動かさない。Node.js Middleware(15.2+) 未対応・Cloudflare は新規に vinext(beta) を推奨中 → アダプタ変動リスク |
| D VPS | Hetzner(SIN) + Coolify + 自前 Postgres + Better Auth + R2 | 収益化後・運用委託できる場合 | **東京なし**（SIN で +60〜80ms）。バックアップ・PITR・パッチ・障害対応が全部自分。固定費なので請求爆発は原理的に無いが、**ソロ運用の時間コストと事故リスク**が大きい |
| E Supabase → Neon | — | 不採用 | 東京なし。compute が従量（最大 16CU × $0.222/h ≒ $2,600/月）。ハードキャップなし（通知のみ、自動停止は「Coming Soon」） |
| F Supabase → PlanetScale Postgres | — | 保留 | 東京あり・固定価格（PS-10 HA $41）。ただし Auth / RLS-JWT / PostgREST を失う。DB だけ差し替える価値は薄い |
| G Cloudflare 完結（Workers + D1） | — | 不採用 | D1 は SQLite・**DB 上限 10GB（増枠不可）**。正典の RLS / Postgres 設計と非互換 |
| H Auth → Clerk / Firebase | — | 不採用 | Clerk 1M MRU ≒ $17,000/月、Identity Platform ≒ $4,400/月。Supabase Auth（$2,925/月）より高い。WorkOS AuthKit は 1M MAU まで $0 だが Supabase 側で third-party MAU $0.00325 が別途かかり、結局同額 |
| I Auth → Better Auth（自前） | Postgres 上に自前 | 100万 MAU 級で検討 | $0。ただし Supabase RLS の `auth.uid()` 前提を捨て、認可を app 層へ。将来の Expo 直結設計とも衝突。MVP では見送り |

---

## 4. 規模別コスト試算

### 4.1 前提（すべて推定。変えれば表は変わる）

| 変数 | 値 | 根拠 |
|---|---|---|
| PV / MAU / 月 | 40 | SNS 型・週2〜3回来訪 |
| ログイン率 | 40%（Auth MAU = 0.4 × MAU） | ガレージ型は visitor 多め |
| 画像アップロード / MAU / 月 | 1.2 枚 | 投稿者 20% × 4 投稿 × 1.5 枚 |
| 画像リクエスト / PV | 8（平均 45KB） | カード＋詳細 |
| HTML＋JSON / PV | 60KB、静的アセット 80KB（ブラウザキャッシュで償却） | Next.js 標準 |
| Edge Requests / PV | 10 | HTML＋アセット＋RSC/API |
| Function 呼出 / PV | 1.0（匿名ページの半分は ISR/CDN ヒット）、CPU 40ms | |
| DB → app 転送 / 呼出 | 30KB | |
| 保存画像 | 12か月分蓄積、1枚 1.05MB（クライアント縮小原本 0.8MB ＋ バリアント3種 0.25MB） | |
| Supabase compute | 1k: Micro（クレジット内）/ 10k: Small $15 / 100k: Large $110 / 1M: 2XL ＋ Read Replica 2XL $820 | 公式単価 |
| PITR | 10万 MAU から $100/月 | 公式 |
| 為替 | USD 表記。Hetzner のみ EUR | |

### 4.2 月額合計（USD）

| MAU | PV/月 | **A 現行** | **B 現行＋R2** | **C Workers＋Supabase＋R2** | D VPS（EUR＋USD） |
|---|---|---|---|---|---|
| 1,000 | 4万 | $49 | **$45** | $30 | €16 |
| 10,000 | 40万 | $120 | **$83** | $68 | €60 ＋ $23 |
| 100,000 | 400万 | $773 | $403 | **$317** | €240 ＋ $72 |
| 1,000,000 | 4,000万 | **$8,012** | $4,319 | **$2,465** | €740（推定含む）＋ $382 |

### 4.3 費目別（1M MAU、B と C の差が出る行を太字）

| 費目 | 100k MAU | 1M MAU（B: Vercel） | 1M MAU（C: Workers） | 単価（公式） |
|---|---|---|---|---|
| App: Edge Requests | $78 | **$1,014** | — | Vercel hnd1 $2.60/M（10M 込） |
| App: Fast Data Transfer | $0（560GB < 1TB） | **$736** | — | Vercel hnd1 $0.16/GB（1TB 込） |
| App: Functions（呼出＋CPU＋メモリ） | $13 | $135 | — | $0.60/M ＋ $0.202/CPU-h ＋ $0.0167/GB-h |
| App: Workers | — | — | **$41** | $5 ＋ $0.30/M req ＋ $0.02/M CPU-ms。静的・egress $0 |
| DB compute | $110 | $820 | $820 | 固定段階 |
| DB disk / egress / PITR | $104 | $222 | $222 | $0.125/GB、$0.09/GB（250GB 込）、PITR $100 |
| **Auth MAU** | $0（4万 < 10万） | **$975** | **$975** | $0.00325/MAU（10万込） |
| 画像保存（R2） | $23 | $227 | $227 | $0.015/GB、egress $0 |
| 画像（参考: CF Images なら） | $392 | $3,920 | $3,920 | 配信 $1/10万 ＋ 保存 $5/10万枚 |
| Rate limit（Upstash） | $4 | $40 | $40 | $0.20/10万 cmd。予算上限設定可 |
| Cloudflare Pro zone / Email | $45 | $115 | $115 | $25 / Resend Scale $90 |

読み方: **1M MAU では A の 60% が「画像配信＋Vercel 転送」**。C はその 2 費目がほぼ消え、残りは **DB 固定段階費（自分で選ぶ）と Auth MAU（ユーザー数に比例＝収益と相関）**。

### 4.4 シナリオ感度

| シナリオ | 変化 | A 1M | C 1M |
|---|---|---|---|
| 軽量（PV/MAU 20、画像 5/PV） | 転送・配信半減 | $4,700 | $2,400 |
| 重量（PV/MAU 80、画像 12/PV、ログイン率 60%） | 配信 3 倍・Auth 1.5 倍 | $17,300 | $3,300 |
| DB 高負荷（4XL ＋ 4XL replica） | compute ＋$1,100 | ＋$1,100 | ＋$1,100 |

**C は入力を 2〜3 倍にしても月 $3,000 台に留まる。A は $17,000 まで伸びる。**

---

## 5. 一番危険なコスト要因（順位付き）

| # | 要因 | 単価の性質 | 1M 不正リクエストあたりの露出 | ブレーキの有無 |
|---|---|---|---|---|
| 1 | **Cloudflare Images 配信課金** | PV × 画像枚数に線形、キャッシュヒット込み | $10 | **なし**（Budget alert は通知のみ） |
| 2 | **Vercel Edge Requests ＋ FDT** | 全リクエスト（ボット・LLM クローラ含む。DDoS 緩和で遮断した分は非課金） | **$28**（ページ 140KB 想定） | Spend Management pause（数分遅延、全停止） |
| 3 | **Supabase Auth MAU** | ログインユーザー数に線形 | — | Spend Cap ON なら**猶予後に org 全体が制限**、OFF なら課金 |
| 4 | DB の段階アップ強制 | `COUNT(*)`・毎 PV の `view_count` UPDATE・無限 Feed クエリが CPU を食い、$110 → $410 → $960 と上がる | — | 設計で回避（§7） |
| 5 | バグループ | 事例では最速で 5 桁到達（Cloudflare KV 31 億 write $15k、Firestore 1,160 億 read $72k）。Vercel は再帰保護あり、Cloudflare Queues / DO には上限なし | 数時間で数千ドル | `limits.cpu_ms`、冪等性、MVP では Queues / DO を使わない |
| 6 | LLM クローラ × 画像変換 | Metacast 事例（画像最適化 API を 1 日 6.6 万回） | Transform $0.50/1k unique | 事前生成なら変換課金ゼロ。クローラブロック |
| 7 | Workers（C 採用後） | 上限なし。ただし単価が Vercel の 1/30 | $0.90 | DDoS 緩和は無制限・WAF/Rate Limiting で前段遮断・KV フラグの kill switch |

**構造的な教訓（事例 21 件の統合）**: 爆発するのは「帯域 / egress」「関数呼出」「DB read/KV write のループ」「画像変換」。Auth MAU の巨額事例は無い。真のハードキャップを持つのは Vercel（pause）・Netlify Free・Supabase Spend Cap（対象項目のみ）・Upstash（max budget）・MongoDB Flex。Cloudflare / Neon / PlanetScale / DO / Render は通知のみ。

---

## 6. ベンダーロックイン／移行難易度

| 依存先 | ロックの中身 | 出口 | 難易度（推定） |
|---|---|---|---|
| Supabase Postgres | 標準 PG。RLS は素の SQL | `supabase db dump` → 任意の PG。egress $0.09/GB | 低（1〜2 日） |
| Supabase Auth | `auth.users`（bcrypt ハッシュ）・ソーシャル連携・RLS の `auth.uid()` | ハッシュ移行可。RLS の JWT 前提を app 層へ移す作業が主 | 中（1〜2 週） |
| supabase-js / PostgREST | クライアント SDK | 正典の `services/` 層に閉じ込めれば差し替え可 | 中 |
| Vercel | Next.js 自体は可搬。**ISR / Image Optimization / KV(廃止→Upstash) / Blob / Global Config / `x-vercel-*` ヘッダ / Cron** が固有 | OpenNext（Cloudflare / AWS）。固有 API を使わなければ数日 | 低〜中 |
| Cloudflare Images | 画像 ID ＋ variant URL。原本は API で 1 枚ずつ取得 | 全画像ダウンロード＋DB URL 全行更新 | **高**（枚数に比例） |
| R2 | S3 互換 | `rclone` で他 S3 へ。egress $0 | 低 |
| Upstash | Redis 互換 | 任意の Redis | 低 |
| D1（不採用） | SQLite 方言 | — | 高 |

**MVP で守る可搬性ルール（提案）**
1. DB には `storage_key` を保存し、画像 URL は描画時に `IMG_BASE + key + variant` で合成
2. Vercel 固有 API（Blob / Global Config / Cron / `next/image` の Vercel 最適化）を使わない。`next/image` は `unoptimized` か R2 向け custom loader
3. リクエストの国・IP・UA は `lib/request-context.ts` に抽象化（`x-vercel-ip-country` / `cf-ipcountry` 両対応）
4. Rate limit / feature flag のストアは Redis 互換 API 経由のみ
5. **CI で月 1 回 OpenNext(Cloudflare) ビルドを通す**（可搬性の回帰テスト。壊れたら固有依存が入った合図）

---

## 7. 世界展開時の問題

| 領域 | 問題 | 対策 |
|---|---|---|
| レイテンシ | DB が東京単一。US/EU から SSR → DB 往復 150〜250ms × クエリ数 | **Function は DB と同居（hnd1 固定）**、匿名ページは CDN キャッシュで配る。Vercel の multi-region は DB と離れて逆効果。Supabase Read Replica（同 compute 単価・対応リージョン一覧は未確認） |
| 転送単価 | Vercel FDT は地域で $0.15〜0.35/GB（東京 $0.16）。Edge Requests $2.00〜3.20/M | C（Workers）なら地域差なし |
| 画像 | R2 ＋ Cloudflare CDN は元から全球 | — |
| 法務 | GDPR / UK GDPR の**削除権**と正典 L1「物理 DELETE 禁止」が衝突 | 「匿名化＋ inactive」を削除相当と定義する裁定が必要（要裁定） |
| 年齢 | COPPA（13 歳未満） | 登録時の年齢確認文言 |
| 通報 | DMCA 代理人・各国の違法コンテンツ対応 | 正典の `content_reports` を国別に運用 |
| 決済 | サポーター課金の税務（VAT / 消費税） | Stripe Tax 等（未調査） |
| Cloudflare 規約 | Free/Pro/Business の CDN で「画像等の大容量配信が過大」だと制限され得る。**R2（有料 Developer Platform）経由の配信は規約上の正規経路** | 画像は必ず R2 経由。Vercel 由来の HTML を Cloudflare に通さない（Vercel 非推奨と一致） |
| Enterprise 圧力 | Cloudflare で大規模化すると営業主導で Enterprise 移行を迫られた事例（一方当事者の記述） | R2 / Workers は S3 互換・OpenNext で出口を維持 |

---

## 8. 請求爆発を防ぐ安全装置（多段ブレーキ）

### 8.1 Layer 0 — プロバイダ側の停止装置（初日に設定）

| プロバイダ | 設定 | 挙動（公式） | 注意 |
|---|---|---|---|
| Vercel | Spend Management: 予算額 **$60**（MVP）、通知 50/75/100%、**Pause production deployment ON** | 100% でチーム全プロジェクトの本番を pause。チェックは数分おき | pause = 全停止。解除は手動。予算に seat は含まれない |
| Supabase | Spend Cap **ON**（既定） | 対象項目の超過分は次サイクルまで不可（402 / read-only）。課金なし | **compute / Read Replica / PITR / Disk IOPS は対象外**。予算額指定・通知は不可 |
| Upstash | PAYG の **max monthly budget $10** | 到達でレート制限、超過請求なし。70/90% 通知 | — |
| Cloudflare | Budget alerts $10 / $50 / $200。Workers `limits.cpu_ms = 100` | **通知のみ・使用量は止まらない** | 支払い失敗で R2 データが 30 日後に削除され得る → **R2 の支払いカードは限度額で止めない** |
| Resend | Free（3,000 通/月・100 通/日）→ Pro | プラン上限で止まる | Supabase 内蔵 SMTP は 2 通/時で本番不可 |
| Supabase Auth | Custom SMTP ＋ 初期 30 通/時 | — | サインアップ爆発時のメール費を抑える |

### 8.2 Layer 1 — 常時有効の整流（設計で入れる）

1. **匿名リクエストは DB に到達させない**: Feed / Search / Browse / Detail の匿名ビューは `s-maxage=60〜300, stale-while-revalidate`。スパイクの大半は匿名なので CDN が吸収
2. Rate limit（正典暫定値を基本に、匿名を厳しく）: 投稿 10/分、コメント 5/5 分、画像 20/分、検索 ログイン 30/分・**匿名 10/分/IP**、通報 3/時、**グローバル検索 QPS 上限**（超過は「混雑中」）
3. **Turnstile**（無料）: サインアップ・初回投稿・通報・匿名検索の連打時
4. Bot: `robots.txt` で `/api` `/search?q=` を Disallow、Cloudflare「AI bot policies」（全プラン・Training/Agent をブロック、Search は許可）、画像ドメインの盗用は **WAF custom rule（referer 条件、Free 5 ルール）** で遮断。※標準の Hotlink Protection は jpg/png/gif のみ対応で **WebP/AVIF 非対応**、かつ Google 画像も遮断するため使わない
5. `view_count` は Redis `INCR` に溜めて 5 分ごとに flush（DB write を 1/100 に）
6. like / favorite は **trigger で counter 列を維持**（`COUNT(*)` を毎表示で走らせない）
7. Feed はカーソルページング＋上限 200 件、フォロー数上限 5,000
8. 画像はクライアントで **最長辺 2048px・1.5MB 以下**に縮小してからアップロード（10MB 上限はサーバ側の最終防衛）。バリアントはアップロード完了時にサーバで 3 種を事前生成して R2 へ（変換課金ゼロ、配信は CDN キャッシュ）
9. ユーザー当たり: 画像容量 **1GB**（正典の `SUM(file_size)`）、アップロード 100 枚/日、投稿 200 件/日（人間が届かない濫用上限。無制限方針と両立）
10. バグループ対策: 再帰しうる処理（画像再処理・通知・cron）に冪等キーと最大反復回数。MVP では Queues / Durable Objects を使わない

### 8.3 Layer 2 — 縮退モード（管理画面スイッチ ＋ 予算消化率で自動）

`system_mode` を 1 行の DB テーブル（＋Redis キャッシュ 30 秒）で持ち、Middleware と Server Action が参照する。

| モード | 発動目安（月予算の消化ペース） | 挙動 |
|---|---|---|
| **normal** | 〜60% | 通常 |
| **economy** | 60% | 匿名キャッシュ TTL ×5、Feed 20→10 件、匿名検索停止（ログイン誘導）、おすすめ再計算停止、OGP 生成停止、画像は `card` バリアントのみ |
| **restricted** | 80% | **新規登録一時停止**、**画像投稿一時停止**（テキスト投稿は可）、コメント制限半減、匿名 API 遮断 |
| **readonly** | 95% | 全書き込み 503（DB は生存・閲覧はキャッシュから）。Supabase 側も DB ロールを read-only に切替可 |
| **frozen** | 100% / Vercel pause | Cloudflare DNS を **Workers 静的ページ（無料・無制限）** の「混雑中・閲覧のみ」へ切替。DNS TTL 300 秒。Cloudflare API で自動化可 |

- 自動化: Cloudflare Cron Trigger（無料）が 1 時間ごとに Vercel / Supabase の使用量を取得し消化ペースを算出 → `system_mode` を更新 → LINE / メール通知。**使用量 API の可用性は要確認**（取れない項目は 50/75/100% のプロバイダ通知を代替）
- **管理画面の緊急スイッチ**は自動判定と独立に手動で任意モードへ。解除も手動（自動復帰しない）
- 「全停止の前に踏めるブレーキ」= **economy → restricted → readonly → frozen の 4 段**。frozen でもデータと閲覧は残る

### 8.4 Layer 3 — 財務側

- 月予算 = 通常請求の 3 倍を上限に。Vercel は Spend Management、Upstash は max budget、Supabase は Spend Cap で機械的に止まる
- **カードを分ける**: Vercel / Upstash = 限度額の低いカード（止まってよい）、Cloudflare / Supabase = 止めてはいけない（データ削除・DB 停止）
- R2 は **Backblaze B2 へ夜間 rclone**（$6.95/TB、Cloudflare 向け egress 無料）。Supabase は日次バックアップ（Pro 7 日）＋ 10 万 MAU から PITR

---

## 9. MVP 時点で入れておくべき制限（提案。正典の暫定値との差分）

| 項目 | 正典 | 提案 | 理由 |
|---|---|---|---|
| 画像 | 10MB / 8000px | **クライアント縮小 2048px・1.5MB → サーバ上限 10MB** | 保存 1/5、変換不要、R2 費 1/5 |
| 画像バリアント | Cloudflare Images variants 5 種 | **R2 に事前生成 3 種（public 1200 / card 400 / avatar 96）** | 配信・変換課金ゼロ。OG は `public` を流用 |
| 保存先 | Cloudflare Images | **R2**（`storage_key` 保存） | §5 #1 |
| 匿名検索 | 30/分 | **10/分/IP ＋ Turnstile** | Search は広告ゼロ面 = 費用のみ |
| 匿名ページ | — | **CDN キャッシュ必須（60〜300 秒）** | §8.2-1 |
| `view_count` | 直接 UPDATE | Redis バッファ | DB write 1/100 |
| like / favorite 件数 | `COUNT(*)` | trigger counter 列 | CPU 段階アップ回避 |
| Vercel 予算 | 既定 $200 | **$60 ＋ pause ON** | MVP の通常請求 $20 の 3 倍 |
| Supabase | Spend Cap 既定 ON | 維持。**Auth-MAU 8 万で裁定** | §0-4 |
| 開発環境 | 本番＋開発 2 プロジェクト（$35） | **CLI ローカル or 別 Free org**（$0） | 公式: Free と Pro は別 org なら併用可 |
| ユーザー容量 | — | 1GB / 100 枚/日 / 200 投稿/日 | 濫用上限のみ |
| 通知メール | — | Resend Free → Pro。Supabase Custom SMTP 必須 | 内蔵 SMTP は 2 通/時 |
| 可搬性 | — | §6 の 5 ルール ＋ 月 1 回 OpenNext ビルド | 出口の維持 |
| 動画 | 非対応 | 非対応のまま。将来も **Vercel 経由では絶対に配信しない**（Cloudflare Stream / R2） | 帯域単価 |

---

## 10. 成長後に解放してよい機能（トリガー付き）

| トリガー | 解放・変更 |
|---|---|
| 1 万 MAU | Supabase Small→Medium。Resend Pro。Cloudflare 有料機能不要 |
| **Vercel 請求 $300/月 超**（≒10〜30 万 MAU） | **app 層を Workers(OpenNext) へ**（DB・画像・Auth は不変）。Cloudflare Pro zone（WAF Managed Rules・Rate Limiting 2 ルール） |
| Auth-MAU 8 万 | Spend Cap OFF ＋ 自前監視、または Better Auth 移行計画 |
| 10 万 MAU | PITR、Supabase Large/XL、画像枠を「サポーター特典」で拡張（RIG 9→15 等）、LOG 本文 2,000 字（正典どおり） |
| 50 万 MAU | Read Replica（同 compute 単価）、検索を Typesense（東京 $0.03/h）等へ分離、通知（`notifications` テーブル）解禁 |
| 100 万 MAU | 4XL ＋ replica ×2、英語版本格展開（Workers は全球同一単価）、Realtime（Supabase $2.50/M msg）は最後まで慎重 |
| 収益化後 | 動画（Cloudflare Stream）、画像枚数の大幅緩和、Vercel Enterprise 交渉 or 完全 Workers |

---

## 11. 最終推奨構成

### MVP（0〜1 万 MAU、月 $45〜85）

```
Cloudflare DNS（DNS-only で Vercel を指す。緊急時に静的ページへ切替可）
├─ myrigrc.com        → Vercel Pro (hnd1, Fluid)  Next.js 15
│                        Spend Management $60 / pause ON
├─ img.myrigrc.com    → Cloudflare R2（Custom domain ＋ CDN cache ＋ referer WAF rule）
│                        バリアントはアップロード時に Vercel Function(sharp) で事前生成
├─ DB / Auth          → Supabase Pro (ap-northeast-1, Micro→Small)  Spend Cap ON
│                        RLS / PGroonga / pg_trgm。開発は CLI ローカル
├─ Rate limit / flags → Upstash Redis PAYG（max budget $10）
├─ Bot / 人間確認     → Cloudflare Turnstile（無料）＋ AI クローラブロック
└─ Email             → Resend（Supabase Custom SMTP）
```

### 成長期（10 万〜100 万 MAU、月 $320〜2,500）

- app 層のみ **Cloudflare Workers(OpenNext)** へ。Supabase / R2 / Upstash は不変
- Cloudflare Pro zone・WAF・Rate Limiting・KV の kill switch
- Supabase Large → 2XL ＋ Read Replica、PITR
- Auth は Supabase のまま（1M MAU で $975 = 収益連動費として許容）。Better Auth は「Auth 費が収益の 10% を超えたら」

### 採用しない

Neon（東京なし・従量 compute）、D1（10GB 上限）、Clerk / Firebase Auth（高価）、VPS 単独運用（ソロの時間コスト）、Cloudflare Images（配信課金）、Vercel Hobby（商用不可）、Cloudflare を Vercel の前段に置く構成（Vercel 非推奨）。

---

## 12. 公式値と推定値の区別・未確認事項

**公式（2026-09-05 確認）**: 本書の単価・プラン・Spend Cap / Spend Management / Budget alerts の挙動・リージョン・規約はすべて各社公式ページから転記。
**推定**: §4.1 のトラフィック変数、Provisioned Memory の按分、Hetzner CCX33 SIN 価格、DB サイズ、移行日数。

| 未確認 | 影響 |
|---|---|
| Vercel / Supabase の **使用量取得 API**（自動縮退の入力） | 取れなければ 50/75/100% 通知を人手で受ける |
| Supabase Read Replica の対応リージョン一覧（東京可否） | 世界展開時の遅延対策 |
| Supabase の新規プロジェクト既定 PG バージョン（15 or 17） | PGroonga / pg_search の可用性 |
| Cloudflare WAF で遮断したリクエストが Workers 課金に乗らないこと（処理順序上は乗らないはずだが明文未確認） | C の露出見積り |
| Vercel Global Config の write 単価（公式ページ 3 箇所で不一致） | 使わない方針なら影響なし |
| Vercel Pro の multi-region 数（3 or 5、公式ページで不一致） | 使わない方針 |
| Supabase JWT を自前発行した場合の MAU 課金の扱い | Better Auth 併用案の可否 |
| 事例 21 件のうち Cara / RetainDB / Cloudflare カジノ事案の最終決着 | 一方当事者の記述 |

---

## 13. 主要出典（公式）

- Vercel: /pricing, /docs/spend-management, /docs/limits/fair-use-guidelines, /docs/pricing/regional-pricing/hnd1, /docs/functions/usage-and-pricing, /docs/image-optimization/limits-and-pricing, /docs/vercel-firewall/ddos-mitigation, /kb/guide/cloudflare-with-vercel
- Supabase: /pricing, docs/guides/platform/cost-control, billing-faq, compute-and-disk, manage-your-usage/egress・monthly-active-users・read-replicas, free-project-pausing, auth/auth-smtp, auth/rate-limits, database/extensions
- Cloudflare: developers.cloudflare.com/images/pricing, /r2/pricing, /workers/platform/pricing・limits, /workers/static-assets/billing-and-limitations, /billing/manage/budget-alerts, /waf/rate-limiting-rules, /turnstile/plans, /workers/framework-guides/web-apps/opennext, service-specific-terms-application-services
- 代替: neon.com/pricing・docs/introduction/regions・spending-notifications, planetscale.com/pricing, hetzner.com/cloud, fly.io/docs/about/pricing・cost-management, railway.com/pricing・docs/pricing/cost-control, render.com/pricing, digitalocean.com/pricing, aws.amazon.com/lightsail/pricing, clerk.com/pricing, cloud.google.com/identity-platform/pricing, workos.com/pricing, better-auth.com, algolia.com/pricing, meilisearch.com/pricing, cloud.typesense.org/pricing, upstash.com/pricing/redis, resend.com/pricing, backblaze.com/cloud-storage/pricing, bunny.net/pricing
- 事例: news.ycombinator.com/item?id=39521986（Netlify $104k）, netlify.com/blog/introducing-netlify-free-plan, blog.cara.app/blog/finances-and-future-of-cara, metacast.app/blog/engineering/postmortem-llm-bots-image-optimization, blog.tomilkieway.com/72k-1, aws.amazon.com/about-aws/whats-new/2024/05/amazon-s3-no-charge-http-error-codes, mongodb.com（Flex tier）, docs.cloud.google.com/billing/docs/how-to/budgets-spend-caps
