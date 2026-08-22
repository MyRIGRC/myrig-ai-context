# App-Ready Design & Implementation Rules

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> 本書が持つのは「将来のアプリ化を見据えた**構造の作り方**」だけ。
> **「既存仕様と異なる」ことだけを理由に案を捨てない。**
> 差分を明示すればイタヤ裁定で変更できる。
>
> ただし以下は **L1（恒久ルール・逸脱禁止）**。節ごとに再掲する。
>
> - **責務分離**（Rule 1 / Rule 7：UI・services・サーバー専用処理を混ぜない）
> - **画像の所有分離**（Rule 5：プロフィール画像を `images` に入れない）
> - **HOLD 項目を確定として扱わないこと**（Rule 6 認証方式）

**策定日:** 2026-05-02 / **最終更新:** 2026-08-22
**適用範囲:** MyRIG Web版（Next.js）のモックアップ制作・実装全般
**目的:** 将来のExpo/React Nativeアプリ化を前提に、Web版の設計段階から守るべき構造ルールを定義

### 個別の値の正本（本書では持たない）

| 項目 | 正本 |
|---|---|
| 色・デザイントークン | `docs/design/color-token-v8.md` / `design-nogo-list.md` |
| URL・ページ構成 | `docs/ui/page-role-matrix-v1.md` |
| DBスキーマ（App所有） | `docs/schema/myrig_db_schema_v1_6.md` |
| DBスキーマ（Research所有） | `docs/schema/db-schema-answers-v1.md` |
| 認証・未ログイン挙動 | `docs/ui/auth-guard-spec-v1.md` ＋ auth-onboarding-minimum-spec-v1 |

**本書の記述と上記が食い違う場合は、常に上記が正。**

---

## 前提方針

- **Web本体:** Next.js + TypeScript + Supabase + Vercel + Cloudflare Images
- **将来アプリ:** Expo / React Native + TypeScript + Supabase（本命）
- **Capacitor:** 不採用
- **monorepo化:** 将来移行前提（今はNext.js単体プロジェクトで進行）
- **Server Actions / SSR:** 制限なく使用OK（Expo化時はWeb/アプリ別ビルド）

---

## Rule 1: データ処理の分離 — L1

Supabaseクエリ・ビジネスロジックをUIコンポーネントに直書きしない。

```
features/{domain}/
  components/   ← UI（Web専用でOK）
  hooks/        ← useRig, useParts, useGarage 等
  services/     ← Supabase呼び出し（将来Expo側で共有）
  types/        ← 型定義（将来Expo側で共有）
```

### 共有対象の判断基準

| 層 | Web専用OK | 将来Expo共有 |
|---|---|---|
| components/ | ✅ | — |
| hooks/ | — | ✅ |
| services/ | — | ✅ |
| types/ | — | ✅ |
| Server Actions | ✅ | — |

### services層の原則

- 1ドメイン1ファイル（`services/rigs.ts`, `services/parts.ts`, `services/images.ts`）
- Supabase clientの注入はパラメータまたはファクトリパターン（Web用/App用で差し替え可能に）
- サーバー専用処理はServer Actionsに書いてOK。**将来Expoで共有する見込みのあるものだけ**、
  同等のクライアント版をservices層にも持つ（Rule 7 と同じ基準）

---

## Rule 2: URL設計は Page Role Matrix を正本とする

**URL の正本は `docs/ui/page-role-matrix-v1.md`。本書では列挙しない。**

### 守るべきこと

- **URL構造が変わると通知・共有リンク・Deep Link が壊れる。**変更は正規の裁定を経ること
- **変更する場合は 301 リダイレクトを必ず用意する**（`/saved` → `/garage/favorites` が実例）
- ドメインは **`myrigrc.com`**（ハイフン無し。OAuthリダイレクト・OGP・Deep Link に直結する）

### Deep Link対応表（将来）

| Web URL | App Deep Link |
|---|---|
| `https://myrigrc.com/rig/123` | `myrig://rig/123` |
| `https://myrigrc.com/parts/456` | `myrig://parts/456` |
| `https://myrigrc.com/user/taro` | `myrig://user/taro` |

---

## Rule 3: デザイントークンのTS管理

色・余白・角丸・フォントサイズをTSオブジェクトとして定義し、CSS変数とExpo themeの両方に変換可能にする。

