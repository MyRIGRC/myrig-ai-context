# 情報・法務・サポート 方向裁定 v1（2026-09-29）

> 🟢 **状態: OPEN（方向裁定済み・mock 未着手）**。イタヤ 2026-09-29 19:51「進めましょう」「基本的に全部 OK」で 7 点（3 は 3′）を採用。

**裁定者:** イタヤ（2026-09-29 19:51）／ 提案: Claude（Cowork・主査）／ 監査: GPT（正典取得済み・3 回）・Gemini（正典未確認・3 回）
**revision:** MYRIG-20260929-122（この裁定の push で採番）／ 直前 = 121（レーン引き継ぎ）
**レーン:** 情報・法務・サポート（引き継ぎ `_state/HANDOFF_20260929_info-legal-support.md`）
**正典への反映:** `docs/schema/myrig_db_schema_v1_6.md` **v1.6-r11** ／ `docs/ui/auth-guard-spec-v1.md` **v1.5** ／ `docs/ui/page-role-matrix-v1.md`（Info / Legal / Support 行）／ `docs/ui/pc-mobile-spec-inheritance-v1.1.md` #36 #37 #38 ／ `docs/support/implementation_checklist.md` 4-2
⛔ Production DB 非接触。migration 未実行。物理 DELETE 禁止。⚠️ 法的な要否・文面は全部「要確認」（Claude / GPT / Gemini は法律の専門家ではない）。[DRAFT] は外さない。

---

## 経緯

Claude が現物（PC 3 面 / Mobile 4 面 / footer / mobile-shell / settings / error-state / schema / auth-guard / mock 側 2026-05 設計文書）を棚卸し → 案 v1（P1〜P9）→ GPT・Gemini 監査 → Claude 判断で v2 → 再監査 → v3 → イタヤ裁定。
初案の穴は監査で 2 者が独立に同じ指摘をしたものが多い（news 廃止・reason 統一・user 通報の同居・問い合わせの D8 処遇・金額ボタン）。下の「罠」は途中の案で実際に出た穴。

## D1 面と route（mock 7 面 / 公開 route 8 本 ＋ 予約 1）

| 面 | route | ログイン | PC mock | Mobile mock |
|---|---|---|---|---|
| MyRIG とは | `/about` | 不要 | `pc/myrig-about-v0.1.html`（「運営者」節を足す） | `about.html` |
| 使い方 | `/help` | 不要 | support 面 `?view=help` | `help.html?view=help` |
| お問い合わせ | `/contact` | 不要（匿名可） | support 面 `?view=contact` | `help.html?view=contact` |
| 通報（URL 指定） | `/report` | D5 | support 面 `?view=report` | `help.html?view=report` |
| お知らせ | `/news` | 不要 | 新規（announcements の公開 read-only） | 新規 |
| 利用規約 | `/legal/terms` | 不要 | legal 面 `?view=terms` | `legal.html?view=terms` |
| プライバシーポリシー | `/legal/privacy` | 不要 | legal 面 `?view=privacy` | `legal.html?view=privacy` |
| 特定商取引法 | `/legal/tokushoho` | — | **予約・非公開**（D7） | 同 |
| 応援する | `/support-us` | 不要 | `pc/myrig-support-us-v0.1.html` | `support-us.html` |

- **作らない**: `/feedback`（`/contact?kind=feedback` で種別を選んだ状態で開く）/ `/guide`（= `/help`）/ `/company`（= `/about` の運営者節）/ 「ヘルプセンター」（= `/help`）
- **redirect**: `/terms` → `/legal/terms`、`/privacy` → `/legal/privacy`、`/support` → `/support-us`（OAuth コンソール等に旧 URL を登録済みかは**不明** → redirect を置けば要確認が消える）
- **`/news`** = schema r10 で `announcements` は SELECT 全公開なのに公開 UI が無かった逆向きの不整合を解く。障害・OAuth 不調・規約改定を未ログイン・登録検討者・停止中の人へ届ける唯一の経路。**緊急告知をキャッシュしない**（動的取得）
- mock の面の分け方: PC は 1 ファイル 7 タブを **support（help / contact / report）と legal（terms / privacy / tokushoho）の 2 ファイル**へ分け、Mobile の `help.html` / `legal.html` と対応させる。節は `?view=` で開ける（`settings-v1.html?view=` の既存慣習）。切替で `replaceState`。旧 PC 面の「Empty State」タブは別責務なので外す（`docs/empty-state-spec-v1.md` の参照は要確認）
- Shell: サイドバーなし・BottomNav なし・中央本文型（mock 側 spec §4）。未ログインはロゴ＋ログインだけの最小ヘッダー

