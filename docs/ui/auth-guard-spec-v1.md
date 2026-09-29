# Auth Guard / Login Required UI Spec v1

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> いまモックアップ制作フェーズ。デザインとサービス概念を**議論しながら作る**段階なので、
> 本書は「今こうなっている」という出発点であって、議論の打ち切りではない。
> **「既存仕様と異なる」ことだけを理由に案を捨てないこと。**
> 差分を明示すればイタヤ裁定で変更できる。
>
> ただし **§4「`next` パラメータ安全性」は L1**（open redirect 対策。セキュリティ）。

**作成日:** 2026-05-22 (MR-AUDIT-002 / A7)
**最終更新:** 2026-09-29
**ステータス:** 確定 v1.5
**前提:** `auth-onboarding-minimum-spec-v1` / `nextjs-routing-table-v1` / `appheader-interaction-spec-v1` / `dialog-interaction-spec-v1`
⚠️ **上記4本＋`error-states-decomposition-MR-AUDIT-002` は本repo内に未収録。**
参照が必要になった時点で所在を確認すること。

> ✅ **2026-08-22 イタヤ裁定・解消済み（GPT監査A・revision020→021監査で追加是正）**:
> §5 skeleton を「matcher拡張＋内部分岐」方式へ修正した（Next.js公式と同じnegative-lookahead方式）。
> Maintenance/Suspendedは全ページ対象、P1のredirectだけをパス判定で絞る。詳細は §5。
> ガード優先順位: **Maintenance > Suspended > P1 Auth**。
> 🔴 **2026-09-28 v1.3**: 登録状態判定を追加し **Maintenance > Suspended > 登録状態判定 > P1 / P2 / P3** とした（§4.4）。
> ⚠️ **未確定（実装時に確定）**: (1) 対象Next.jsバージョン（16なら`middleware.ts`はdeprecated、
> `proxy.ts`へ改称する）／(2) Maintenance/Suspendedのredirectとは別のAPI用契約
> （現在のskeletonは全リクエストを対象にするためAPIルートもHTTP redirectになる。
> 必要ならmatcherから`api`を除外し、APIは503/403 JSON等の別レスポンスにする）。

---

## 0. 目的

Next.js 実装前に「**未ログイン時の挙動**」を確定する。

- ページ単位の認証必須棚卸し
- 認証必須ページに未ログインで到達した場合のパターン分岐（P1 / P2 / P3）
- Login Required Modal の責務と文言
- `next` パラメータの安全性
- `middleware.ts` の matcher と skeleton

UI 挙動を 3 パターンに整理し、ページごとに使い分けるルールを固定する。

---

## 1. 認証必須ページ棚卸し

`docs/nextjs-routing-table-v1.md` §5〜§8 を実装観点で棚卸し。

| 区分 | URL pattern | 認証必須 | 挙動パターン |
|---|---|---|---|
| Garage（自分の） | `/garage/:path*` | ✅ | **P1**（redirect to `/login?next=`）|
| Settings | `/settings/:path*` | ✅ | **P1** |
| Notifications | `/notifications` | ✅ | **P2**（Login Required Modal）|
| Register | `/register/:path*` | ✅ | **P2** — Global Header の Create 導線や直接 URL から起動した場合 |
| Public 系の mutate 操作 | Follow / Like / Favorite / Pin / Comment | ❌ ページとしては公開 | **P3**（Public Mode Login Modal）|
| LOG Composer Modal 起動 | `/register/log` または Header CreateMenu | ✅ | P2 — Composer 起動が認証必須のため |

> **補足:** `(authed)` route group 配下は全て middleware で session を確認する。挙動パターンは「未ログインで route 到達 / 操作開始したときの UI」のこと。

---

## 2. 未ログイン時の 3 パターン

### 2.1 P1 — Redirect to `/login?next=`

**対象:** `/garage/:path*` / `/settings/:path*`

**挙動:**

1. middleware が session 不在を検知
2. 元 URL を URL-encode して `next` パラメータに付与
3. `/login?next=<encoded>` へ redirect
4. ログイン成功後、`next` を検証 → 安全なら遷移

**理由:**

