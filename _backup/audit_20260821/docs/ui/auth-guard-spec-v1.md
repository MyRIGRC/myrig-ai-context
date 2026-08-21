# Auth Guard / Login Required UI Spec v1

**作成日:** 2026-05-22 (MR-AUDIT-002 / A7)
**ステータス:** 確定 v1
**前提:** `docs/auth-onboarding-minimum-spec-v1.md` / `docs/nextjs-routing-table-v1.md` / `docs/appheader-interaction-spec-v1.md` / `docs/dialog-interaction-spec-v1.md`

> **⚠️ 改訂注記（2026-07-23 Coworkナレッジ監査 — 本文は未改訂・本注記が優先）**
>
> 1. **§3.1の共通文言・§3.3の「Reserved」扱いは旧定義。** #14裁定（2026-07-17 イタヤ承認・P11実装済み・契約v0.4条文化）で置換: context は既存**8種を維持**しつつ、Description は**ベネフィット説明の5文言グループ**に解決する（例: `register`=「ログインすると、RIG・パーツ・LOGを登録して、自分のガレージを管理できます。」）。現行の正は **mobile-component-contract v0.5 §3.7の文言表**。
> 2. §4.2 `safeNext` の二段フォールバック（不正値→`/` / login・signupループ→`/garage`）は現行どおり有効（契約v0.5もこの正典に準拠 — P05 erratum #1で契約側を本書に合わせて訂正済み）。
> 3. 本文への反映は#19キュー⑤（Next.js実装前・PC正本改訂と同時）で実施する。

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

> **（旧定義 — #14裁定で置換済み。冒頭の改訂注記参照。現行の正は契約v0.5 §3.7の5文言グループ表）**

| 要素 | 文言 |
|---|---|
| Title | ログインが必要です |
| Description | この機能を使うにはログインしてください |
| Primary CTA | ログイン |
| Secondary CTA | 新規登録 |
| Close | 外側クリック / Escape / × ボタン |

### 3.2 挙動ルール

- Primary CTA → `/login?next=<encoded current URL>` へ遷移
- Secondary CTA → `/signup?next=<encoded current URL>` へ遷移
- close 時は元ページに留まる（リダイレクトしない）
- focus trap 必須（`docs/dialog-interaction-spec-v1.md` §3 準拠）
- Modal open 時は元ページの scroll lock

### 3.3 文脈別の文言バリエーション（Reserved）

> **（旧定義 — #14裁定により「Reserved」ではなく実装済み。context 8種は維持・表示文言は5グループに解決。冒頭の改訂注記参照）**

将来「フォロー」「いいね」など mutate 文脈で Description を差し替える余地を残す。MVP は共通文言で開始する。

```ts
type LoginRequiredModalContext =
  | 'default'      // 'この機能を使うにはログインしてください'
  | 'follow'       // 'フォローするにはログインしてください'
  | 'like'         // 'いいねするにはログインしてください'
  | 'favorite'     // 'お気に入りに追加するにはログインしてください'
  | 'pin'          // 'ピン留めするにはログインしてください'
  | 'comment'      // 'コメントするにはログインしてください'
  | 'register'     // '登録するにはログインしてください'
  | 'notifications';  // '通知を見るにはログインしてください'
```

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
    '/notifications/:path*',
    '/register/:path*',
  ],
};
```

### 5.1 matcher と挙動パターンの対応

| matcher | パターン | UI |
|---|---|---|
| `/garage/:path*` | **P1** | middleware で直接 redirect |
| `/settings/:path*` | **P1** | 同上 |
| `/notifications/:path*` | **P2 推奨** | matcher には含めるが、middleware は session check のみ。ページ側で「未ログイン時は Login Required Modal を表示」する実装も可。P1/P2 のどちらにするかは本実装着手時に最終決定（現状は P2 を第一候補）|
| `/register/:path*` | **P2 推奨** | 同上。Create 導線からの起動は元ページを維持したいケースが多いため P2 |

> **方針:**
> - P1 は middleware で確実に redirect
> - P2 は middleware では redirect せず、ページ Server Component / Client Component 側で `<LoginRequiredModal />` を表示する
> - matcher に含めるかどうかは「未ログインで route 到達した場合の挙動」で決まる。P2 のページは matcher から外し、page.tsx で session を取得 → 未ログインなら Modal を出す
> - 上記 matcher 例は **P1 想定**。本実装着手時に P2 候補（`/notifications` / `/register/:path*`）を matcher から外す可能性あり

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
