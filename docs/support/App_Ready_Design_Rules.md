# App-Ready Design & Implementation Rules

**策定日:** 2026-05-02 / **監査:** 2026-08-21
**適用範囲:** MyRIG Web版（Next.js）のモックアップ制作・実装全般
**目的:** 将来のExpo/React Nativeアプリ化を前提に、Web版の設計段階から守るべきルールを定義

> **⚠️ 本書の位置づけ（2026-08-21 監査・3AIクロスチェックで改訂）**
>
> 本書は 2026-05 策定であり、色・URL・images・notifications・認証方式のいずれも
> **その後の裁定より2〜3世代古い状態**だった。個別の値を持つことをやめ、
> **各正典への参照に降格**する。本書が持つのは「アプリ化を見据えた構造の作り方」だけとする。
>
> | 項目 | 正本 |
> |---|---|
> | 色・デザイントークン | `docs/design/color-token-v8.md` / `design-nogo-list.md` |
> | URL・ページ構成 | `docs/ui/page-role-matrix-v1.md` |
> | DBスキーマ（App所有） | `docs/schema/myrig_db_schema_v1_6.md` |
> | DBスキーマ（Research所有） | `docs/schema/db-schema-answers-v1.md` |
> | 認証・未ログイン挙動 | `docs/ui/auth-guard-spec-v1.md` ＋ auth-onboarding-minimum-spec-v1 |
>
> **本書の記述と上記が食い違う場合は、常に上記が正。**

---

## 前提方針

- **Web本体:** Next.js + TypeScript + Supabase + Vercel + Cloudflare Images
- **将来アプリ:** Expo / React Native + TypeScript + Supabase（本命）
- **Capacitor:** 不採用
- **monorepo化:** 将来移行前提（今はNext.js単体プロジェクトで進行）
- **Server Actions / SSR:** 制限なく使用OK（Expo化時はWeb/アプリ別ビルド）

---

## Rule 1: データ処理の分離

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
- サーバー専用処理はServer Actionsに書いてOKだが、同等のクライアント版をservices層にも持つ

---

## Rule 2: URL設計は Page Role Matrix を正本とする

**URL の正本は `docs/ui/page-role-matrix-v1.md`。本書では列挙しない**
（2026-08-21 監査で訂正: 旧記載は8本のパスを列挙して「凍結」としていたが、
その後 Session 86 で `/saved` 系の廃止・`/garage/favorites` `/garage/pins` への統一が実施済みで、
本書の一覧は現行と一致していなかった）。

### 守るべきこと（構造ルールとしては有効）

- **URL構造が変わると通知・共有リンク・Deep Link が壊れる。**変更は正規の裁定を経ること
- **変更する場合は 301 リダイレクトを必ず用意する**（`/saved` → `/garage/favorites` が実例）
- ドメインは **`myrigrc.com`**（2026-08-21 監査で訂正。旧記載の `myrig-rc.com` はハイフン有無の誤記で、
  CORE.md・implementation_checklist.md はいずれも `myrigrc.com`。OAuthリダイレクト・OGP・Deep Link に直結するため重要）

### Deep Link対応表（将来）

| Web URL | App Deep Link |
|---|---|
| `https://myrigrc.com/rig/123` | `myrig://rig/123` |
| `https://myrigrc.com/parts/456` | `myrig://parts/456` |
| `https://myrigrc.com/user/taro` | `myrig://user/taro` |

---

## Rule 3: デザイントークンのTS管理

色・余白・角丸・フォントサイズをTSオブジェクトとして定義し、CSS変数とExpo themeの両方に変換可能にする。

> ⚠️ **色の実値は本書に置かない**（2026-08-21 監査で削除）。
> 旧記載のサンプル（`primary:#1A73E8` / `error:#D32F2F` / `success:#388E3C`）は
> **Material Design の既定色をそのまま書いたもの**で、MyRIG の v8 パレットとも
> NG-7「黄・赤・青と白黒以外の色を新しく足さない」とも全面的に矛盾していた。
> **カテゴリ色（`--cat-*`）の正本は `docs/design/color-token-v8.md`。**
> 面・文字・境界（`--color-*`）は `SoT_tokens-v6.css`（docs/外・モックアップ資産）、
> ステータス色（`--rig-status-*`）は別系統として維持。
> **エラー色・成功色は現時点でどの正典にも定義が無い**（NG-7「黄赤青と白黒以外を足さない」との
> 整合を含めて未裁定。必要になった時点でイタヤ裁定を取ること）。
> ここで示すのは「TSオブジェクトとして持ち、CSS変数とExpo themeの両方へ変換する」という**構造の型だけ**。