**なぜ**
- 法務 route を `/legal/*` に寄せる: 09-13 裁定（LEGAL の真源）と `implementation_checklist` 4-2 が `/legal/*`。2026-05 の routing-table（`/terms /privacy`）が古い
- 面を減らす判断は「MVP だから」ではなく、実体が 1 つのものに名前が 3 つ付いていた（使い方 / ヘルプセンター / guide）から

**罠**
- Claude v1「お知らせは通知受信トレイで足りる」→ 受信トレイは要ログイン。DB は全公開・UI は会員だけ、という逆の不整合（GPT M11・Gemini 前提 2）
- Claude v1「mock 7 面」→ 公開 route は 8 本。数え方を「mock 7 面 / route 8 本 ＋ 予約 1」に固定（GPT）
- Gemini「/feedback を別面に」→ メール必須 / 任意の衝突は kind 別のサーバー検証（D4）で解けるので面は増やさない

## D2 導線の Single Source（`SoT_site-links.js`）

- 新設 `pc/assets/js/SoT_site-links.js`: INFO（about / help / contact / news）・LEGAL（terms / privacy / tokushoho）・SUPPORT（support-us）を `{ key, label, route, href: { pc, mobile }, enabled }` で 1 か所に持つ
- **consumer**: `SoT_footer.js`（COLS / LEGAL / support）・`js/mobile-shell.js`（INFO_LINKS）・`SoT_app-shell.js`（PC 34 面のユーザーメニュー「ヘルプ」の href）・`SoT_settings.js`（`FILES.help` → contact）・`SoT_error-state.js`（suspended の `/contact`）・`SoT_notifications.js`（重要なお知らせ → `/news`）・`SoT_auth-shell.js`（同意文の 利用規約 / プライバシー をリンクに）
- Mobile が `pc/assets/js/SoT_*.js` を読む前例 = settings / notifications
- **09-13 裁定 §2-1「LEGAL の真源 = `SoT_footer.js`」は置き場所だけ失効。内容契約（文言・並び・行き先を面が手書きしない）は継承**。旧 `LEGAL` は `DEPRECATED 2026-09-29 / successor: SoT_site-links.js / remove after: 参照 0 を gate で確認` を書いて移行期間だけ残す
- footer の COLS から `ops`（運営情報）・`news`（→ INFO へ移す）・`helpcenter` キーを撤去。サービス列 = MyRIGとは / 使い方 / お問い合わせ / お知らせ
- `enabled: false` の項目は DOM に出さない（tokushoho・support-us）
- gate: 83 面（PC 41 ＋ Mobile 42）で site-links が consumer より前に読み込まれていることを全件検査

**罠**
- Gemini S-3「読み込み順の事故を避けるため app-shell に同居」→ Mobile は `SoT_app-shell.js` を読まない。分離して gate で順序を検査する

## D3 ほかの面から来る導線（総合）

