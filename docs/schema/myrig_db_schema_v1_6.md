# MyRIG RC — Database Schema Design v1.6

## 命名規則

- テーブル名: `snake_case` 複数形（`rigs`, `parts`, `maintenance_logs`）
- カラム名: `snake_case`（`created_at`, `rig_type`）
- 外部キー: `参照先テーブル単数_id`（`user_id`, `rig_id`）
- boolean: `is_` プレフィックス（`is_public`, `is_archived`）
- 日時: `_at` サフィックス（`purchased_at`, `deleted_at`）
- JSONB: 自由入力・可変構造のデータに使用
- UUID: 全テーブルの主キー（Supabase標準）
- 論理削除: `deleted_at` NULLABLE（物理削除しない）
- 制約付きTEXT: 固定値セットは `TEXT + CHECK` で管理（enumより柔軟）

---

## Domain 1: ユーザー

### `profiles`
Supabase Auth (`auth.users`) と1:1。認証情報以外の全プロフィール。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, FK → auth.users.id | Supabase Authと同一ID |
| username | TEXT | UNIQUE, NOT NULL | @表示名。URL slug |
| display_name | TEXT | | 表示用ニックネーム |
| bio | TEXT | | 自己紹介文 |
| avatar_url | TEXT | | Cloudflare Images URL |
| cover_image_url | TEXT | | ガレージカバー画像URL |
| country_code | TEXT | | ISO 3166-1 alpha-2 |
| preferred_rig_type | TEXT | | 大大カテゴリ優先表示 |
| preferred_subcategory | TEXT | | サブカテゴリ優先表示 |
| website_url | TEXT | | 個人サイトURL |
| social_links | JSONB | DEFAULT '[]' | [{platform, url, label}] |
| is_public | BOOLEAN | DEFAULT true | プロフィール公開設定 |
| comments_enabled_rig_part | BOOLEAN | DEFAULT true | RIG＋パーツへのコメント受付ON/OFF |
| comments_enabled_log | BOOLEAN | DEFAULT true | LOGへのコメント受付ON/OFF |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除 |

**プロフィール画像はこのテーブルで完結。`images`テーブルには含めない。**

---

## Domain 2: RIG・パーツ・ログ

### `rigs`
ユーザーが登録するRIG（車体）。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | オーナー |
| rig_master_id | UUID | FK → rig_masters.id, NULLABLE | 公式マスターとの紐付け |
| rig_type | TEXT | NOT NULL, DEFAULT 'rc' | CHECK (rig_type IN ('rc','mini4wd','miniz','drone','airplane','boat')) |
| manufacturer_id | UUID | FK → manufacturers.id, NULLABLE | メーカー |
| manufacturer_name_cache | TEXT | | 非正規化。表示高速化用 |
| model_name | TEXT | NOT NULL | モデル名 |
| base_model | TEXT | | ベース車種（載せ替え時） |
| category_id | UUID | FK → categories.id, NULLABLE | サブカテゴリ |
| nickname | TEXT | | ユーザーがつけた愛称 |
| description | TEXT | | 説明文 |
| ownership_status | TEXT | NOT NULL, DEFAULT 'current' | CHECK (ownership_status IN ('current','past','wishlist')) |
| is_public | BOOLEAN | DEFAULT true | 公開設定 |
| purchased_at | DATE | NULLABLE | 購入日 |
| purchase_price | INTEGER | NULLABLE | 最小通貨単位（円/セント） |
| currency_code | CHAR(3) | DEFAULT 'JPY' | ISO 4217 通貨コード |
| purchase_store | TEXT | NULLABLE | 購入店名 |
| build_details | JSONB | DEFAULT '{}' | register-rigの詳細データ全格納 |
| external_links | JSONB | DEFAULT '[]' | [{label, url}] |
| product_line | TEXT | NULLABLE | ブランドシリーズ名（例：Mini-Z, Clod Buster） |
| platform | TEXT | NULLABLE | シャーシ/プラットフォーム名（例：SCX10 III, BB-01） |
| sort_order | INTEGER | DEFAULT 0 | ガレージ内表示順 |
| view_count | INTEGER | DEFAULT 0 | 閲覧数 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | |

