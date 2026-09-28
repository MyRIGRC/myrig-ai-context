# HANDOFF — 認証・オンボーディング Mobile v2（2026-09-27 22:00 JST / Claude）

canon: MYRIG-20260924-118（revision 採番なし・この HANDOFF は未 commit）
前段: `_state/HANDOFF_20260927_auth-onboarding.md`（レーン開始時）→ 本 HANDOFF（PC CLOSE 後・Mobile 着手前）

## 0. 最初に必ず読む
1. revision 突き合わせ（revision.txt と _AI/MyRIG_CURRENT.md 冒頭）→ CORE → CURRENT → 本 HANDOFF
2. mock 作業コピー: `~/Desktop/MyRIG/App/MOKUP/myrig_pc_Ver3`（リポジトリ MyRIGRC/myrig-mockup）
   - ⚠️ **PC Auth v2 の変更はすべて未 commit**（基点 `4ae40c3`）。GitHub の main には無い。**Mac の作業コピーが正**（クラウドで clone し直すと失われる）
   - 変更 44 件（新規・変更）＋ 削除 4 件（旧 PC 4 面）。一覧と経緯は mock 側 `_state/AUTH_EXPLORATION_20260927.md`（**これが PC v2 の全記録。最初に読む**）
   - commit / push はイタヤの明示指示があるまでしない
3. 実行環境の注意（今日の学び）: Mac の VM で `git status` 等を叩くと `.git/index.lock` が残ることがある → device 側で git を実行しない。device_commit_files は SVG に C2PA メタデータを注入する → バイナリ・SVG は base64 tar で書く。クラウドの長い gate 連続実行は環境の再起動で途中停止する → 1 本ずつ結果を記録し、止まったら続きから

## 1. PC v2 の到達点（CLOSE 済み 2026-09-27）
- 面: `pc/myrig-auth-login-v2.html` / `-signup-v2.html` / `-onboarding-v2.html` / `pc/myrig-error-states-v2.html`、はじめてガイド（Garage Top 等で `?guide=1` / `?guide=garage`）
- 裁定（すべて採用・詳細は AUTH_EXPLORATION）
  - D-A 旧 mock 資料は参考 / D-B Auth Shell は App Shell の外 / D-D 新しい色相なし（主操作は中立）/ D-F ErrorState は Shell を持たない
  - D-C はじめてガイド = 実画面 Spotlight 型・**2 章**（第 1 章「MyRIGの基本」6 段 / 第 2 章「Garageの使い方」11 段・任意開始）。第 1 章末で「Garageの使い方も見る」「ここで終える」（⛔「あとで見る」は禁止語）。アカウントメニューから章別に再実行
  - D-E はじめの設定: 必須はユーザー名だけ（3〜24 字・maxlength 24）。任意 = 表示名・国・地域（ISO 3166-2 の選択式・自由入力なし）・**地域の公開は明示 opt-in（開くと必ず OFF）**・国旗 SVG（flag-icons MIT）。興味カテゴリは Settings へ
  - 文言: WELCOME TO MYRIG / MyRIGへようこそ / 基本プロフィール / MyRIGをはじめる
  - 右プレビュー = **「あなたの公開プロフィール」カードの完成見本だけ**（カバー・アイコン・表示名・@username・国旗・国・地域・小さい RIG / PARTS / LOG）。⛔ RIG の空枠・登録予告・ブラウザ枠
  - アイコン / カバー画像は任意（カード下から設定・外す）。保存先は現行 schema `profiles.avatar_url` / `cover_image_url`
  - next: あり → 元のページ＋「ようこそ」toast（ガイドなし）/ なし → /garage＋ガイド第 1 章。E2E は実在する未ログイン操作から（Feed いいね / 公開ガレージ フォロー）。mock 専用 `?guest=1` は next に入れない
  - CLOSE 済み面のリンク先だけの横断移行は再 OPEN 扱いにしない（Garage Top v7 のログアウト 1 行を付け替え済み）
- 監査: 独立監査 3 回・外部（Gemini / ASTRA）→ すべて対応。ASTRA 最終監査 M1（連続スラッシュ）修正 → 再監査 MUST 0・CLOSE 可
- gate: `_state/auth_check.py`（A01〜A26・`--selftest` 43/43・**`--close`** = 旧 Auth 面への参照 0）/ `_state/auth_browser_check.py`（B1〜B15・88 項目）

