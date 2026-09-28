# 認証・オンボーディング CLOSE 裁定（2026-09-28）

**裁定者:** イタヤ（実機 3 回・2026-09-28 09:38 / 14:20 / 15:58）／ 提案: Claude（Cowork）／ クロスチェック: GPT（同見解）
**revision:** MYRIG-20260928-119 ／ **対象:** PC v2（2026-09-27 CLOSE）＋ Mobile v2（2026-09-28 CLOSE）
**記録原本（mock）:** `_state/AUTH_EXPLORATION_20260927.md`（PC）／ `_state/AUTH_MOBILE_V2_20260927.md`（Mobile・認証方式・登録の流れ・シミュレーション）
**正典への反映:** `docs/ui/auth-guard-spec-v1.md` **v1.3**（§4.3 改訂・§4.4 新設）

---

## D1 認証方式（HOLD「認証方式」→ 裁定）

**MVP = 「Googleで続ける」「Facebookで続ける」「メールアドレスで続ける」（6 桁の確認コード・パスワードなし）。**
ログイン面と新規登録面で同じボタン・同じ文言。OAuth もメールも、初回はそのまま登録・2 回目からはログイン。
⛔ パスワード・電話番号は持たない。Apple・X は MVP の外（X はアプリ取得済み。足すときは `SoT_auth-flow.js` の `AUTH_METHODS` に 1 行）。

**なぜ**
- Facebook: RC のコミュニティが世界的に厚く、アプリも取得済み（イタヤ）。Instagram でのログインは Meta が一般アカウント向けに提供していない（2024-12 Basic Display API 終了）ので置かない。Meta 系は Facebook で受ける
- パスワードなし: 漏れ・使い回し・リセット画面の一群が消える。趣味のサービスで持つ理由が弱い
- 電話番号なし: SMS 費用・番号の再割当・SIM 乗っ取り・国際対応。個人情報として重い割に復旧手段として強くない
- Apple: Web OAuth は secret の 6 か月更新と Developer 登録が要る。ネイティブ iOS アプリを出す時に再検討
- 「○○で登録」「○○でログイン」の書き分けは OAuth の実態と合わない（同じ操作）→「○○で続ける」
- Facebook はメールが取れないアカウントがある → ログインは成立。連絡先メールは設定で足すよう勧める（設定レーン）

## D2 登録完了の定義と認証の 3 状態

**登録完了 = はじめの設定を終えて、有効な `profiles`（行があり・`deleted_at IS NULL`）を作った時点。認証が成功しただけでは登録完了としない。**
登録途中（auth.users あり・profiles なし）の間は profiles を作らない（schema v1.6 `profiles.username NOT NULL` と整合）。

| 状態 | `/login`・`/signup` | `/onboarding` | 自分のものを扱う操作 | 公開ページ |
|---|---|---|---|---|
| 未ログイン | 表示 | `/login?next=` | P1 / P2 / P3 | 閲覧可 |
| 認証済み・登録途中 | → `/onboarding`（next 保持） | 表示 | → `/onboarding?next=`（実行しない） | 閲覧可 |
| 登録完了 | → 安全な next / `/garage` | → `/garage` | 通過 | 閲覧可 |

ガード優先順位: **Maintenance > Suspended > 登録状態判定 > P1 / P2 / P3**（Suspended を「profile が無いから Onboarding へ」と誤って流さない）。
実装時の判定順: ① 論理削除済み profile あり → 特別扱い（PENDING）／ ② 有効 profile あり → 登録完了 ／ ③ profile なし → 登録途中。
⛔「このアカウントはすでに登録されています」は出さない（あれば同じアカウントにログインするだけ）。

**罠**: 判定を「username 未設定か」にすると、username NOT NULL の schema では「username の無い profiles 行」を作れず矛盾する（GPT 指摘）。論理削除済みの profile を「profile なし = 登録途中」に落とすと、Onboarding の profile 新規作成が同じ id の既存行と衝突する。

## D3 救済と個人情報

