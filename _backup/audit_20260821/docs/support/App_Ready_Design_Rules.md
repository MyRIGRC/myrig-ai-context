# App-Ready Design & Implementation Rules

**策定日:** 2026-05-02
**適用範囲:** MyRIG Web版（Next.js）のモックアップ制作・実装全般
**目的:** 将来のExpo/React Nativeアプリ化を前提に、Web版の設計段階から守るべきルールを定義

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

## Rule 2: URL設計の固定

以下のパス構造を確定し、変更しない。将来 `myrig://` スキームにマッピングする。

```
/rig/[rigId]
/parts/[partId]
/log/[logId]
/user/[username]
/garage
/garage/rigs/[rigId]
/search
/settings
```

### Deep Link対応表（将来）

| Web URL | App Deep Link |
|---|---|
| `https://myrig-rc.com/rig/123` | `myrig://rig/123` |
| `https://myrig-rc.com/parts/456` | `myrig://parts/456` |
| `https://myrig-rc.com/user/taro` | `myrig://user/taro` |

- 通知タップ時にこのマッピングでアプリ内遷移させる
- URL構造が変わると通知・共有リンクが壊れるため、パス設計は一度確定したら凍結

---

## Rule 3: デザイントークンのTS管理

色・余白・角丸・フォントサイズをTSオブジェクトとして定義し、CSS変数とExpo themeの両方に変換可能にする。

```ts
// tokens/colors.ts
export const colors = {
  primary: '#1A73E8',
  background: '#FFFFFF',
  surface: '#F5F5F5',
  text: {
    primary: '#1A1A1A',
    secondary: '#666666',
  },
  border: '#E0E0E0',
  error: '#D32F2F',
  success: '#388E3C',
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

- マジックナンバーでの色・余白指定（`#333`, `margin: 12px` 等の直書き）
- CSS変数のみでの定義（TS側にも必ず持つ）

---

## Rule 4: タッチターゲット最低48px

すべてのインタラクティブ要素（ボタン・リンク・アイコン・トグル等）のタップ領域を最低48×48pxで設計する。

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

rig / part / log / user全てで共通の画像テーブルを使用する。

```sql
images (
  id              UUID PRIMARY KEY,
  owner_id        UUID REFERENCES auth.users(id),
  entity_type     TEXT NOT NULL,  -- 'rig' | 'part' | 'log' | 'user_avatar'
  entity_id       UUID NOT NULL,
  cloudflare_image_id  TEXT NOT NULL,
  variant         TEXT,           -- 'public', 'thumbnail' 等
  width           INTEGER,
  height          INTEGER,
  sort_order      INTEGER DEFAULT 0,
  is_cover        BOOLEAN DEFAULT false,
  alt             TEXT,
  created_at      TIMESTAMPTZ DEFAULT now()
);
```

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

```ts
// services/auth.ts
export const authService = {
  signInWithEmail: (email: string, password: string) => { ... },
  signInWithOAuth: (provider: 'google' | 'github') => { ... },
  signOut: () => { ... },
  getSession: () => { ... },
  onAuthStateChange: (callback) => { ... },
};
```

### Deep Link対応の準備

- Supabase Dashboard の「Redirect URLs」に将来のアプリスキーム枠を確保
  - `https://myrig-rc.com/auth/callback`（Web用）
  - `myrig://auth-callback`（将来App用・予約）
- OAuth/Magic Linkのリダイレクト先をハードコードせず、環境変数で切り替え可能にする

```ts
const REDIRECT_URL = process.env.NEXT_PUBLIC_AUTH_REDIRECT_URL
  ?? 'https://myrig-rc.com/auth/callback';
```

---

## Rule 7: サーバー専用処理の分離意識

Server Actions / Route Handlers / SSR を自由に使ってOKだが、以下を守る。

### 原則

- **読み取り系:** クライアントサイドでも取得可能なservices層を持つ（SSRと併用OK）
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

- 全てのデータ取得をServer Actionsだけで行う（クライアント版も持つ）
- Node.js専用ライブラリへの過度な依存（将来Expo側で代替が必要になる）

---

## Rule 8: 通知テーブルの設計予約

MVP時点で実装不要だが、テーブル設計だけ確定しておく。

```sql
notifications (
  id              UUID PRIMARY KEY,
  user_id         UUID REFERENCES auth.users(id),
  type            TEXT NOT NULL,  -- 'comment' | 'like' | 'pin' | 'follow'
  entity_type     TEXT NOT NULL,  -- 'rig' | 'part' | 'log'
  entity_id       UUID NOT NULL,
  actor_id        UUID REFERENCES auth.users(id),
  is_read         BOOLEAN DEFAULT false,
  created_at      TIMESTAMPTZ DEFAULT now()
);
```

- Web版: アプリ内通知（ベルアイコン）として実装
- App版: Expo Push Notifications → APNs/FCM 連携

---

## チェックリスト

モックアップ・実装のレビュー時に確認：

- [ ] Supabaseクエリがcomponents/内に直書きされていない
- [ ] URL構造がRule 2の定義に準拠している
- [ ] 色・余白にマジックナンバーがない（トークン参照している）
- [ ] タップ可能な要素が全て48×48px以上
- [ ] 画像関連の処理が共通imagesテーブル前提になっている
- [ ] 認証処理がservices/auth.tsに集約されている
- [ ] サーバー専用処理とクライアント共有処理が分離されている

---

## 関連ファイル

- `RC_Master_Research_Rules_v4.2.md`（RC調査ルール）
- `Research_Workflow_Rules.md`（調査ワークフロー）
