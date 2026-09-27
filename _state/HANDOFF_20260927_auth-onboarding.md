# HANDOFF — 次スレッド: 認証・オンボーディングの作り直し（2026-09-27 09:32 JST / Cowork）

> revision: **MYRIG-20260924-118**（GitHub main。mock `4ae40c3` / canon `4369836` 時点で push 済み）
> 前スレッド: Library レーン（PC v5 → Mobile v5 → 後片付け）を CLOSE。

## 0. 作業開始時にやること（CORE / プロジェクト指示どおり）
1. `revision.txt` と `_AI/MyRIG_CURRENT.md` 冒頭の revision を**両方**取得して一致を確認（GitHub main または `~/Desktop/MyRIG/myrig-ai-context` を `git fetch` ＋ `git status -sb`）
2. `_AI/MyRIG_CORE.md` → `_AI/MyRIG_CURRENT.md`（NOW ブロック）→ この HANDOFF の順に読む
3. 回答冒頭に読み取った revision を明示

## 1. いまどこにいるか
- **確定（緑）**: Home / 検索 / Feed / ブラウズ / 登録・投稿 / **Library（PC ＋ Mobile 8 面）**
- **要確認（オレンジ）= 数か月前の旧モックのまま。今回の作り直しの流れでは一度も触っていない**:
  認証・オンボーディング 4 / 設定・通知 2 / 情報・法務・サポート 4 ／ ほか Garage 系（group--wip: My Garage / Owner Manage / Public Garage）・ログ詳細 要確認 1
- **イタヤ裁定（2026-09-27）**: Front-wide（全面横断）は**後回し**。オレンジのグループを 1 つずつ作り直して CLOSE し、**全部そろってから Front-wide を 1 回だけ**やる（作り直す面に横断修正をかけても消える / やり直しになるため）。
- 次のレーン = **認証・オンボーディング**（新しい利用者が最初に通る入口。Library・Register の「ログインが必要」からもつながる）

## 2. 次レーンの対象（Launcher `data-g="authpages"`）
| 面 | Mobile | PC |
|---|---|---|
| ログイン / 新規登録 | `auth.html` | `pc/myrig-auth-v1.html` |
| はじめの設定（オンボーディング） | `onboarding.html` | `pc/myrig-auth-onboarding-pc-v0.2.html` |
| ウェルカムツアー | `welcome-tour.html` | `pc/myrig-welcome-tour-v0.1.html` |
| エラー状態パック | `error-states.html` | `pc/myrig-error-states-v0.1.html` |
関連: Launcher の「認証・状態デモ」（`index-e-roomclip.html?guest=1` / `?login-modal=1`）

## 3. 読むべき正典・既知の論点
- `docs/ui/auth-guard-spec-v1.md`（L2。**§4 `next` パラメータ安全性は L1** = open redirect 対策）: P1 Redirect `/login?next=` / P2 Login Required Modal / P3 Public Mode Modal / 文脈別文言（#14）
- `docs/ui/mobile-component-contract-v0.5.md`: §3.1 Header の未ログイン「新規登録」ピル（#13 成立条件: signup ファーストビューに「登録済みの方はログイン」/ next を落とさない / onboarding 経由で next へ）、§3.7 LoginRequiredModal、§3.8 認証ガード境界
- `docs/ui/pc-mobile-spec-inheritance-v1.1.md` #33 / #34（OAuth プロバイダ）/ `docs/ui/page-role-matrix-v1.md`
- ⚠️ **HOLD（CURRENT）: 認証方式** — プロバイダとメール認証の有無が 4〜5 文書で不一致。一次資料 `auth-onboarding-minimum-spec-v1` は repo 未収録。**実装フェーズのビジネス判断 = 今は催促しない**。モックは「プロバイダ一覧は差し替え可能な 1 か所」にしておく
- ⚠️ `profiles` の RLS（`id = auth.uid()`）は「onboarding / profile 編集の設計と一緒に決まる」（CURRENT の残置 4 件）→ オンボーディングで触れる
- auth-guard-spec が前提にしている `nextjs-routing-table-v1` / `appheader-interaction-spec-v1` / `dialog-interaction-spec-v1` / `error-states-decomposition-MR-AUDIT-002` も repo 未収録（必要になったら所在確認）