- これらのページは「ログインしている人専用のアプリ領域」
- 未ログインユーザーがそのまま到達するシナリオが想定されない（外部リンク・ブックマーク・直 URL 等）
- ページ自体を見せずに即 Login に流すのが自然

---

### 2.2 P2 — Login Required Modal

**対象:** `/notifications` / Global Header / Create 導線から起動した `/register/:path*` 等

**挙動:**

1. 元ページ（Home / Browse / Search 等）を維持したまま Modal を開く
2. Modal の Primary CTA「ログイン」→ `/login?next=<元URL>`
3. Secondary CTA「新規登録」→ `/signup?next=<元URL>`
4. close（外側クリック / Escape / × ボタン）→ 元ページにそのまま留まる

**理由:**

- Global Header から Create / Notifications を押した瞬間に即座に `/login` に飛ばすと **「何が起きたか分からない」** 体験になる
- ユーザーが「いま自分は Home にいる」状態を維持しつつ、ログインの必要性を明示する
- close で戻れるため離脱率が下がる

---

### 2.3 P3 — Public Mode Login Modal

**対象:** Public Garage `/user/[username]` の Follow / Detail ページの Like / Favorite / Pin / Comment 等、**公開ページ上の mutate 操作**

**挙動:**

1. ページ自体は公開のため、未ログインでも閲覧 OK
2. ユーザーが mutate ボタン（Like / Follow 等）を押した瞬間に Login Modal を開く
3. CTA は P2 と同じ。close で元ページに留まる

**理由:**

- 公開ページの閲覧体験を壊さない
- mutate しようとした瞬間だけログインを要求する
- P2 との差異は「ページ全体が認証必須か / mutate 操作のみ認証必須か」

---

## 3. Login Required Modal

### 3.1 文言（P2 / P3 共通）

**#14裁定（2026-07-17 イタヤ承認）による現行定義。**

| 要素 | 文言 |
|---|---|
| Title | ログインが必要です（全context共通） |
| **Description** | **contextに応じて5グループに解決**（下記 §3.3） |
| Primary CTA | ログイン |
| Secondary CTA | 新規登録 |
| Close | 外側クリック / Escape / × ボタン |

### 3.2 挙動ルール

- Primary CTA → `/login?next=<encoded current URL>` へ遷移
- Secondary CTA → `/signup?next=<encoded current URL>` へ遷移
- close 時は元ページに留まる（リダイレクトしない）
- focus trap 必須（`docs/dialog-interaction-spec-v1.md` §3 準拠）
- Modal open 時は元ページの scroll lock

### 3.3 文脈別の文言（#14裁定・実装済み）

**context は8種を維持し、表示文言は5グループに解決する。**
呼び出し側を変えずに将来文言を細分化できる設計。

```ts
type LoginRequiredModalContext =
  | 'default' | 'register' | 'follow' | 'like'
  | 'favorite' | 'pin' | 'comment' | 'notifications';
```

| context | Description |
|---|---|
| `register` | ログインすると、RIG・パーツ・LOGを登録して、自分のガレージを管理できます。 |
| `like` / `favorite` / `pin` / `comment` | ログインすると、気になる投稿を保存したり、いいねやコメントを残したりできます。 |
| `follow` | ログインすると、気になるガレージをフォローして更新を追えます。 |
| `notifications` | ログインすると、いいね・コメント・フォローなどの通知を確認できます。 |
| `default`（省略時） | ログインすると、ガレージ管理やお気に入りなど、MyRIGの主な機能が使えます。 |

**実装**: `js/mobile-shell.js` の `LOGIN_CONTEXT_TEXT` に8キーで定義。
うち like / favorite / pin / comment が同一文言＝実質5グループ。
mobile-component-contract v0.5 §3.7 の表と一致。

機能説明型の旧文言（「フォローするにはログインしてください」等）は使わない。

---

## 4. `next` パラメータ安全性

### 4.1 ルール

| 検証項目 | 内容 |
|---|---|
| 同一オリジン | `URL(next, location.origin).origin === location.origin` であること |
| URL encode | 必ず `encodeURIComponent` 経由で付与 |
| 空 / 不正値 | `null` / `undefined` / `""` / 外部 URL / 不正 URL は `/` にフォールバック |
| open redirect 防止 | `//evil.com` / `https://evil.com` などのプロトコル付き / `//` 始まり は全て拒否 |
| relative path only | `/` で始まる相対 path のみ許可 |

