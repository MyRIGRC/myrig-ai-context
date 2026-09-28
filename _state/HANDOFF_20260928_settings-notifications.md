# HANDOFF — 次スレッド: 設定・通知の作り直し（2026-09-28 17:35 JST / Cowork）

> revision: **MYRIG-20260928-119**（GitHub main。canon `4740422` / mock `6a7e579` で push 済み・Mac と一致）
> 前スレッド: 認証・オンボーディング（PC v2 09-27 / Mobile v2 09-28）を CLOSE。裁定原本 `_decisions/2026-09-28_auth-onboarding-close-v1.md`
> 次レーン裁定: 2026-09-28 17:26 イタヤ（GPT 同見解）=「設定・通知」

## 0. 作業開始時にやること（CORE / プロジェクト指示どおり）
1. `revision.txt` と `_AI/MyRIG_CURRENT.md` 冒頭の revision を**両方**取得して一致を確認（GitHub main または `~/Desktop/MyRIG/myrig-ai-context` を `git fetch` ＋ `git status -sb`）
2. `_AI/MyRIG_CORE.md` → `_AI/MyRIG_CURRENT.md`（NOW 119 ブロック）→ この HANDOFF → `_decisions/2026-09-28_auth-onboarding-close-v1.md` の順に読む
3. 回答冒頭に読み取った revision を明示
4. mock 作業コピーは Mac `~/Desktop/MyRIG/App/MOKUP/myrig_pc_Ver3` が正。着手前に `git status -sb` で origin/main（`6a7e579`）と一致を確認

## 1. 進める順番（イタヤ裁定 2026-09-28）
**① 設定全体の棚卸し → ②「ログインとセキュリティ」→ ③ 通知設定 → ④ 通知一覧**
- 最初の成果物は**実装ではなく**: 既存 PC / Mobile の Settings・Notifications 実体の監査 → 処遇表（carry / adapt / merge / defer / drop・pc-mobile-spec-inheritance §3）→ 新構成案。方向が決まってから作る
- ② が先頭なのは、Auth レーンの PENDING がそのまま入力になるため（§3）

## 2. 対象（Launcher `data-g="settingspages"`・要確認 2）
| 面 | Mobile | PC | 参照数（_archive・_state 除く） |
|---|---|---|---|
| 設定 `/settings` | `settings.html` | `pc/myrig-settings-pc-v0.2.5.html` | 57 ファイル |
| 通知 `/notifications` | `notifications.html` | `pc/myrig-notifications-pc-v0.1.1.html` | 53 ファイル |
- PC 設定 v0.2.5 の現構成: 左 settings-nav ＋ 5 セクション（プロフィール［プレビュー＋フォーム］/ アカウント / 投稿設定 / 表示・言語 / データ・退会）（inheritance #31）
- PC 通知 v0.1.1: ヘッダー（すべて既読）→ タブ（すべて / 未読）→ 日付グループ → 通知行 → 未読空状態 ＋ ヘッダー通知ドロップダウン（最新 8 件）（inheritance #32）
- 参照が多い → 新面を作ったら**リンク付け替えを同じバッチで**（Library 後片付けの型）。旧面は git rm（DECISION-4）

## 3. Auth レーンから持ち込む入力（PENDING → このレーンで扱う）
- **設定「ログインとセキュリティ」**: 接続中のログイン方法（Google / Facebook / メール）・ログイン方法の追加（identity linking）・連絡先メール（Facebook でメールが取れないアカウント向け）・Passkey・復旧コード
- **D3 の原則（裁定済み）**: 認証情報は Supabase Auth 側が正本で `profiles` へ写さない ／ **運営が公開プロフィールの情報だけでログインを戻したり移したりしない** ／ 救済は事前に登録した手段だけ
- 論理削除済み profile を持つアカウントの扱い（退会・復帰）＝ auth-guard-spec v1.3 §4.4 判定順 ① の「特別扱い」の中身 → 「データ・退会」と一緒に決める
- 同じメールの identity 統合 / 登録途中アカウントの長期の扱い / 本番の確認コード条件と独自 SMTP（本番条件は実装フェーズ。mock で確定しない）
- 興味カテゴリ: Onboarding から撤去 → **Settings へ**（PC Auth v2 裁定）