## 2. 共有部品（Mobile でもここを使う。面に複製しない = L1）
| ファイル | 持つもの |
|---|---|
| `pc/assets/js/SoT_auth-flow.js` | safeNext（canonPath 正規化）/ nextState / buildAuthUrl / preserveNext / resolvePostAuthDestination / completeOnboarding（一度だけの引き継ぎ）/ validateUsername / AUTH_METHODS / LOGIN_CONTEXT_TEXT / currentPath |
| `pc/assets/js/SoT_auth-shell.js` + `pc/assets/css/SoT_auth-shell.css` | Auth Shell（ヘッダー・showcase・方式ボタン・OAuth 結果・同意文と法務導線・確認バー）。**幅はレスポンシブ 1 本 = Mobile Auth もこの CSS を読む前提** |
| `pc/assets/js/SoT_geo.js` | 国・地域・国旗（⚠️ mock 用の部分実装。本番は Geo Master = PENDING） |
| `pc/assets/js/SoT_welcome-guide.js` + `.css` | ガイドの表示・章・Spotlight（next を見ない）|
| `pc/assets/js/SoT_error-state.js` + `.css` | `<error-state data-state>` 6 状態 |
| `pc/assets/js/SoT_app-shell.js` | 引き継ぎの受け取り（toast / ガイド起動）・メニューの章別再実行（PC App Shell）|

## 3. Mobile v2 でやること
1. **showcase 写真のローテーション（イタヤ 21:55）**: 開くたびに「公式が用意した画像」から 1 枚を選ぶ。定義は SoT_auth-shell.js の `SHOWCASE` 1 か所を配列にする（PC・Mobile・はじめの設定の帯の全部に効く）。🟡 本番の画像選定・運用（公式セット）は PENDING として記録
2. 旧 Mobile 4 面の置き換え: `auth.html` / `onboarding.html` / `welcome-tour.html` / `error-states.html` → Mobile v2（R2 / R4 = pc-mobile-spec-inheritance に従う。PC v2 の裁定を継承し、Mobile で変える点は理由を書く）
   - 参照元（撤去前に 0 にする）: auth.html ← welcome-tour.html / feed.html / onboarding.html / about.html / js/mobile-garage-top.js / index.html / compare.html。onboarding.html ← auth.html / index / compare。welcome-tour / error-states ← index / compare
3. はじめてガイドの Mobile 版: Mobile の実画面（Mobile Garage / BottomNav）に合わせた対象と文言。**実在 UI だけ**。章構成と「任意開始・スキップ・章別再実行」は PC と同じ
4. `js/mobile-shell.js` の DEPRECATED 分（safeNext / LOGIN_CONTEXT_TEXT / buildAuthUrl 相当）を SoT_auth-flow.js の呼び出しへ置き換え（mobile-shell の safeNext は連続スラッシュ対策まで入れてあるが、二重実装は撤去が目標）
5. 実在 E2E を Mobile でも（Mobile Feed / 公開ガレージの未ログイン操作 → 新規登録 → はじめの設定 → 元のページ）。Launcher に Mobile の確認入口
6. Mobile CLOSE 時: 旧 Mobile 4 面を `git rm`（DECISION-4）・Launcher の認証グループを確定表示へ・`auth_check.py --close` を Mobile まで広げて 0

## 4. PENDING（CLOSE を止めない）
- Settings v0.2.5: 国・地域の独自一覧を SoT_geo に揃える / `current_*_image_id` 注記を `profiles.avatar_url` / `cover_image_url` へ是正（Settings レーン）
- Geo Master: 全世界 ISO 3166-1 / 3166-2・source / version / active・物理削除なし・地域表示名のローカライズ（「アーカンソー州」等）
- profiles の地域コード・地域公開可否の列（App schema・migration 未実行）
- 画像の差し替え・削除用 asset ID を別に持つか（画像実装時）
- canon 提起: auth-guard-spec §4.2 の「decode した値を返す」メモの改訂 / CURRENT HOLD「認証方式」の参照先 §7 が現行文書に無い
- `#userMenu` を持たない PC 詳細系ではガイドを再実行できない（Front-wide のヘッダー不揃い）
- 既知 gate: hit_test 既存 1 件（library-search-v2 @720）/ header_propagation はクラウドに比較元 commit が無く実行不能（今回起因ではない）

## 5. CURRENT 反映候補（イタヤ承認待ち）
- [STATE] PC Auth v2 CLOSE（2026-09-27）。ASTRA 最終監査 MUST 0。旧 PC 4 面撤去・旧 Auth 参照 0。変更は mock 作業コピーに未 commit
- [DECISION] はじめてガイド 2 章構成（任意開始・章別再実行）/ はじめの設定の右 = 公開プロフィールカードの完成見本 / 地域公開は明示 opt-in / CLOSE 済み面のリンク先だけの横断移行は再 OPEN 扱いにしない
- [NOW] Mobile v2（上の §3）

