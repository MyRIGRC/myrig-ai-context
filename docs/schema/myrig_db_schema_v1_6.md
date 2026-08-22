# MyRIG RC — Database Schema Design v1.6-r2（App所有領域）

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> 列・型・CHECK 値・インデックスは「今こうなっている」仕様。
> **「既存仕様と異なる」ことだけを理由に案を捨てない。**
> 差分を明示すればイタヤ裁定で変更できる。
>
> ただし以下は **L1（恒久ルール・逸脱禁止）**。節ごとに再掲する。
>
> - **物理DELETE禁止**（削除は `deleted_at` の UPDATE）
> - **App / Research の責務分離**（下表。Research 所有領域を本書で定義しない）
> - **RLS 方針**（セキュリティ）
> - **HOLD 項目を確定として扱わないこと**

**最終更新:** 2026-08-22
※ファイル名は `myrig_db_schema_v1_6.md` のまま（CURRENT.md の索引と一致させるため）

## 適用範囲 — L1

本書が正典として拘束するのは **App 所有領域のみ**。

| 区分 | テーブル | 正本 |
|---|---|---|
| **App 所有** | `profiles` / `rigs` / `parts` / `maintenance_logs` / `rig_parts` / `images` / `likes` / `favorites` / `pins` / `follows` / `comments` / `comment_reports` / `content_reports` / `page_blocks` / `affiliate_links` / `notifications` / `user_plans` | **本書** |
| **Research 所有** | `manufacturers` / `rig_masters` / **`part_masters`**（※単数形） / `rig_categories` / `part_categories` / `master_aliases` / `master_relations` / `master_images` / `master_external_links` / `master_publication` / `rig_master_variants` / `part_master_variants` / `bodies` | **`db-schema-answers-v1.md`** |

⚠️ **`part_masters`（Research・単数形）と `parts_masters`（App・複数形）は同名別義。**
**`parts_masters` の所有区分は未確定**のため上のどちらにも入れていない。

- **App↔Research 写像表（cross_ref）が無い状態で App 側スキーマを進めないこと。**
- **本書内の `parts_masters` を機械的に一括置換しないこと**（誤った同定を固定するため）。
  ER図・FK定義・マイグレーション順序も現行表記のまま据え置いている。

## 命名規則

- テーブル名: `snake_case` 複数形（`rigs`, `parts`, `maintenance_logs`）
- カラム名: `snake_case`（`created_at`, `rig_type`）
- 外部キー: `参照先テーブル単数_id`（`user_id`, `rig_id`）
- boolean: `is_` プレフィックス（`is_public`, `is_archived`）
- 日時: `_at` サフィックス（`purchased_at`, `deleted_at`）
- JSONB: 自由入力・可変構造のデータに使用
- UUID: 全テーブルの主キー（Supabase標準）
- **論理削除: `deleted_at` NULLABLE（物理削除しない）— L1**
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
| rig_type | TEXT | NOT NULL, DEFAULT 'rc-car' | CHECK (rig_type IN ('rc-car','mini4wd','drone-fpv','rc-airplane','rc-boat')) |
| manufacturer_id | UUID | FK → manufacturers.id, NULLABLE | メーカー |
| manufacturer_name_cache | TEXT | | 非正規化。表示高速化用 |
| model_name | TEXT | NOT NULL | モデル名 |
| base_model | TEXT | | ベース車種（載せ替え時） |
| category_id | UUID | FK → **`rig_categories`.id**, NULLABLE | サブカテゴリ（単一 `categories` 表ではない） |
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
| product_line | TEXT | NULLABLE | **マスターからの継承のみ**。ユーザー自由入力は不可 |
| platform | TEXT | NULLABLE | **自由テキスト廃止・マスターからの継承のみ。**未紐付けは NULL。照合は `rig_masters.platform_slug` |
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
| rig_type | TEXT | NOT NULL, DEFAULT 'rc-car' | CHECK (同上・5値) |
| compatible_types | TEXT[] | DEFAULT '{rc-car}' | 対応する大大カテゴリ配列（rig_typeと同一値セット） |
| manufacturer_id | UUID | FK → manufacturers.id, NULLABLE | |
| manufacturer_name_cache | TEXT | | |
| product_name | TEXT | NOT NULL | 製品名 |
| category_id | UUID | FK → **`part_categories`.id**, NULLABLE | パーツカテゴリ（⚠️実DBでは `part_categories` は0行＝未構築） |
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