**`build_details` JSONB構造:**
```json
{
  "mechanics": [
    {"label": "ESC", "value": "Hobbywing Fusion SE2", "note": ""},
    {"label": "Motor", "value": "Built-in 1800kv", "note": ""},
    {"label": "Servo", "value": "Reefs RC 299", "note": "交換候補"}
  ],
  "suspension": [...],
  "exterior": [...],
  "electronics": [...],
  "battery": [...],
  "other": [...],
  "raw_custom_fields": [
    {"label": "塗料", "value": "タミヤ TS-14"}
  ]
}
```

---

### `parts`
ユーザーが登録するパーツ。1パーツ＝複数RIGに装着可能（中間テーブルで管理）。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | オーナー |
| parts_master_id | UUID | FK → parts_masters.id, NULLABLE | 公式マスターとの紐付け |
| rig_type | TEXT | NOT NULL, DEFAULT 'rc' | CHECK (同上) |
| compatible_types | TEXT[] | DEFAULT '{rc}' | 対応する大大カテゴリ配列 |
| manufacturer_id | UUID | FK → manufacturers.id, NULLABLE | |
| manufacturer_name_cache | TEXT | | |
| product_name | TEXT | NOT NULL | 製品名 |
| category_id | UUID | FK → categories.id, NULLABLE | パーツカテゴリ |
| description | TEXT | | ユーザーメモ |
| part_number | TEXT | | メーカー型番 |
| purchased_at | DATE | NULLABLE | |
| purchase_price | INTEGER | NULLABLE | |
| currency_code | CHAR(3) | DEFAULT 'JPY' | ISO 4217 |
| purchase_store | TEXT | NULLABLE | |
| condition | TEXT | DEFAULT 'new' | CHECK (condition IN ('new','used','modded')) |
| is_public | BOOLEAN | DEFAULT true | |
| view_count | INTEGER | DEFAULT 0 | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | |

---

### `rig_parts`
パーツとRIGの多対多リレーション。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | 所有者（RLS用） |
| rig_id | UUID | FK → rigs.id, NOT NULL | |
| part_id | UUID | FK → parts.id, NOT NULL | |
| installed_at | DATE | NULLABLE | 装着日 |
| removed_at | DATE | NULLABLE | 取り外し日（NULLなら現在装着中） |
| note | TEXT | | メモ |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**制約:**
```sql
CREATE UNIQUE INDEX idx_rig_parts_active
ON rig_parts(rig_id, part_id)
WHERE removed_at IS NULL;
```

---

### `maintenance_logs`
整備・走行ログ。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | |
| rig_id | UUID | FK → rigs.id, NULLABLE | 紐づくRIG（任意） |
| log_type | TEXT | NOT NULL, DEFAULT 'maintenance' | CHECK (log_type IN ('maintenance','run','custom','memo')) |
| title | TEXT | NOT NULL | |
| body | TEXT | | 本文（Markdown or plain） |
| location | TEXT | | 場所 |
| weather | TEXT | | 天候 |
| surface | TEXT | | 路面 |
| duration_minutes | INTEGER | | 走行/作業時間 |
| logged_at | DATE | NULLABLE | 実施日（登録日と別） |
| is_public | BOOLEAN | DEFAULT true | |
| view_count | INTEGER | DEFAULT 0 | |
| tags | TEXT[] | DEFAULT '{}' | タグ配列 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | |

---

## Domain 3: カテゴリ・マスターデータ

### `manufacturers`
メーカーマスター。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| name | TEXT | NOT NULL | 正式名称 |
| name_en | TEXT | | 英語表記 |
| slug | TEXT | UNIQUE, NOT NULL | URL用 |
| logo_url | TEXT | | |
| website_url | TEXT | | |
| country_code | TEXT | | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

---

