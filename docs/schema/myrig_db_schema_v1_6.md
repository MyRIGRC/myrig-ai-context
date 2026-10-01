# MyRIG RC — Database Schema Design v1.6-r11（App所有領域）

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

**最終更新:** 2026-09-29（v1.6-r11 / 正典 122・情報・法務・サポート D4 / D5。`support_inquiries`・`user_reports` 新設・通報の書き込み経路 1 本・UNIQUE を開いている通報だけに）
（v1.6-r10 / 正典 120 追補。ASTRA 監査 M1〜M7・D13 ガレージ非公開 = 持ち主の門・本人だけの値を `profile_private` へ）
（v1.6-r9 / 同日。興味カテゴリ = `profiles.preferred_rig_category_slugs TEXT[]`（最大 5・順序）・`preferred_subcategory` 非推奨・ブロック / ミュート一覧の段階取得）
（v1.6-r8 / 2026-09-28: Domain 9 `notifications` を MVP 実行分へ移動・`is_read` → `read_at`・生成条件）
※ファイル名は `myrig_db_schema_v1_6.md` のまま（CURRENT.md の索引と一致させるため）

## 適用範囲 — L1

本書が正典として拘束するのは **App 所有領域のみ**。

| 区分 | テーブル | 正本 |
|---|---|---|
| **App 所有** | `profiles` / `profile_private` / `rigs` / `parts` / `maintenance_logs` / `rig_parts` / `maintenance_log_parts` / `entity_links` / `images` / `likes` / `favorites` / `pins` / `follows` / `comments` / `comment_reports` / `content_reports` / `user_reports` / `support_inquiries` / `page_blocks` / `affiliate_links` / `notifications` / `notification_settings` / `announcements` / `announcement_reads` / `announcement_mute_periods` / `user_blocks` / `user_mutes` / `username_reservations` / `user_plans`（🔴 v1.6-r11: App が正本を持つ全テーブル。**MVP / 将来実行を問わない責務境界の一覧**で、`user_plans` のように MVP で作らないものも含む。「MVPマイグレーション順」は migration の順序で、FK 依存のため Research 所有表も含む。**両者は別目的・どちらも他方の正ではない**） | **本書** |
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
| username | TEXT | UNIQUE, NOT NULL | @表示名。URL slug。🔴 v1.6-r10: **一意は正規化（小文字）で判定**（`UNIQUE (lower(username))`）。**本人は変えられない**（サーバーだけが書く） |
| display_name | TEXT | CHECK (char_length(display_name) <= 50) | 表示用ニックネーム（🔴 v1.6-r10: 長さを DB で縛る） |
| bio | TEXT | CHECK (char_length(bio) <= 300) | 自己紹介文（🔴 v1.6-r10: 300 字） |
| avatar_url | TEXT | | Cloudflare Images URL |
| cover_image_url | TEXT | | ガレージカバー画像URL |
| country_code | TEXT | | ISO 3166-1 alpha-2 |
| preferred_rig_type | TEXT | | 大大カテゴリ優先表示 |
| preferred_subcategory | TEXT | | ⚠️ **v1.6-r9 非推奨（D10）**: 新規に書かない・読まない。後継 = `profile_private.rig_category_slugs`。⛔ 型を変えない・DROP しない |
| website_url | TEXT | CHECK (website_url ~* '^https?://') | 個人サイトURL（🔴 v1.6-r10: `javascript:` 等を DB で拒否。social_links の各 url も同じ検証をサーバーで） |
| social_links | JSONB | DEFAULT '[]' | [{platform, url, label}] |
| is_public | BOOLEAN | DEFAULT true | **ガレージの公開**（🔴 v1.6-r10・D13: **持ち主の門**。false なら本人以外に RIG・パーツ・LOG と、その画像・関係・件数を一切出さない。各 entity の `is_public` はこの下での公開可否。詳細は RLS「持ち主の門」） |
| comments_enabled_rig_part | BOOLEAN | DEFAULT true | RIG＋パーツへのコメント受付ON/OFF |
| comments_enabled_log | BOOLEAN | DEFAULT true | LOGへのコメント受付ON/OFF |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除（= 退会の手続き。D8） |
| purge_started_at | TIMESTAMPTZ | NULLABLE | 🔴 v1.6-r10（M1）: 退会の確定処理を始めた時刻。**入った時点で再開不可** |
| purge_completed_at | TIMESTAMPTZ | NULLABLE | 🔴 v1.6-r10（M1）: 確定処理がすべて終わった時刻（途中で止まったら、これが NULL のものをやり直す） |

**プロフィール画像はこのテーブルで完結。`images`テーブルには含めない。**

### `profile_private`（🔴 v1.6-r10・MVP・D10 / ASTRA M3）
**本人だけが読む値**の 1:1 テーブル。RLS は行単位で、列ごとに公開範囲を分けられない → 公開される `profiles` の行に本人専用の値を置かない。**行が無い = 全部既定値**（登録時に作らない）。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| user_id | UUID | PK, FK → profiles.id | |
| rig_category_slugs | TEXT[] | NOT NULL, DEFAULT '{}', CHECK (cardinality(rig_category_slugs) <= 5) | **マイカテゴリ**（D10）。値 = `rig_categories.slug`。**配列の順 = Home のタブの並び** |
| region_code | TEXT | NULLABLE | 地域（都道府県など）。値の一覧は Geo Master（PENDING） |
| region_public | BOOLEAN | NOT NULL, DEFAULT false | 地域を公開ガレージに出すか |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |

- RLS: SELECT は `user_id = auth.uid()` だけ。**INSERT / UPDATE のポリシーは作らない — 書き込みはサーバー側の処理だけ**（slug・地域コードの実在確認を迂回させない）
- マイカテゴリの書き込み = **サーバー側の原子的な add / remove**
  - add: 5 件未満 かつ 同じ slug が入っていない かつ `rig_categories` に有効な slug として実在するときだけ末尾に足す（行が無ければ作る）
  - remove: その slug だけを除く
  - ⛔ クライアントで配列を組んで丸ごと保存しない（別タブの古い配列で、ほかで選んだものが黙って消える）。方式（RPC / サーバー SQL）は本番実装時（PENDING）
- 読むとき: `rig_categories` に無くなった slug は飛ばす（行は書き換えない）。**add のときは、無効になった slug を同じ処理の中で除いてから数える**（見えない枠で 5 件が埋まらないように）
- 地域の公開: 公開ガレージの地域表示は、サーバー側の読み取り経路が `region_public = true` のときだけ返す
- 退会の確定処理（D8）: `rig_category_slugs = '{}'`・`region_code = NULL`
- 別テーブル（ユーザー × カテゴリ）へ移す条件: カテゴリごとの重み・設定日時の利用・履歴・カテゴリごとの別属性が要るようになったとき
- `profiles.preferred_rig_type` も本人専用の好みなので、使い始めるときはここへ移す（MVP は rc-car 固定で使わない）

---

## Domain 2: RIG・パーツ・ログ

### `rigs`
ユーザーが登録するRIG（車体）。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | オーナー |
| rig_master_id | UUID | FK → **`rig_masters.rig_master_id`**, NULLABLE | 公式マスターとの紐付け。⚠️ 参照先の PK 名は `.id` ではない（v1.6-r4 で是正）。**server-derived**（client が候補から選んだ master identity をサーバーが解決して入れる） |
| rig_master_variant_id | UUID | FK → **`rig_master_variants.variant_id`**, NULLABLE | 選んだ SKU / バリエーション。**Master だけ選んだ場合と Custom RIG は NULL**。⛔ `base_model TEXT` は載せ替え用の別概念であり代用しない。Public RIG Detail の「バリエーション」はここから取る |
| rig_type | TEXT | NOT NULL, DEFAULT 'rc-car' | CHECK (rig_type IN ('rc-car','mini4wd','drone-fpv','rc-airplane','rc-boat')) |
| manufacturer_id | UUID | FK → **`manufacturers.manufacturer_id`**, NULLABLE | メーカー。⚠️ 参照先の PK 名は `.id` ではない（v1.6-r4 で是正）。**server-derived**（Master 紐付きなら master から解決。⛔ client payload で権威値として送らない） |
| manufacturer_name_cache | TEXT | | 非正規化。表示高速化用。**server-derived**（再同期で上書き） |
| model_name | TEXT | NOT NULL | モデル名 |
| base_model | TEXT | | ベース車種（載せ替え時） |
| rig_category_slug | TEXT | FK → **`rig_categories.slug`**, NULLABLE | RIG カテゴリ。⚠️ **v1.6-r4 で `category_id UUID` を廃止**。Research 物理の PK は `slug` であり UUID ではない。⛔ slug を偽 UUID へ変換しない。親カテゴリは `rig_categories.parent_slug` から導出し、ここへ重複保存しない。Master 紐付きは **server-derived**、Custom RIG のみ App 側入力 |
| nickname | TEXT | | ユーザーがつけた愛称 |
| description | TEXT | | **公開**紹介文 |
| tagline | TEXT | NULLABLE | **公開**キャッチコピー（RIG Detail の H1 下 1 行）。⛔ build_details へ入れない |
| user_build_tags | TEXT[] | DEFAULT '{}' | **ユーザーが自分の RIG に付ける公開タグ**（UI 表記は「ビルドタグ」のまま）。⚠️ **v1.6-r4 で `build_tags` から改名**。Research `rig_masters.build_tags`（Master 固定の分類・ユーザー編集不可）と**同名別義だった**ため。⛔ Master の `build_tags` をここへ自動コピーしない（別 provenance）。⛔ build_details へ入れない |
| private_note | TEXT | NULLABLE | **Owner-only** メモ（garage QUICK NOTE）。⛔ 公開面へ出さない |
| ownership_status | TEXT | NOT NULL, DEFAULT 'current' | 所有の軸。CHECK (ownership_status IN ('current','past','wishlist')) |
| usage_status | TEXT | NULLABLE | 利用状況の軸。CHECK (usage_status IS NULL OR usage_status IN ('building','active','stored'))。**`ownership_status='current'` のときだけ意味を持つ**。NULL＝未設定。⛔「整備中」は一時イベントなので状態にしない（LOG で扱う）。⛔「アーカイブ」もここへ混ぜない |
| is_public | BOOLEAN | DEFAULT true | 公開設定 |
| purchased_period | TEXT | NULLABLE | **入手時期。日付精度を落とさず、偽の日を作らない**。`YYYY` / `YYYY-MM` / `YYYY-MM-DD` のいずれか。CHECK (purchased_period ~ '^[0-9]{4}(-[0-9]{2}(-[0-9]{2})?)?$')。辞書順＝時系列順 |
| purchased_at | DATE | NULLABLE | ⚠️ **非推奨（v1.6-r3）**。年月しか分からない入力に架空の 1 日を補完してしまうため、正は `purchased_period`。**DROP しない**（既存値の意味推定変換も行わない） |
| purchase_price | INTEGER | NULLABLE | **Owner-only**。最小通貨単位（円/セント）。RIG では「ベース車両／キットの入手価格」であり総製作費ではない |
| currency_code | CHAR(3) | DEFAULT 'JPY' | ISO 4217 通貨コード |
| purchase_store | TEXT | NULLABLE | **Owner-only**。入手先（店名 / 通販サイト名） |
| build_details | JSONB | DEFAULT '{}' | **設定値・加工・自由項目だけ**を持つ。⛔ 装着している製品個体は入れない（唯一の SoT は `rig_parts`）。⛔ tagline / user_build_tags / private_note もここへ入れない |
| external_links | JSONB | DEFAULT '[]' | ⚠️ **`entity_links` テーブルへの移行対象（v1.6-r3）**。恒久二重管理は禁止。移行完了までの暫定 |
| product_line | TEXT | NULLABLE | **マスターからの継承のみ・server-derived**。ユーザー自由入力は不可。⛔ Register の client payload に載せない |
| platform | TEXT | NULLABLE | **自由テキスト廃止・マスターからの継承のみ・server-derived。**未紐付けは NULL。照合は `rig_masters.platform_slug`。⛔ Register の client payload に載せない |
| sort_order | INTEGER | DEFAULT 0 | ガレージ内表示順 |
| view_count | INTEGER | DEFAULT 0 | 閲覧数 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | |

**`build_details` JSONB構造（v1.6-r3 で責務を限定）:**

`build_details` と `rig_parts` の責務を分ける。判定基準は「**登録済み PARTS として存在する製品個体か否か**」。

| 入るもの | 例 | 保存先 |
|---|---|---|
| 製品個体と装着履歴 | Servo = Reefs 422HD / ESC = Hobbywing Fusion | **`rig_parts`**（唯一の SoT） |
| 設定値・加工・自由項目 | Drag Brake 80% / Shock Oil 35wt / Body Paint PS-5 | **`build_details`** |

```json
{
  "settings": [
    {"label": "Drag Brake", "value": "80%", "note": ""},
    {"label": "Shock Oil", "value": "35wt / 30wt", "note": "前後で変えている"}
  ],
  "finish": [
    {"label": "Body Paint", "value": "タミヤ PS-5", "note": ""}
  ],
  "raw_custom_fields": [
    {"label": "リンク長", "value": "フロント 92mm"}
  ]
}
```

⛔ **旧 `mechanics` / `suspension` / `exterior` / `electronics` / `battery` キーは廃止**（装着製品を
`rig_parts` と二重に持っていた）。既存データの意味推定変換は行わない。
⛔ Register で手入力された製品名（Master 未紐付け）を `build_details` へ逃がさない。

---

### `parts`
ユーザーが登録するパーツ。1パーツ＝複数RIGへ装着できる（中間テーブル `rig_parts` で管理）。
🔴 **v1.6-r7（2026-09-19 / 正典 115）で方針変更**: **同時に複数 RIG へ装着してよい。**
`idx_rig_parts_active_part`（1 PARTS = 同時 1 RIG）は **撤去した**。
裁定原本 `_decisions/2026-09-19_multi-rig-relation-v1.md`。
根拠は「MyRIG は厳密な物品在庫管理ではなく、どの RIG でどの PARTS を使っているかを記録できればよい」。
⚠️ r6 は逆に「順次（同時は 1 台）」へ是正していた。**r7 はそれを取り消している。**
⛔ 数量列は作らない。⛔ 既存データの意味推定変換は行わない。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | オーナー |
| parts_master_id | UUID | FK → 同期 Master の **`part_id`**, NULLABLE | ⛔ **接続は HOLD H-1（cross_ref 未作成）。現時点で値を入れない。** Research 側の唯一の source ID は `part_masters.part_id`（`.id` ではない） |
| rig_type | TEXT | NOT NULL, DEFAULT 'rc-car' | CHECK (同上・5値) |
| compatible_types | TEXT[] | DEFAULT '{rc-car}' | 対応する大大カテゴリ配列（rig_type と同一値セット）。⚠️ **これは App 所有列であり Research 継承値ではない**。Research `part_masters` に同名列は**存在しない**（CONTRACT-EXPORT-20260917）。⛔ Research 値として同期しない。所有の裁定（Research 新設 or App 所有）が出るまで、MVP では `rig_type` rc-car 固定と整合する既定値のまま触らない |
| manufacturer_id | UUID | FK → **`manufacturers.manufacturer_id`**, NULLABLE | **server-derived**（Master 紐付き時）。Custom PARTS は NULL |
| manufacturer_name_cache | TEXT | | **server-derived**（再同期で上書き） |
| product_name | TEXT | NOT NULL | 製品名（Master 由来 or ユーザー入力）。**Public Detail の主タイトルはこれ** |
| nickname | TEXT | NULLABLE | **Owner-only 管理名**（UI 表記「管理名」）。同一 Master を参照する複数 PARTS を区別するため（例: フロント用 / TF2用 / スペア）。⛔ Public の製品名代わりにしない。⚠️ **`rigs.nickname`（公開の愛称）とは可視性が逆**。共有カード / Detail 部品は `entity_type` で分岐する（v1.6-r6 追記） |
| part_category_slug | TEXT | FK → **`part_categories.slug`**, NULLABLE | 親カテゴリ。⚠️ **v1.6-r4 で `category_id UUID` を廃止**（Research 物理の PK は `slug`）。Master 紐付きは **server-derived**、Custom PARTS のみ App 側入力。⚠️ 実 DB では `part_categories` が 0 行＝未構築 |
| part_subcategory_slug | TEXT | FK → **`part_categories.slug`**, NULLABLE | 子カテゴリ。同上 |
| description | TEXT | | **公開**パーツ紹介文（用途・加工内容もここで表現する）。※ v1.6-r2 まで Notes が「ユーザーメモ」だったが、Owner-only メモは `private_note` が正 |
| private_note | TEXT | NULLABLE | **Owner-only** メモ（garage QUICK NOTE）。⛔ 公開面へ出さない |
| part_number | TEXT | | メーカー型番（UI 表記「型番」）。**write authority が経路で分かれる**: Master / Variant 紐付き時は Research `primary_sku`（variant があれば `part_master_variants.primary_sku` 優先）が権威で **server-derived・ユーザー上書き不可**。**Custom PARTS のときだけ**ユーザー入力を許可する |
| purchased_period | TEXT | NULLABLE | **入手時期**。`rigs.purchased_period` と同一契約（`YYYY` / `YYYY-MM` / `YYYY-MM-DD`、同 CHECK） |
| purchased_at | DATE | NULLABLE | ⚠️ **非推奨（v1.6-r3）**。正は `purchased_period`。DROP しない |
| purchase_price | INTEGER | NULLABLE | **Owner-only**。最小通貨単位 |
| currency_code | CHAR(3) | DEFAULT 'JPY' | ISO 4217 |
| purchase_store | TEXT | NULLABLE | **Owner-only**。入手先 |
| ownership_state | TEXT | NOT NULL, DEFAULT 'owned' | **所有の軸**。CHECK (ownership_state IN ('owned','released'))。UI は「所有中 / 手放した」。⛔ 売却 / 譲渡 / 処分の理由分類は MVP では持たない。⛔ 装着の軸（`rig_parts`）とは別 |
| condition | TEXT | NULLABLE | ⚠️ **MVP では使用しない（v1.6-r3）**。`new/used/modded` が「入手時の状態 / 現在の状態 / 加工の有無」という異なる軸を 1 列に混ぜていたため意味論を廃止した。**DEFAULT 'new' を撤廃**し、新規作成時に自動設定しない。列は DROP せず、既存値の意味推定変換も行わない。加工内容は `description` / `images.caption` / LOG で表現する |
| is_public | BOOLEAN | DEFAULT true | |
| view_count | INTEGER | DEFAULT 0 | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | |

---

### `rig_parts`
パーツとRIGの多対多リレーション。**「実際に装着した／していた事実」だけの Single Source of Truth。**

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | 所有者（RLS用） |
| rig_id | UUID | FK → rigs.id, NOT NULL | |
| part_id | UUID | FK → parts.id, NOT NULL | |
| status | TEXT | NOT NULL, DEFAULT 'active' | **関係の事実状態**。CHECK (status IN ('active','removed')) |
| installed_at | DATE | NULLABLE | 装着日。**不明なら NULL**（偽の日付を入れない） |
| removed_at | DATE | NULLABLE | 取り外し日。**不明なら NULL** |
| note | TEXT | | メモ |
| created_at | TIMESTAMPTZ | DEFAULT now() | |

**v1.6-r3 の変更点 — 事実状態と日付を分離した。**
旧版は `removed_at IS NULL` を「現在装着中」の意味に使っていたため、
**「昔付けていたが外した日付は不明」** を表現できず、偽の日付を入れるしかなかった。
`status` を独立させ、日付は分かるときだけ入れる。

| 意味 | status | installed_at | removed_at |
|---|---|---|---|
| 現在装着中 | `active` | 分かれば | NULL |
| 取り外し済み・日付も分かる | `removed` | 分かれば | 日付 |
| **取り外し済み・日付は不明** | `removed` | 分かれば | **NULL** |

再装着しても過去 relation を上書きしない。**新しい行を足して履歴として残す。**

**制約:**
```sql
-- 同じ RIG に同じ PARTS が二重に active で付かない
CREATE UNIQUE INDEX idx_rig_parts_active_pair
ON rig_parts(rig_id, part_id)
WHERE status = 'active';

-- ⛔ v1.6-r7（2026-09-19 / 正典 115）で **撤去**。
--    1 つの PARTS が同時に複数 RIG へ active で装着されてよくなったため。
--    ⛔ 復活させない。復活させると「移す確認」も同時に戻さないと INSERT が落ちる。
-- CREATE UNIQUE INDEX idx_rig_parts_active_part
-- ON rig_parts(part_id)
-- WHERE status = 'active';
```

🔴 **v1.6-r7 時点で `rig_parts` の一意制約は `idx_rig_parts_active_pair` **だけ**。**
同じ RIG に同じ PARTS を二重に `active` で付けることだけを禁じる。
App もそこだけ守ればよい（picker はすでに関連付け済みの相手を候補から外す）。

⛔ **`planned`（購入予定 / 取り付け予定）は入れない。** 装着の事実がないため。
⛔ **Garage に存在しない RIG 名だけの relation は作らない。** `rig_id` の FK を張れないため。
   必要なら最小 RIG 登録へ誘導するか、`parts.private_note` に書く。
🔴 **v1.6-r7: 送信機・バッテリー等の「複数 RIG で共有して使う機材」も `rig_parts` でよい。**
   RIG ごとに `active` 行を持つだけ。衝突していた `idx_rig_parts_active_part` が無くなったため、
   **HOLD H-2 は解消**（`_decisions/2026-09-19_multi-rig-relation-v1.md` §3-2）。
   ⛔ ただし「共有機材」という分類や専用テーブルは作らない。ただの relation として扱う。

#### 🔴 v1.6-r6: 親 entity の状態変化 → `rig_parts` の伝播規則（2026-09-18 / 正典 114。根拠は r7 で差し替え）

🔴 **v1.6-r7 で根拠だけ差し替えた（規則そのものは不変）。**
旧根拠は `idx_rig_parts_active_part`（撤去済み）だったが、
**新根拠は事実の整合**: 手放した / 削除した RIG に `active` な装着が残り続けると、
「いまこの RIG に付いている」という表示が嘘になる。
これを防ぐ規則。**DDL 変更は無い。App の操作規則として守る。**
⛔ 索引が無くなったことを理由にこの規則を緩めない。

| 操作 | `rig_parts` への作用 |
|---|---|
| `rigs.deleted_at` を立てる | **事前に**その RIG の `active` 行を全て `status='removed'` へ。`removed_at` は **NULL のまま**（日付を捏造しない）。⛔ 行を消さない |
| `rigs.ownership_status` → `past` | **1 回だけ一括確認**（裁定 114 Q4）。「パーツも一緒に手放した」→ 各 `parts.ownership_state='released'` ＋ `rig_parts` を `removed` ／「パーツは手元に残した」→ `rig_parts` を `removed` のみ。**既定は後者** |
| `parts.ownership_state` → `released` | その PARTS の `active` 行を `status='removed'` へ |
| `parts.deleted_at` を立てる | 同上 |

⛔ **`active` が無いことを「予備」と自動判定しない**（Field Contract §3 のまま）。表示は「装着記録なし」まで。
⛔ 過去行を書き換えない。再装着は新しい行。

---

### `maintenance_logs`
整備・走行ログ。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | |
| rig_id | UUID | FK → rigs.id, NULLABLE | 紐づくRIG（任意） |
| log_type | TEXT | **NULLABLE**（DEFAULT なし） | CHECK (log_type IN ('maintenance','run','custom','memo'))。**v1.6-r5 で NOT NULL / DEFAULT を撤廃**。NULL＝分類していない |
| title | TEXT | **NULLABLE** | **v1.6-r5 で NOT NULL を撤廃。** LOG は本文が主役でタイトルは任意 |
| body | TEXT | **NOT NULL, DEFAULT ''** | 本文（Markdown or plain）。**v1.6-r5 で NOT NULL 化。** draft 実体作成のため空文字を許す |
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

**`log_type` は4値 ＋ NULL が正（v1.6-r5）。**
`setup` / `other` は v1.2 で廃止した値。**将来5値化する裁定が出ても `setup` / `other` は再利用せず別 slug を使う。**

### 🔴 v1.6-r5: LOG Composer 契約への追随（2026-09-17 / 正典 111）