### 4.2 検証関数（実装メモ）

```ts
// lib/auth/safeNext.ts
export function safeNext(rawNext: string | null | undefined): string {
  if (!rawNext) return '/';
  try {
    const decoded = decodeURIComponent(rawNext);
    if (!decoded.startsWith('/')) return '/';
    if (decoded.startsWith('//')) return '/';
    // ループ防止: /login や /signup を next にしない
    if (decoded.startsWith('/login') || decoded.startsWith('/signup')) return '/garage';
    return decoded;
  } catch {
    return '/';
  }
}
```

> Server / Client どちらでも使えるよう pure function とする。

### 4.3 認証成功後の遷移先

🔴 **2026-09-28 v1.3 改訂（イタヤ承認）**: 判定を「username 未設定か」から「**有効な `profiles` があるか**」へ変更。
schema v1.6 で `profiles.username` は NOT NULL のため、username の無い `profiles` 行は作れない。
**登録途中の間は `profiles` を作らず、Onboarding 完了（「MyRIGをはじめる」の成功）時に username 込みで作成する = 登録完了。**
認証（OAuth / メールの確認コード）が成功しただけでは MyRIG の登録完了としない。

- 認証成功・**有効な `profiles` あり**（登録完了）+ `next` あり → `safeNext(next)` へ遷移
- 認証成功・有効な `profiles` あり + `next` なし → `/garage` へ遷移
- 認証成功・**有効な `profiles` なし**（はじめての人・Onboarding を途中でやめた人 = 登録途中）→ `/onboarding`
  （`next` は保持し、Onboarding 完了後に遷移。Login / Signup のどちらから来ても同じ）
- ⛔「このアカウントはすでに登録されています」は出さない（認証アカウントがあれば同じアカウントにログインするだけ）

**有効な `profiles`** = `profiles` 行が存在し、論理削除されていない（`deleted_at IS NULL`）こと。`is_public` は判定に関係させない。
⚠️ **論理削除済みの `profiles` を持つ認証アカウント**（退会済み等）は「登録途中」と扱わない（Onboarding は `profiles` の新規作成になり、
同じ id の論理削除行と衝突するため）。扱い（退会・復帰の仕様）は PENDING。それまでは Onboarding へ送らない。

**実装時の判定順（この順を崩さない）**: ① 論理削除済みの `profiles` 行あり → **`/resume`「MyRIG を再開しますか？」**（🔴 v1.4 2026-09-29: 設定・通知 D8 / schema v1.6-r10 Domain 11。猶予中（30 日以内）= 再開できる・30 日を過ぎた（消去の開始待ちを含む）/ 確定処理が始まった = 再開できないと伝える。はじめの設定へ送らない・自動で戻さない・出口は 再開 / ログアウトだけ・猶予中は再開の処理以外を書き込めない）／ ② 有効な `profiles` あり → 登録完了 ／ ③ `profiles` 行なし → 登録途中

### 4.4 認証の 3 状態と転送（2026-09-28 v1.3 新設・イタヤ承認）

**ガード優先順位: Maintenance > Suspended > 登録状態判定 > P1 / P2 / P3。**
Suspended のユーザーを「有効な profile が無いから Onboarding へ」と誤って流さないため、登録状態判定は Suspended の後に置く。

🔴 **v1.5（2026-09-29・情報・法務・サポート D8）**:
- **Suspended の redirect 除外** = `/account-suspended` ＋ `/contact` `/help` `/legal/*`。アカウント制限画面の CTA「お問い合わせ」が `/contact` へ着ける（v1.4 までは自分自身へ戻るループだった）。`/contact` からの送信は `support_inquiries(kind='account')` だけをサーバー側で許す（UI は `?kind=account&from=suspended` で種別を固定）
- **退会猶予中（判定順 ①）は例外を作らない**。猶予中は再開の処理以外を書き込めない・出口は 再開 / ログアウトだけ（設定・通知 D8・CLOSE 済み）。`/resume` に「お問い合わせは一度ログアウトしてから」の 1 行を置き、公開の `/contact` へ匿名で送る
- 停止 > 再開は**この順で成立する**（Suspended を 登録状態判定 ① の前に判定する）。停止中アカウントの 30 日確定処理を止めるかは PENDING（/admin レーン）
- 情報・法務・サポートの面（`/about /help /contact /report /news /legal/* /support-us`）は P3（公開）。`/report` の面内ダイアログはログイン必須（P2 相当・Login Required Modal）

