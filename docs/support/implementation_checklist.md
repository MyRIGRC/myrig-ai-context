# MyRIG RC — 実装初期設定チェックリスト

**最終更新:** 2026-08-21（docs精査: 認証方式をHOLD化 / signup後の遷移をonboarding経由に訂正 /
sitemap の旧URL `/subcategory/[slug]` を是正 / 物理DELETE禁止との関係を注記）

Next.js + Supabase + Vercel + Cloudflare Images の実装開始時に必ず対応する項目。
Claude Codeに渡す前にこのリストを確認し、順番に実装する。

---

## Phase 0: プロジェクト初期化（最優先）

### 0-1. .gitignore
```
.env
.env.*
.env.local
.env.production
node_modules/
.next/
out/
supabase/.temp/
.vercel/
*.log
```
**Claude Codeへの指示時ルール:** `.env`ファイルの中身をチャットに貼らない。環境変数名だけ伝えて値は自分で設定する。

### 0-2. 環境変数テンプレート
`.env.example` を作成し、Gitにコミット：
```
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Cloudflare Images
CLOUDFLARE_ACCOUNT_ID=
CLOUDFLARE_IMAGES_API_TOKEN=

# Google AdSense（将来）
NEXT_PUBLIC_ADSENSE_CLIENT_ID=

# アフィリエイト
AMAZON_JP_ASSOCIATE_TAG=
AMAZON_US_ASSOCIATE_TAG=
RAKUTEN_AFFILIATE_ID=
```

**絶対厳守:** `SUPABASE_SERVICE_ROLE_KEY` と `CLOUDFLARE_IMAGES_API_TOKEN` は `NEXT_PUBLIC_` を付けない。サーバーサイドのみで使用。

### 0-3. Supabaseプロジェクト構成
- **本番用:** `myrig-production`
- **開発用:** `myrig-development`
- 開発中はdevelopmentに接続。本番デプロイ時のみproductionに切り替え。
- Claude Codeには常にdevelopment用の接続情報のみ渡す。

---

## Phase 1: 認証・セキュリティ基盤

### 1-1. Supabase Auth設定

> ⚠️ **HOLD: 認証方式は未裁定**（2026-08-21 監査・3AIクロスチェックで検出）
> プロバイダとメール認証の有無が**5系統に割れている**（App_Ready_Design_Rules.md Rule 6 の HOLD 表を参照）。
> **実装フェーズで裁定する。**それまで下記の具体設定を確定事項として扱わない。

- OAuth プロバイダ有効化（**どのプロバイダかは裁定待ち**）
- メール/パスワード認証を採用するか（**裁定待ち**）
- リダイレクトURL設定（本番 `https://myrigrc.com/auth/callback` + localhost）

### 1-2. signup後の遷移フロー

**新規ユーザーは `/onboarding` を経由する**（auth-guard-spec-v1 §4.3 が正）:

```
signup（OAuth成功）
→ username 未設定なら /onboarding（next は session に退避）
→ onboarding 完了後 → safeNext(next)（next 無しなら /garage）
```

既存ユーザーは `/auth/callback` → `safeNext(next)`（next 無しなら `/garage`）。

必要なページ:
- `/auth/callback` — Supabase Auth callbackハンドラー
- `/onboarding` — 初期設定ウィザード（ユーザー名・表示名・国/地域・興味カテゴリ）

（2026-08-21 監査で訂正: 旧記載は `signup → /auth/verify-email → /auth/callback → /garage` で
**onboarding を素通りしていた**ほか、未裁定のメール確認フローを前提にしていた。
`/auth/verify-email` の要否はメール認証採用の裁定後に決める）

### 1-3. 管理者アクセス制御
Supabase Auth のカスタムクレーム:
```sql
-- profiles テーブルに role カラム追加（or app_metadata で管理）
-- 方式: auth.users.raw_app_meta_data に {"is_admin": true} をセット
```
Next.js ミドルウェアで `/admin/*` パスを保護:
```
middleware.ts → is_admin チェック → 非管理者は403
```

### 1-4. Rate Limiting

> ⚠️ **以下の数値は暫定の初期値**（2026-08-21 監査）。裁定根拠が正典内に無いため、
> 確定値としては扱わない。運用開始後に実測で調整すること。

Vercel Edge Middlewareで実装:
```
POST /api/rigs       → 10件/分
POST /api/parts      → 10件/分
POST /api/logs       → 10件/分
POST /api/comments   → 5分5件（DB設計書と同じ）
POST /api/images     → 20件/分
POST /api/reports    → 3件/時
GET  /api/search     → 30件/分
```
`@upstash/ratelimit` + Vercel KV が最も軽量。

---

## Phase 2: SEO・メタ情報

### 2-1. OGPメタタグ（Next.js Metadata API）
全ページに`generateMetadata()`を実装:
```typescript
// 公開詳細ページ（rig-detail等）
export async function generateMetadata({ params }) {
  const rig = await getRig(params.id);
  return {
    title: `${rig.model_name} — MyRIG RC`,
    description: rig.description?.slice(0, 160),
    openGraph: {
      title: `${rig.model_name} — MyRIG RC`,
      description: rig.description?.slice(0, 160),
      images: [rig.primaryImage?.url],
      type: 'article',
    },
    twitter: { card: 'summary_large_image' },
  };
}
```

### 2-2. robots.txt