裁定原本: **`_decisions/2026-09-17_log-composer-contract-v1.md`**
⛔ **Production DB への migration は実行していない。既存行の意味推定変換もしていない。**

```sql
-- log_type: 必須分類 → 任意分類
ALTER TABLE maintenance_logs ALTER COLUMN log_type DROP NOT NULL;
ALTER TABLE maintenance_logs ALTER COLUMN log_type DROP DEFAULT;
-- CHECK は 4 値のまま。PostgreSQL の CHECK は NULL を UNKNOWN として通すので NULLABLE と共存する
--   CHECK (log_type IN ('maintenance','run','custom','memo'))

-- title: 必須 → 任意
ALTER TABLE maintenance_logs ALTER COLUMN title DROP NOT NULL;

-- body: LOG の主データ。draft 実体作成のため空文字を許し、公開時だけ非空を担保する
ALTER TABLE maintenance_logs ALTER COLUMN body SET DEFAULT '';
UPDATE maintenance_logs SET body = '' WHERE body IS NULL;   -- NOT NULL 化の前処理
ALTER TABLE maintenance_logs ALTER COLUMN body SET NOT NULL;
ALTER TABLE maintenance_logs ADD CONSTRAINT chk_logs_public_body
  CHECK (is_public = false OR char_length(btrim(body)) >= 1);
```

| 項目 | 契約 |
|---|---|
| `log_type` NULL | **「分類していない」**。`memo` は「ユーザーがメモとして分類した」。**別物**。⛔ 5 値目 `other` を復活させない |
| 既存 `maintenance` 行 | **推測で NULL へ変換しない。** DEFAULT で入った値と本人が選んだ値を区別できないため、既存値はそのまま残す |
| `title` NULL | ⛔ 本文冒頭から偽 title を生成しない。⛔ 空文字 title を見出しとして描画しない。見出しごと出さず excerpt を繰り上げる |
| 本文の最小文字数 | **App の UX 値（現行 trim 後 10 文字）。⛔ DB 契約値として固定しない。** DB が守るのは「公開 LOG なのに本文が完全に空」を作らないことだけ（1 文字以上） |
| draft 作成 | **`is_public=false` を明示して INSERT する。** `is_public` の DEFAULT true に draft 生成を依存させない |
| `location` | 維持。`log_type` 非依存の任意情報。現段階は自由テキスト。⛔ 正規化 / place master / GPS / 地図 / サーキット DB を先回りして作らない |
| `duration_minutes` / `surface` / `weather` | **列は維持（DROP しない）。既存データも変更しない。** ただし **LOG Composer v1 から新規入力させない**（`duration_minutes` は滞在/実走/作業/バッテリー単位で意味が曖昧・`surface` は自由テキスト 1 欄では比較軸として弱い・`weather` は本文の状況説明のほうが有用）。「DB にあるから UI へ出す」はしない |
| `logged_at` | `DATE NULLABLE` 維持。`purchased_period` 方式にしない。実施日であり、⛔ `created_at`（投稿日時）を代用にしない。NULL は全件表示から除外しないが、日付期間フィルタでは実施日として扱わない |
| 写真 | 最大 3 枚（出所は `SoT_register-family.js` の `photoMax('log')`）。**Cover なし**（`hasCover('log')=false`）。`sort_order` が表示順 SoT。新規 LOG 画像の `is_primary` は代表指定に使わない。代表が要る面は **`sort_order` 最小**。⛔「1 枚目を Cover」と意味変更しない |
| `images.caption` | **列は禁止しない。** MVP の LOG UI で入力・表示しないだけ |
| 関連パーツ | **`maintenance_log_parts` を作らない**（v1 非搭載）。⛔ tags から `part_id` を推定しない |
| `entity_links` | LOG は v1 非搭載。⛔ 本文 URL を autolink / linkify しない（`moderation_status` を迂回する経路を作らない） |

---

### `maintenance_log_parts`
**🔴 v1.6-r6 新設（2026-09-18 / 正典 114）。** LOG と PARTS の関連。
裁定原本: `_decisions/2026-09-18_relationship-mvp-v1.md`
⛔ **Production DB への migration は実行していない。**

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | RLS 用。**App が `log.user_id = part.user_id = user_id` を保証する** |
| log_id | UUID | FK → maintenance_logs.id, NOT NULL | |
| part_id | UUID | FK → parts.id, NOT NULL | **関係の SoT はこの 2 列** |
| sort_order | INTEGER | DEFAULT 0 | 表示順 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除。**物理 DELETE 禁止** |

```sql
CREATE UNIQUE INDEX idx_log_parts_pair ON maintenance_log_parts(log_id, part_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_log_parts_part ON maintenance_log_parts(part_id) WHERE deleted_at IS NULL;  -- PARTS Detail の逆引き
CREATE INDEX idx_log_parts_log  ON maintenance_log_parts(log_id)  WHERE deleted_at IS NULL;  -- LOG Detail の順引き
```

| 項目 | 契約 |
|---|---|
| SoT | **`log_id ↔ part_id` の関係そのもの**。「この LOG がどの PARTS に関連するか」だけを持つ |
| RIG 非依存 | `maintenance_logs.rig_id` が **NULL でも張れる**。RIG のみ / PARTS のみ / 両方 / どちらも無し の **4 状態すべて許可**（裁定 114 Q1） |
| ⛔ **`rig_parts_id` を持たない** | **裁定 114 Q2。** 「その LOG 当時どの RIG に付いていたか」は MVP で追跡しない。<br>🔴 **将来 `rig_parts_id UUID NULL` を追加する場合、既存行を遡って埋めることはできない。** 自動推定は禁止で、`rig_parts.installed_at` / `removed_at` は NULL 可のため日付からも復元できない。**過去分は恒久的に NULL のままとする。**「後から埋められる」と誤解しないこと |
| 関連数の上限 | **DB 制約にしない。** UI の MVP 値として Composer で暫定 10 件（裁定 114 Q6） |
| 候補 | picker は `parts.ownership_state='owned'` のみを出す（裁定 114 Q5）。⚠️ **既に張られた relation は `released` になっても消えない**（表示は続く。picker に出ないだけ） |
| ⛔ tags | **`maintenance_logs.tags` から `part_id` を機械的に同定しない**（111 のまま） |
| ⛔ 代用禁止 | **「装着 RIG の LOG」を「このパーツの LOG」として見せない**（111 / PARTS Detail v1-open の裁定を維持）。PARTS Detail の「関連するLOG」は**本表の行だけ** |
| 表示契約 | LOG Detail「関連するパーツ」＋ PARTS Detail「このパーツに関連するLOG」を**同時に**持つ。111 §12 が再 OPEN の条件として求めた「PARTS Detail 側の表示契約とセット」を満たす |

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

## Domain 3-B: Research ↔ App 境界契約（v1.6-r4 / 2026-09-17）

正本: Research 主査 `RIG_PARTS_CONTRACT_EXPORT_20260917`（Research 所有領域。⛔ App レーンは本文を編集しない）。
裁定: `_decisions/2026-09-17_research-app-boundary-contract-v1.md`。

### B-0. 参照の意味 — **本書の「FK → Research Master」は App 内の同期 Master への参照**

⚠️ **Research DB（`ualrrrsmhlnpwfqrrsjc`）と App / Production は別 project。**
物理 FK も JOIN も **project を跨げない**。

したがって本書および Domain 2 の

```
rigs.rig_master_id          FK → rig_masters.rig_master_id
rigs.rig_master_variant_id  FK → rig_master_variants.variant_id
rigs.manufacturer_id        FK → manufacturers.manufacturer_id
rigs.rig_category_slug      FK → rig_categories.slug
parts.part_category_slug    FK → part_categories.slug
parts.parts_master_id       FK → （同期 Master の）part_id
```

は **Research DB へ直接 FK を張る意味ではない。**
**Research の master テーブルを PK(UUID) 不変・列名不変のまま App 側へ同期複製したあと、
App project 内のその同期 Master を参照する**という契約である。

- **同期方式そのもの（複製 / レプリケーション / ETL / 頻度）は App 側の決定事項。** 本書では定義しない。
- **Research 側の要求は 3 点のみ**（これを満たす限り方式は問わない）:
  1. **PK UUID を再採番しない**
  2. **列名を改名しない**
  3. **Research 行を App 側で物理 DELETE しない**（`db_register=false` / publication で非表示化する）
- 本書の語彙: **FK** = 同期 Master の PK をそのまま外部キーとして参照 /
  **JOIN** = 表示時に同期 Master 側を読む（App 列へ複製しない） /
  **CACHE可** = App 所有テーブルへ表示用に複製してよい（**再同期で上書きされる前提**） /
  **コピー禁止** = App 所有テーブルへ複製しない /
  **XREF** = 境界表（cross_ref）が無いと接続できない。
- ⚠️ **`parts_masters` だけは同期の前段（cross_ref）が未作成**なので、上の同期が成立していても
  接続できない（HOLD H-1）。

### B-1. 境界キー — これ以外を接続に使わない

| 対象 | 唯一の境界キー |
|---|---|
| メーカー | `manufacturers.manufacturer_id`（UUID PK）。⛔ `.id` 表記は誤り |
| RIG マスター | `rig_masters.rig_master_id` |
| RIG バリアント | `rig_master_variants.variant_id` |
| **PARTS マスター** | **`part_masters.part_id`（UUID PK）のみ。⛔ `.id` ではない** |
| PARTS バリアント | `part_master_variants.variant_id`（親は `part_master_id` → `part_masters.part_id`） |
| カテゴリ | `rig_categories.slug` / `part_categories.slug`（**PK は slug**。親子は `parent_slug` 自己参照） |

**⛔ 境界キー禁止（Research 主査裁定）**
`part_slug`（UNIQUE なし・台帳と 51.5% 不一致）/ `primary_sku`（NULL・`-`・`N/A`・他社 SKU 接頭辞・JAN 混在）/
`part_name` / `evidence_url` / `canonical_url` / `scraped_from`（同一 URL 共有 913 件）/
`master_aliases.alias_value` / `alias_sku` / `manufacturers.slug` / `platform_slug` / `variant_slug` /
これらの組合せハッシュ / App 側で生成した UUID。

**Research 同期 Master への 3 要求（Research 側の条件）**
(1) PK UUID を再採番しない (2) 列名を改名しない (3) Research 行を App 側で物理 DELETE しない。

### B-2. write authority — 誰がその値を書くか

「UI に入力欄が無い＝不整合」ではない。**書き込み主体**で分ける。

| 区分 | 誰が書くか | 例 |
|---|---|---|
| **user-input** | ユーザーが Register で入力 | `nickname` / `description` / `tagline` / `user_build_tags` / `private_note` / `ownership_status` / `usage_status` / `ownership_state` / `purchased_period` / `purchase_price` / `purchase_store` / `is_public` / 写真と `images.caption` / `entity_links` |
| **server-derived** | client は **master identity だけ**を送り、サーバーが同期 Master を引いて解決・cache 生成 | `rig_master_id` / `rig_master_variant_id` / `manufacturer_id` / `manufacturer_name_cache` / `rig_category_slug` / `part_category_slug` / `part_subcategory_slug` / `platform` / `product_line` / Master 紐付き時の `part_number` |
| **custom-only** | Master 未紐付けのときだけユーザー入力 | Custom RIG / Custom PARTS の カテゴリ・型番・メーカー名 |
| **relation導出** | 保存しない。`rig_parts` から都度導出 | PARTS の「装着状況」 |

⛔ **Register の client payload に Master 継承値を hidden field として大量に詰めない。**
client → master identity を送る / server → 同期 Master を参照して FK と cache を作る。

### B-3. Resolver / Picker の候補契約

- **`db_register = false` → 候補から除外（確定）。** Research の物理 DELETE 代替。App 側で物理 DELETE しない。
- **Picker に publication gate はかけない（v1.6-r4 の裁定）。** 理由: `master_publication` 行が無い part が
  **64%**（2026-08-10）あり、公開判定をそのまま候補条件にすると Register が機能しない。
  `master_publication_effective` は **公開表示の契約**であって**登録内部検索の契約ではない**。
  → **picker eligibility** と **public display gate** を別契約として扱う。

### B-4. 公開面の publication gate（確定）