| 入口 | 行き先 | 備考 |
|---|---|---|
| PC footer（41 面） | INFO 4・LEGAL 2（tokushoho は非表示）・support-us（非表示） | D2 |
| PC ユーザーメニュー「ヘルプ」（34 面） | `/help` | app-shell が site-links から差す。22 面の `#` を解消 |
| Mobile ユーザーメニュー「情報」（42 面） | INFO 4 ＋ LEGAL 2 | data-href の toast を実リンクへ |
| 設定「ログインとセキュリティ」（乗っ取りの救済） | `/contact?kind=account` | 「ヘルプから」→ お問い合わせに直接 |
| 設定「アカウントの管理」 | 開示請求 = `/contact?kind=data_request`（D9） | 面は作らない・案内だけ |
| `/resume`（退会猶予中） | 文言「お問い合わせはログアウト後に」 | 例外化しない（D8 維持・D8 節） |
| アカウント制限（suspended） | `/contact?kind=account&from=suspended` | kind 固定・変更不可 |
| 通知「重要なお知らせ」 | `/news` | 現 mock は `about.html` |
| Help の CTA | `/contact` / `/contact?kind=feedback` / `/report` | |
| Auth 同意文 | `/legal/terms` `/legal/privacy` | 現 mock は文字だけでリンクなし |
| コメント ⋯ / 公開ガレージ ⋯ / RIG・PARTS・LOG の ⋯ | 面内ダイアログ `SoT_report.js`（D5） | PC は現状 処理なし |
| Library 詳細「情報修正を報告する」 | 別系統（`master-correction-report-spec-v1`） | このレーンの外 |

## D4 お問い合わせの受け皿 `support_inquiries`

- 列: `kind`（account / content / bug / feedback / data_request / rights / other）・`email`・`subject`・`body`・`related_url`・`user_id`（NULL 可・FK profiles）・`status`（open / replied / closed）・`created_at`・`handled_by`・`handled_at`
- **書き込み経路は 1 本 = サーバー側**（Server Action / RPC は実装時）。クライアント INSERT の RLS ポリシーは作らない。**client から直接 SELECT 不可。運営者は `/admin` のサーバー側経路（service role）だけ。RLS はポリシー 0**（GPT MUST 3）
- **`user_id` はサーバー側 session からだけ導出**。client 値は捨てる。**session があり、かつ `profiles` 行が存在するときだけ**入れる。未ログイン・**登録途中（session あり・profiles なし）**は NULL（`auth.users.id` をそのまま入れると FK 違反 — GPT commit 前確認 ①）
- **email の必須はサーバーで kind 別に検証**: 必須 = account / rights / data_request、任意 = content / bug / feedback / other
- **Suspended は書込不可。例外は `kind='account'` だけ**（救済の申し立て）。Suspended 画面からは `?kind=account&from=suspended` で種別を固定・変更不可
- **受付と本人確認を分離**: data_request / account 救済は受け付けた後に本人確認の工程へ。確認できるまで開示・移行・ログインの復旧をしない（Auth D3「公開プロフィールの情報だけで戻さない」と同型）。手順は**法務 要確認**
- 匿名経路の悪用対策: rate limit ＋ bot 対策（Cloudflare Turnstile 要確認）
- 運営への通知メールは「新規 #ID / kind」だけ。本文は /admin で読む（メールに個人情報の写しを増やさない）
- 完了文言は kind ごと（mock 側 spec §6〜8 の 3 文を使う）
- **D8 確定処理での扱い**: `user_id` を NULL に切り離すだけ。email / body は運営記録として残す。**保持期間は 🔴 HOLD（法務）・Release Blocker**。確定まで期限列・自動 NULL 化ジョブ・仮の月数を作らない（CORE「HOLD を確定値として扱わない」）。行は消さない

**なぜ**
- テーブルを持つ: D9 開示請求の記録・/admin のキュー（未着手・受け皿の形だけ）・退会確定処理との関係を持てる。メールだけでは持てない
- /feedback を吸収: 受け皿も経路も同じ。違いは「返信を約束しない」の 1 文で、kind ごとの完了文で出せる

**罠**
- Claude v1 は `user_id` の出所を書いていなかった → client が別人の id を送れる（GPT M3）
- Claude v1「Suspended の Server Action 403 = 採用」と「kind=account は通す」が矛盾 → 「原則不可・例外は account だけ」に一本化（GPT M1）
- Claude v2 の必須条件で content / other が抜けた（GPT M2）
- Claude v2「保持期間 N か月を schema の placeholder に」→ HOLD の確定値扱い（GPT M3）
- Gemini M-1「D8 で profiles を消すと FK 違反」は誤り。D8 は profiles 行を匿名化して残す（FK は壊れない）。ただし処遇未決という指摘自体は正しかった