## 4. 進め方（Library で確立した型）
1. **現状の棚卸し**（旧 4 面 × PC / Mobile を実画面で確認・正典との差分）
2. **設計案を先に出して議論**（⚠️ イタヤ指摘 2026-09-25:「言ったことをそのまま実行するのではなく意見を出す。議論して詰める」）
3. 方向が決まったら **PC ベースで一気に仕上げる**（イタヤ 2026-09-25:「GPT とのやり取りは指示が細かすぎる。まず一気に仕上げて、ASTRA 監査で詰める」）
4. 監査: ASTRA（利用制限時は Claude の**独立監査エージェント** = 別コンテキストで欠陥を探す役。旧版との状態比較 ＋ 変異注入）
5. イタヤ実機確認 → CLOSE → 旧面の退避・リンク付け替え・Launcher 更新まで同じバッチで

## 5. Library で作った共通の土台（再利用する / 壊さない）
- 118 D20「リフォームしやすい土台」: 値はトークン / 部品は 1 か所 / name・id・slug 分離 / gate。Next.js では版番号を名前に持ち込まない
- Mobile Shell 契約: `js/mobile-library-shell.js`（SubHeader ＋ タブ ＋ BottomNav ＋ 登録シート ＋ `LIB.shell` の Mobile 実装）。BottomNav の markup を面に複製しない
- `?theme=` は「その面だけの表示指定」（自分の URL にだけ残し、リンクには入れない。Mobile 裁定 M7）
- 戻り状態は `from=`（safeFrom で自サイト内のみ）。Mobile ヘッダー「←」も from= に従う
- gate: `_state/library_check.py`（L01〜L60）/ `_state/mobile_library_check.py`（M01〜M17）/ `_state/library_browser_check.py`（B1〜B7・Playwright）

## 6. 環境・運用メモ（前スレッドで踏んだもの）
- Cowork の device VM には **Playwright が無い** → ブラウザ検査・スクショは cloud 側で。ファイルは device 側で編集し、tar にまとめて stage → cloud で展開して検査
- device_bash で git を使うと `.git/*.lock` が残ることがある → 削除許可（MyRIG フォルダ）を取ってから、0B・git プロセス無しを確認して除去
- **`mockup` は mock だけ**を commit / push する。canon は Cowork がローカル commit → イタヤが `git push origin main`（device VM に GitHub 認証が無い）
- Front-wide の既存 gate（42 本）は 2 コアの cloud で並列 6 だと遅い・一部が負荷でフレーク → 差分が出たら単独で再実行して確認。環境依存で両方動かない 4 本（detail_dom_parity / header_propagation / shelf_propagation / webgrammar_batch1）と、既存 FAIL 2 本（mobile_register_check static FAIL 1 / hit_test 1 FAIL）は Library 作業前から同じ
- **PC Garage v6 8 面は凍結 baseline**（garage_check G1 がバイト不変を要求）→ 触らない
- PC v3 Library 7 面は Front-wide 検査の fixture として pc/ に残置（導線からのリンク 0）→ P-M7

## 7. 持ち越し（Front-wide でまとめて）
- P-M7: PC v3 Library 7 面を検査 fixture から外して `_archive/` へ
- Mobile 全面の BottomNav 共通化（Library Shell は同じ API で先行）
- CURRENT 記載の Front-wide 優先項目: Mobile 9 面の「＋」createSheet 欠落 / 廃止 LOG 種別の残存 / PC の invalid route / shared footer の `#` link / theme persistence 欠落（PC Register・Composer・Auth）
- Library PENDING: P-M1 カテゴリ chip の sticky / P-M3 Top 新着件数（実機で必要なら）/ `--dt-action` と `--lib-buy` のグローバル化

## 8. 恒久ルール（抜粋。CORE / プロジェクト指示が正）
Production DB 非接触 / migration 未実行 / 物理 DELETE 禁止（mv で退避）/ commit・push は明示指示のみ / 正典への書き込みは Cowork に一本化 / 勝手に新 revision を採番しない / force push 禁止 / secret を出さない / ファイル時刻は JST（ZoneInfo）/ 回答は日本語・結論先・最適解 1 つ / 外部 AI への指示には「ファイルを直接書き換えない・Production DB 非接触・commit / push しない」