**`log_type` は4値が正。**
`setup` は v1.2 で廃止した値。**将来5値化する裁定が出ても `setup` は再利用せず別 slug を使う。**

---

## Domain 3: カテゴリ・マスターデータ（**本書では定義しない**）— L1

対象: `manufacturers` / `rig_categories` / `part_categories` / `rig_masters` / `parts_masters`
**正本: `db-schema-answers-v1.md`（DB Research 主査裁定）**

本書は列定義を持たない。要点だけ再掲する（詳細は正本を見ること）。

| 項目 | Research 正本での扱い |
|---|---|
| `rig_type` | 5値 `rc-car` / `mini4wd` / `drone-fpv` / `rc-airplane` / `rc-boat`。**旧6値 `('rc',…,'miniz',…)` は使わない**（Mini-Z の受け皿は `size_class='mini-z'`） |
| `size_class` | 絞り込み軸の正本として**存在する**（`scale` は表示用、`spec_data.scale` は抽出生値で比較軸ではない）。⚠️**値の集合はHOLD**（13値を確定として実装しない） |
| `power_source` | **存在する。**UIに出すかは App 判断でよいが、**データ層は必ず持つ** |
| `rig_masters.platform` | `platform_slug`（正規化キー・照合はこれ）/ `platform_name` / `platform_name_ja` に分離。`platforms` テーブルは作らない。**部分文字列照合は禁止**（過去に318件の誤接続） |
| `rigs.platform` | **自由テキスト廃止・マスターからの継承のみ。**未紐付けは NULL。ユーザー自由入力は許さない |
| `categories` | **`rig_categories` / `part_categories` に分離。**RIG 24件 / パーツ親14・子90 の2階層で凍結 |
| `spec_data` | 列名は `specs` ではなく **`spec_data`**。**RIG側は承認11キー**。**パーツ側のキー設計は未定**（⚠️`spec_schema` 列はDBに存在せず `part_categories` は0行） |
| aliases | **`master_aliases` が正本**（`alias_kind` / `locale` 付き）。App 側は JOIN して検索対象に含める。→ 下記 HOLD |

### この領域の HOLD

- **`aliases`**: `master_aliases` が正本であることは確定。既存の `parts_masters.aliases`（＋GIN索引
  `idx_parts_masters_aliases`）を削除するか併存移行するかは未裁定。
  **移行方針が出るまで新規参照を増やさないこと。**

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

⚠️ `images.alt`（画像代替テキスト）の追加要否は未裁定（App_Ready_Design_Rules からの申し送り）。

---

## Domain 5: ソーシャル

✅ **2026-08-22 イタヤ裁定・HOLD解除。** likes/favorites/pins/followsの4テーブルに
`deleted_at`を追加し、解除操作（アンいいね・お気に入り解除・ピン解除・フォロー解除）を
`deleted_at`のUPDATEで行う（CORE「物理DELETEは禁止」を例外なく維持。L1改訂は不要）。
UNIQUE制約は再操作（一度解除して再度いいね等）に対応するため部分ユニークインデックス
（`WHERE deleted_at IS NULL`）へ変更する。

### `likes`
♥いいね。公開カウント・通知あり。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig','part','log','comment')) |
| entity_id | UUID | NOT NULL | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除（アンいいね） |

**UNIQUE（部分インデックス）:** `(user_id, entity_type, entity_id) WHERE deleted_at IS NULL`

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
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除（お気に入り解除） |

**UNIQUE（部分インデックス）:** `(user_id, entity_type, entity_id) WHERE deleted_at IS NULL`

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
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除（ピン解除） |