- 持つ個人情報は認証に要るものだけ。認証情報（メール・provider）は Supabase Auth 側が正本で、`profiles` へ写さない
- **運営が公開プロフィールの情報だけでログインを戻したり移したりしない**（公開ガレージを見れば誰でも「本人です」と言えるため、乗っ取りの入口になる）
- 救済は事前に登録した手段（別のログイン方法・連絡先メール・将来の Passkey / 復旧コード）だけ → 設定「ログインとセキュリティ」（設定レーン・PENDING）

## D4 画面（PC / Mobile 共通の Auth 面。Mobile 専用の Auth 面は作らない）

- **R4**: Mobile のログイン・新規登録は、認証操作より前に写真を置かない。写真は認証の内容（方法・切替・同意文）の後に「Open your garage.」の帯として、最初の画面の残りを埋める（240〜420px）。はじめの設定の帯は「はじめて MyRIG に入る導入」として別の役割で残す。
  罠: フォーム側の行を伸ばす（min-height:100vh の grid）と、背の高い枠で写真だけが下へ離れる（compare.html で実際に起きた）
- **はじめの設定**: 先頭は確認済みアカウントの行（サービスのアイコン＋✓「Googleアカウントを確認しました」／メール／「このアカウントで MyRIG の登録を続けます。」／「別のアカウントを使う」）。灰色の箱＋ⓘ・「ログインしています」は警告に見え、「もうログインしているのに何の画面？」となった
- **ボタンは「登録を完了する」**（「MyRIGをはじめる」は Google で続けた時点と何が違うのか分からない）
- **PC**: 右の列 = 見本 → 画像の設定 → 登録を完了する（1 枚の sticky カード）。左の国の下にボタンがあると、画像を設定できることに気づかないまま登録してしまう
- **Mobile**: 基本プロフィール → 見本（画像の設定）→ 登録を完了する の 1 枚のシート（3 枚のカードにしない。影は隙間に見える）。帯の文は 1 行、「ユーザー名以外はあとから変更できます」は基本プロフィールの見出しの下（読む場所に置く）
- **はじめてガイド**: 同じ部品・同じ 2 章に Mobile の実在 UI の段（第 1 章 6・第 2 章 11）。Library は BottomNav に無い → 「探す」の説明で入口を伝える
- **ErrorState**: 完全共有。Mobile で変わるのは App の器だけ（`error-states-v2.html`）
- **mobile-shell**: 自前の safeNext / 文言 / `/login?next=` を撤去し `SoT_auth-flow.js` へ一本化（L1 Shared UI Single Source）
- 確認コード: 全角・空白入りの貼り付けを受ける（maxlength を付けない）／ 再送 60 秒 ／ 「MyRIG をはじめる」の二度押しで 2 回保存しない ／ コード画面に行き先の一文

## 撤去

旧 8 面（PC `myrig-auth-v1` / `-onboarding-pc-v0.2` / `-welcome-tour-v0.1` / `-error-states-v0.1`、Mobile `auth` / `onboarding` / `welcome-tour` / `error-states`）を git rm（DECISION-4・`_archive` へ複製しない）。参照 0（`auth_check.py --close`）。

## 失効した旧記述

- pc-mobile-spec-inheritance #33（Google・X・Facebook・メール認証なし）/ #34（Google・Apple）/ #35（静的 5 枚の Welcome Tour）/ #39（エラーのタブ切替 1 面）→ 本裁定で置き換え（文書側の改訂は Front-wide 監査時）
- auth-guard-spec §4.3「新規ユーザー（username 未設定）」→ v1.3 で「有効な profiles なし」へ

## PENDING（CLOSE を止めない）

設定「ログインとセキュリティ」／ 論理削除済み profile を持つアカウントの扱い（退会・復帰）／ 同じメールの identity 統合（Supabase linking）／ 途中で残った登録途中アカウントの長期の扱い ／ 本番の確認コード条件（失効 10 分・試行・再送 60 秒・送信上限）と独自 SMTP ／ Facebook アプリの公開設定（審査・データ削除窓口）／ 本番の showcase 公式セット（選定・許諾・運用）／ Geo Master ／ profiles の地域コード・地域公開列