Public Detail / Library / Search / 公式画像 / 公式説明 / 公式外部リンクは
**VIEW `master_publication_effective` を唯一の公開判定源**とする。
⛔ App 側で `effective_display_mode` / `effective_logo_mode` を再計算・固定化しない。
公式画像は `display_status='approved_image'` **かつ** `effective_display_mode='image_enabled'` のときだけ。
公式リンクは `master_external_links`（`display_status='active'`）が正本で、
⛔ Domain 4 の `entity_links`（**ユーザー投稿リンク**）とは別物。混ぜない・コピーしない。

### B-5. compatibility

- `compatible_platforms` — **Research 正本**。⛔ App ユーザー入力禁止・推測展開禁止・
  **部分文字列照合禁止**（`trx4m ⊃ trx-4` で 318 件の誤接続事故）。1 対多 token は据置。XREF まで **HOLD**。
- `compatible_types` — Research に同名列が**存在しない**。App 所有列として扱い、⛔ Research 継承値として同期しない。

### B-6. App 側が前提にしてはいけない Research 未確定事項

`part_categories.rig_type`（物理実在未確認 → PARTS の `rig_type` は MVP で `rc-car` 固定）/
`part_categories.spec_schema`（未確定 → spec facet は当面 App 実装対象外）/
`manufacturers.is_active`（正典間で矛盾）/ `size_class` 13 値（Category v1.4 未反映）。

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
| caption | TEXT | NULLABLE | **公開**フォトノート。ユーザーが画像に付ける説明（Detail の「フォトノート」）。⛔ `alt` とは別概念 |
| sort_order | INTEGER | DEFAULT 0 | 表示順 |
| is_primary | BOOLEAN | DEFAULT false | メイン画像フラグ（＝カバー） |
| width | INTEGER | | 元画像幅 |
| height | INTEGER | | 元画像高さ |
| file_size | INTEGER | | バイト数 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除 |

⚠️ `images.alt`（画像代替テキスト）の追加要否は **未裁定のまま**。`caption`（ユーザーが書く公開フォトノート）と
`alt`（アクセシビリティ用の代替テキスト）は別概念であり、**v1.6-r3 の裁定対象は `caption` だけ**。

---

### `entity_links`
**ユーザーが自分で登録する外部リンクの Single Source。** RIG / PARTS / LOG で共有する。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| user_id | UUID | FK → profiles.id, NOT NULL | 登録者（RLS用） |
| entity_type | TEXT | NOT NULL | CHECK (entity_type IN ('rig','part','log')) |
| entity_id | UUID | NOT NULL | 対象のID |
| service | TEXT | NOT NULL | youtube / instagram / x / facebook / tiktok / vimeo / site |
| label | TEXT | | 表示名。SNS はサービス名、個人サイトはユーザー入力 |
| url | TEXT | NOT NULL | https:// のみ。アフィリエイトURLは拒否 |
| moderation_status | TEXT | NOT NULL, DEFAULT 'not_required' | CHECK (moderation_status IN ('not_required','pending','approved','rejected')) |
| sort_order | INTEGER | DEFAULT 0 | 表示順 |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 論理削除 |

**`moderation_status` の運用**

| service | 初期値 | 遷移 |
|---|---|---|
| SNS 6種（ドメイン照合で本人性が担保できる） | `not_required` | なし |
| `site`（個人サイト・任意ドメイン） | `pending` | 運営確認 → `approved` / `rejected` |

⚠️ **個人サイトの URL を変更したら `approved` を引き継がず `pending` へ戻す。**
（承認済みの殻だけ残して中身を差し替えられるのを防ぐ）

**Master 公式リンクとの関係**
- Master 公式リンク（メーカー製品ページ等）の正本は **Research 側 `master_external_links`**。
  ⛔ `entity_links` へコピーしない。
- Public Detail では **ユーザーリンクと Master 公式リンクを同じ視覚ブロックへ合成してよい。**
  データ源を分離していれば、表示を1ブロックにまとめることは矛盾しない。
- アフィリエイトリンクは `affiliate_links`（Domain 6）が正本。これも `entity_links` とは別物。

⚠️ **`rigs.external_links` JSONB は本テーブルへの移行対象。恒久二重管理は禁止。**
移行 DDL / データ移行は本書では定義しない（Production DB への migration は別裁定）。

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
| status | TEXT | NOT NULL, DEFAULT 'published' | CHECK (status IN ('published','pending','hidden','deleted','withdrawn')) — 🔴 v1.6-r8: `withdrawn` = 投稿者が退会確定（body は空。画面が「退会したユーザーのコメント」と描く） |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 削除時刻の記録専用。表示制御はstatusで行う |

**status値の意味：** published=公開 / pending=保留 / hidden=オーナー・運営非表示 / deleted=削除 / withdrawn=投稿者が退会確定（v1.6-r8）

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

**UNIQUE:** ~~`(comment_id, reporter_user_id)`~~ → 🔴 **v1.6-r11: 開いている通報だけ** = `CREATE UNIQUE INDEX ... ON comment_reports(comment_id, reporter_user_id) WHERE status IN ('open','reviewing')`。resolved / rejected の後に内容が変われば再通報できる（rate limit を併用）

🔴 **v1.6-r11 書き込み経路 1 本**（comment_reports / content_reports / user_reports 共通）: 通報の作成は**サーバー側の専用経路だけ**（SECURITY DEFINER RPC / Server Action は実装時）。**直接 INSERT の RLS ポリシーは作らない**（裁定原本 `_decisions/2026-09-29_info-legal-support-v1.md` D5）。専用経路の必須条件:
- `reporter_user_id` は `auth.uid()` / session から導出し、client 値を信用しない
- **reporter 自身の状態を関数の中で判定する**: 対応する有効な `profiles` が存在し、通報できるアカウント状態であること。**登録途中（profiles なし）・退会猶予中（`deleted_at IS NOT NULL`）・確定処理中・Suspended は拒否**。UI / middleware は通報 API のセキュリティ境界にしない（authenticated なら RPC を直接叩ける）
- INSERT の前に検証: 対象の実在（論理削除済みを除く）・self-report の拒否・`reason_code` が CHECK の値・**開いている通報（open / reviewing）の重複**・rate limit
- `SECURITY DEFINER` を使うなら **固定 `search_path`**。作成時に **`REVOKE EXECUTE ... FROM PUBLIC`**（PostgreSQL は新規 function の EXECUTE を PUBLIC に既定付与する）→ 必要な role（`authenticated`）だけ `GRANT EXECUTE`。**作成と権限設定は同一 transaction**。`anon` / `public` に残さない。RPC が迂回路にならないこと
`reporter_user_id` は NOT NULL のまま。退会確定処理（Domain 11）は profiles 行を匿名化して残すので FK は壊れず、参照先に個人情報は残らない。

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

**UNIQUE:** ~~`(entity_type, entity_id, reporter_user_id)`~~ → 🔴 **v1.6-r11: 開いている通報だけ**（partial unique・`WHERE status IN ('open','reviewing')`。comment_reports と同じ）

🔴 **v1.6-r11**: 書き込み経路 1 本（comment_reports と同じ。直接 INSERT の RLS ポリシーは作らない）。`entity_type` に `user` を**足さない**（ユーザーの通報は `user_reports`）。`reason_code` は 6 種を**維持**（`wrong_info` = ユーザー投稿の誤情報。Library マスターの修正報告 `master-correction-report-spec-v1` とは別）。UI の「危険・違法」は `inappropriate` に畳む（ラベル「不適切・危険・違法なコンテンツ」）。

**reason_code値（report.htmlのUIと対応）：**
- `inappropriate` = 不適切なコンテンツ・画像
- `spam` = スパム・宣伝目的の投稿
- `copyright` = 著作権侵害
- `wrong_info` = 表記ミス・情報の誤り
- `harassment` = 嫌がらせ・ハラスメント
- `other` = その他


---

### `user_reports`（🔴 v1.6-r11 新設・情報・法務・サポート D5）
ユーザー（アカウント）の通報。投稿の通報（`content_reports`）・コメントの通報（`comment_reports`）と**別表**。処置が違う（投稿 = 非公開化 / ユーザー = 停止）ので同じ表に混ぜない。`target_user_id` に FK を張れる。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | |
| target_user_id | UUID | FK → profiles.id, NOT NULL | 通報対象のユーザー |
| reporter_user_id | UUID | FK → profiles.id, NOT NULL | 通報者。session から |
| reason_code | TEXT | NOT NULL, CHECK (reason_code IN ('impersonation','harassment','spam','inappropriate','other')) | impersonation = なりすまし |
| note | TEXT | NULLABLE | 自由記述 |
| status | TEXT | NOT NULL, DEFAULT 'open', CHECK (status IN ('open','reviewing','resolved','rejected')) | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| resolved_at | TIMESTAMPTZ | NULLABLE | |
| resolved_by | UUID | FK → profiles.id, NULLABLE | 対応した運営者 |

**UNIQUE:** `(target_user_id, reporter_user_id) WHERE status IN ('open','reviewing')`（partial）
**CHECK:** `target_user_id <> reporter_user_id`（self-report はサーバー側関数でも拒否）
**書き込み経路 1 本**（comment_reports と同じ）。SELECT は運営者ロールのみ（`/admin` のサーバー側経路）。
退会猶予中（`profiles.deleted_at IS NOT NULL`）の対象も通報できる。停止（suspended）> 再開（/resume）は auth-guard のガード順で成立。停止中アカウントの確定処理の扱いは **PENDING（/admin レーン）**。

---

### `support_inquiries`（🔴 v1.6-r11 新設・情報・法務・サポート D4）
お問い合わせ・フィードバック・開示請求・権利者からの申し立ての受け皿。**未ログインでも送れる**。/admin のキューと運営記録。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK, DEFAULT gen_random_uuid() | 運営メールには「新規 #ID / kind」だけを載せる |
| kind | TEXT | NOT NULL, CHECK (kind IN ('account','content','bug','feedback','data_request','rights','other')) | account = ログイン・乗っ取り・停止の申し立て / data_request = 開示請求（D9）/ rights = 権利者・非会員からの通報 |
| email | TEXT | NULLABLE | **必須は kind 別にサーバーで検証**: 必須 = account / rights / data_request、任意 = content / bug / feedback / other |
| subject | TEXT | NULLABLE | |
| body | TEXT | NOT NULL | |
| related_url | TEXT | NULLABLE | |
| user_id | UUID | FK → profiles.id, NULLABLE | **サーバー側 session からだけ導出**。client 値は捨てる。**session があり、かつ対応する `profiles` 行が存在するときだけ**その `profiles.id` を入れる。未ログイン・**登録途中（session あり・`profiles` なし）**は NULL（`auth.users.id` をそのまま入れると FK 違反。認証 D2「Onboarding 完了まで profiles を作らない」） |
| status | TEXT | NOT NULL, DEFAULT 'open', CHECK (status IN ('open','replied','closed')) | |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| handled_by | UUID | FK → profiles.id, NULLABLE | |
| handled_at | TIMESTAMPTZ | NULLABLE | |

- **書き込み経路 1 本 = サーバー側**（Server Action / RPC は実装時）。クライアント INSERT の RLS ポリシーは作らない。匿名は rate limit ＋ bot 対策（Cloudflare Turnstile 要確認）
- **Suspended は書き込み不可。例外は `kind='account'` だけ**（救済の申し立て。UI は `?kind=account&from=suspended` で種別を固定）
- **受付と本人確認を分離**: data_request / account は受け付けた後に本人確認の工程へ。確認できるまで開示・移行・ログインの復旧をしない。手順は 🔴 **HOLD（法務）**
- **client から直接 SELECT 不可**。運営者は `/admin` のサーバー側読み取り経路（service role）だけから参照（RLS はポリシー 0）
- **退会確定処理（Domain 11）**: ~~`user_id` を NULL に切り離すだけ。email / body は運営記録として残す~~ → 🔴 **2026-09-30 定点版（D4 更新・イタヤ委任で Claude 決定）: `user_id` と `email`（ほか本人がわかる列）を NULL にする**。種別・受付日時・対応状況は運営記録として残す。行は消さない（物理 DELETE 禁止）
- **保持期間**: ~~🔴 HOLD~~ → **定点版（2026-09-30・専門家の確認で変わりうる）: 対応が終わってから 1 年で `email` / `subject` / `body` / `related_url` を NULL にする**（行と種別・日時・状態は残す）。Legal Hold（D12）の対象は除く。期限列・自動 NULL 化ジョブは schema r12 で定義する（まだ作らない）
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
    "category_slug": "rock-crawler",
    "subcategory_slug": "comp-crawler",
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