## D5 通報

- **3 表**: `comment_reports`（現行 4 reason 維持）/ `content_reports`（現行 6 reason 維持・**`wrong_info` を残す**・entity_type は rig / part / log のまま）/ **`user_reports` 新設**（`target_user_id` FK profiles・reason = impersonation / harassment / spam / inappropriate / other）
- フォームの `unsafe_or_illegal` は `inappropriate` に畳む（ラベル「不適切・危険・違法なコンテンツ」）。`impersonation` は user_reports だけ
- **書き込み経路 1 本 = サーバー側関数**（SECURITY DEFINER RPC / Server Action は実装時）。reporter は session から。**reporter の状態は関数の中で判定**（登録途中・退会猶予中・確定処理中・Suspended は拒否。UI / middleware を境界にしない — GPT MUST 1）。対象の実在・self-report の拒否・reason の値・開いている重複・rate limit を INSERT 前に検証。**直接 INSERT の RLS ポリシーは削除**。SECURITY DEFINER なら固定 `search_path`・作成時に `REVOKE EXECUTE FROM PUBLIC` → authenticated だけ GRANT・同一 transaction（RPC を迂回路にしない — GPT ②・MUST 2）
- **UNIQUE は「開いている通報だけ」**: `(対象, reporter) WHERE status IN ('open','reviewing')` の partial unique。内容が変わった後に再通報できる
- 主経路 = **面内ダイアログ `SoT_report.js`**（PC modal / Mobile sheet を 1 部品で）。対象 comment / rig / part / log / user。ログイン必須。自分の投稿には出さない。重複は「すでに受け付けています」。完了文「報告を受け付けました。即時対応を保証するものではありません」
- `/report` 面 = URL 指定の通報。ログイン中は同じダイアログ。**未ログイン（権利者・非会員）は `support_inquiries` kind='rights'**（メール必須）。通報 3 表に匿名 INSERT は作らない
- `reporter_user_id` は NOT NULL のまま。D8 は profiles を匿名化して残すので FK は壊れず、参照先に個人情報は残らない
- /admin は 4 表を VIEW / UNION で 1 queue（/admin 未着手・形だけ）
- 停止（suspended）> 再開（/resume）は既存のガード順（Maintenance > Suspended > 登録状態判定 ①）で成立する。新しい規則は要らない。**停止中アカウントの 30 日確定処理を止めるかは PENDING（/admin レーン）**

**なぜ**
- user を別表: `target_user_id` に FK を張れる。処置が違う（投稿 = 非公開化 / ユーザー = 停止）ので同じ表に混ぜると管理画面の取り違えを誘発する
- reason を統一しない: `content_reports` はユーザー投稿用で、投稿の誤情報（`wrong_info`）の行き場が要る。情報修正 spec は Library マスター専用

**罠**
- Claude v1「reason を 1 組に統一・wrong_info は情報修正 spec へ」→ ユーザー投稿の誤情報を通報できなくなる（GPT M5・Gemini M-6）
- Claude v1「user を content_reports の entity_type に」→ FK が張れない・処置が違う（GPT M6・Gemini M-5）
- 現行 UNIQUE は永久 1 回 → resolved 後に内容が変わっても再通報できない（GPT M7）
- 現行 RLS「auth.uid() なら INSERT 可」を残すと、rate limit・self-report 拒否・対象の実在確認を直接 INSERT で迂回できる（GPT M4）

## D6 規約・プライバシーに書く事実（[DRAFT] のまま・文面は法務確認前）