### `categories`
カテゴリマスター。2階層（大カテゴリ → サブカテゴリ）。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| rig_type | TEXT | NOT NULL | 所属する大大カテゴリ |
| parent_id | UUID | FK → categories.id, NULLABLE | NULLなら大カテゴリ |
| name | TEXT | NOT NULL | |
| name_en | TEXT | | |
| slug | TEXT | NOT NULL | URL用 |
| sort_order | INTEGER | DEFAULT 0 | 表示順 |
| target | TEXT | NOT NULL, DEFAULT 'rig' | CHECK (target IN ('rig','part')) |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**UNIQUE:** `(rig_type, slug, target)`

---

### `rig_masters`
Official Library製品マスター。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| rig_type | TEXT | NOT NULL | |
| manufacturer_id | UUID | FK → manufacturers.id, NOT NULL | |
| model_name | TEXT | NOT NULL | |
| category_id | UUID | FK → categories.id, NULLABLE | |
| description | TEXT | | |
| specs | JSONB | DEFAULT '{}' | スペック構造化データ |
| msrp | INTEGER | NULLABLE | メーカー希望小売価格 |
| currency_code | CHAR(3) | DEFAULT 'JPY' | |
| release_date | DATE | NULLABLE | |
| is_discontinued | BOOLEAN | DEFAULT false | |
| slug | TEXT | UNIQUE, NOT NULL | URL用 |
| image_url | TEXT | | メイン画像 |
| product_line | TEXT | NULLABLE | ブランドシリーズ名（例：Mini-Z, Clod Buster） |
| platform | TEXT | NULLABLE | シャーシ/プラットフォーム名（例：SCX10 III, BB-01） |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |

---

### `parts_masters`
Official Libraryパーツマスター。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| rig_type | TEXT | NOT NULL | |
| compatible_types | TEXT[] | DEFAULT '{}' | |
| manufacturer_id | UUID | FK → manufacturers.id, NOT NULL | |
| product_name | TEXT | NOT NULL | |
| category_id | UUID | FK → categories.id, NULLABLE | |
| part_number | TEXT | | メーカー型番 |
| description | TEXT | | |
| specs | JSONB | DEFAULT '{}' | |
| aliases | TEXT[] | DEFAULT '{}' | 通称・略称・旧名・海外名 |
| msrp | INTEGER | NULLABLE | |
| currency_code | CHAR(3) | DEFAULT 'JPY' | |
| release_date | DATE | NULLABLE | |
| is_discontinued | BOOLEAN | DEFAULT false | |
| slug | TEXT | UNIQUE, NOT NULL | |
| image_url | TEXT | | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |

---

## Domain 4: メディア

### `images`
RIG・パーツ・ログの画像統合管理。**プロフィール画像は含まない。**

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | アップロードしたユーザー |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig','part','log')) |
| entity_id | UUID | NOT NULL | 対象のID |
| url | TEXT | NOT NULL | Cloudflare Images URL |
| thumbnail_url | TEXT | | サムネイルURL |
| sort_order | INTEGER | DEFAULT 0 | 表示順 |
| is_primary | BOOLEAN | DEFAULT false | メイン画像フラグ |
| width | INTEGER | | 元画像幅 |
| height | INTEGER | | 元画像高さ |
| file_size | INTEGER | | バイト数 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除 |

---

## Domain 5: ソーシャル

### `likes`
♥いいね。公開カウント・通知あり。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig','part','log','comment')) |
| entity_id | UUID | NOT NULL | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**UNIQUE:** `(user_id, entity_type, entity_id)`

---

### `favorites`
★お気に入り。自分の保存リスト・公開カウント。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig','part','log')) |
| entity_id | UUID | NOT NULL | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**UNIQUE:** `(user_id, entity_type, entity_id)`

---

### `pins`
📌ピン留め。非公開・一時保存。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig','part','log')) |
| entity_id | UUID | NOT NULL | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**UNIQUE:** `(user_id, entity_type, entity_id)`

---

### `follows`
ユーザーフォロー。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| follower_id | UUID | FK → profiles.id, NOT NULL | フォローする側 |
| following_id | UUID | FK → profiles.id, NOT NULL | フォローされる側 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**UNIQUE:** `(follower_id, following_id)`
**CHECK:** `follower_id != following_id`

---

