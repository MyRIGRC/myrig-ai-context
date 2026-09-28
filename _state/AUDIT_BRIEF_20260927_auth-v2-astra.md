# ASTRA 監査指示 — MyRIG PC Auth v2（認証・オンボーディング作り直し）（revision MYRIG-20260924-118 / mock 未 commit）

- 発行: 2026-09-27 12:10 JST（Cowork / Claude）
- 対象 revision: **MYRIG-20260924-118**（canon は GitHub main と一致。mock は `4ae40c3` に未 commit の変更 27 ファイル）
- 監査対象: mock `App/MOKUP/myrig_pc_Ver3/` の **PC Auth v2 の 5 面 ＋ 共有部品 5 本 ＋ 既存面への変更 14 本 ＋ gate 2 本**
- 監査対象外:
  - Mobile の認証 4 面（`auth.html` / `onboarding.html` / `welcome-tour.html` / `error-states.html`）。旧版のまま無変更
  - 旧 PC 4 面（`pc/myrig-auth-v1.html` / `-auth-onboarding-pc-v0.2.html` / `-welcome-tour-v0.1.html` / `-error-states-v0.1.html`）。CLOSE 時に撤去する
  - App Header 共通の青い「新規登録」（Front-wide で扱う）
  - HOLD「認証方式」（プロバイダの選定。催促しない）
- 作業記録: mock `_state/AUTH_EXPLORATION_20260927.md`（Claude の独立監査を 2 回実施し、その対応まで記載）

---

## 1. 裁定の状態

| | 内容 | 状態 |
|---|---|---|
| D-A | mock 側の旧 Auth 資料（`docs/auth-onboarding-minimum-spec-v1.md` 等）は参考資料。現行の canon より優先しない | 採用（2026-09-27） |
| D-B | Login / Signup / Onboarding は Auth Shell とし、App Shell（BottomNav を持つ側）の外に置く | 採用 |
| D-C | 5 ステップの Welcome Tour を廃止する。認証後は next を最優先し、無ければ /garage。ようこそは 1 回だけの toast | **R4 実機比較待ち**（比較モードを保持） |
| D-D | 新しい色相を足さない。エラーは既存 danger の文字とアイコン、正常は ✓ ＋中立色。主操作は中立の黒 | 採用 |
| D-E | Onboarding の必須はユーザー名だけ。任意は表示名・国。#34 の地域・興味を外す | **R4 実機比較待ち**（比較モードを保持） |
| D-F | ErrorState は Shell を持たない。Shell は発生した route 側が決める。OAuth の中断・失敗は Auth 面の中の状態として扱う | 採用 |

正典: auth-guard-spec-v1（**§4 の next の安全性は L1**）/ mobile-component-contract v0.5 §3.1 #13・§3.7・§4 / pc-mobile-spec-inheritance #33〜#35・#39・R2・R4 / design-nogo-list（L1）/ region-behavior-matrix §3-F / CORE の Shared UI Single Source（L1）

---

## 2. 作ったもの

### 面（PC）
| 面 | ファイル | 状態の切り替え |
|---|---|---|
| ログイン | `pc/myrig-auth-login-v2.html` | `?next=` / `?error=cancelled\|failed` / `?mock-user=new\|existing` |
| 新規登録 | `pc/myrig-auth-signup-v2.html` | 同上 |
| はじめの設定 | `pc/myrig-auth-onboarding-v2.html` | **`?fields=proposed`（提案）/ `?fields=carry`（現行 #34）** / `?next=` |
| ようこそ（mock 専用の比較面） | `pc/myrig-auth-welcome-v2.html` | **`?mode=drop`（提案）/ `?mode=carry`（現行 #35）** / `?next=` / `?reset-tour=1` |
| エラー | `pc/myrig-error-states-v2.html` | `?state=not-found\|private\|server-error\|suspended\|garage-not-found\|maintenance` / `&host=app\|minimal\|bare` |

各面の右下にある「MOCK 確認用」で、状態・next・テーマ・面を切り替えられます（本番には出ない）。

### 共有部品（新規）
- `pc/assets/js/SoT_auth-flow.js`
  - 持っている機能: safeNext / nextState / buildAuthUrl / withNext / preserveNext / resolvePostAuthDestination / resolveAfterOnboarding / validateUsername / AUTH_METHODS / LOGIN_CONTEXT_TEXT
  - `[data-auth-link]` の href もここが埋める