> **モック配信環境は意図的に全面 noindex**（2026-08-21 確認）。
> `robots.txt` は `User-agent: * / Disallow: /`、`vercel.json` も全パスに `X-Robots-Tag: noindex, nofollow`。
> 下記は**本番実装時の仕様**であり、モック側の設定と食い違っていて正しい。
```typescript
// app/robots.ts
export default function robots() {
  return {
    rules: [
      { userAgent: '*', allow: '/', disallow: ['/garage/', '/admin/', '/auth/', '/api/'] },
    ],
    sitemap: 'https://myrigrc.com/sitemap.xml',
  };
}
```

### 2-3. sitemap.xml
```typescript
// app/sitemap.ts — 公開ページのみ
// /rig/[id], /parts/[id], /log/[id], /user/[username]
// /category/[rigType], /category/[rigType]/[categorySlug]
// /parts, /parts/category/[partCategorySlug]
// /library/rigs/[masterSlug], /library/parts/[masterSlug], /library/makers/[makerSlug]
// ※2026-08-21 監査で訂正: 旧記載の /subcategory/[slug] は page-role-matrix に存在しないURL
```

### 2-4. canonical URL
rig-detailとgarage-rig-detailが同一RIGを参照する。
公開URL（`/rig/[id]`）をcanonicalに設定。ガレージURL（`/garage/rigs/[id]`）にはnoindexを付ける。

---

## Phase 3: 画像アップロード

### 3-1. Cloudflare Images Direct Upload
フロー:
```
1. クライアント → Next.js API Route（/api/images/upload-url）
2. API Route → Cloudflare Images API（Direct Upload URL取得）
3. クライアント → Cloudflare Images（画像を直接アップロード）
4. Cloudflare → webhook or クライアントが完了通知 → imagesテーブルにレコード作成
```
**メリット:** 画像データがサーバーを経由しない。Vercelの帯域制限に引っかからない。

### 3-2. 画像バリデーション（アプリ層）

> ⚠️ **以下の数値も暫定の初期値**（2026-08-21 監査）。裁定根拠が正典内に無い。

```
- 最大ファイルサイズ: 10MB
- 許可形式: JPEG, PNG, WebP, HEIC
- 最大解像度: 8000x8000px
- 1RIGあたり最大10枚（imagesテーブルのCOUNTで制御）
```

### 3-3. Cloudflare Images Variants
事前に作成するバリアント:
```
- public   → fit=scale-down, w=1200（詳細ページメイン）
- thumb    → fit=cover, w=400, h=267（3:2サムネ）
- card     → fit=cover, w=280, h=187（Grid Card）
- avatar   → fit=cover, w=96, h=96（プロフィール）
- og       → fit=cover, w=1200, h=630（OGP用）
```

---

## Phase 4: Cookie・法的対応

### 4-1. Cookie同意バナー

> ⚠️ **同意モデルが未裁定（2026-08-21 監査で検出）**
> 旧記載は「**同意するボタン**でlocalStorageに保存」（明示同意型）と
> 「**続行することで**同意したものとみなされます」（黙示同意型）を**同一節に併記**していた。
> AdSense/Analytics を同意後ロードにするなら**明示同意型でなければ成立しない**。
> どちらを採るかイタヤ裁定。下記は明示同意型を前提とした実装案。

最小実装（明示同意型の場合）:
```
- 初回アクセス時にバナー表示（画面下部固定）
- 「同意する」ボタンでlocalStorageに保存
- AdSense/Google Analyticsは同意後にのみロード
- 2回目以降は非表示
```
テキスト案: 「このサイトではCookieを使用しています。詳しくはプライバシーポリシーをご覧ください。」
＋「同意する」ボタン（「続行することで同意」の文言は明示同意型と矛盾するため使わない）

**実装状況**: モックには**バナー未実装**（DOM・localStorage・同意ゲートいずれも無い。2026-08-21確認）。

### 4-2. 特定商取引法の表示
アフィリエイト収益がある場合の対応:
- 方式: フッターに「特定商取引法に基づく表記」リンク
- 内容: 運営者名・連絡先・サービス内容
- privacy.htmlまたは別ページ`/legal/tokushoho`に配置

---

## Phase 5: デプロイ・運用

### 5-1. Vercel設定
```
- 本番ドメイン: myrigrc.com
- プレビュー: 自動（PRごと）
- 環境変数: Vercel Dashboardで設定（.envファイルではなく）
- Edge Config or KV: Rate Limiting用
```

### 5-2. Supabase本番設定
```
- RLS: 全テーブル有効化（必須）
- Email Templates: カスタマイズ（確認メール・パスワードリセット）
- Webhook: 将来の通知用に設定枠だけ確保
```

### 5-3. Cloudflare設定
```
- Images: Variants作成（上記5種）
- カスタムドメイン設定（任意）
```

---

## Claude Code 安全ルール（全セッション共通）

Claude Codeへの指示には以下を常に含める:

```
【安全ルール — 必ず守ること】
1. .envファイルの内容を出力・表示しない
2. SUPABASE_SERVICE_ROLE_KEY をクライアント側コード（app/以下のClient Component、useEffect内等）に書かない
3. NEXT_PUBLIC_ プレフィックスのない環境変数をクライアントに露出させない
4. DELETE / DROP / TRUNCATE を含むSQLは実行前に内容を表示して確認を求める
   （※データ行の物理DELETEは CORE.md で**禁止**。削除は `deleted_at` の UPDATE で行う。
   本項は DDL・移行作業時の最終手段に対する確認ゲートであり、物理DELETEの許可ではない）
5. node_modules/ や .next/ をgit addしない
6. 本番DBのURLが指定されている場合、書き込み操作の前に必ず確認する
```
