# HANDOFF — 次スレッド: 情報・法務・サポートの作り直し（2026-09-29 17:25 JST / Cowork）

> revision: **MYRIG-20260929-121**（この引き継ぎの push で採番）。直前 = 120（設定・通知レーン CLOSE・canon `cd63994`）/ mock `e6b76eb`（2026-09-29 17:22 mockup 済み）
> レーンの順番は Launcher「次のレーン」どおり: 設定・通知（CLOSE）→ **情報・法務・サポート** → Garage 系。別のレーンにするならイタヤ裁定で差し替える

## 1. 最初にやること
1. revision.txt と _AI/MyRIG_CURRENT.md 冒頭の revision が一致することを確認（CDN キャッシュ対策）
2. この引き継ぎ書と、直前のレーンの裁定原本 `_decisions/2026-09-28_settings-notifications-v1.md`（とくに D8 退会・D9 データのエクスポート・CLOSE の PENDING）を読む
3. 現物の面を開いて棚卸し（下の §2）→ Claude 案 → ASTRA / GPT → Claude 判断 → イタヤ裁定

## 2. 対象の面（いまの現物）
| 面 | PC | Mobile | 正典での扱い |
|---|---|---|---|
| ヘルプ・お問い合わせ・フィードバック・報告/通報 | `pc/myrig-support-legal-report-pc-v0.1.html`（1 ファイルに同居） | `help.html` | **どの正典にも記載なし**（page-role-matrix 冒頭の注記） |
| 規約・ポリシー（利用規約 9 条・プライバシー 8 条・特商法） | 同上 | `legal.html` | 記載なし。本文は [DRAFT]・法務未確認 |
| MyRIG とは | `pc/myrig-about-v0.1.html` | `about.html` | pc-mobile-spec-inheritance #36（PROPOSED） |
| 応援する（支援・金額・使い道） | `pc/myrig-support-us-v0.1.html` | `support-us.html` | 同 #37（PROPOSED）。課金は schema Domain 8（将来用） |
- **リンクの行き先の棚卸しが要る**: フッター（`SoT_footer.js`）は `/legal/privacy` `/legal/terms` `/legal/tokushoho`、Mobile ユーザーメニューの「情報」（`js/mobile-shell.js` INFO_LINKS）は `/about /guide /company /contact /help /privacy /terms /legal/tokushoho` を持つ。**/guide /company /contact に当たる面が無い**・route の書き方が 2 系統
- ほかのレーンからこのレーンに来る導線: 設定「ログインとセキュリティ」の「自分で戻せないときは ヘルプ から問い合わせ」（乗っ取りの救済）/ 退会（D8）/ データの開示請求（D9 = 運営が /admin で書き出す・案内はお問い合わせ）/ 通報（schema `content_reports` `comment_reports`）/ 通知の「重要なお知らせ」→ /about 系の案内

## 3. このレーンで必ず考えること（「MVP だから考えない」にしない）
- 面の構成と route（1 ファイル同居の分け方・PC / Mobile の対応・フッターとメニューの定義を 1 か所に）
- お問い合わせ・通報の入口と、運営側に届く先（管理画面 /admin は未着手 → 受け皿の形だけ決める）
- 規約・プライバシーポリシーに、決まった事実を正しく書くこと（ログイン方法・メールの扱い・退会 30 日・消去の範囲・通知・外部サービス（Supabase / Cloudflare / Vercel 等））。⚠️ 法的な要否・文面は**要確認（Claude は法律の専門家ではない）**。[DRAFT] 表記は外さない
- 応援する（支払い）: 特商法の表記・課金の扱いは Domain 8（将来用）との関係を決める。偽の領収書・決済画面に見えるものを作らない
- 色・形の規約（NG-1〜7）・Mobile 48px・Shared UI Single Source（PC / Mobile で中身を 1 か所から描く。設定・通知の `SoT_settings.js` 型）

## 4. 前のレーンで分かった進め方（そのまま使う）
- **主査チェック 5 つ**（提案・実装の前）: 既存の正典を同じ概念の別名まで検索 / 画面の約束 1 つごとに成り立たせる DB・権限・処理を指す / 書き込みの経路を全部数える / 新しい列を誰が読めるか / 採る案に対して採らない理由のある別案を 1 つ
- 裁定を受けても、実装の前に弱点を一言書く（黙って「はい」と言わない）
- **前提を渡さない監査**: 小さな是正の確認は Claude のサブエージェント（正典・経緯を渡さない）。ASTRA は節目だけ（1 回で 5 時間枠の約 95% を使う）
- browser gate を作って回す（例 `_state/settings_browser_check.py`）。故障を入れて FAIL することも確かめる
- CLOSE のときは Launcher の状態表示（グループ「確定 N」・「いま」・「次のレーン」）も更新する（設定・通知で一度忘れた）

## 5. 作業環境のメモ
- Mac の正典は Cowork から直接読める。**Mac の端末から GitHub へは push できない**（認証なし）→ 正典は `git bundle` を作って作業環境へ渡し、作業環境（`myrigrc/myrig-ai-context` を追加済み）から push した（revision 120）
- 正典フォルダ・モックフォルダとも、このセッションでは削除を許可済み（新しいスレッドでは改めて許可が要る）。git の lock が残ったら `.git/_to_delete_stale_lock/` へ移す
- モックの公開はイタヤの `mockup`（Claude は commit しない）