## Domain 9: 通知（🔴 v1.6-r8 で MVP 実行分へ移動）

🔴 **v1.6-r8（2026-09-28 / 正典 120）**: 旧見出し「将来用・MVPでは作成しない」は**失効**。**アプリ内通知を MVP に含める**（メール・Push は MVP の外）。
裁定原本: `_decisions/2026-09-28_settings-notifications-v1.md`（D1 / D2 / D5 = 通知設定を MVP に含める・20:30 改訂）。

```
notifications
├── id (UUID, PK)
├── user_id (UUID, FK → profiles.id, NOT NULL) — 通知を受け取るユーザー
├── actor_id (UUID, FK → profiles.id, NULLABLE) — アクションしたユーザー。⛔ type='favorite' では常に NULL
├── type (TEXT, NOT NULL) — CHECK (type IN ('like','favorite','follow','comment','comment_reply','security'))  ← v1.6-r8: security = 個人宛ての重要なお知らせ（actor_id NULL・設定で止められない）
├── entity_type (TEXT, NULLABLE)
├── entity_id (UUID, NULLABLE)
├── comment_id (UUID, NULLABLE, FK → comments.id) — 🔴 v1.6-r10（S8）: comment / comment_reply で、どのコメントかを指す（開いたときにそのコメントへ移る）
├── event (TEXT, NULLABLE) — 🔴 v1.6-r10（M7）: type='security' の出来事。CHECK (event IN ('login_method_added','login_method_removed','signed_out_everywhere'))。type='security' のときだけ NOT NULL
├── meta (JSONB, NOT NULL, DEFAULT '{}') — 🔴 v1.6-r10（M7）: 出来事の最小限の属性（例 {"provider":"facebook"}）。⛔ 文言を保存しない（表示のときに作る = 言語を変えても同じデータから描ける）
├── read_at (TIMESTAMPTZ, NULLABLE) — 既読にした時刻。NULL = 未読（v1.6-r8 で is_read BOOLEAN を置き換え）
├── created_at (TIMESTAMPTZ, DEFAULT now())
```

**生成条件（L2・v1.6-r8）**
- `like` / `favorite` / `follow` は、**その actor がその対象に初めて行った時だけ**作る
  （元テーブル `likes` / `favorites` / `follows` に同じ actor × 対象の行が、**論理削除済みも含めて**無い場合）。
  付けたり外したりで通知を連打させない。`favorite` はこの条件により「通知の件数 = 保存した人数」になる
- `comment` / `comment_reply` は毎回作る
- 自分の行為（自分の entity へのいいね等）は作らない。`pins` は通知しない。System 通知は MVP の外
- ⛔ **`type='favorite'` の行に `actor_id` を入れない**。favorites の個別行は本人しか読めない（RLS）ため、通知に actor を持つと別経路で漏れる

**既読と束ね（L2・v1.6-r8）**
- 通知は 1 件ずつ保存し、**束ねは表示層で行う**（束ねのための列・テーブルを持たない）。束ねるのは `like` / `favorite` の同じ `type` × 同じ対象だけ
- 既読の束 = 同じ `type` × 同じ対象 × 同じ `read_at`。1 回の既読操作で読んだ分を 1 つの束として残す（`is_read` の真偽値ではどの操作で読んだかが残らず、既読にした瞬間に束がばらける）
- 「すべて既読」は**押した時刻より前に作られた未読**だけに `read_at` を入れる（処理中に届いた新着を巻き込まない）
- 1 回の既読操作は**サーバーで決めた同一時刻**を入れる。**未読行だけ**を更新し、既読行の `read_at` は上書きしない
- 生成元: `likes` / `favorites` / `follows`（初回判定あり）・`comments`（毎回。親コメントの有無で `comment` / `comment_reply`）
- 初回判定: 成功した INSERT に対し、**論理削除済みを含む過去の履歴**から初回かを判定し、同一トランザクション（トリガー）で通知を作る。部分 UNIQUE は「今有効な行」の重複しか止めない（解除後の再 INSERT は通る）ので、保証の本体は履歴判定。再試行・同時実行・解除後の再登録で重複しないことは実装時に検証する。**物理 DELETE 禁止（L1）が前提**
- ⛔ ユーザー側に `last_seen_at` 等の「前回見た時刻」を持って束ねの基準にしない

**だれに届くか（L2・v1.6-r10・再監査）**
- 1 つのコメントについて: entity の持ち主へ `comment`、親コメントの書き手へ `comment_reply`。**同じ人なら 1 件**。自分自身へは作らない
- コメントへのいいねの行き先 = 親の entity ＋ そのコメントの位置
**一覧の取り方（L2・v1.6-r10）**
- INDEX: `(user_id, created_at DESC)` / `(user_id) WHERE read_at IS NULL`
- 束ねた単位で返す読み取り経路（type × 対象 × read_at でまとめる）でページを分ける（1 件ずつでページを切ると、束が 2 ページに割れて人数が狂う）
- 行は消さない（L1）。一覧に出す期間（例: 直近 90 日）は実装時に決める

**開いたときの行き先（L2・v1.6-r10・S8）**
- 行き先は `entity_type` / `entity_id`（＋ `comment_id`）から表示のときに作る。URL を保存しない
- 対象が削除・非公開・持ち主の門で見えない（D13）ときは、行き先の画面で「見られません」と出す（通知の行は消さない）
- `security` は ログインとセキュリティ（/settings/login）へ

### `notification_settings`（🔴 v1.6-r8・MVP・D5 改訂 2026-09-28 20:30）
MyRIG 内の通知の受け取り方。**1 ユーザー 1 行。行が無い = 全部 ON**（登録時に作らない）。

| Column | Type | Constraints | Notes |
|---|---|---|---|
| user_id | UUID | PK, FK → profiles.id | |
| enabled | BOOLEAN | NOT NULL, DEFAULT true | 全体 ON/OFF。OFF の間は新しい通知を作らない（過去分は消さない・遡って作らない） |
| like_on | BOOLEAN | NOT NULL, DEFAULT true | いいね |
| favorite_on | BOOLEAN | NOT NULL, DEFAULT true | お気に入り（名前なし通知） |
| comment_on | BOOLEAN | NOT NULL, DEFAULT true | コメントと返信（comment / comment_reply） |
| follow_on | BOOLEAN | NOT NULL, DEFAULT true | フォロー |
| announcement_on | BOOLEAN | NOT NULL, DEFAULT true | お知らせ（全員宛ての一般案内）。⛔ 重要なお知らせ・security はこの設定で止めない（D7） |
| updated_at | TIMESTAMPTZ | DEFAULT now() | |

- 通知の生成は `enabled AND <種類>_on`（行が無ければ true）を満たすときだけ。全体 OFF でも種類ごとの値は保つ
- ⛔ メール / Push / 頻度の列を先に作らない（配信経路を足すときに設計する）
- 🔴 D6: 通知を作らない条件に「受け手が送り手をブロック / ミュートしている」を足す（作って隠さない）
- 🔴 v1.6-r10（M5）: `enabled` / `announcement_on` を変えたときは、同じトランザクションで `announcement_mute_periods` を開く / 閉じる（Domain 9）。**最初の行の INSERT も対象**（行が無い = 全部 ON から変わったとみなす）

### `announcements` / `announcement_reads`（🔴 v1.6-r8・MVP・D7）
全員宛てのお知らせ。**1 人ずつ `notifications` に行を作らない。**
```
announcements
├── id (UUID, PK)
├── level (TEXT, NOT NULL) — CHECK (level IN ('normal','critical'))  normal = お知らせ（設定で止められる）/ critical = 重要（止められない）
├── title / body / url (TEXT)  ※ 日英は本番の i18n 方式に合わせる（ここでは決めない）
├── published_at (TIMESTAMPTZ) / created_at / deleted_at
├── expires_at (TIMESTAMPTZ, NULLABLE) — 🔴 v1.6-r10: 重要なお知らせの効き目が終わる時刻（過ぎたら一覧の上に固定しない）
announcement_reads（🔴 v1.6-r10・M5: 1 人 1 行の「最後に読んだ時刻」を廃止）
├── user_id (UUID, FK → profiles.id)
├── announcement_id (UUID, FK → announcements.id)
├── read_at (TIMESTAMPTZ, NOT NULL)
├── PK (user_id, announcement_id)   ← **読んだときにだけ行を作る**（配信のための行は作らない）
announcement_mute_periods（🔴 v1.6-r10・M5）
├── id (UUID, PK)
├── user_id (UUID, FK → profiles.id, NOT NULL)
├── muted_at (TIMESTAMPTZ, NOT NULL)
├── resumed_at (TIMESTAMPTZ, NULLABLE) — NULL = いまもオフ
```
- **なぜ**: 旧 `last_read_at` 1 個では、新しいお知らせを読んだ瞬間に**それより古い未読の重要なお知らせまで既読**になる。また「オフの間に出た一般のお知らせを、オンに戻しても届けない」を判定できない（ASTRA M5）
- ⛔ 「オンに戻した時刻」1 個で判定しない: オフにする前に出て未読のままのお知らせまで消える（GPT の反例）
- **一般のお知らせ（normal）を見せる条件**: `published_at >= profiles.created_at` かつ **どのオフ期間 [muted_at, resumed_at) にも入っていない**
- **重要なお知らせ（critical）**: `published_at >= profiles.created_at` **または `expires_at > now()`（いま効いているもの = 登録直後の人にも見せる）** なら見せる（オフ期間を無視）。未読でも `expires_at` を過ぎたら一番上に固定しない
- **オフ期間の開閉**: お知らせが「実際に止まっている」= `enabled = false` または `announcement_on = false`。サーバーが `notification_settings` の変更と同じトランザクションで、止まった瞬間に期間を開き、動き出した瞬間に閉じる（全体スイッチも含む）
- 未読 = 見せる条件を満たし、`announcement_reads` に行が無い。「すべて既読」は押した時点で見えている未読の分だけ行を作る（同じ `read_at`）
- RLS: announcements は SELECT 全公開（published_at <= now() AND deleted_at IS NULL）・変更は管理者のみ / announcement_reads は本人の SELECT・INSERT のみ / announcement_mute_periods は本人の SELECT のみ（書き込みはサーバー）

## Domain 10: 安全（🔴 v1.6-r8・MVP・D6）

### `user_blocks` / `user_mutes`
| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | UUID | PK | |
| user_id | UUID | FK → profiles.id, NOT NULL | ブロック / ミュートした人 |
| target_id | UUID | FK → profiles.id, NOT NULL | された人。CHECK user_id != target_id |
| created_at | TIMESTAMPTZ | DEFAULT now() | |
| deleted_at | TIMESTAMPTZ | NULLABLE | 解除（論理削除） |
- UNIQUE（部分）: `(user_id, target_id) WHERE deleted_at IS NULL`
- ブロック時: 両方向の `follows` を論理解除（解除しても戻さない）。以後、target から user の投稿への comments / likes / favorites / follows の INSERT を拒否（RLS / trigger）
- 🔴 **v1.6-r10（ASTRA M2）: INSERT だけ止めても抜け道が残る** — 旧来の「自分の行なら UPDATE 可」のままだと、解除済みのいいね・フォローの `deleted_at` を NULL に戻す、または対象列を書き換えることで、ブロック判定を通らずに関係が復活する。
  → 関係テーブル（likes / favorites / pins / follows）の**ユーザーの UPDATE は「`deleted_at` を入れる（解除）」だけ**。`deleted_at` を NULL に戻す・対象列（entity_type / entity_id / following_id 等）を変える UPDATE は拒否する（trigger）。**やり直しは新しい行の INSERT** — ブロック判定・初回通知の判定が INSERT の 1 か所に集まる（`rig_parts` の「再装着は新しい行」と同じ考え方）