| 状態 | 定義 | `/login`・`/signup` | `/onboarding` | 自分のものを扱う画面・操作（`/garage`・`/settings`・Register・いいね・フォロー等） | 公開ページ |
|---|---|---|---|---|---|
| 未ログイン | session なし | 表示 | `/login?next=/onboarding` | 従来どおり P1 / P2 / P3 | 閲覧可 |
| 認証済み・登録途中 | session あり・有効な `profiles` なし | → `/onboarding`（`next` を保持） | 表示 | → `/onboarding?next=`（P2 / P3 の操作も実行しない） | 閲覧可 |
| 登録完了 | session あり・有効な `profiles` あり | → 安全な `next`、無ければ `/garage` | → `/garage`（安全な `next` があればそこ） | 通過 | 閲覧可 |

- `next` の扱いはすべて §4（L1）の safeNext を通す。Onboarding 自身を `next` の着地先にしない
- 未確定（PENDING 継続）: 同じメールアドレスの identity の統合（Supabase の linking）/ 途中で残った登録途中アカウントの長期の扱い / 本番の確認コードの条件（失効・試行回数・再送・送信上限）

---

## 5. middleware.ts skeleton

実装着手時の参考骨格。Supabase session check は本実装で具体化する。

✅ **2026-08-22 イタヤ裁定・解消済み（GPT監査A・revision020→021監査で追加是正）**: matcherを静的アセット等を
除く全パスへ拡張し、Maintenance/Suspendedは全ページで実行、P1のredirectだけをパス判定（正規表現配列）で絞る
方式に変更した（Next.js公式と同じnegative-lookahead方式）。
全体ガードとP1ガードを別ファイルに分離する案は、Next.js/Vercelのmiddleware/proxyが1ファイル1matcherである
制約と相性が悪いため不採用。

**ガード優先順位: Maintenance > Suspended > 登録状態判定（§4.4・v1.3）> P1 Auth**（Maintenance ON時はSuspendedユーザーも
`/maintenance`へ誘導される。session取得もスキップされる。障害・保守モードとして意図した挙動）。