## 6. 進捗（2026-09-27 23:20 JST / Claude）
- ⚠️ §0 訂正: PC Auth v2 は `221a8fa`（21:52 mock update）で commit・push 済み。Mac 作業ツリー = 221a8fa。以下は 221a8fa からの未 commit 差分
- §3-1 showcase ローテーション: 済（SHOWCASE_SET 7 枚・直前と同じ 1 枚を避ける・はじめの設定の帯は引き継ぐ・`?showcase=N`）
- §3-2〜5: 済（未 commit）。記録 = mock `_state/AUTH_MOBILE_V2_20260927.md`（処遇表つき）
  - Auth 面は PC と共通（本番 1 route）。mock だけ platform で「ホーム」「ガレージ」を読み替え（`?platform=mobile` / 直前の App Shell）
  - Mobile ErrorState = `error-states-v2.html` / はじめてガイド = 同じ部品に Mobile の段（第 1 章 6・第 2 章 11）/ mobile-shell の DEPRECATED 分を撤去 → MyRIGAuth（takeHandoff も 1 か所）
  - gate: auth_check A01〜A31 FAIL 0・selftest 56/56 / auth_browser_check FAIL 0 / 97 / 既存 17 本は変更前後で同一（garage_integrity GIP3 の期待値だけ更新）
- 🟡 R4 裁定待ち: Mobile のログイン・新規登録で写真の帯を出すか（案 = 出す / PC v2 CLOSE 時点・GPT = 出さない）。比較 = 確認バー「Mobile の写真」`?m-showcase=off`
- 残り（§3-6 CLOSE バッチ）: イタヤ実機確認 → 旧 Mobile 4 面 `git rm`・Launcher 認証グループを確定表示・`auth_check.py --close` 0（いま FAIL 4 = 旧 4 面のファイルだけ）→ commit / push はイタヤ指示後

## 7. 進捗（2026-09-28 09:55 JST / Claude）
- mock HEAD = `80b8019`（イタヤ push 済み: Mobile v2・Launcher の見比べ化まで）。以下は 80b8019 からの未 commit 差分
- イタヤ裁定（GPT 同見解）: **R4 = Mobile のログイン・新規登録は写真なし**（はじめの設定の帯は残す）/ **認証方式 = Google・Facebook・メール（6 桁コード・パスワードなし）で「○○で続ける」**。Apple・X は MVP の外（X はアプリ取得済み）。Instagram でのログインは Meta が一般アカウント向けに提供していないので置かない
- はじめの設定に「どのアカウントで作るか」＋「別のアカウントでやり直す」
- 救済・個人情報の方針（CURRENT 反映候補）: 認証情報は Supabase Auth 側だけ（profiles へ写さない）・電話番号は持たない・運営の手作業でログインを戻さない・設定「ログインとセキュリティ」は設定レーン
- gate: auth_check A01〜A32 FAIL 0・selftest 62/62 / auth_browser_check 99 / 0
- 残り: イタヤ実機確認 → Mobile Auth CLOSE（旧 4 面 git rm・Launcher 確定表示・`--close` 0・回帰）

## 8. 進捗（2026-09-28 13:05 JST / Claude）
- 登録の流れを確定・シミュレーション（PC / Mobile 各 10 通り）→ mock `_state/AUTH_MOBILE_V2_20260927.md`「登録の流れ」節
  - 登録完了 = 「MyRIGをはじめる」でユーザー名確定。認証後の行き先は「ユーザー名があるか」だけ（途中でやめた人も はじめの設定へ）・「すでに登録されています」は出さない
  - はじめの設定の先頭 =「〜でログインしています／ログアウトして選び直す」・確認コードの全角と空白入りの貼り付け・再送 60 秒・二度押し止め
- gate: auth_check A01〜A32 FAIL 0・selftest 65/65 / auth_browser_check 101 / 0（B19 = 流れのシミュレーション）/ launcher_link 231 / 0
- auth-guard-spec への提案 R-a〜R-f（ログイン中の /login 転送・完了者の /onboarding 転送・登録途中の操作制限・同じメールの統合・途中アカウントの扱い・確認コードの条件）
- 残り: イタヤ実機確認 → Mobile Auth CLOSE

## 9. Mobile Auth CLOSE（2026-09-28 16:40 JST / Claude）— レーン完了
- イタヤ実機 3 回（ログイン・はじめの設定・Mobile の流れ）→ 15:58「CLOSE で進めて」
- 旧 Mobile 4 面 git rm（DECISION-4）/ Launcher 認証グループ確定表示 / `auth_check.py --close` FAIL 0 / 回帰 17 本 = base と同一（GIP3 は検査の解析を修正 → 610/0）
- mock 未 commit 差分（4a61b9a から）: 削除 4・index.html・_state（AUTH_MOBILE_V2・garage_integrity_check）
- canon 未 commit: `docs/ui/auth-guard-spec-v1.md` v1.3（§4.3 改訂・§4.4 新設）・本 HANDOFF・`HANDOFF_20260927_auth-mobile-v2.md` 自身
- **commit バッチ（イタヤ指示後）**: mock = `mockup`（CLOSE 一式）/ canon = auth-guard-spec v1.3 ＋ CURRENT NOW 節（下の反映候補）＋ revision 119 採番を同一 commit
- 次のレーン: イタヤ裁定待ち（予定 = 設定・通知。「ログインとセキュリティ」= 接続中のアカウント・ログイン方法の追加・連絡先メール・Passkey・復旧コード を含む）
