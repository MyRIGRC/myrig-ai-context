# Auth Guard / Login Required UI Spec v1

**作成日:** 2026-05-22 (MR-AUDIT-002 / A7)
**最終更新:** 2026-08-21（#14裁定を本文統合・middleware matcher を是正）
**ステータス:** 確定 v1.1
**前提:** `auth-onboarding-minimum-spec-v1` / `nextjs-routing-table-v1` / `appheader-interaction-spec-v1` / `dialog-interaction-spec-v1`
⚠️ **上記4本＋`error-states-decomposition-MR-AUDIT-002` は本repo内に未収録**（2026-08-21確認）。
参照が必要になった時点で所在を確認すること。

> **✅ 改訂反映済み（2026-08-21 — 本文統合完了）**
>
> 2026-07-23 の改訂注記が指していた項目は、**すべて本文へ統合済み**。
> 注記と本文が二重状態だった問題は解消した。
>
> 1. §3.1 / §3.3 → **#14裁定（context 8種・文言5グループ）を本文へ反映。**
>    実装 `js/mobile-shell.js:498-507` と一致を確認済み
> 2. §4.2 `safeNext` の二段フォールバック（不正値→`/` / login・signupループ→`/garage`）は**現行どおり有効**。
>    実装 `js/mobile-shell.js:488-495` が完全準拠
> 3. §5 / §5.1 → **middleware matcher の自己矛盾を是正**（P2＝matcher対象外で固定）
>
> **既知の未解消（GPT外部監査 2026-08-21）**: §5 skeleton は Maintenance / Suspended の全体ガードを
> middleware 内に置いているが、matcher が `/garage` `/settings` のみのため**公開ページで実行されない**。
> matcher 拡張＋内部分岐か、全体ガードとP1ガードの分離かは**要裁定**（`_audit/gpt-review-20260821.md` A）。

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

**#14裁定（2026-07-17 イタヤ承認・P11実装済み）を反映した現行定義**
（2026-08-21 本文統合。実装 `js/mobile-shell.js:498-507` と一致を確認済み）。

| 要素 | 文言 |
|---|---|
| Title | ログインが必要です（全context共通） |
| **Description** | **contextに応じて5グループに解決**（下記 §3.3） |
| Primary CTA | ログイン |
| Secondary CTA | 新規登録 |
| Close | 外側クリック / Escape / × ボタン |

> 旧定義は Description を「この機能を使うにはログインしてください」の**共通1文**としていたが、
> #14裁定で**ベネフィット説明の5文言グループ**へ置換された。

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

**実装**: `js/mobile-shell.js:498-507` `LOGIN_CONTEXT_TEXT` に8キーで定義済み。
うち like / favorite / pin / comment が同一文言＝実質5グループ。
mobile-component-contract v0.5 §3.7 の表と一致。

> 旧定義は「Reserved（将来差し替える余地を残す）」「MVPは共通文言で開始」としていたが、
> **#14裁定で実装済みとなった。**（2026-08-21 本文統合）
> 旧文言案（「フォローするにはログインしてください」等の機能説明型）は、
> ベネフィット説明型へ置換されたため使わない。

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

- 既存ユーザー（OAuth 成功）+ `next` あり → `safeNext(next)` へ遷移
- 既存ユーザー + `next` なし → `/garage` へ遷移
- 新規ユーザー（username 未設定）→ `/onboarding`（`next` は session に退避 / onboarding 完了後に遷移）

---

## 5. middleware.ts skeleton

実装着手時の参考骨格。Supabase session check は本実装で具体化する。