### `comments`
コメント。RIG・パーツ・ログに対して投稿可能。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | コメント投稿者 |
| entity_type | TEXT | NOT NULL, CHECK (entity_type IN ('rig','part','log')) | |
| entity_id | UUID | NOT NULL | 対象コンテンツID |
| parent_id | UUID | FK → comments.id, NULLABLE | 返信先（1階層のみ。アプリ層で強制） |
| body | TEXT | NOT NULL | プレーンテキストのみ。500文字上限（アプリ層） |
| status | TEXT | NOT NULL, DEFAULT 'published' | CHECK (status IN ('published','pending','hidden','deleted')) |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 削除時刻の記録専用。表示制御はstatusで行う |

**status値の意味：** published=公開 / pending=保留 / hidden=オーナー・運営非表示 / deleted=削除

**parent_id整合性（trigger/アプリ層で実装）：**
- 親のentity_type/entity_idが子と一致すること
- 親自身のparent_idはNULLであること（1階層強制）
- entity_type='log'の場合、parent_idは常にNULL（LOGはフラット表示）

**表示ルール：**
- RIG・パーツ：1階層ツリー表示（コメント→返信）
- LOG：フラット時系列表示（parent_id不使用）

**権限ルール：**
- 投稿者本人：自分のコメントを削除可能（status→deleted）
- コンテンツオーナー：他人のコメントを非表示可能（status→hidden）。削除不可
- 運営者：非表示解除・完全削除・制裁対応

**制御（アプリ層）：**
- ログイン必須（ゲスト投稿不可）
- URLパターン検出→投稿ブロック（全面禁止）
- レート制限：30秒間隔、5分5件上限
- プレーンテキスト限定（HTML/Markdown/画像/メンションなし）

---

### `comment_reports`
コメント専用通報。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| comment_id | UUID | FK → comments.id, NOT NULL | 通報対象コメント |
| reporter_user_id | UUID | FK → profiles.id, NOT NULL | 通報者 |
| reason_code | TEXT | NOT NULL, CHECK (reason_code IN ('spam','abuse','harassment','other')) | |
| note | TEXT | NULLABLE | 自由記述 |
| status | TEXT | NOT NULL, DEFAULT 'open', CHECK (status IN ('open','reviewing','resolved','rejected')) | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| resolved_at | TIMESTAMPTZ | NULLABLE | |
| resolved_by | UUID | FK → profiles.id, NULLABLE | 対応した運営者 |

**UNIQUE:** `(comment_id, reporter_user_id)`

---

### `content_reports`
RIG・パーツ・LOG自体の通報。comment_reportsとは別管理。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| entity_type | TEXT | NOT NULL, CHECK (entity_type IN ('rig','part','log')) | 通報対象の種別 |
| entity_id | UUID | NOT NULL | 通報対象のID |
| reporter_user_id | UUID | FK → profiles.id, NOT NULL | 通報者 |
| reason_code | TEXT | NOT NULL, CHECK (reason_code IN ('inappropriate','spam','copyright','wrong_info','harassment','other')) | |
| note | TEXT | NULLABLE | 自由記述 |
| status | TEXT | NOT NULL, DEFAULT 'open', CHECK (status IN ('open','reviewing','resolved','rejected')) | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| resolved_at | TIMESTAMPTZ | NULLABLE | |
| resolved_by | UUID | FK → profiles.id, NULLABLE | 対応した運営者 |

**UNIQUE:** `(entity_type, entity_id, reporter_user_id)`

**reason_code値（report.htmlのUIと対応）：**
- `inappropriate` = 不適切なコンテンツ・画像
- `spam` = スパム・宣伝目的の投稿
- `copyright` = 著作権侵害
- `wrong_info` = 表記ミス・情報の誤り
- `harassment` = 嫌がらせ・ハラスメント
- `other` = その他

---

### `comments`（将来用 → MVP実装に昇格済み。上記参照）

---

## Domain 6: マネタイズ

### `affiliate_links`
製品マスターに紐づくアフィリエイトリンク。国別管理。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig_master','parts_master')) |
| entity_id | UUID | NOT NULL | 製品マスターID |
| country_code | TEXT | NOT NULL | JP / US / GLOBAL |
| store_name | TEXT | NOT NULL | amazon_jp / rakuten / amain 等 |
| url | TEXT | NOT NULL | アフィリエイトURL |
| priority | INTEGER | DEFAULT 0 | 表示順 |
| is_active | BOOLEAN | DEFAULT true | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |

---

## Domain 7: ページ管理（ウィジェットCMS）

### `page_blocks`
トップページ・カテゴリページ等のセクション構成を管理。Netflix/WordPress Widget型のブロックCMS。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| page_type | TEXT | NOT NULL | CHECK (page_type IN ('index','category_top','subcategory_top')) |
| page_ref_id | UUID | NULLABLE | カテゴリ/サブカテゴリID。indexはNULL |
| block_type | TEXT | NOT NULL | CHECK (block_type IN ('content_feed','banner_image','banner_html','adsense','featured')) |
| display_name | TEXT | NULLABLE | セクションタイトル（「注目のガレージ」等） |
| config | JSONB | NOT NULL, DEFAULT '{}' | block_typeごとに構造が異なる（下記参照） |
| sort_order | INTEGER | NOT NULL, DEFAULT 0 | ページ内の並び順 |
| is_active | BOOLEAN | NOT NULL, DEFAULT true | ON/OFF |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |

**block_type別のconfig構造:**

#### `content_feed` — RIG/パーツ/LOG一覧
```json
{
  "entity_type": "rig",
  "card_style": "hero",
  "filter": {
    "category_id": "uuid-here",
    "subcategory_id": "uuid-here",
    "manufacturer_id": "uuid-here",
    "rig_type": "rc"
  },
  "sort_logic": "popular_week",
  "manual_ids": ["uuid-1", "uuid-2"],
  "count": 6
}
```

**card_style値:** `'hero'` / `'grid_standard'` / `'grid_compact'` / `'list_standard'` / `'list_compact'`

**sort_logic値:** `'newest'` / `'popular_week'` / `'popular_month'` / `'most_liked'` / `'most_commented'` / `'manual'`

**entity_type値:** `'rig'` / `'part'` / `'log'` / `'mixed'`

#### `banner_image` — 画像バナー+リンク
```json
{
  "image_url": "https://...",
  "link_url": "/subcategory/rock-crawler",
  "alt_text": "春のクローラー特集 2026"
}
```

#### `banner_html` — HTMLフリー入力
```json
{
  "html": "<div>...</div>"
}
```

#### `adsense` — Google AdSense枠
```json
{
  "ad_slot": "1234567890",
  "ad_format": "horizontal"
}
```

#### `featured` — 特集セクション
```json
{
  "title": "春のクローラー特集 2026",
  "description": "...",
  "image_url": "https://...",
  "link_url": "/feature/spring-crawler-2026",
  "badge_text": "SPECIAL",
  "period_start": "2026-03-30",
  "period_end": "2026-04-30"
}
```

**拡張性:** 新しいblock_typeを追加する場合、CHECK制約に値を追加しconfig構造をアプリ層で定義するだけ。テーブル構造の変更は不要。

---

## Domain 8: 課金（将来用・MVPでは作成しない）

### `user_plans`
```
user_plans
├── id (UUID, PK)
├── user_id (UUID, FK → profiles.id, NOT NULL)
├── plan_code (TEXT, NOT NULL) — free / supporter
├── billing_cycle (TEXT, NOT NULL) — CHECK (billing_cycle IN ('monthly','yearly'))
├── status (TEXT, NOT NULL) — CHECK (status IN ('active','canceled','expired','trialing'))
├── started_at (TIMESTAMPTZ, NOT NULL, DEFAULT now())
├── expires_at (TIMESTAMPTZ, NULLABLE)
├── canceled_at (TIMESTAMPTZ, NULLABLE)
├── created_at (TIMESTAMPTZ, DEFAULT now())
├── updated_at (TIMESTAMPTZ, DEFAULT now())
```

**`plan_limits`は不要。** プランごとの制限値はアプリ定数で管理。DB化はプラン数が増えてから。

---

## Domain 9: 通知（将来用・MVPでは作成しない）