- RLS: 本人（user_id = auth.uid()）だけ SELECT / INSERT / UPDATE。**相手には見せない**
- 🔴 v1.6-r10（再監査）: 関係テーブルと同じく、**UPDATE は `deleted_at` を入れる（解除）だけ**。`target_id` の書き換え・解除の取り消しは拒否し、やり直しは新しい行の INSERT（ブロック時のフォロー解除を必ず通すため）。ブロックの INSERT はサーバー処理（フォローの両方向解除と同じトランザクション）
- 🔴 **一覧の読み方（v1.6-r9・D12）**: 件数無制限の全件描画をしない。**新しい順・段階取得**（cursor = `(created_at, id)`。offset は途中の解除・追加で重複や飛びが出る）。1 回の件数は UI の調整値で正典に固定しない
- 🔴 **ブロックはミュートの効き目を含む（D6）**: 同じ相手に両方あるときは、画面ではブロックの欄に 1 行（「ミュートも設定中」）。**データは別々に持つ** — ブロックを解いてもミュートは残る（黙って消さない）
- INDEX: `(user_id, created_at DESC, id DESC) WHERE deleted_at IS NULL`（一覧）/ `(target_id, user_id) WHERE deleted_at IS NULL`（投稿・通知を作るときの判定）

## Domain 11: 退会（🔴 v1.6-r8・D8）
- **親の門**: 公開面の SELECT（rigs / parts / maintenance_logs / images / comments / entity_links / likes 等の関係と件数）は、既存条件に加えて **持ち主の `profiles.deleted_at IS NULL`** を満たすときだけ。⛔ 退会で子の `deleted_at` を一括更新しない
  → 🔴 v1.6-r10 で D13（ガレージ非公開）と合わせて **「持ち主の門」** に一般化（RLS 共通原則）
- 🔴 **状態（v1.6-r10・M1。状態の列は作らず時刻 3 つで表す）**
  | 状態 | 条件 |
  |---|---|
  | 利用中 | `deleted_at IS NULL` |
  | 猶予中（30 日・再開できる） | `deleted_at IS NOT NULL AND purge_started_at IS NULL AND now() < deleted_at + 30 日` |
  | 期限切れ・消去の開始待ち（再開できない） | `deleted_at IS NOT NULL AND purge_started_at IS NULL AND now() >= deleted_at + 30 日`（🔴 ASTRA 最終監査 M1: 消去の処理が遅れても、31 日目以降は再開させない） |
  | 確定処理中（再開できない） | `purge_started_at IS NOT NULL AND purge_completed_at IS NULL` |
  | 確定済み | `purge_completed_at IS NOT NULL` |
  - **再開** = `UPDATE profiles SET deleted_at = NULL WHERE id = 本人 AND purge_started_at IS NULL AND now() < deleted_at + interval '30 days'`（サーバー。時刻はサーバーの now()。🔴 期限の条件を入れる = 画面の「期限を過ぎると戻せない」をデータで守る）
  - **確定処理の開始** = `UPDATE profiles SET purge_started_at = now() WHERE id = … AND deleted_at <= now() - 30 日 AND purge_started_at IS NULL`
  - 2 つとも**同じ profiles 行への条件付きの 1 回の更新**なので行ロックで順番が決まり、両方が成立することはない。**画像の削除・匿名化・Auth ソフト削除は `purge_started_at` を立てた後だけ**
  - 確定処理の各段は何度やっても同じ結果になるように作り、全部終わったら `purge_completed_at`。途中で止まったものは `purge_completed_at IS NULL` から再実行
- 🔴 **猶予中の本人は書き込めない（M1）**: ユーザーの INSERT / UPDATE のポリシーは、共通原則の「本人の profile が利用中」を満たすときだけ。退会前に発行されたトークンで投稿・いいね等を続けさせない（例外 = 再開の処理だけ）
- **30 日後の確定処理**（元に戻せない・1 回だけ）: profiles の個人情報列を NULL / 既定値へ・username を `withdrawn-<random>` へ / 投稿の自由入力・個人情報性のある列を NULL / 空へ（行は残す）/ 画像の実ファイルを消し images 行は論理削除 / 他人の投稿へのコメントは status='withdrawn'・body=''（🔴 v1.6-r10: **`status='published'` のものだけ**。hidden / deleted / pending は変えない = 非表示・削除済みを復活させない）/ `profile_private` を既定値へ / Auth ソフト削除
- 🔴 **退会したユーザーのコメントの読み方（v1.6-r10・ASTRA M6）**: RLS は行を見せる / 見せないしか決められず、**同じ行の本文や投稿者だけを隠せない**。
  → `comments` の生の行の公開 SELECT は今のまま `status='published'` だけ（withdrawn の行は生では読めない）。
  → **公開のコメント一覧は 1 つの読み取り経路（View / RPC）** から返す: published はそのまま、withdrawn は `id` / `parent_id` / `created_at` / `status` だけ（`user_id` と `body` は NULL）。画面が「退会したユーザーのコメント」と描く。親（rig / part / log）の公開・持ち主の門は published と同じ条件
- 🔴 **前提なし再監査（v1.6-r10）で足した確定処理の対象**
  - その人の関係（likes / favorites / pins / follows / user_blocks / user_mutes）は論理削除する（件数・フォロー一覧から消える）
  - その人のコメントの本文は **status に関係なく** 空にする（published は withdrawn へ・ほかの status はそのまま）
  - アバター・カバー画像の実ファイルを消し、`avatar_url` / `cover_image_url` を NULL（profiles は images テーブルに入らないので明記）
  - `username_reservations` への書き込みは確定処理と同じトランザクション。**新規登録とユーザー名の確認時に予約を照合する**
  - その人が actor の通知は残す（表示は「退会したユーザー」）。通報（reporter）は運営の記録として残す
- 🔴 **猶予中の見え方（v1.6-r10）**: その人のコメント・いいね・フォローもほかの人に出さない（書いた人が利用中であることを条件にする）。再開すれば元に戻る
- 🔴 **再開の画面（v1.6-r10・auth-guard-spec §4.3 判定順 ① の具体）**: 猶予中の人がログインしたら、ほかの画面へ行かせず「再開しますか？」を出す（期限は日付で表示・「MyRIG を再開する」/「ログアウト」）。この間の書き込みは再開の処理だけ
- `username_reservations`（user 名の再利用防止）: `fingerprint` (TEXT, PK) = 正規化 username の **HMAC（サーバー秘密鍵）**・`created_at`。⛔ 素の username・素のハッシュを保存しない。秘密鍵は正典・チャットに書かない

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
  `status`（v1.6-r3。旧 `removed_at IS NULL`）で同じ役割（現在有効かどうか）を表す。
  「全テーブルがdeleted_atを持つ」わけではない。**
- ✅ **v1.6-r3 追記**: `rig_parts` の取り外しは `status='removed'` への UPDATE。
  **`removed_at` は日付が分かるときだけ入れる**（不明なら NULL のまま。偽の日付を作らない）。
  再装着は過去行を書き換えず新しい行を足す。
- **`deleted_at`（または`rig_parts`の`removed_at`）を持つ全テーブルの**全SELECTポリシーに
  対応する列の `IS NULL` 条件を含める
- 公開データ: `is_public = true AND deleted_at IS NULL` **かつ持ち主の門（下）**
- 自分のデータ: `user_id = auth.uid() AND deleted_at IS NULL`
- INSERT/UPDATE: `user_id = auth.uid()` **かつ本人の profile が利用中**（`deleted_at IS NULL`。🔴 v1.6-r10・M1 — 退会の猶予中は書けない）
- 🔴 **持ち主の門（v1.6-r10・D13 ＋ D8）**: 本人以外に見せるものは、次の 4 つを全部満たすときだけ
  1. 持ち主の profile が利用中（`profiles.deleted_at IS NULL`。D8 退会）
  2. 持ち主のガレージが公開（`profiles.is_public = true`。D13）
  3. その entity 自身が公開（`is_public = true`）
  4. その entity が削除されていない（`deleted_at IS NULL`）
  - **下位の設定は上位の非公開を突き破らない**（entity の `is_public = true` は「ガレージが公開なら、これも出してよい」の意味）
  - **同じ条件をすべての入口で使う（横漏れ防止）**: 詳細ページ・公開ガレージ・検索・Browse・Feed・Library の「使っている人」等の関連表示・いいね / お気に入りの件数・`rig_parts` / `maintenance_log_parts` / `entity_links` の関係経由・画像・コメント（親の持ち主で判定）・フォロー一覧（一覧の持ち主のガレージが公開のときだけ）
  - **門の外に残るもの**: その人がほかの人の公開投稿に書いたコメント・付けたいいね（書いた先の持ち主の門で判定）。名前から公開ガレージへ移ると「このガレージは非公開です」。profiles の名前・アバター等の基本情報は利用中なら読める（コメントの表示に要る）
  - 実装: 門の判定は 1 つの関数（例 `owner_is_visible(owner_id)`）にまとめ、各ポリシーと集計はそれを呼ぶ（入口ごとに条件を書き写さない）
- 🔴 **関係テーブルの UPDATE（v1.6-r10・M2）**: likes / favorites / pins / follows のユーザーの UPDATE は `deleted_at` を入れる（解除）だけ。復活・対象の書き換えは拒否し、やり直しは新しい行の INSERT（Domain 10）
- ✅ **2026-08-22 GPT監査で修正**: **DELETEポリシーは作らない。** どのテーブルにも
  `user_id = auth.uid()`によるDELETEポリシーを設けない（CORE.md「物理DELETEは禁止」に例外なし）。
  削除・解除操作（RIG/パーツ/ログの削除、rig_partsの取り外し、いいね/お気に入り/ピン/フォローの解除）は
  すべて対応する論理削除列（`deleted_at`または`removed_at`）のUPDATEで行う。
  ユーザー向けポリシーにDELETEを残さない。
  ✅ **2026-08-22 GPT総合監査で是正**: 旧記述にあった「運用・移行時はservice roleで物理DELETE」は
  CORE(L1)「物理DELETEは禁止」の例外化にあたるため削除した。**例外経路は設けない。**

### 🔴 判定と書き込みの経路（v1.6-r10・前提なし再監査 2026-09-29）
**なぜ**: 「ブロックされていたら拒否」「通知オフなら作らない」「初回だけ通知」は、**相手（や本人の過去）の行を読んで判定する**。ところが PostgreSQL では、ポリシーの中の問い合わせや SECURITY INVOKER のトリガーにも RLS がかかる。相手の `user_blocks` や `notification_settings`、自分の論理削除済みの行は読めない → **「見えない = 無い」として判定が素通しになる**（ブロックが効かない・オフでも通知が作られる・解除して付け直すたびに通知）。
1. **門の関数（ガード）を 1 つにまとめる**: likes / favorites / follows / comments の INSERT は、SECURITY DEFINER の関数（`search_path` 固定・uid は関数の中で `auth.uid()` から取る。引数で受けない）で一度に判定する
   - 書く人の profile が利用中 / 対象が見える（持ち主の門）/ コメントなら `comments_enabled_*` / **どちらかがブロックしていない**
   - **判定できないときは止める側に倒す（fail-closed）**
   - ブロックの確定と相手の INSERT が同時に走ってもフォローが残らないよう、2 人の組に対して順番を決めてロックする