**色の実値は本書に置かない。**

- カテゴリ色（`--cat-*`）の正本は `docs/design/color-token-v8.md`
- 面・文字・境界（`--color-*`）は `SoT_tokens-v6.css`（docs/外・モックアップ資産）
- ステータス色（`--rig-status-*`）は別系統として維持
- ⚠️ **エラー色・成功色はどの正典にも定義が無い**（NG-7「黄・赤・青と白黒以外を足さない」との
  整合を含めて未裁定。必要になった時点でイタヤ裁定を取ること）

ここで示すのは「TSオブジェクトとして持ち、CSS変数とExpo themeの両方へ変換する」という**構造の型だけ**。

```ts
// tokens/colors.ts — 値は color-token-v8.md から取り込む（下記は構造の例示）
export const colors = {
  cat: {
    rig:   { base: '#FBFF00', on: '#151515' },
    parts: { base: '#D92D20', on: '#ffffff' },
    log:   { base: '#2F5F8F', on: '#ffffff', dark: '#3F709E' },
  },
  action: { /* 中立ソリッド。実値は color-token-v8.md §3 */ },
  // 面・文字・境界（--color-*）は SoT_tokens-v6.css が正本
  // ステータス色（--rig-status-*）は別系統として維持
} as const;

// tokens/spacing.ts
export const spacing = {
  xs: 4,
  sm: 8,
  md: 16,
  lg: 24,
  xl: 32,
  xxl: 48,
} as const;

// tokens/radius.ts
export const radius = {
  sm: 4,
  md: 8,
  lg: 12,
  xl: 16,
  full: 9999,
} as const;
```

### Web側での使用

- TSトークンからCSS変数を生成するユーティリティを用意
- CSS Modules / Tailwind のどちらでもトークン値を参照可能にする

### 禁止事項

対象は**共有トークン（色・基本余白・角丸・フォントサイズ）に限る**。
コンポーネント固有の一回限りの値までは縛らない。

- 共有トークンをマジックナンバーで直書きすること
- 共有トークンをCSS変数だけで定義すること（Expo共有のためTS側にも持つ）

---

## Rule 4: タッチターゲット最低48px

**独立したインタラクティブ要素**（ボタン・アイコンボタン・トグル・リスト行・ナビ項目・カードのCTA等）の
タップ領域を最低48×48pxで設計する。

対象外: **本文中のインラインテキストリンク**（段落内の文字リンク）。行の高さに従うため48px確保の対象にしない。

### モックアップ段階

- ボタン高さ: 最低48px
- アイコンボタン: アイコン自体が小さくてもタップ領域は48×48px確保
- リスト項目: タップ可能な行は最低48px高

### 実装段階

```css
/* 最小タッチターゲット */
.touchable {
  min-height: 48px;
  min-width: 48px;
}

/* アイコンボタンの場合 */
.icon-button {
  padding: 12px; /* 24pxアイコン + 12px×2 = 48px */
}
```

### Safe Area対応

```css
/* iPhoneノッチ・ホームバー対応 */
.bottom-nav {
  padding-bottom: env(safe-area-inset-bottom);
}
.top-header {
  padding-top: env(safe-area-inset-top);
}
```

---

## Rule 5: 画像の共通imagesテーブル設計

rig / part / log で共通の画像テーブルを使用する。

**列定義の正本は `docs/schema/myrig_db_schema_v1_6.md` Domain 4 `images`。本書では定義しない。**

- `entity_type` は `'rig' | 'part' | 'log'` の3種
- **プロフィール画像・ガレージカバー画像は `profiles.avatar_url` / `profiles.cover_image_url` で持つ。
  images テーブルには入れない — L1**
- ⚠️ schema 側への申し送り: **`images.alt`（画像代替テキスト）**が schema 正典に存在しない。
  アクセシビリティ要件として追加要否を schema 側で判断すること（要件は捨てていない）

### 画像アップロードフロー

```
クライアント（Web/App共通）
  → リサイズ（クライアント側で実施）
  → Cloudflare Images API へアップロード
  → cloudflare_image_id を取得
  → images テーブルに INSERT
```

### 禁止事項

- エンティティごとに別テーブルを作る（`rig_images`, `part_images` 等）
- Supabase Storageへの直接保存（Cloudflare Images統一）
- リサイズをサーバー側だけに依存（アプリでも動くようクライアント側で処理）