```ts
// tokens/colors.ts — 値は color-token-v8.md から取り込む（下記は構造の例示）
export const colors = {
  cat: {
    rig:   { base: '#FBFF00', on: '#151515' },
    parts: { base: '#D92D20', on: '#ffffff' },
    log:   { base: '#2F5F8F', on: '#ffffff', dark: '#3F709E' },
  },
  action: { /* 中立ソリッド。実値は要確認 — color-token-v8.md §3 */ },
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

- **共有トークン（色・基本余白・角丸・フォントサイズ）**をマジックナンバーで直書きすること
- 共有トークンをCSS変数だけで定義すること（Expo共有のためTS側にも持つ）

（2026-08-21 監査で範囲を限定: 旧記載は「色・余白の直値を全面禁止」「CSS変数は必ずTSにも二重保持」と
読めたが、コンポーネント固有の一回限りの値まで禁止すると SoT CSS 運用と二重管理になる。
**共有トークンに限る**のが本来の意図）

---

## Rule 4: タッチターゲット最低48px

**独立したインタラクティブ要素**（ボタン・アイコンボタン・トグル・リスト行・ナビ項目・カードのCTA等）の
タップ領域を最低48×48pxで設計する。

対象外: **本文中のインラインテキストリンク**（段落内の文字リンク）。行の高さに従うため48px確保の対象にしない
（2026-08-21 監査で明記。旧記載「すべてのリンク」は本文リンクまで含む読み方になっていた）。

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

**列定義の正本は `docs/schema/myrig_db_schema_v1_6.md` Domain 4 `images`。本書では定義しない**
（2026-08-21 監査で削除: 旧記載のSQLは `owner_id` / `cloudflare_image_id` / `is_cover` など
**列名がschema正典と全て食い違い**、さらに `entity_type` に `'user_avatar'` を含めていたが、
schema正典は「**プロフィール画像は含まない**」と明記している）。

- `entity_type` は `'rig' | 'part' | 'log'` の3種
- **プロフィール画像・ガレージカバー画像は `profiles.avatar_url` / `profiles.cover_image_url` で持つ。**
  images テーブルには入れない

> ⚠️ **schema側への申し送り（2026-08-21 監査）**: 旧定義にあった **`alt TEXT`（画像代替テキスト）**が
> schema正典の `images` に存在しない。アクセシビリティ要件としては必要なので、
> **`images.alt` の追加要否を schema 側で判断すること**（本書からは削除したが、要件を捨てたわけではない）。

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

> ⚠️ **HOLD: 認証方式（プロバイダ・メール認証の有無）は未裁定**（2026-08-21 監査・3AIクロスチェックで検出）
>
> 4文書で記述が割れており、**どれが現行仕様かを確定できていない**。
>
> | 出所 | 記述 |
> |---|---|
> | 本書（旧記載・2026-05策定） | Google / **GitHub** OAuth ＋ メール/パスワード |
> | implementation_checklist.md Phase 1-1/1-2 | Google OAuth ＋ **メール/パスワード ＋ メール確認フロー** |
> | auth-guard-spec-v1 §7 | **OAuth only**（auth-onboarding-minimum-spec-v1 を参照） |
> | pc-mobile-spec-inheritance #33 | Google / **X / Facebook** OAuth・**メール認証なし** |
> | 同 #34特記（PC現物の onboarding 画面） | Google / **Apple**（#33 と不一致と明記されている） |
>
> **決定は実装フェーズで行う。**それまで本書はプロバイダを具体列挙しない。
> 一次資料である `auth-onboarding-minimum-spec-v1`（本repo未収録）の確認が先。

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

## Rule 7: サーバー専用処理の分離意識

Server Actions / Route Handlers / SSR を自由に使ってOKだが、以下を守る。

### 原則

- **読み取り系:** 将来Expoで共有する見込みのあるものは、クライアントからも呼べるservices層を持つ（SSRと併用OK）。
  Web専用と割り切れる読み取りまで二重化する必要はない（2026-08-21 監査で限定）
- **書き込み系:** Server Actionsに書いてOK（将来Expo側はSupabase直接 or Edge Functions経由）
- **認証チェック:** middleware.tsで一元管理

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

**列定義の正本は `docs/schema/myrig_db_schema_v1_6.md` Domain 9 `notifications`。本書では定義しない**
（2026-08-21 監査で削除: 旧記載の `type` は `'comment' | 'like' | 'pin' | 'follow'` で、
schema正典の `'like' | 'favorite' | 'follow' | 'comment' | 'comment_reply'` と不一致だった。
**`pin` が余分、`favorite` と `comment_reply` が欠落**していた）。

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

- `RC_Master_Research_Rules_v4.4_final_rev3`（RC調査ルール。**DB Research PJ 側が保持し本repo未収録**。
  2026-08-21 監査で版を訂正 — 旧記載の v4.2 は db-schema-answers-v1 §正典が挙げる v4.4_final_rev3 より古い）
- `MyRIG_Category_Structure_v1.4`（カテゴリ正典。同じくDB Research PJ側が保持）
- `Research_DB_Schema_v1.2`（Research側スキーマ正本。同上）

※上記3件はいずれも **DB Research PJ が原本を保持**しており、本repoには写しがない。
本repo内でこれらの裁定内容を参照する場合は `docs/schema/db-schema-answers-v1.md`（App側の写し）を見ること。