- `pc/assets/js/SoT_auth-shell.js` ＋ `pc/assets/css/SoT_auth-shell.css`
  - 描くもの: Header / showcase / 認証方式ボタン / OAuth の結果表示 / 同意文と法務導線 / ようこそ toast / 確認バー
- `pc/assets/js/SoT_error-state.js` ＋ `pc/assets/css/SoT_error-state.css`
  - `<error-state data-state>`
- テーマは既存の `SoT_app-shell.js` の bootstrap を使う（Auth 面に独自実装を持たない）

### 既存面への変更
| ファイル | 変更 |
|---|---|
| `pc/assets/js/SoT_entity-actions.js` | PC LoginRequiredModal の変更は 3 点:<br>・文言と next は MyRIGAuth から取る<br>・**主操作をログインに**（§3.1。旧実装は逆）<br>・開閉を `MyRIG.overlay` に載せ、focus trap・Escape・scroll lock・focus 復帰を付けた（§3.2） |
| PC 10 面（Feed / RIG・PARTS・LOG Detail / Owner Detail 2 / Public Garage 4） | entity-actions の前に `SoT_auth-flow.js` を読む |
| `pc/myrig-feed-v3.html` | ・`/login?next=%2Ffeed` の直書き 4 本を `data-auth-link` に置き換え<br>・ゲスト用の空状態にある follow 文言を「ガレージ」に修正 |
| `js/mobile-shell.js` | safeNext を 3 点修正:<br>・§4.1 の同一オリジン確認と、制御文字の拒否を追加<br>・ループ判定に locale と大文字も含める<br>・受け取った値を返す（二重 decode の修正）<br>あわせて DEPRECATED 注記を追加 |
| `index.html` / `compare.html` | 認証グループの PC 側 4 本を v2 に差し替え |

### gate
- `_state/auth_check.py`: A01〜A20 FAIL 0 / selftest 20/20
- `_state/auth_browser_check.py`: B1〜B10 FAIL 0 / 33（Playwright）
- 既存の gate 42 本は、変更前と変更後で結果が同じ

---

## 3. 見てほしいこと（優先順）

1. **next の安全性（L1）**
   - 悪性の値が `/` に落ちるか: `//` / `/\` / scheme 付き / encode した変種 / 制御文字
   - ループ防止が効くか: `/login` `/signup`、locale 付き、大文字、`/./`
   - 正当な値が壊れないか: `%25` `%26` `%2B` `#hash` を含む next が、login → signup → onboarding → 着地 と往復しても元のまま残るか
   - **canon §4.2 の実装メモ（decode した値を返す）と、今回の実装（受け取った値を返す）の違いは正しいか**。canon を改訂するべきか意見を
2. **既存面の退行**
   - LoginRequiredModal を載せ替えた PC 10 面: Feed / Detail / Public Garage で、いいね・保存・フォローが従来どおり動くか
   - Mobile 面で bottom-sheet への委譲が生きているか
   - `mobile-shell.js` の safeNext を変えた影響
3. **§4.3 の遷移**
   - 既存ユーザー: next があれば next、無ければ /garage
   - 新規ユーザー: onboarding を経て next へ
   - next が onboarding を指しているときは、そこに着地させない
   - login ↔ signup の往復で next を落とさないか（#13）
4. **D-B / D-D / D-F の実装**
   - Auth 面が App Shell の部品を持っていないか
   - 法務導線がどの面・状態でも**ちょうど 1 か所で、しかも押せる**か（§3-F）
   - 色が NG-7 どおりか（緑なし・青い CTA なし・黒の塗りは主操作だけ）
   - ErrorState が Shell を持っていないか
5. **R4 比較の公平さ**
   - `?fields=` / `?mode=` の carry 側が現行 #34 / #35 を忠実に再現しているか
   - 比較面の作りが、どちらかの案に有利に見えるようになっていないか
6. **文言とアクセシビリティ**
   - 禁止語が無いか
   - 「削除」「退会」と断定していないか
   - 内部ラベルを表示していないか
   - h1 が 1 つか / live region / aria-invalid / focus 順（390 幅を含む）
7. **gate**: A01〜A20 と B1〜B10 に、空振りで PASS する検査が無いか。見逃している契約があれば、gate にする提案を

## 4. 報告形式
MUST（契約違反・退行・事故）/ SHOULD（改善）/ 意見（裁定が要るもの）/ gate にできるか、に分けて書く。各項目に再現手順（URL と操作）を付ける。

⛔ ファイルを直接書き換えない。⛔ Production DB 非接触。⛔ commit / push しない。