```
notifications
├── id (UUID, PK)
├── user_id (UUID, FK → profiles.id) — 通知を受け取るユーザー
├── actor_id (UUID, FK → profiles.id) — アクションしたユーザー
├── type (TEXT) — CHECK (type IN ('like','favorite','follow','comment','comment_reply'))
├── entity_type (TEXT, NULLABLE)
├── entity_id (UUID, NULLABLE)
├── is_read (BOOLEAN, DEFAULT false)
├── created_at (TIMESTAMPTZ)
```

---

## 統計カウントの方針

- **`view_count`**: `rigs`, `parts`, `maintenance_logs` に直接保持（インクリメント更新）
- **`like_count` / `favorite_count`**: テーブルに持たず、COUNTで取得。パフォーマンス問題発生時にキャッシュカラム追加
- **画像総容量**: `SUM(images.file_size) WHERE user_id = X AND deleted_at IS NULL` で集計。キャッシュカラムは不要

---

## Row Level Security (RLS) 方針

**テーブル別に個別ポリシーを設計。**

### 共通原則
- 全SELECTポリシーに `deleted_at IS NULL` を含める
- 公開データ: `is_public = true AND deleted_at IS NULL`
- 自分のデータ: `user_id = auth.uid() AND deleted_at IS NULL`
- INSERT/UPDATE/DELETE: `user_id = auth.uid()`

### テーブル別の特記事項
- **images**: SELECTは全公開（Cloudflare URLの推測不可能性で担保）。INSERT/DELETEは`user_id = auth.uid()`
- **likes/favorites/pins**: SELECTは全公開。非公開エンティティへのアクションはUI層で制御
- **follows**: follower_id = auth.uid() でINSERT/DELETE。SELECTは全公開
- **マスターデータ**: SELECT全公開。変更は管理者ロールのみ
- **rig_parts**: user_id = auth.uid() でINSERT/UPDATE/DELETE
- **comments**: SELECTはstatus='published'のみ全公開。INSERTはauth.uid()必須。自分のコメントのstatus更新のみ可能
- **comment_reports**: INSERTはauth.uid()必須。SELECTは運営者ロールのみ
- **content_reports**: INSERTはauth.uid()必須。SELECTは運営者ロールのみ。同一ユーザーから同一コンテンツへの重複通報はUNIQUE制約で防止
- **page_blocks**: SELECT全公開（is_active=trueのみ）。変更は管理者ロールのみ

---

## インデックス設計