```ts
// Next.js 15なら middleware.ts / Next.js 16以降なら proxy.ts へ改称する
// （Next.js 16では middleware.ts はdeprecated、proxy.ts / export function proxy()）
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

// ⚠️ NEXT_PUBLIC_ プレフィックスは避ける（ビルド時にJSへインライン化され、
// デプロイ後のenv切替では反映されないため保守モードの運用スイッチとして不適切）。
// server-only変数名を使う。切替方法（env再デプロイ / Feature Flag / DB検査）は実装時に確定。
const MAINTENANCE_MODE = process.env.MAINTENANCE_MODE === 'on';

// ✅ 2026-08-22 GPT総合監査で追加: locale正規化
// page-role-matrix #24裁定「MVP時点から日英2言語・/en/* プレフィックス方式」により、
// /en/garage のようなパスが来る。locale接頭辞を外してから判定しないとP1認証を素通りする。
const LOCALES = ['en'] as const; // ja はプレフィックス無し（デフォルト）

function stripLocale(pathname: string): { locale: string | null; path: string } {
  for (const l of LOCALES) {
    if (pathname === `/${l}` || pathname.startsWith(`/${l}/`)) {
      return { locale: l, path: pathname.slice(l.length + 1) || '/' };
    }
  }
  return { locale: null, path: pathname };
}

// locale付きのredirect先を作る（/en/garage で弾かれたら /en/login へ戻す）
function withLocale(locale: string | null, path: string): string {
  return locale ? `/${locale}${path}` : path;
}

// P1: middlewareで直接 /login へ redirectする保護ルート（locale除去後のパスで判定）
const P1_PROTECTED_PATTERNS = [
  /^\/garage(\/|$)/,
  /^\/settings(\/|$)/,
];

function isP1Protected(path: string): boolean {
  return P1_PROTECTED_PATTERNS.some((re) => re.test(path));
}

// ✅ 2026-08-22 GPT総合監査で追加: 管理者ルート
// page-role-matrix「Admin プレフィックス /admin/*。認証 middleware で保護」および
// implementation_checklist（L1）「/admin/* → is_admin チェック → 非管理者は403」に対応。
function isAdminRoute(path: string): boolean {
  return /^\/admin(\/|$)/.test(path);
}

export async function middleware(req: NextRequest) {
  const { pathname, search } = req.nextUrl;
  // locale接頭辞を外して判定用パスを作る（/en/garage → /garage）
  const { locale, path } = stripLocale(pathname);

  // 1) Maintenance — 全ページ対象（最優先）
  if (MAINTENANCE_MODE) {
    if (path !== '/maintenance') {
      const url = req.nextUrl.clone();
      url.pathname = withLocale(locale, '/maintenance');
      return NextResponse.redirect(url);
    }
    return NextResponse.next();
  }

  // 2) Session 取得（実装時に具体化）
  const session = await getSession(req); // Supabase session lookup

  // 3) Suspended — 全ページ対象。🔴 v1.5: 除外は /account-suspended ＋ 情報・法務・サポートの 3 種だけ
  //    （/contact = 救済の申し立て・/help・/legal/*。/about /news /report /support-us は除外しない。
  //     /contact の書き込みは kind='account' だけをサーバー側で許す = schema v1.6-r11 support_inquiries）
  const SUSPENDED_ALLOW = ['/account-suspended', '/contact', '/help'];
  if (session?.user?.accountStatus === 'suspended') {
    if (!SUSPENDED_ALLOW.includes(path) && !path.startsWith('/legal/')) {
      const url = req.nextUrl.clone();
      url.pathname = withLocale(locale, '/account-suspended');
      return NextResponse.redirect(url);
    }
    return NextResponse.next();
  }

  // 4) Auth Guard P1（未ログインで protected route）— P1ルートのみ判定
  if (isP1Protected(path) && !session?.user) {
    const url = req.nextUrl.clone();
    url.pathname = withLocale(locale, '/login');
    url.searchParams.set('next', pathname + search); // nextは元のlocale付きパスを保持
    return NextResponse.redirect(url);
  }

  // 5) Admin Guard（/admin/* は認証＋is_adminが必須）
  //    未ログイン → /login へ（P1と同じ扱い）／ログイン済みだが非管理者 → 403
  if (isAdminRoute(path)) {
    if (!session?.user) {
      const url = req.nextUrl.clone();
      url.pathname = withLocale(locale, '/login');
      url.searchParams.set('next', pathname + search);
      return NextResponse.redirect(url);
    }
    if (!session.user.isAdmin) {
      // 管理画面の存在を伏せたい場合は404を返す方針もある。実装時に確定（下記§5.2）
      return new NextResponse(null, { status: 403 });
    }
  }

  return NextResponse.next();
}

export const config = {
  // Next.js公式と同じnegative-lookahead方式。静的アセット・画像最適化・favicon・sitemap・robotsを除外。
  // ⚠️ 現状は `api` を含む（HTTP redirectがAPIルートにも掛かる）。
  //    APIをJSON契約で応答させたい場合は、matcherに `api` を追加除外し、
  //    Maintenance→503 JSON / Suspended→403 JSON をAPI側で別実装する（実装時に確定）。
  matcher: [
    '/((?!_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt|.*\\.(?:svg|png|jpg|jpeg|gif|webp)$).*)',
  ],
};
```

> **⚠️ matcherを広げても、P2のページ（`/notifications` `/register/:path*`）にP1のredirectはかからない。**
> `isP1Protected()` が garage/settings のみを対象にしているため、matcherに含まれていても
> 「4) Auth Guard P1」ブロックはスキップされる。**P2は引き続きpage.tsx側で session確認 →
> `<LoginRequiredModal />` を表示する方式のまま。**