**UNIQUE（部分インデックス）:** `(user_id, entity_type, entity_id) WHERE deleted_at IS NULL`

---

### `follows`
ユーザーフォロー。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| follower_id | UUID | FK → profiles.id, NOT NULL | フォローする側 |
| following_id | UUID | FK → profiles.id, NOT NULL | フォローされる側 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除（フォロー解除） |

**UNIQUE（部分インデックス）:** `(follower_id, following_id) WHERE deleted_at IS NULL`
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
- 運営者：非表示解除・論理削除（status→deleted）・制裁対応（✅ 2026-08-22 GPT監査で訂正。
  「完全削除」は物理DELETEと読めるためCORE違反。運営者権限も論理削除に統一する）

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
| page_type | TEXT | NOT NULL | CHECK (page_type IN ('index','category_top','subcategory_top','parts_browse_top','parts_subcategory')) |
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
    "rig_type": "rc-car"
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
  "link_url": "/category/rock-crawler/comp",
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
- **`like_count` / `favorite_count`**: テーブルに持たず、`COUNT(*) WHERE deleted_at IS NULL`で取得
  （✅ 2026-08-22 GPT監査で明記。対象entityが非公開の場合はcount自体を返さない。RLS節参照）。
  パフォーマンス問題発生時にキャッシュカラム追加
- **画像総容量**: `SUM(images.file_size) WHERE user_id = X AND deleted_at IS NULL` で集計。キャッシュカラムは不要

---

## Row Level Security (RLS) 方針 — L1

**テーブル別に個別ポリシーを設計。**

### 共通原則
- ✅ **2026-08-22 イタヤ裁定・HOLD解除**: `likes` / `favorites` / `pins` / `follows` にも
  `deleted_at` を追加した（GPT監査B解消）。**ただし `rig_parts` は例外で、この4テーブルとは別に
  `removed_at` で同じ役割（現在有効かどうか）を表す。「全テーブルがdeleted_atを持つ」わけではない。**
- **`deleted_at`（または`rig_parts`の`removed_at`）を持つ全テーブルの**全SELECTポリシーに
  対応する列の `IS NULL` 条件を含める
- 公開データ: `is_public = true AND deleted_at IS NULL`
- 自分のデータ: `user_id = auth.uid() AND deleted_at IS NULL`
- INSERT/UPDATE: `user_id = auth.uid()`
- ✅ **2026-08-22 GPT監査で修正**: **DELETEポリシーは作らない。** どのテーブルにも
  `user_id = auth.uid()`によるDELETEポリシーを設けない（CORE.md「物理DELETEは禁止」に例外なし）。
  削除・解除操作（RIG/パーツ/ログの削除、rig_partsの取り外し、いいね/お気に入り/ピン/フォローの解除）は
  すべて対応する論理削除列（`deleted_at`または`removed_at`）のUPDATEで行う。
  運用・移行時にどうしても物理DELETEが要る場合は、RLSポリシーではなくservice role（RLSをバイパスする
  管理者経路）で行う。ユーザー向けポリシーにDELETEを残さない

### テーブル別の特記事項

✅ **2026-08-22 イタヤ裁定・HOLD解除。** 旧「SELECT全公開」方針（pinsの「非公開」定義と矛盾、
親が非公開でも images/comments が読めた問題）を、親の`is_public`をJOIN判定する方式へ変更する。
詳細検討は `_decisions/2026-08-22_rls-security-model-v1.md`（案A採用）。**実装はNext.js着手時に行う
（モックアップ段階では対象データが存在しないため実害なし。着手前に必ずこの通りに実装すること）。**

- **images**: SELECTは「自分の行」または「親（rig/part/log）が`is_public=true AND deleted_at IS NULL`」の場合のみ。
  entity_typeごとに参照先テーブル（rigs/parts/maintenance_logs）をCASE分岐でEXISTS判定する。
  INSERTは`user_id = auth.uid()`。削除は`deleted_at`のUPDATE（物理DELETEなし）