```sql
-- ユーザーのRIG一覧
CREATE INDEX idx_rigs_user_id ON rigs(user_id) WHERE deleted_at IS NULL;

-- ユーザーのパーツ一覧
CREATE INDEX idx_parts_user_id ON parts(user_id) WHERE deleted_at IS NULL;

-- ユーザーのログ一覧
CREATE INDEX idx_logs_user_id ON maintenance_logs(user_id) WHERE deleted_at IS NULL;

-- RIGのログ一覧
CREATE INDEX idx_logs_rig_id ON maintenance_logs(rig_id) WHERE deleted_at IS NULL;

-- マスター紐付け（UGC→マスター集約用）
CREATE INDEX idx_rigs_master ON rigs(rig_master_id) WHERE rig_master_id IS NOT NULL AND deleted_at IS NULL;
CREATE INDEX idx_parts_master ON parts(parts_master_id) WHERE parts_master_id IS NOT NULL AND deleted_at IS NULL;

-- 画像取得
CREATE INDEX idx_images_entity ON images(entity_type, entity_id) WHERE deleted_at IS NULL;

-- いいね・お気に入り・ピン
CREATE INDEX idx_likes_entity ON likes(entity_type, entity_id);
CREATE INDEX idx_favorites_entity ON favorites(entity_type, entity_id);
CREATE INDEX idx_pins_entity ON pins(entity_type, entity_id);

-- カテゴリ検索
CREATE INDEX idx_rigs_category ON rigs(category_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_rigs_rig_type ON rigs(rig_type) WHERE deleted_at IS NULL;
CREATE INDEX idx_parts_category ON parts(category_id) WHERE deleted_at IS NULL;

-- フォロー
CREATE INDEX idx_follows_follower ON follows(follower_id);
CREATE INDEX idx_follows_following ON follows(following_id);

-- 公開一覧（検索・フィード用）
CREATE INDEX idx_rigs_public ON rigs(is_public, created_at DESC) WHERE deleted_at IS NULL AND is_public = true;
CREATE INDEX idx_parts_public ON parts(is_public, created_at DESC) WHERE deleted_at IS NULL AND is_public = true;
CREATE INDEX idx_logs_public ON maintenance_logs(is_public, created_at DESC) WHERE deleted_at IS NULL AND is_public = true;

-- rig_parts
CREATE UNIQUE INDEX idx_rig_parts_active ON rig_parts(rig_id, part_id) WHERE removed_at IS NULL;
CREATE INDEX idx_rig_parts_rig ON rig_parts(rig_id);
CREATE INDEX idx_rig_parts_part ON rig_parts(part_id);

-- パーツマスター aliases 検索（GIN）
CREATE INDEX idx_parts_masters_aliases ON parts_masters USING GIN(aliases);

-- アフィリエイト
CREATE INDEX idx_affiliate_entity ON affiliate_links(entity_type, entity_id, country_code);

-- コメント取得（エンティティ別・公開のみ）
CREATE INDEX idx_comments_entity ON comments(entity_type, entity_id, status, created_at DESC);

-- 返信取得
CREATE INDEX idx_comments_parent ON comments(parent_id, status, created_at ASC);

-- ユーザーのコメント一覧
CREATE INDEX idx_comments_user ON comments(user_id, created_at DESC);

-- 通報管理
CREATE INDEX idx_comment_reports_comment ON comment_reports(comment_id, status, created_at DESC);

-- 通報者別
CREATE INDEX idx_comment_reports_reporter ON comment_reports(reporter_user_id, created_at DESC);

-- コンテンツ通報
CREATE INDEX idx_content_reports_entity ON content_reports(entity_type, entity_id, status, created_at DESC);
CREATE INDEX idx_content_reports_reporter ON content_reports(reporter_user_id, created_at DESC);

-- page_blocks（ページ別ブロック取得）
CREATE INDEX idx_page_blocks_page ON page_blocks(page_type, page_ref_id, is_active, sort_order) WHERE is_active = true;
```

---

## ER図（テキスト版）

```
profiles ──1:N──→ rigs
profiles ──1:N──→ parts
profiles ──1:N──→ maintenance_logs
profiles ──1:N──→ comments

rigs ──N:1──→ rig_masters      (rig_master_id, NULLABLE)
parts ──N:1──→ parts_masters    (parts_master_id, NULLABLE)

rigs ──N:M──→ parts             (via rig_parts)
rigs ──1:N──→ maintenance_logs
rigs ──1:N──→ images

parts ──1:N──→ images
maintenance_logs ──1:N──→ images

manufacturers ──1:N──→ rigs
manufacturers ──1:N──→ parts
manufacturers ──1:N──→ rig_masters
manufacturers ──1:N──→ parts_masters

categories ──1:N──→ rigs
categories ──1:N──→ parts
categories ──self ref──→ categories (parent_id)

profiles ──N:M──→ profiles      (via follows)

likes/favorites/pins → polymorphic (entity_type + entity_id)
    → 対象: rigs / parts / maintenance_logs
likes → 追加対象: comments

comments → polymorphic (entity_type + entity_id)
    → 対象: rigs / parts / maintenance_logs
comments ──self ref──→ comments (parent_id, 1階層のみ)
comments ──1:N──→ comment_reports

content_reports → polymorphic (entity_type + entity_id)
    → 対象: rigs / parts / maintenance_logs

rig_masters/parts_masters ──1:N──→ affiliate_links

page_blocks → 参照: categories (page_ref_id, NULLABLE)
```

---

## マイグレーション順序

### MVP実行分（19テーブル）
1. `manufacturers`
2. `categories`
3. `profiles`（auth.users依存）
4. `rig_masters`
5. `parts_masters`
6. `rigs`（rig_masters依存）
7. `parts`（parts_masters依存）
8. `rig_parts`
9. `maintenance_logs`
10. `images`
11. `likes`
12. `favorites`
13. `pins`
14. `follows`
15. `affiliate_links`
16. `comments`
17. `comment_reports`（comments依存）
18. `content_reports`
19. `page_blocks`