### 5.1 matcher拡張後の挙動パターン対応

| URL pattern | matcherに含まれる | Maintenance/Suspended | P1 redirect判定 | UI |
|---|---|---|---|---|
| `/garage/:path*` | ✅（広いmatcherに包含） | ✅ 効く | ✅ `isP1Protected()`が対象 | middlewareで直接redirect |
| `/settings/:path*` | ✅ | ✅ 効く | ✅ | 同上 |
| **`/en/garage/:path*` `/en/settings/:path*`** | ✅ | ✅ 効く | ✅ **`stripLocale()`後に判定** | 同上（redirect先は`/en/login`） |
| **`/admin/:path*`（`/en/admin/*`含む）** | ✅ | ✅ 効く | — | **未ログイン→`/login` ／ 非管理者→403**（§5.2） |
| `/notifications/:path*` | ✅ | ✅ 効く | ❌ 対象外 | page.tsxでLogin Required Modal（**P2は変更なし**） |
| `/register/:path*` | ✅ | ✅ 効く | ❌ 対象外 | 同上（**P2は変更なし**） |
| 公開ページ全般（トップ・検索・Public Garage等） | ✅ | ✅ 効く | ❌ 対象外 | 通常表示 |

> **方針:**
> - Maintenance/Suspendedはmatcherでほぼ全パスを対象にし、公開ページでも確実に効かせる
> - P1のredirectは`isP1Protected()`のパス判定のみで絞る。matcherの範囲とは独立させる
> - **判定は必ず`stripLocale()`後のパスで行う。** `/en/garage`が素通りするのを防ぐ（GPT総合監査で検出）
> - P2のページはP1判定の対象外のまま。挙動は無変更

### 5.2 Admin Guard（`/admin/*`）

✅ **2026-08-22 GPT総合監査で追加**（page-role-matrix「Admin プレフィックス `/admin/*`。認証 middleware で
保護」／implementation_checklist L1「`/admin/*` → `is_admin` チェック → 非管理者は403」に対応。
従来auth-guard側に`/admin`の記述が一切なく、設計漏れになっていた）

| 状態 | 挙動 |
|---|---|
| 未ログイン | `/login?next=...` へredirect（P1と同じ） |
| ログイン済み・非管理者 | **403** を返す |
| ログイン済み・管理者 | 通過 |

- 管理者判定のソースは `session.user.isAdmin`（実際のカラム/クレーム名は実装時に確定。
  `profiles.is_admin` 相当を想定）
- ⚠️ **実装時に確定**: 非管理者に403を返すか、管理画面の存在自体を伏せて404を返すか
  （セキュリティ上は404が有利、運用上は403が分かりやすい）

---

## 6. P3 — Public Mode Login Modal の実装方針

✅ **2026-08-22 GPT監査(revision021)で是正**: §5のmatcher拡張により公開ページも
middlewareを通るため、旧「ページ自体はmiddlewareを通さない（matcherに含めない）」の記述を撤回する。

- ページ自体は §5 の広いmatcherに含まれ、middlewareを通る。
  ただしP3ページに対しては Maintenance/Suspended のみ適用され、**P1認証redirectは掛からない**
  （`isP1Protected()` の対象外のため）
- mutate ボタン Component が Client Component として `useSession()` でログイン状態を確認
- 未ログインなら `<LoginRequiredModal context="follow|like|..." />` を open
- ログイン後の楽観的 mutate 実行は対象外（本 Spec はあくまで「未ログイン挙動」の確定）

### 6.1 `/account-suspended` 直接アクセスの扱い（未決定）

Suspendedユーザーが `/account-suspended` を開いた場合の無限redirect回避は§5で対応済み。
一方、**Suspendedではないユーザーが `/account-suspended` を直接開いた場合**の扱いは未仕様。
- 「本人専用エラー画面」にしたい → 逆向きguard（sessionが正常なら他ページへredirect）が必要
- 単なる説明ページとしてよい → 現状通り誰でも閲覧可

**実装時に確定する**。現時点でモックアップの動作を妨げるものではない。