## 4. 読むべき正典・既知の制約
- `docs/ui/auth-guard-spec-v1.md` **v1.3**（§4.3 / §4.4 = 3 状態・優先順位。設定は「自分のものを扱う操作」= 登録完了が前提）
- `docs/schema/myrig_db_schema_v1_6.md`:
  - `profiles` にある設定系の列: `is_public` / `comments_enabled_rig_part` / `comments_enabled_log` / `preferred_rig_type` / `preferred_subcategory` / `website_url` / `social_links` / `country_code` / `deleted_at`（subdivision・地域公開列は**無い** = PENDING）
  - ⚠️ **Domain 9 `notifications` は「将来用・MVP では作成しない」**（type = like / favorite / follow / comment / comment_reply）→ 通知一覧・通知設定の扱い（MVP で出すか / 何を mock で確定するか）を最初に整理する。**スキーマ・migration は触らない**
- `docs/ui/mobile-component-contract-v0.5.md` §3.1: **Mobile Header に通知アイコン・アバターは置かない（裁定済み・再提案禁止）** → Mobile の通知の入口は別に考える
- `docs/ui/page-role-matrix-v1.md`: Settings = Utility「アカウント・プロフィール・通知・テーマ設定」/ Notifications = Relationship
- `docs/ui/region-behavior-matrix-v1.md`（Global Shell の通知）/ `pc-mobile-spec-inheritance-v1.1.md` #31 / #32
- 正典の古い記述（是正候補・Front-wide でまとめても可）: `docs/support/implementation_checklist.md` §1「username 未設定なら /onboarding」「/onboarding = 興味カテゴリ」→ v1.3 と不一致 / page-role-matrix「Onboarding = RIG 登録誘導」

## 5. Auth レーンで作った共通の土台（再利用する / 壊さない）
- `pc/assets/js/SoT_auth-flow.js`: `AUTH_METHODS`（ログイン方法の一覧はここから描く。面に直書きしない）・safeNext / buildAuthUrl / takeHandoff
- mock の platform 切替: `?platform=pc|mobile` ＋ `<html data-mock-platform>`（compare.html の iframe 間で漏れない）
- `SoT_auth-shell.js/css`: `.au-acct`（確認済みアカウントの行）など → 「接続中のログイン方法」の見た目の出発点にできる
- `SoT_error-state.js`（PC / Mobile 共有）/ `SoT_welcome-guide.js`（User Menu から章を再生）
- `js/mobile-shell.js`: LoginRequiredModal・guide・handoff を共有 API へ委譲済み（L1）
- Launcher は**カード = PC / Mobile の対**（data-mo / data-pc）。状態・流れも状態カード。⛔ 手書きの「PC確認 / Mobile確認」リンク行を作らない。compare.html の PAIRS も同時更新
- gate: `_state/auth_check.py`（A01〜A32・`--close`）/ `auth_browser_check.py`（B1〜B19）。新レーンの gate は同じ型で作る

## 6. 進め方（Library / Auth で確立した型）
1. 現状の棚卸し（旧 2 面 × PC / Mobile を実画面で・正典との差分）
2. 設計案を先に出して議論（イタヤ:「言ったことをそのまま実行するのではなく、構成を自分で組み立てる」）
3. 方向が決まったら PC / Mobile を一気に作る → gate → Launcher「見比べ」でイタヤ実機確認
4. CLOSE → 旧面 git rm・リンク付け替え・Launcher 確定・回帰（既存 gate の base 対 wt 比較）まで同じバッチ
5. canon: 裁定原本 `_decisions/` ＋ CURRENT ＋ revision 採番を同一 commit

## 7. 環境・運用メモ
- device VM に Playwright なし → ブラウザ検査は cloud（http.server ＋ Playwright）。ファイル書き戻しは `device_commit_files` → sha256 で一致確認
- device_bash の git は `-c maintenance.auto=false -c gc.auto=0` を付ける（`.git/*.lock` 残り対策）。author を `git -c user.*` で上書きしない
- device VM から GitHub へ push できない → mock はイタヤの `mockup`、canon はローカル commit → イタヤ push
- PC Garage v6 8 面は凍結 baseline（garage_check G1）→ 触らない

## 8. 恒久ルール（抜粋。CORE / プロジェクト指示が正）
Production DB 非接触 / migration 未実行 / 物理 DELETE 禁止 / commit・push は明示指示のみ / 正典への書き込みは Cowork に一本化 / 勝手に新 revision を採番しない / secret をチャットに出さない / JST 記録