### 将来実行分（MVPでは作成しない）
20. `user_plans`
21. `notifications`

---

## 変更履歴

### v1.0 → v1.1
1. `rig_parts`に`user_id`追加
2. `rig_parts`に部分ユニーク制約追加
3. `images`テーブルから`profile`を除外
4. 全CHECK制約追加
5. `currency_code`追加
6. RLS方針をテーブル別設計に変更
7. RLSに`deleted_at IS NULL`を共通含有
8. 公開一覧インデックスを`parts`/`maintenance_logs`にも追加

### v1.1 → v1.2
1. **`rigs.rig_master_id`追加** — UGCと公式マスターの紐付け。収益化の核
2. **`parts.parts_master_id`追加** — 同上。パーツマスターへの集約でアフィ導線強化
3. **`watchlists` → `pins`にリネーム** — UI用語（📌ピン留め）と統一
4. **`maintenance_logs.log_type`値変更** — `setup/other` → `custom/memo`
5. **`parts_masters.aliases`追加** — 製品名揺れ・通称・海外名の吸収用（TEXT[]）
6. **`images.deleted_at`追加** — 他テーブルと論理削除設計を統一
7. **`user_plans`テーブル構造確定** — 将来用。MVPでは作成しない
8. **マスター紐付け用インデックス追加** — `idx_rigs_master`, `idx_parts_master`
9. **`parts_masters.aliases`用GINインデックス追加**
10. **マイグレーション順序変更** — `rig_masters`/`parts_masters`を`rigs`/`parts`より先に（FK依存）

### v1.2 → v1.3
1. **`rigs.product_line`追加** — ブランドシリーズ名（例：Mini-Z, Clod Buster）。NULLABLE
2. **`rigs.platform`追加** — シャーシ/プラットフォーム名（例：SCX10 III, BB-01）。NULLABLE
3. **`rig_masters.product_line`追加** — 同上。マスターデータ側
4. **`rig_masters.platform`追加** — 同上。マスターデータ側

### v1.3 → v1.4
1. **`comments`テーブルをMVP実装に昇格** — status追加、将来用から変更
2. **`comment_reports`テーブル新規追加** — コメント専用通報
3. **`profiles`に`comments_enabled_rig_part`/`comments_enabled_log`追加** — コメント受付2系統ON/OFF
4. **`likes`のentity_type CHECKに`'comment'`追加** — コメントへの♥リアクション
5. **コメント関連インデックス5本追加**
6. **マイグレーション順序更新** — MVP 15→17テーブル（comments + comment_reports追加）
7. **notifications.typeに`'comment_reply'`追加予定** — 将来用

### v1.4 → v1.5
1. **`page_blocks`テーブル新規追加** — ウィジェット型CMS。index/category_top/subcategory_topのセクション構成を管理
2. **block_type 5種定義** — content_feed / banner_image / banner_html / adsense / featured
3. **content_feedのsort_logic 6種定義** — newest / popular_week / popular_month / most_liked / most_commented / manual
4. **page_blocks用インデックス追加** — `idx_page_blocks_page`
5. **RLS方針追加** — page_blocksはSELECT全公開（is_active=true）、変更は管理者のみ
6. **マイグレーション順序更新** — MVP 17→18テーブル（page_blocks追加）
7. **Domain番号再編** — Domain 7: ページ管理（新規）、Domain 8: 課金、Domain 9: 通知

### v1.5 → v1.6
1. **`content_reports`テーブル新規追加** — RIG/パーツ/LOG自体の通報。report.htmlのUIに対応
2. **reason_code 6種定義** — inappropriate / spam / copyright / wrong_info / harassment / other
3. **content_reports用インデックス2本追加** — `idx_content_reports_entity`, `idx_content_reports_reporter`
4. **RLS方針追加** — content_reportsはINSERTにauth.uid()必須、SELECTは運営者のみ
5. **マイグレーション順序更新** — MVP 18→19テーブル（content_reports追加）