```ts
// middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

const MAINTENANCE_MODE = process.env.NEXT_PUBLIC_MAINTENANCE === 'on';

export async function middleware(req: NextRequest) {
  const { pathname, search } = req.nextUrl;

  // 1) Maintenance（MR-AUDIT-002 / A3 と統合）
  if (MAINTENANCE_MODE) {
    const isAsset = pathname.startsWith('/_next') || pathname.startsWith('/static');
    if (pathname !== '/maintenance' && !isAsset) {
      const url = req.nextUrl.clone();
      url.pathname = '/maintenance';
      return NextResponse.redirect(url);
    }
  }

  // 2) Session 取得（実装時に具体化）
  const session = await getSession(req); // Supabase session lookup

  // 3) Suspended（MR-AUDIT-002 / A3 と統合）
  if (session?.user?.accountStatus === 'suspended') {
    if (pathname !== '/account-suspended') {
      const url = req.nextUrl.clone();
      url.pathname = '/account-suspended';
      return NextResponse.redirect(url);
    }
  }

  // 4) Auth Guard P1（未ログインで protected route）
  if (!session?.user) {
    const url = req.nextUrl.clone();
    url.pathname = '/login';
    url.searchParams.set('next', pathname + search);
    return NextResponse.redirect(url);
  }

  return NextResponse.next();
}

export const config = {
  matcher: [
    '/garage/:path*',
    '/settings/:path*',
  ],
};
```

> **⚠️ 2026-08-21 監査で訂正（3AIクロスチェック一致）**
> 旧 skeleton は matcher に `/notifications/:path*` と `/register/:path*` を含めていた。
> しかし skeleton 内の `// 4) Auth Guard P1` ブロックは matcher 内の全 route を**無条件で `/login` へ redirect** するため、
> P2（元ページを維持したまま Modal を開く）と**正面から矛盾**していた。
> §5.1 の但し書き自身も「本実装着手時に matcher から外す可能性あり」と揺れていた。
> mobile-component-contract v0.5 §3.8 は「`/notifications` `/register/:path*` は **P1 matcher から除外**」を
> 既に確定扱いにしているため、契約側に合わせて matcher から除外した。
> **P2 のページは matcher に入れず、page.tsx 側で session 確認 → `<LoginRequiredModal />` を表示する。**

### 5.1 matcher と挙動パターンの対応

| URL pattern | matcher に含める | パターン | UI |
|---|---|---|---|
| `/garage/:path*` | ✅ 含める | **P1** | middleware で直接 redirect |
| `/settings/:path*` | ✅ 含める | **P1** | 同上 |
| `/notifications/:path*` | ❌ 含めない | **P2（確定）** | page.tsx で session 取得 → 未ログインなら Login Required Modal を表示 |
| `/register/:path*` | ❌ 含めない | **P2（確定）** | 同上。Create 導線からの起動は元ページを維持したいため P2 |

> **方針:**
> - P1 は middleware で確実に redirect（`/garage/:path*` / `/settings/:path*`）
> - P2 は middleware では redirect せず、ページ Server Component / Client Component 側で `<LoginRequiredModal />` を表示する
> - **P2 のページは matcher に含めない。** matcher に入れると skeleton の `// 4) Auth Guard P1` ブロックが無条件 redirect するため P2 が成立しない
> - （2026-08-21 監査: 旧記載は「matcher には含める」「外す可能性あり」と揺れており、上の skeleton と矛盾していた。
>   mobile-component-contract v0.5 §3.8 の確定扱いに合わせて P2＝matcher対象外で固定した）

---

## 6. P3 — Public Mode Login Modal の実装方針

- ページ自体は middleware を通さない（matcher に含めない）
- mutate ボタン Component が Client Component として `useSession()` でログイン状態を確認
- 未ログインなら `<LoginRequiredModal context="follow|like|..." />` を open
- ログイン後の楽観的 mutate 実行は対象外（本 Spec はあくまで「未ログイン挙動」の確定）

---

## 7. 関連 docs

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
| v1（注記） | 2026-07-23 | Coworkナレッジ監査で冒頭に改訂注記を追加（#14文言置換・§3.1/§3.3旧定義マーク）。本文・版数は不変。本文反映は#19キュー⑤ |
| v1（改訂） | **2026-08-21** | **docs精査（3AIクロスチェック）で §5 middleware skeleton と §5.1 を本文改訂。** matcher から `/notifications/:path*` `/register/:path*` を除外（skeleton の `// 4) Auth Guard P1` ブロックが matcher 内を無条件 redirect するため、P2「元ページ維持のまま Modal」と矛盾していた）。§5.1 の揺れを解消し **P2＝matcher対象外**で確定。**※§3.1/§3.3 の冒頭注記の本文統合は未実施。ステータス「確定 v1」は据え置き** |