| 参照先 | 関係 |
|---|---|
| `docs/auth-onboarding-minimum-spec-v1.md` | OAuth only / Login / Signup / Onboarding 全般 |
| `docs/nextjs-routing-table-v1.md` §5〜§8 | 認証必須ページの URL 一覧（本 Spec の §1 の元データ）|
| `docs/dialog-interaction-spec-v1.md` §3 | Modal / focus trap / scroll lock の共通契約 |
| `docs/appheader-interaction-spec-v1.md` | Create 導線・UserMenu の未ログイン挙動（本 Spec の P2 と整合）|
| `docs/error-states-decomposition-MR-AUDIT-002.md` | middleware の Suspended / Maintenance との統合 |

---

## 8. 改訂履歴

| バージョン | 日付 | 内容 |
|---|---|---|
| v1 | 2026-05-22 | 初版（MR-AUDIT-002 / A7）。P1 / P2 / P3 パターン / Login Required Modal 文言 / `next` 安全性 / `middleware.ts` skeleton + matcher を確定 |
| v1.1 | 2026-08-21 | #14裁定（context 8種・文言5グループ）を §3.1 / §3.3 本文へ反映。matcher から `/notifications/:path*` `/register/:path*` を除外し **P2＝matcher対象外**で確定 |
| v1.2 | 2026-08-22 | GPT監査A解消。Maintenance/Suspendedが公開ページで無効だった問題を、matcher拡張＋`isP1Protected()`によるパス内分岐へ変更して解消。P2の挙動（matcher非依存の判定）は無変更 |
| v1.5 | 2026-09-29 | 情報・法務・サポート D8（裁定原本 `_decisions/2026-09-29_info-legal-support-v1.md`）: Suspended の redirect 除外に `/contact` `/help` `/legal/*` を追加（`SUSPENDED_ALLOW`）。退会猶予中は例外化せず `/resume` に案内文。情報・法務・サポートの面は P3 と明記 |
| v1.4 | 2026-09-29 | §4.3 判定順 ① の PENDING を解消: 論理削除済み profile = `/resume`（設定・通知 D8・mock `pc/myrig-auth-resume-v2.html`・auth_check A33）。再開後は元のページか /garage へ・「MyRIG を再開しました」を 1 回だけ |
| v1.3 | 2026-09-28 | 認証・オンボーディング作り直し（イタヤ承認・GPT 同見解）。§4.3 の判定を「username 未設定」→「有効な `profiles`（存在・未論理削除）」へ。登録途中は `profiles` を作らず Onboarding 完了時に作成（`username NOT NULL` と整合）。§4.4 新設: 未ログイン / 認証済み・登録途中 / 登録完了 の 3 状態と `/login`・`/signup`・`/onboarding`・保護操作の転送。ガード優先順位を Maintenance > Suspended > 登録状態判定 > P1/P2/P3 へ。論理削除済み profile は登録途中と扱わない（PENDING） |
| v1.2-r3 | 2026-08-22 | GPT総合監査(revision023)のHIGH2件: (1) locale正規化を追加。`/en/garage`等がP1認証を素通りしていた（page-role-matrix #24裁定の`/en/*`プレフィックス方式と未接続だった）。`stripLocale()`/`withLocale()`を導入し全判定をlocale除去後のパスで行う (2) §5.2 Admin Guard新設。page-role-matrix「`/admin/*`は認証middlewareで保護」およびimplementation_checklist L1「is_adminチェック→非管理者403」が、auth-guard側に一切記述されていなかった設計漏れを解消 |
| v1.2-r2 | 2026-08-22 | GPT監査(revision020→021)の追加是正6件: 冒頭ヘッダーをv1.2/2026-08-22へ更新／`NEXT_PUBLIC_MAINTENANCE`→server-only `MAINTENANCE_MODE`（envインライン化問題）／matcherに`sitemap.xml``robots.txt`除外を追加してNext.js公式例に合わせる／§6 P3の「matcherに含めない」旧記述を撤回／ガード優先順位（Maintenance>Suspended>P1 Auth）を明記／Next.js 16なら`middleware.ts`→`proxy.ts`改称の注記／APIルート用に別契約(503/403 JSON)の実装時確定を注記／§6.1に`/account-suspended`直接アクセスの未決定項目を明示 |