2. **通知を作る処理も SECURITY DEFINER の関数**: 受け手の `notification_settings`・ミュート / ブロック・論理削除済みを含む履歴（初回判定）を読んで作る
3. **profiles の書き込みの制限**（RLS は行単位で列を縛れない → 列単位の GRANT かトリガー）: 本人が変えられるのは 表示名・自己紹介・国・サイト / SNS・アバター / カバー・`is_public`・`comments_enabled_*` だけ。`username`・`deleted_at`・`purge_*` はサーバーだけ。**プロフィールの保存は 1 本のサーバー処理**（`profiles` と `profile_private` の地域を同じトランザクションで。片方だけ成功しない）
4. **profiles の読み方**: 生の行の SELECT は本人だけ。**ほかの人は公開用の読み取り経路（View / RPC）1 本から**: ガレージ公開なら公開してよい列 / **非公開なら ユーザー名・表示名・アバターだけ**（非公開にした人の SNS・サイト・自己紹介を API から取らせない）/ 猶予中・確定済みは返さない（表示は「退会したユーザー」）
5. **列を 1 つだけ変えてよい UPDATE**: notifications の `read_at`、関係テーブルの `deleted_at` は、列単位の GRANT かトリガーで守る（ポリシーの文言では縛れない）
6. **notification_settings の書き込み**: 変えた列だけを更新する（行ごとの上書きで別タブの変更を消さない）。お知らせのオフ期間の開閉は、この表の **AFTER INSERT OR UPDATE** トリガー（SECURITY DEFINER）で行う（ブラウザから直接書かれても期間が記録される）。🔴 ASTRA 最終監査 M2: 行は登録時に作らないので、**最初のオフは INSERT で来る** → INSERT のときは「前 = 全部 ON（行が無い）」として、実際に止まった / 動き出したかを判定する
7. **ログイン方法の追加・削除（security 通知の生成元）**: Supabase Auth の中で起きるので、アプリの表への書き込みを契機にできない → **追加・削除は必ず MyRIG のサーバー API を通し、同じ処理で security 通知を作る**。ブラウザから直接 identity をつなぐ経路は閉じる（Supabase の設定・Auth Hook で閉じられるかは**要確認**）
   - **乗っ取り対策**: 追加して間もない方法（mock 7 日。値は実装時）では、それより前からある方法を外せない。ログインとセキュリティに「ログイン方法の変更の履歴」を出す（既読で消えない）。取り戻すための問い合わせ窓口をヘルプに書く
   - メールの追加で「別のアカウントで使われています」は、**コードを確かめた後（そのアドレスを持っていると確かめた後）にだけ**出す（登録済みかどうかの調査に使わせない）
8. **画像**: RLS は DB の行を隠すだけ。非公開・退会の後も、画像の URL を知っていれば開ける → 署名付き URL（Cloudflare Images で可能かは**要確認**）か、非公開にしたときの配信の扱いを実装前に決める（PENDING）

### テーブル別の特記事項
🔴 **v1.6-r10 読み替え**: 下の各行の「親（rig/part/log）が `is_public=true AND deleted_at IS NULL`」は、すべて **持ち主の門の関数 ＋ entity の公開** で判定する（RLS 共通原則）。

✅ **v1.6-r3 追記 — `entity_links` / 非公開 entity の relation 経由漏洩**
- `entity_links` の SELECT は**親 entity の `is_public` を JOIN 判定**する（images / comments と同じ方式）。
  加えて公開面では `moderation_status IN ('not_required','approved')` かつ `deleted_at IS NULL` のものだけ出す。
  `pending` / `rejected` は所有者本人にだけ見せる。
- **非公開 entity の情報を relation 経由で公開面へ漏らさない。**
  公開 PARTS が非公開 RIG と `rig_parts` を持っていても、公開面に
  **非公開 RIG の名前 / 画像 / リンク、および非公開 RIG を推測できる表示**を出さない。
  件数表示も、非公開分を含めた実数を出すと存在を推測させるため公開分だけで数える。
- **Owner-only 列は公開面のクエリに含めない**:
  `parts.nickname`（管理名） / `parts.private_note` / `rigs.private_note` /
  両テーブルの `purchase_price` / `purchase_store` / `purchased_period` の扱いは
  「価格・入手先は Owner-only、入手時期は公開可」とする。

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
  🔴 v1.6-r10: 「全公開」は失効 → **両方の持ち主が利用中 かつ 一覧の持ち主のガレージが公開** のときだけ（非公開の人のフォロー一覧を出さない）。INSERT は門の関数を通す
  解除（UPDATE `deleted_at`）は`follower_id = auth.uid()`の行のみ許可（他人のフォロー関係は解除不可）
- **マスターデータ**: SELECT全公開。変更は管理者ロールのみ
- **rig_parts**: `user_id = auth.uid()`でINSERT/UPDATE。物理DELETEなし。
  🔴 **v1.6-r6 で是正**: 旧記述「取り外しは`removed_at`のUPDATE」は **v1.6-r3 の変更を反映していなかった**
  （同じ本文の「共通原則」側は r3 で是正済みだったが、この行だけ旧いまま残っていた）。
  **正: 取り外しは `status='removed'` への UPDATE。`removed_at` は日付が分かるときだけ入れる（不明なら NULL）。**
- **maintenance_log_parts**（v1.6-r6）: `user_id = auth.uid()` で INSERT/UPDATE。物理DELETEなし（`deleted_at` の UPDATE）。
  SELECT は「自分の行」または **`maintenance_logs.is_public = true` かつ `parts.is_public = true` かつ両者 `deleted_at IS NULL`** の場合のみ。
  ⚠️ **非公開 PARTS は公開面の一覧にも件数にも出さない**（上の「非公開 entity の relation 経由漏洩」と同じ原則。裁定 114 Q3）。
  **App が `log.user_id = part.user_id` を保証する**（他人の PARTS を自分の LOG へ張れない）
- **comments**: SELECTは`status='published'`かつ親（rig/part/log）が`is_public=true AND deleted_at IS NULL`の場合のみ。
  INSERTはauth.uid()必須。自分のコメントのstatus更新のみ可能
  🔴 v1.6-r10: 親の判定は持ち主の門（D13）を含む。コメントを書いた人の門では判定しない（非公開ガレージの人のコメントも、公開投稿の上では見える）。
  **退会したユーザーのコメント（withdrawn）は生の行では読ませず、公開のコメント一覧の読み取り経路でだけ本文・投稿者なしで返す**（Domain 11・M6）
  🔴 v1.6-r10（再監査）: **自分の entity に付いたコメントは持ち主本人が読める**（非公開にした自分の RIG でも・通知の抜粋のため）。**持ち主による他人のコメントの非表示**（Domain 5 のコメントの節）はサーバー経由の UPDATE（「自分のコメントの status だけ」と矛盾していた）。書いた人が猶予中なら出さない
- **comment_reports / content_reports / user_reports**: 🔴 v1.6-r11 **ユーザーの直接 INSERT ポリシーは作らない**（サーバー側関数 1 本。旧「INSERTはauth.uid()必須」は失効）。SELECTは運営者ロールのみ。重複防止は開いている通報だけの partial unique
- **support_inquiries**: 🔴 v1.6-r11 **client からの直接 INSERT / SELECT / UPDATE ポリシーは一切作らない**（RLS 有効・ポリシー 0）。書き込みはサーバー側 1 本。**運営者の読み取りも `/admin` のサーバー側経路（service role）だけ**で、operator JWT を RLS で通す SELECT ポリシーは作らない。DELETEポリシーは作らない（共通原則）
- **page_blocks**: SELECT全公開（is_active=trueのみ）。変更は管理者ロールのみ
- 🔴 **notifications（v1.6-r8）**: SELECTは`user_id = auth.uid()`のみ。UPDATEは`user_id = auth.uid()`かつ`read_at`列のみ。
  INSERTはユーザーに許可しない（元テーブルへの書き込みを契機にサーバー側で作る）。DELETEポリシーは作らない（共通原則）。
  `type='favorite'` の行は `actor_id` が NULL のため、受け手も誰が保存したかを読めない
- 🔴 **notification_settings（v1.6-r8 → r10）**: SELECT は本人のみ。書き込みは変えた列だけ（判定と書き込みの経路 6）。DELETE ポリシーは作らない
- 🔴 **profile_private（v1.6-r10）**: SELECT は `user_id = auth.uid()` のみ。INSERT / UPDATE のポリシーは作らない（書き込みはサーバー側の処理だけ）。⛔ 本人専用の値を公開される `profiles` の行へ戻さない（RLS は列を隠せない）
- 🔴 **notifications（v1.6-r10 追記）**: `event` / `meta` / `comment_id` はサーバーだけが書く（ユーザーの UPDATE は従来どおり `read_at` だけ）
- 🔴 **announcement_reads / announcement_mute_periods（v1.6-r10）**: Domain 9 のとおり

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