- **favorites**: 個別行のSELECTは`user_id = auth.uid() AND deleted_at IS NULL`のみ（他人の個別行は不可）。
  **公開カウント（「◯件お気に入り」表示）は維持する**が、個別行そのものは公開しない。
  件数は`WHERE deleted_at IS NULL`かつ**対象entityが`is_public=true AND deleted_at IS NULL`の場合のみ**
  返すCOUNT用の関数またはビュー経由で提供する（非公開entityのcountは返さない。RLSを迂回した個別行閲覧もさせない）。
  解除は`deleted_at`のUPDATEで行う（物理DELETEなし）
- **pins**: **完全非公開。** SELECTは`user_id = auth.uid() AND deleted_at IS NULL`のみ。
  他人には件数含め一切公開しない。解除は`deleted_at`のUPDATE
- ✅ **2026-08-22 GPT監査で修正**: **likes**: SELECTは「自分の行」または「参照先が公開」の場合のみ
  （旧: 全公開。favorites/pins/images/commentsと同じ基準に揃える）。
  entity_typeがrig/part/logなら親が`is_public=true AND deleted_at IS NULL`、
  entity_typeがcommentなら対象コメントが`status='published'`かつ**そのコメントの親（rig/part/log）も公開**の場合。
  いずれも`deleted_at IS NULL`を条件に含める。解除（アンいいね）は`deleted_at`のUPDATE
- **follows**: SELECTは全公開（`deleted_at IS NULL`）。INSERTは`follower_id = auth.uid()`。
  解除（UPDATE `deleted_at`）は`follower_id = auth.uid()`の行のみ許可（他人のフォロー関係は解除不可）
- **マスターデータ**: SELECT全公開。変更は管理者ロールのみ
- **rig_parts**: `user_id = auth.uid()`でINSERT/UPDATE。取り外しは`removed_at`のUPDATEで行う（物理DELETEなし）
- **comments**: SELECTは`status='published'`かつ親（rig/part/log）が`is_public=true AND deleted_at IS NULL`の場合のみ。
  INSERTはauth.uid()必須。自分のコメントのstatus更新のみ可能
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