**決まった事実（文面と食い違っているもの）**
- ログイン方法 = Google / Facebook / メールの確認コード。パスワードを持たない。**Apple は MVP の外**（現 mock は「Google・Apple」）
- 認証情報（メール・provider）は Supabase Auth 側が正本で `profiles` へ写さない
- 通知はアプリ内のみ。重要なお知らせ（規約変更・重大障害・ログイン方法の変更）は止められない
- 退会 = 30 日の猶予・本人が再開・確定処理の範囲（プロフィールの匿名化・画像の実ファイル削除・投稿の自由入力の消去・コメント withdrawn・ユーザー名の再利用不可）
- 開示請求の窓口 = お問い合わせ（kind=data_request）
- ブロック・ミュート
- 外部サービス = Supabase（Auth・DB）/ Vercel（配信）/ Cloudflare Images（画像）/ Google・Facebook（OAuth）
- 問い合わせ・通報で取得する情報（email / body / reporter）と利用目的 ← **D4 / D5 確定後に差分監査 1 回**（GPT S5）

**要確認として残す（断定しない）**
- Cookie 同意モデル（`implementation_checklist` 4-1 HOLD。このレーンでは裁定しない）
- 解析・広告・アフィリエイトの開示と免責
- 利用年齢の下限（Google・Facebook の規約との整合）
- 事業者の名称・住所・代表者 / 個人情報の利用目的 / 開示・訂正・利用停止の手続 / 苦情・問い合わせ窓口 / 安全管理措置の概要（GPT S4・個人情報保護委員会のガイドラインの枠組み）
- 投稿コンテンツの運営による利用許諾 / モデレーションの裁量（無通告の非公開化・停止）（Gemini C-1）
- 準拠法・管轄（placeholder のまま）
- 非公開・退会後の画像 URL の到達性（S13 PENDING。確認前に規約へ書かない）

## D7 応援する・特定商取引法（3′）

- **応援するは作るが、MVP 公開時は出さない**。面は mock で作り切る。footer・メニューへの表示は site-links の `enabled` で止める。**出す時期はイタヤが決める**（数か月後でよい）
- 出す条件 = 外部サービス確定 ＋ 法務確認（特商法の要否を含む）
- Phase 1 = 外部サービス経由（MR-PLAN-002・第一候補 OFUSE・予備 BOOTH。**採用は未確定**。手数料・規約は 2026-05 時点の値）。自前決済なし
- **金額ボタン（¥300 / 500 / 1,000）は撤去**。CTA は「応援ページへ（外部サイト）」1 本。外部先未確定なことを mock で正直に出す
- 文言: 「寄付」「投げ銭」「送金」を使わない（MR-PLAN-002）。PC の alert「Stripe Payment Links」・Mobile の「応援は寄付であり…返金は…特商法」を直す
- Supporter バッジは public 文面から外し「サポーター向けの仕組みを検討中」に下げる。データは将来（Domain 8 `user_plans`）
- **特商法** = route 予約・面は非公開。enabled 条件 = 決済先決定 ＋ 法務確認完了。`implementation_checklist` 4-2「アフィリエイト収益がある → 特商法表示」は**要確認に降格**（アフィリエイトだけで販売者になるとは言えない）
- 特典: **Phase 1 = 特典なし / Phase 2 = `/plans` で特典**（MR-PLAN-002）を維持。単発応援に特典を付けたいなら MR-PLAN-002 の改訂 = 別レーン（Domain 8 と一緒に設計）
- pc-mobile-spec-inheritance #37 の `/support` → `/support-us` に訂正

**なぜ**
- 外部先の金額 UI を MyRIG に置くと「ここで決済される」と誤認させ、外部側とズレた瞬間に偽 UI になる（GPT S6・Gemini S-1）
- バッジ「予定」は外部支援と将来の `user_plans.supporter` を同じものに見せる（GPT S7・Gemini S-2）
- 特商法 DRAFT を公開しない: 存在しない販売の事業者情報・返品規定を placeholder で出す。外部経由なら販売者が誰かも要確認

## D8 到達性（Suspended / 未ログイン / 乗っ取り / 退会猶予中 / Maintenance）