-- v1.6-r6: LOG ↔ PARTS
CREATE UNIQUE INDEX idx_log_parts_pair ON maintenance_log_parts(log_id, part_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_log_parts_part ON maintenance_log_parts(part_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_log_parts_log  ON maintenance_log_parts(log_id)  WHERE deleted_at IS NULL;

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
CREATE INDEX idx_rigs_category ON rigs(rig_category_slug) WHERE deleted_at IS NULL;
CREATE INDEX idx_rigs_rig_type ON rigs(rig_type) WHERE deleted_at IS NULL;
CREATE INDEX idx_parts_category ON parts(part_category_slug) WHERE deleted_at IS NULL;

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

-- 🔴 v1.6-r11: 通報 3 表の重複防止は「開いている通報だけ」の partial unique（旧 UNIQUE 制約は作らない）
CREATE UNIQUE INDEX uq_comment_reports_active ON comment_reports(comment_id, reporter_user_id)
  WHERE status IN ('open','reviewing');
CREATE UNIQUE INDEX uq_content_reports_active ON content_reports(entity_type, entity_id, reporter_user_id)
  WHERE status IN ('open','reviewing');
CREATE UNIQUE INDEX uq_user_reports_active ON user_reports(target_user_id, reporter_user_id)
  WHERE status IN ('open','reviewing');

-- 🔴 v1.6-r11: ユーザー通報
CREATE INDEX idx_user_reports_target ON user_reports(target_user_id, status, created_at DESC);
CREATE INDEX idx_user_reports_reporter ON user_reports(reporter_user_id, created_at DESC);

-- 🔴 v1.6-r11: お問い合わせ（/admin のキュー・本人の紐づけ）
CREATE INDEX idx_support_inquiries_status ON support_inquiries(status, created_at DESC);
CREATE INDEX idx_support_inquiries_user ON support_inquiries(user_id, created_at DESC) WHERE user_id IS NOT NULL;

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

rigs ──N:M──→ parts             (via rig_parts / 同時 active は 1 台)
rigs ──1:N──→ maintenance_logs  (rig_id, NULLABLE)
rigs ──1:N──→ images

maintenance_logs ──N:M──→ parts (via maintenance_log_parts / v1.6-r6。rig_id が NULL でも張れる)

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

user_reports ──N:1──→ profiles (target_user_id)          🔴 v1.6-r11
user_reports ──N:1──→ profiles (reporter_user_id)
support_inquiries ──N:1──→ profiles (user_id, NULL 可)    🔴 v1.6-r11

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

### MVP実行分

> 🔴 v1.6-r11: 見出しの表数表記（旧「20テーブル ＋ …」）は r8〜r11 で増えて古くなっていたため撤去。この一覧は **migration の順序**（FK 依存のため Research 所有表も含む）。App / Research の責務境界は「適用範囲」の App 所有一覧が持つ。別目的
1. `manufacturers` ※Research所有
2. `rig_categories` / `part_categories` ※Research所有
3. `profiles`（auth.users依存）
4. `rig_masters` ※Research所有
5. `parts_masters`（本書の表記。**実体は cross_ref 待ち** — 上記注意）
6. `rigs`（rig_masters依存）
7. `parts`（parts_masters依存）
8. `rig_parts`
9. `maintenance_logs`
9-b. `maintenance_log_parts`（maintenance_logs / parts 依存。**v1.6-r6 で新設**）
10. `images`
10-b. `entity_links`（rigs / parts / maintenance_logs 依存。**v1.6-r3 で新設**）
11. `likes`
12. `favorites`
13. `pins`
14. `follows`
15. `affiliate_links`
16. `comments`
17. `comment_reports`（comments依存）
18. `content_reports`
19. `page_blocks`
19-b. `notifications`（profiles 依存。🔴 **v1.6-r8 で将来実行分から移動**）
19-c. `notification_settings`（profiles 依存。🔴 **v1.6-r8 新設**）
19-d. `announcements` / `announcement_reads`（🔴 v1.6-r8 新設・D7）
19-e. `user_blocks` / `user_mutes`（🔴 v1.6-r8 新設・D6）
19-f. `username_reservations`（🔴 v1.6-r8 新設・D8）
19-g. `profile_private`（profiles 依存。🔴 v1.6-r10 新設・D10 / M3）
19-i. `user_reports`（profiles 依存。🔴 v1.6-r11 新設・情報・法務・サポート D5）
19-j. `support_inquiries`（profiles 依存・user_id NULL 可。🔴 v1.6-r11 新設・同 D4）
19-h. `announcement_mute_periods`（profiles 依存。🔴 v1.6-r10 新設・M5）。`announcement_reads` は r10 の形（user × announcement）で作る

### 将来実行分（MVPでは作成しない）
20. `user_plans`
~~21. `notifications`~~ → v1.6-r8 で MVP 実行分へ移動（19-b）

---

## 未確定（要裁定）

- `size_class` / `power_source` / `platform_slug` の **App側への実列追加DDL**が未設計
  （`size_class` は値集合が HOLD 中）
- **App↔Research 写像表（cross_ref）が未作成。**本文中の `parts_masters` が
  App側 / Research側どちらを指すか曖昧な箇所が残る（機械的な一括置換をしないこと）。
  **PARTS Master ID の接続は HOLD H-1 継続**（Domain 3-B / `_decisions/2026-09-17_…`）。
  RIG 側の cross_ref も PARTS と同時に作る（Research 主査要請）
- `images.alt`（画像代替テキスト）の追加要否（**`caption` は v1.6-r3 で確定。`alt` は別概念として未裁定のまま**）
- **`maintenance_log_parts.rig_parts_id`（LOG 時点の装着エピソード）の追加要否。**
  v1.6-r6 では**意図的に持たない**（裁定 114 Q2）。追加する場合、**既存行は遡って埋められない**（推定禁止）。
- **複数 RIG で共有して使う機材**（送信機・バッテリー等）の受け皿。
  `rig_parts` は `idx_rig_parts_active_part` により 1 PARTS = 同時 1 RIG なので、そこへは混ぜられない
- **`rigs.external_links` → `entity_links` のデータ移行手順**（Production DB への migration は未着手）
- **`condition` の将来設計。** v1.6-r3 で意味論を廃止したが、
  「入手時の状態」「加工の有無」を別軸として再設計する余地は残す
- **`build_details` 旧キー（mechanics / suspension / …）の扱い。** 新キー（settings / finish）へ
  どう寄せるかは未裁定。**意味推定での自動変換は禁止**
- ~~関係テーブル（likes / favorites / pins / follows）の解除手段と物理DELETE禁止の両立~~
  ✅ 2026-08-22 イタヤ裁定・解消済み（4テーブルへdeleted_at追加。上記ソーシャル節参照）
- ~~RLS のセキュリティモデル~~ ✅ 2026-08-22 イタヤ裁定・解消済み（上記 RLS 節参照）

---

## 版の履歴（要点のみ）

詳細な差分は git 履歴と `_backup/audit_20260821/` を参照。

| 版 | 要点 |
|---|---|
| **v1.6-r11** | **情報・法務・サポート（2026-09-29 / 正典 122・裁定原本 `_decisions/2026-09-29_info-legal-support-v1.md` D4 / D5）。** `support_inquiries` 新設（kind 7・email 必須は kind 別・user_id は session からだけ・Suspended は account だけ・保持期間は HOLD で値を入れない）/ `user_reports` 新設（ユーザー通報を content_reports から分離）/ comment_reports・content_reports の UNIQUE を開いている通報だけの partial unique へ / 通報 3 表と inquiries の書き込みをサーバー側関数 1 本にし**直接 INSERT の RLS を廃止** / content_reports の reason 6 種・entity_type 3 種は維持（初案の reason 統一・user 追加は GPT・Gemini の監査で撤回）。⛔ Production DB migration は行っていない |
| **v1.6-r10** | **ASTRA 監査の是正と D13（2026-09-29 / 正典 120 追補）。** 退会: `profiles.purge_started_at` / `purge_completed_at`・再開と確定処理の競合を行ロックで排除・猶予中は書けない（M1）/ 関係テーブルの UPDATE は解除だけ・やり直しは INSERT（M2 ブロックの抜け道）/ **`profile_private` 新設**（マイカテゴリ・地域・地域の公開。r9 で `profiles` に置いた `preferred_rig_category_slugs` はここへ移した = 未適用の列なので行の移行なし）（M3）/ `announcement_reads` を user × announcement へ・**`announcement_mute_periods` 新設**（M5）/ 退会コメントは published だけを withdrawn へ・公開一覧は専用の読み取り経路（M6）/ `notifications` に `event` / `meta` / `comment_id`（M7・S8）/ **持ち主の門**（ガレージ非公開 = 全部非公開・全入口で同じ関数・D13）/ **前提なし再監査の是正**: 判定と通知の生成を SECURITY DEFINER の関数へ（RLS の下で判定が素通しになる）・profiles の列の書き込み制限と公開用の読み取り経路・username の小文字一意・長さと URL の CHECK・退会の確定処理の対象を補った・猶予中の見え方と再開画面・security 通知はサーバー API から・追加直後の方法で古い方法を外せない・通知の宛先 / 索引 / ページ分け・`announcements.expires_at`。⛔ Production DB migration は行っていない。裁定原本 D13 と「再監査の扱い」 |
| **v1.6-r9** | **マイカテゴリ（2026-09-29 / 正典 120 追補・D10 / D12）。** **`profiles.preferred_rig_category_slugs TEXT[]` 新設**（最大 5 = CHECK・配列の順 = 並び・書き込みはサーバーの原子的な add / remove だけ・slug の実在確認をサーバーで）/ `profiles.preferred_subcategory` を**非推奨**（型を変えない・DROP しない）/ 同日の別テーブル案 `user_interest_categories` は採らなかった/ `user_blocks`・`user_mutes` に一覧の段階取得と「ブロックはミュートを含む・データは別々」を明記。⛔ Production DB migration は行っていない。裁定原本 `_decisions/2026-09-28_settings-notifications-v1.md` D10〜D12 |
| **v1.6-r8** | **設定・通知（2026-09-28 / 正典 120・D1〜D9）。** `notifications` を MVP 実行分へ・`is_read` → `read_at`・`type` に `security` / `notification_settings` / `announcements`・`announcement_reads` / `user_blocks`・`user_mutes` / 退会（Domain 11）・`username_reservations` / `comments.status` に `withdrawn`。⛔ Production DB migration は行っていない（この行は r9 で補った。r8 の時点で履歴に書き漏れていた） |
| v1.1 | `rig_parts` に user_id と部分ユニーク制約 / CHECK 制約整備 / RLS をテーブル別設計へ |
| v1.2 | `rigs.rig_master_id` `parts.parts_master_id` 追加 / `watchlists`→`pins` / **`log_type` の `setup`・`other` を廃止し `custom`・`memo` へ** / `parts_masters.aliases` と GIN 索引 |
| v1.3 | `rigs` / `rig_masters` に `product_line` `platform` 追加 |
| v1.4 | `comments` を MVP へ昇格 / `comment_reports` 追加 / コメント受付ON/OFF 2系統 |
| v1.5 | `page_blocks`（ウィジェット型CMS）追加。block_type 5種・sort_logic 6種 |
| v1.6 | `content_reports` 追加。reason_code 6種 |
| **v1.6-r4** | **Research ↔ App 境界契約の是正（2026-09-17）。** CONTRACT-EXPORT-20260917 との突き合わせで見つかった境界不整合を閉じた。`rigs.build_tags` → **`user_build_tags`** へ改名（Research `rig_masters.build_tags` と同名別義だった）/ FK 参照先を Research 実 PK へ是正（`rig_masters.rig_master_id` / `manufacturers.manufacturer_id`）/ **`category_id UUID` を廃止**し `rig_category_slug` / `part_category_slug` / `part_subcategory_slug`（Research PK は slug）へ / **`rigs.rig_master_variant_id` 新設**（SKU を失わない）/ `parts.part_number` の write authority を経路別に定義（Master 紐付きは `primary_sku` が権威・ユーザー上書き不可）/ `parts.compatible_types` を「App 所有・Research 継承値ではない」と明記 / **Domain 3-B 新設**（境界キー・write authority・picker eligibility・publication gate・compatibility）。⛔ Production DB migration は行っていない |
| **v1.6-r7** | **Multi-RIG Relation（2026-09-19 / 正典 115）。** **`idx_rig_parts_active_part`（1 PARTS = 同時 1 active RIG）を撤去。** 1 PARTS は複数 RIG と同時に `active` な relation を持ってよい。残る一意制約は `idx_rig_parts_active_pair`（同じ RIG に同じ PARTS を二重に付けない）だけ / `parts` の説明文を **r6 の「順次（同時は 1 台）」から差し戻し** / **HOLD H-2（共有機材の複数 RIG 同時関連）を解消**（`rig_parts` に RIG ごとの `active` 行で持つ。⛔ 専用テーブルも「共有機材」分類も作らない）/ r6 の伝播規則は **規則を変えず根拠だけ差し替え**（索引の帰結 → 手放した RIG に `active` が残ると表示が嘘になるから）/ 「別 RIG に装着中なら移す確認」（裁定 114 §3-3）を **取り下げ**。⛔ 列は 1 本も変えていない（索引 1 本を落とすだけ）。⛔ 数量列は作らない。⛔ 既存データの意味推定変換は行わない。⛔ Production DB migration は行っていない。裁定原本 `_decisions/2026-09-19_multi-rig-relation-v1.md` |
| **v1.6-r6** | **Relationship MVP（2026-09-18 / 正典 114）。** **`maintenance_log_parts` 新設**（`log_id ↔ part_id` のみ。⛔ `rig_parts_id` は持たない＝裁定 Q2。RIG 無し LOG でも張れる＝Q1。UI 上限 10 は DB 制約にしない＝Q6）/ `rig_parts` に **親 entity の状態変化の伝播規則**を明記（RIG 削除・`past` / PARTS `released`・削除 → `active` を `removed` へ。これを怠るとその PARTS が二度と装着できなくなる）/ `parts` の説明文「複数RIGに装着可能」を **「順次（同時は 1 台）」** へ是正（索引と読み方が食い違っていた）/ `parts.nickname` に「`rigs.nickname` と可視性が逆」を追記 / RLS の `rig_parts` 行が **v1.6-r3 の `status` 化を反映していなかった**のを是正 / 非公開 PARTS を relation 経由でも公開面に出さない・数えないことを明記（Q3）。⛔ 既存テーブルの**列は 1 本も変えていない**。⛔ Production DB migration は行っていない。裁定原本 `_decisions/2026-09-18_relationship-mvp-v1.md` |
| **v1.6-r5** | **LOG Composer 契約への追随（2026-09-17 / 正典 111）。** `maintenance_logs.log_type` を **任意分類**へ（NOT NULL / DEFAULT `'maintenance'` を撤廃。NULL＝分類していない。CHECK は 4 値のまま） / `title` の NOT NULL を撤廃（LOG は本文が主役） / `body` を **NOT NULL DEFAULT `''`** 化し、公開時だけ非空を担保する CHECK を追加（draft 実体作成のため空文字を許す） / `duration_minutes` `surface` `weather` は**列を維持したまま Composer v1 から新規入力させない**契約 / 写真は最大 3・Cover なし・`sort_order` が表示順 SoT。⛔ Production DB migration は行っていない。⛔ 既存 `maintenance` 行を推測で NULL へ変換していない。裁定原本 `_decisions/2026-09-17_log-composer-contract-v1.md` |
| **v1.6-r3** | **Register ↔ Detail ↔ DB データ契約の統合裁定（2026-09-16）。** `rigs` に `tagline` / `build_tags` / `private_note` / `usage_status` / `purchased_period` 追加・`purchased_at` を非推奨化・`build_details` を「設定値・加工・自由項目だけ」に限定（旧 mechanics 系キー廃止）・`external_links` を移行対象化 / `parts` に `nickname` / `private_note` / `ownership_state` / `purchased_period` 追加・`condition` の意味論廃止（DEFAULT 'new' 撤廃）・`description` を公開紹介文と明記 / `rig_parts` に `status` 追加（事実状態と日付を分離、日付不明を表現可能に）・active 一意制約を `rig_id,part_id` と `part_id` の2本に / `images` に `caption` 追加 / **`entity_links` 新設**（RIG・PARTS・LOG 共通のユーザーリンク） / RLS に非公開 entity の relation 経由漏洩防止を追記 |
| v1.6-r2 | **Research 所有領域の列定義を削除し参照へ降格** / App側 `rig_type` を5値へ / `category_id` の FK 参照先を `rig_categories`・`part_categories` に明記 / `rigs.platform` `product_line` をマスター継承のみに / RLS の `deleted_at` 条件をテーブル限定へ / `page_blocks.page_type` に parts 系2種を追加 / `log_type` 4値で決着 |