-- いいね・お気に入り・ピン（一覧取得用。deleted_at除外）
CREATE INDEX idx_likes_entity ON likes(entity_type, entity_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_favorites_entity ON favorites(entity_type, entity_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_pins_entity ON pins(entity_type, entity_id) WHERE deleted_at IS NULL;

-- ✅ 2026-08-22 GPT監査で追加: 再操作（解除→再いいね等）を許すための部分UNIQUE INDEX
-- （本文の「UNIQUE（部分インデックス）」表記に対応する実DDL。従来は本文記載のみでDDLが無かった）
CREATE UNIQUE INDEX idx_likes_active_unique ON likes(user_id, entity_type, entity_id) WHERE deleted_at IS NULL;
CREATE UNIQUE INDEX idx_favorites_active_unique ON favorites(user_id, entity_type, entity_id) WHERE deleted_at IS NULL;
CREATE UNIQUE INDEX idx_pins_active_unique ON pins(user_id, entity_type, entity_id) WHERE deleted_at IS NULL;

-- カテゴリ検索
CREATE INDEX idx_rigs_category ON rigs(category_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_rigs_rig_type ON rigs(rig_type) WHERE deleted_at IS NULL;
CREATE INDEX idx_parts_category ON parts(category_id) WHERE deleted_at IS NULL;

-- フォロー
CREATE INDEX idx_follows_follower ON follows(follower_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_follows_following ON follows(following_id) WHERE deleted_at IS NULL;
-- ✅ 2026-08-22 GPT監査で追加: 再フォローを許すための部分UNIQUE INDEX
CREATE UNIQUE INDEX idx_follows_active_unique ON follows(follower_id, following_id) WHERE deleted_at IS NULL;

-- 公開一覧（検索・フィード用）
CREATE INDEX idx_rigs_public ON rigs(is_public, created_at DESC) WHERE deleted_at IS NULL AND is_public = true;
CREATE INDEX idx_parts_public ON parts(is_public, created_at DESC) WHERE deleted_at IS NULL AND is_public = true;
CREATE INDEX idx_logs_public ON maintenance_logs(is_public, created_at DESC) WHERE deleted_at IS NULL AND is_public = true;

-- rig_parts
CREATE UNIQUE INDEX idx_rig_parts_active ON rig_parts(rig_id, part_id) WHERE removed_at IS NULL;
CREATE INDEX idx_rig_parts_rig ON rig_parts(rig_id);
CREATE INDEX idx_rig_parts_part ON rig_parts(part_id);

-- パーツマスター aliases 検索（GIN）
-- ⚠️ HOLD: 対象の parts_masters は所有区分未確定。aliases の正本は master_aliases。
--    この索引の存続可否は未裁定。新規に本索引へ依存する実装を増やさないこと。
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

rig_categories ──1:N──→ rigs      （※Research正本。単一categories表ではない）
part_categories ──1:N──→ parts    （※同上・実DBは0行＝未構築）

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

page_blocks → 参照: rig_categories / part_categories (page_ref_id, NULLABLE)
```

---

## マイグレーション順序

> **1・2・4 は Research 所有テーブル。**FK 依存の都合で順序表には残すが、
> **DDL の内容は `db-schema-answers-v1.md` が正本。**
>
> ⚠️ **5番 `parts_masters` は所有区分が未確定**（Research の `part_masters` と同一かが決まっていない）。
> **App↔Research 写像表（cross_ref）が無い状態でマイグレーションを流さないこと。**

### MVP実行分（20テーブル）
1. `manufacturers` ※Research所有
2. `rig_categories` / `part_categories` ※Research所有
3. `profiles`（auth.users依存）
4. `rig_masters` ※Research所有
5. `parts_masters`（本書の表記。**実体は cross_ref 待ち** — 上記注意）
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

## 未確定（要裁定）

- `size_class` / `power_source` / `platform_slug` の **App側への実列追加DDL**が未設計
  （`size_class` は値集合が HOLD 中）
- **App↔Research 写像表（cross_ref）が未作成。**本文中の `parts_masters` が
  App側 / Research側どちらを指すか曖昧な箇所が残る（機械的な一括置換をしないこと）
- `images.alt`（画像代替テキスト）の追加要否
- ~~関係テーブル（likes / favorites / pins / follows）の解除手段と物理DELETE禁止の両立~~
  ✅ 2026-08-22 イタヤ裁定・解消済み（4テーブルへdeleted_at追加。上記ソーシャル節参照）
- ~~RLS のセキュリティモデル~~ ✅ 2026-08-22 イタヤ裁定・解消済み（上記 RLS 節参照）

---

## 版の履歴（要点のみ）

詳細な差分は git 履歴と `_backup/audit_20260821/` を参照。

| 版 | 要点 |
|---|---|
| v1.1 | `rig_parts` に user_id と部分ユニーク制約 / CHECK 制約整備 / RLS をテーブル別設計へ |
| v1.2 | `rigs.rig_master_id` `parts.parts_master_id` 追加 / `watchlists`→`pins` / **`log_type` の `setup`・`other` を廃止し `custom`・`memo` へ** / `parts_masters.aliases` と GIN 索引 |
| v1.3 | `rigs` / `rig_masters` に `product_line` `platform` 追加 |
| v1.4 | `comments` を MVP へ昇格 / `comment_reports` 追加 / コメント受付ON/OFF 2系統 |
| v1.5 | `page_blocks`（ウィジェット型CMS）追加。block_type 5種・sort_logic 6種 |
| v1.6 | `content_reports` 追加。reason_code 6種 |
| v1.6-r2 | **Research 所有領域の列定義を削除し参照へ降格** / App側 `rig_type` を5値へ / `category_id` の FK 参照先を `rig_categories`・`part_categories` に明記 / `rigs.platform` `product_line` をマスター継承のみに / RLS の `deleted_at` 条件をテーブル限定へ / `page_blocks.page_type` に parts 系2種を追加 / `log_type` 4値で決着 |