---

## Rule 6: 認証ロジックの集約

Supabase Authの呼び出しを `services/auth.ts` に集約する。

> ⚠️ **HOLD: 認証方式（プロバイダ・メール認証の有無）は未裁定 — L1**
>
> 文書間で記述が割れており、現行仕様を確定できていない。**決定は実装フェーズで行う。**
> それまで**本書はプロバイダを具体列挙しない**し、どの案も確定として扱わない。
> 割れている出所の一覧は `_AI/MyRIG_CURRENT.md`。一次資料 `auth-onboarding-minimum-spec-v1`
> （本repo未収録）の確認が先。

```ts
// services/auth.ts — provider の具体値は認証方式の裁定後に確定する
type AuthProvider = string; // 'google' | ... （裁定待ち）

export const authService = {
  signInWithOAuth: (provider: AuthProvider) => { ... },
  signOut: () => { ... },
  getSession: () => { ... },
  onAuthStateChange: (callback) => { ... },
  // メール/パスワード認証を採用するかは未裁定
};
```

### Deep Link対応の準備

- Supabase Dashboard の「Redirect URLs」に将来のアプリスキーム枠を確保
  - `https://myrigrc.com/auth/callback`（Web用）
  - `myrig://auth-callback`（将来App用・予約）
- OAuth のリダイレクト先をハードコードせず、環境変数で切り替え可能にする

```ts
const REDIRECT_URL = process.env.NEXT_PUBLIC_AUTH_REDIRECT_URL
  ?? 'https://myrigrc.com/auth/callback';
```

---

## Rule 7: サーバー専用処理の分離意識 — L1

Server Actions / Route Handlers / SSR を自由に使ってOKだが、以下を守る。

### 原則

- **読み取り系:** 将来Expoで共有する見込みのあるものは、クライアントからも呼べるservices層を持つ（SSRと併用OK）。
  Web専用と割り切れる読み取りまで二重化する必要はない
- **書き込み系:** Server Actionsに書いてOK（将来Expo側はSupabase直接 or Edge Functions経由）
- **認証チェック:** **P1（全ページ認証必須）と全体ガード（Maintenance / Suspended）のみ middleware.ts。**
  P2（元ページ維持のままModal）と P3（mutate操作時のみ）は page / component 側で判定する
  （auth-guard-spec-v1 §2・§5.1）

### 判断フロー

```
この処理はサーバーでしか動かない？
  → YES → Server Actions / Route Handlers に配置
           ＋ 将来Expo用に同等のEdge Function化が可能か意識する
  → NO  → services/ に配置（Web/App共有候補）
```

### 禁止事項

- **Expo共有予定の**データ取得をServer Actionsだけで行う（クライアント版も持つ）
- Node.js専用ライブラリへの過度な依存（将来Expo側で代替が必要になる）

---

## Rule 8: 通知テーブルの設計予約

MVP時点では実装しない（マイグレーション「将来実行分」）。

**列定義の正本は `docs/schema/myrig_db_schema_v1_6.md` Domain 9 `notifications`。本書では定義しない。**

- Web版: アプリ内通知（ベルアイコン）として実装
- App版: Expo Push Notifications → APNs/FCM 連携

---

## チェックリスト

モックアップ・実装のレビュー時に確認：

- [ ] Supabaseクエリがcomponents/内に直書きされていない
- [ ] URL構造が page-role-matrix-v1.md に準拠している
- [ ] 共有トークン（色・基本余白）にマジックナンバーがない（v8トークンを参照している）
- [ ] 独立したタップ要素が48×48px以上（本文中インラインリンクは対象外）
- [ ] 画像関連の処理が共通imagesテーブル前提になっている
- [ ] 認証処理がservices/auth.tsに集約されている
- [ ] サーバー専用処理とクライアント共有処理が分離されている

---

## 関連ファイル

以下3件は **DB Research PJ が原本を保持**しており、本repoには写しがない。
本repo内でこれらの裁定内容を参照する場合は `docs/schema/db-schema-answers-v1.md`（App側の写し）を見ること。

- `RC_Master_Research_Rules_v4.4_final_rev3`（RC調査ルール）
- `MyRIG_Category_Structure_v1.4`（カテゴリ正典）
- `Research_DB_Schema_v1.2`（Research側スキーマ正本）