| 状態 | /contact | /help | /legal/* | /news | /report | /support-us |
|---|---|---|---|---|---|---|
| 未ログイン | 可（匿名） | 可 | 可 | 可 | rights のみ（D5） | 可（公開後） |
| ログイン | 可 | 可 | 可 | 可 | 可 | 可 |
| Suspended | **kind=account だけ** | 可 | 可 | 可 | 不可 | 不可 |
| 退会猶予中（session あり） | **不可 → /resume**。ログアウト後に匿名で | 不可 | 不可 | 不可 | 不可 | 不可 |
| 乗っ取り被害（ログイン不能） | 未ログインと同じ。kind=account | 可 | 可 | 可 | — | — |
| Maintenance | 不可（全面 /maintenance） | 不可 | 不可 | 不可 | 不可 | 不可 |

- auth-guard-spec §5 の **Suspended redirect 除外 = `/account-suspended` ＋ `/contact` `/help` `/legal/*`**（`/about` `/news` `/report` `/support-us` は除外しない）
- **退会猶予中は例外化しない**（v1.4 §4.3 ①「猶予中は再開の処理以外を書き込めない・出口は 再開 / ログアウトだけ」= CLOSE 済み D8）。`/resume` に「お問い合わせはログアウト後に」の 1 行
- 乗っ取り被害の文言は Auth D3 と矛盾させない（「確認できる範囲で案内」。公開プロフィールの情報だけで戻さない）

**罠**
- Gemini M-2「猶予中の /contact を除外パスに」→ CLOSE 済み D8 を黙って変える（GPT M9）。「アクセスで意図せず復活」は D4 / D8（本人の明示選択のみ）で既に成立しない

## D9 ゲート・正典化の範囲

- `_state/info_browser_check.py`: site-links に `#` が無い / INFO・LEGAL の行き先が実在 / `?view=` 深リンクが効く / **ユーザー可視本文**に Apple・寄付・投げ銭・Stripe が無い（コメント・fixture は対象外） / [DRAFT] バッジあり / Mobile 48px / PC 34 面のヘルプが site-links 由来 / 83 面の読み込み順 / Suspended 画面のお問い合わせが実リンク（kind 固定）/ 旧 route（/terms /privacy /support /guide /company /feedback）の参照 0。**故障を入れて FAIL することも確かめる**
- 正典に固定 = route 表・redirect・受け皿テーブル・通報 3 表・書き込み経路 1 本・site-links の真源・法務に書く事実の一覧・Suspended 除外・3′。文言・レイアウト・FAQ 項目・完了文は mock が仕様

## HOLD / PENDING

- 🔴 **HOLD（法務・Release Blocker）**: `support_inquiries` の保持期間。本番公開前に解消（未決のまま本文・メールを無期限保持しない）
- 🔴 **HOLD（法務）**: 本人確認の手順（data_request / account 救済）
- **PENDING（/admin レーン）**: 停止中アカウントの 30 日確定処理を止めるか / 4 表 UNION の queue
- **PENDING（別レーン）**: 単発応援への特典（MR-PLAN-002 改訂）/ 外部支援サービスの採用 / 特商法の要否
- **PENDING**: Cookie 同意モデル（checklist 4-1・既存 HOLD）/ `docs/empty-state-spec-v1.md` の参照先

## 失効した旧記述

- 09-13 裁定 §2-1「法務導線の真源 = `SoT_footer.js` の LEGAL」→ 置き場所だけ失効（D2）。内容契約は継承
- pc-mobile-spec-inheritance #37 `/support` → `/support-us`
- `implementation_checklist` 4-2「アフィリエイト収益がある → 特商法表示」→ 要確認
- mock 側 `docs/nextjs-routing-table-v1.md` §(support) の `/terms /privacy /feedback`・「Mock の統合タブ UI は本番で使わない」→ D1（PC は 2 ファイル ＋ `?view=`。本番 route は routing-table どおり別 page）
- mock 側 `docs/support-legal-report-minimum-spec-v1.md` §8 通報理由 7 種・対象 6 種 → D5
- mock 側 `docs/support-payment-policy-MR-PLAN-002.md` §9「金額ボタン」→ D7
