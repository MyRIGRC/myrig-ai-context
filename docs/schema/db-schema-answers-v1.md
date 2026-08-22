# MyRIG — スキーマ確認依頼 v1 への回答（DB Research 主査裁定）

> **拘束力: L1（恒久ルール・逸脱禁止）— §0 責務境界 / §2 確定事項**
>
> §0 と §2 は **DB Research 所有領域の正本**であり、App 側の一存では変えられない。
> 変更提案は「Research への照会」という形でしか出せない。
>
> **§3 推奨（App側責務）は L2（現在の確定仕様・より良い案の提案歓迎）。**
> L2 の節では「既存仕様と異なる」ことだけを理由に案を捨てない。
> 差分を明示すればイタヤ裁定で変更できる。

回答日: 2026-07-30 (JST) / 回答元: MyRIG Database Research PJ / 主査
正典: MyRIG_Category_Structure_v1.4 / RC_Master_Research_Rules_v4.4_final_rev3 / Research_DB_Schema_v1.2
記録: App実装プロジェクト側の写し。原文は DB Research PJ が保持。

> ⚠️ **本書のうち以下は HOLD。確定事項として実装しないこと。**
>
> - §2 `size_class` の固定値集合13値 — 実データは18パターンで enum 運用されていない
> - Q6 `event_tags` 確定値12種 — `event_tags` 列が DB に存在しない
> - §2 `categories` パーツ親14・子90 — `part_categories` テーブルは0行（未構築）
> - Q12 `spec_data` パーツ側キー — 未設計。`spec_schema` 列は DB に存在しない
>
> HOLD の現在地索引は `_AI/MyRIG_CURRENT.md`。**本書と CURRENT が食い違う場合は CURRENT を優先する。**

---

## 0. 総論（App側が最初に飲み込むこと）— L1

**`myrig_db_schema_v1.6` は正典ではない。**
Q1〜Q4・Q6・Q12 で「スキーマに無い」と報告した軸は、**すべて Research DB 側の正典に存在する**。
無いのは App 側スキーマの取りこぼしであって、設計上「持たない方針」ではない。
**App側は独自にカラムを新設せず、正本定義に合わせて追加する。**

### 責務境界（混ぜない）

| | 対象 |
|---|---|
| **DB Research が正本を決める** | manufacturers / rig_masters / rig_master_variants / part_masters / part_master_variants / bodies / rig_categories / part_categories / master_aliases / master_relations / master_images / master_external_links / master_publication |
| **App側が決める**（DB Researchは推奨のみ） | rigs / rig_parts / maintenance_logs / profiles / images / 検索実装 / ファセット / 公開制御 |

**App↔Research 写像表（cross_ref）が無い状態でApp側スキーマを進めない。**
App `parts_masters` と Research `part_masters`、App `rigs.platform` と Research `rig_masters.platform_slug` は
列名も値域も異なる。写像表を作るまで同名別義のカラムを増やさない。

---

## 1. 回答サマリ

| | 論点 | 状態 |
|---|---|---|
| Q1 | スケール | **決まっている**（`size_class` が正本。App側に無いのは欠落） |
| Q2 | rig_type | **決まっている**（5値。`miniz` は廃止・Mini-Z は rc-car 内包） |
| Q3 | パワーソース | **決まっている**（`power_source` を持つ。撤去は誤り） |
| Q4 | platform | **決まっている**（rig_masters が platform master 本体。別表は作らない） |
| Q5 | surface / weather | 未定（App側責務・推奨のみ） |
| Q6 | categories | **決まっている**（RIG 24 / パーツ親14・子90 / 2階層で凍結） |
| Q7 | 全文検索 | 未定（App側責務。ただし aliases は `master_aliases` が正本） |
| Q8 | ファセット件数 | 未定（App側責務・推奨のみ） |
| Q9 | wishlist | 未定（App側責務・推奨のみ） |
| Q10 | preferred_* | 未定（App側責務・推奨のみ） |
| Q11 | 集計キャッシュ | 未定（App側責務・推奨のみ） |
| Q12 | spec_data キー | 一部（rig側11キー確定 / パーツ側は未定） |

---

## 2. 確定事項（App側が従うもの）— L1

### スケール = `size_class`（正規化カラム）

3つに分離済み。混同しない。

| 列 | 役割 |
|---|---|
| `size_class` TEXT | **絞り込み軸の正本。固定値集合** |
| `scale` TEXT | メーカー公称の表記（表示用） |
| `spec_data.scale` | 公式ページからの抽出生値（承認11キーの1つ） |

実在列: `rig_masters.scale` / `rig_masters.size_class` / `rig_master_variants.scale` / `bodies.scale`

- 正規化カラムで持つ。**JSONB には置かない**（抽出生値であり表記ゆれ防止の仕組みが無いため絞り込みに使わない）
- 絞り込みに使うのは **`size_class` のみ**
- 固定値集合: `1/5` `1/6` `1/7` `1/8` `1/10` `1/12` `1/14` `1/16` `1/18` `1/24` `1/27` `mini-z` `other`
  > ⚠️ **この13値は HOLD。確定値として実装しない。**
  > 実データは18パターンで enum 運用されておらず、検索UIは13値で実装済みという乖離がある。
  > 「実装13値を正として実データを移行」か「実データ主導で再定義」かの裁定待ち。
- **分母整数は採らない**（`mini-z` のような非分数値が入るため）。範囲指定が必要になった時点で
  generated column `scale_denominator INT` を追加する
- **マスター由来。`rigs` は継承のみ**。ユーザーは編集しない（`build_tags` と同じ扱い）。
  カスタム機体（マスター未紐付け）のみ手動選択
- **ボディ載せ替え・ホイールベース延長で `size_class` は変えない。** その情報は `build_details` に記述する
- ジャンル差は `rig_type` がスコープを切る。ミニ四駆は `mini4wd` で分離（1/32固定なので軸に出さない）。
  Mini-Z は rc-car 内の `size_class='mini-z'`

### `rig_type` = 5値

| slug | name_ja | Phase |
|---|---|---|
| `rc-car` | RCカー | Phase 1 (MVP) |
| `mini4wd` | ミニ四駆 | Phase 2 |
| `drone-fpv` | ドローン/FPV | Phase 3 |
| `rc-airplane` | RC飛行機 | Phase 4 |
| `rc-boat` | RCボート | Phase 4 |

旧6値 `('rc','mini4wd','miniz','drone','airplane','boat')` は**全面的に旧。使わない。**
`rc`→`rc-car` / `drone`→`drone-fpv` / `airplane`→`rc-airplane` / `boat`→`rc-boat` / **`miniz` は廃止**。
ドローン/飛行機/ボートは値として残す（サイドバーの「計画中」表示は正しい）。

**Mini-Z の受け皿は `size_class`。** `product_line` でも `categories` でもない。
Category v1.4 は rc-car に「Mini-Z含む」と明記。
正 = `size_class='mini-z'`（絞り込み軸）／併記可 = `rig_masters.product_line='Mini-Z'`（表示用）。
**`product_line` を絞り込み軸に使うのは不可。**

同一値セットで揃える: `profiles.preferred_rig_type` / `parts.compatible_types` /
`rig_categories.rig_type` / `part_categories.rig_type` / `rig_masters.rig_type` / `part_masters.rig_type`

MVPは `rc-car` のみ0件以外。「RCのみ選択可、ミニ四駆は予定表示」で正しい。

### `power_source` を持つ（撤去は誤り）

正典 enum（Rules §G-9）: `electric_brushed` / `electric_brushless` / `nitro` / `gasoline` / `hybrid` / `none` / `unknown`
実在列: `rig_masters.power_source`

**「電動かエンジンか」の表示は `build_details` ではない。**
Rules §E: 大分類ラベルは `rig_type` + `power_source` から UI 側で生成する。

- rc-car + electric_brushed/brushless → 電動RCカー
- rc-car + nitro → エンジンRCカー（グロー）
- rc-car + gasoline → エンジンRCカー（ガソリン）

「電動RCカー」のような固定表示カラムを製品マスターに追加しない。
**UIフィルタに出すかはApp側判断でよいが、データ層は必ず持つ。**

### `platform` = `rig_masters` が master 本体

`platforms` テーブルは作らない。`rig_masters` がそれ自体である。

- `rig_masters.platform_slug` — 正規化キー（**絞り込み・照合はこれを使う**）
- `rig_masters.platform_name` / `platform_name_ja` — 表示名
- 同シャーシ関係は `master_relations.relation_type='same_chassis'`

`rigs.platform` は**自由テキストを廃止し継承のみ**。マスター未紐付けでは NULL。ユーザー自由入力は許さない。
`product_lines` テーブルも作らない。`rig_masters.product_line` の値のみを許容集合とする（マスター化は保留）。

**正規化ルール（既に事故が出ている領域。厳守）**

- slug は小文字・数字・ハイフンのみ。空白は除去
- ローマ数字はアラビア数字へ（`SCX10 III` → `scx10-3`）
- 照合は完全一致、または接頭辞＋完全剰余（境界認識）に限る。**部分文字列照合は禁止**
  （過去に `trx4m` ⊃ `trx-4` の誤収束で **318件の誤接続**が発生）
- 1トークンが複数 rig に展開されうるもの（`kraton` → 1/5・1/8・1/10 等）は単射置換できないため置換しない

### `categories` = RIG 24 / パーツ親14・子90 / 2階層で凍結

**RIGカテゴリ（`rig_categories`, `rig_type='rc-car'`）の正は 24件。**

- オフロード11: `rock-crawler` `comp-crawler` `buggy` `truggy` `stadium-truck` `short-course`
  `desert-truck` `monster-truck` `basher` `rally` `pulling`
- オンロード7: `drift` `touring` `formula` `pan-car` `oval` `drag-car` `speed-run`
- スケール&特殊6: `semi-truck` `tank` `construction` `motorcycle` `lowrider` `truck-trial`

パーツ親カテゴリは**14で確定・凍結**。子カテゴリは**90で確定**（追加はレコード追加のみ）。
⚠️ ただし `part_categories` テーブルは実DB上0行（未構築）。

**2階層で足りる。3階層にしない。** `CHECK (level IN (1,2))` で確定済み。
「タイヤ・ホイール → タイヤ → 1.9インチ」の3階層目は**分類ではなくスペック**。
`spec_data` で表現し、絞り込みも `spec_data` 側で行う。
Category v1.4 運用原則「spec_data で検索強化（親子分類より優先）」。
サイズを子カテゴリ化すると 1.9/2.2/2.6/1.55 で子が爆発し、親14凍結が崩れる。

`rig_masters` ↔ カテゴリの突き合わせは `rig_masters.myrig_category` が正本列。
ただし**実データの充足率は未測定**。「列は確定・値の網羅は未確定」と扱う。

### `master_aliases` が aliases の正本

`rig_masters` に `aliases` 列は追加しない。`master_aliases` が正本。
`entity_type` の実在値は **`rig_master` / `rig_master_variant` / `part_master`（単数形） / `manufacturer`** の4種。
1テーブルで扱う設計。

> App 側の `parts_masters`（複数形）とは同名別義。混同しない。

ハイラックス / Hilux / TF2 はここに入る（`alias_kind` と `locale` で区別）。
App側は `master_aliases` を JOIN して検索対象に含める。

### `spec_data` の承認キー（rig側11個・ホワイトリスト固定）

`scale` / `drive_type` / `power_type` / `length_mm` / `width_mm` / `height_mm` /
`wheelbase_mm` / `ground_clearance_mm` / `weight_g` / `motor_size` / `gear_ratio`

これ以外のキーを抽出・投入しない。単位は mm・g の明示値のみ採用。

| 希望キー | 正しい置き場 |
|---|---|
| スケール | `size_class`（正規化カラム）。`spec_data.scale` は生値であり比較軸ではない |
| 駆動方式 | `drive_type`（承認済み） |
| 全長 / 幅 / WB / 車重 | `length_mm` / `width_mm` / `wheelbase_mm` / `weight_g`（承認済み） |
| モーター種別 | `power_source`（正規化カラム。spec_data ではない） |
| キット / RTR | `kit_type` enum（Rules §G-1・8値）。spec_data ではない |
| 防水 | 承認キー外。**未定**（追加は週次ゲート判断） |

パーツ側 `spec_data` のキー設計は**未定**。想定の置き場は `part_categories.spec_schema` JSONB。
**App側は当面 `spec_data` を絞り込み軸に使わない。**

> ⚠️ **HOLD**: `spec_schema` 列は DB 全体に存在せず、`part_categories` も0行。
> `part_masters.spec_data` の実在キーは225種で事実上の自由入力運用。
> **ホワイトリスト方式の前提インフラが未構築。**

表示側は `master_publication.spec_display_schema`（Rules §G-8・14値）に一本化済み。
`rig_masters` / `part_masters` 側に `spec_display_schema` 列を追加しない。

---

## 3. 推奨（App側責務・DB Researchは裁定しない）— L2

> この節は App 側の裁量領域。より良い案があれば差分を明示して提案してよい。

### Q5 surface / weather

固定値セットにする。参照テーブルではなく `CHECK` 制約でよい。

- surface: `rock` 岩 / `dirt` 土 / `grass` 芝 / `asphalt` アスファルト / `carpet` カーペット /
  `sand` 砂 / `snow` 雪 / `indoor-track` 屋内コース / `outdoor-track` 屋外コース / `other` その他
- weather: `sunny` 晴れ / `cloudy` 曇り / `rain` 雨 / `snow` 雪 / `indoor` 屋内 / `other` その他

`location` は自由テキストのままでよい（地名正規化はしない）。

`tags` TEXT[] はユーザー自由入力。**ただしフィルタに出すなら別軸が要る。**
運営正規タグは **`event_tags`**（Category v1.4 で確定値12種: `recon-g6` / `hill-climb` /
`endurance` / `demolition` 等）を使う。**自由 `tags` と `event_tags` を同じ入力欄に混ぜない。**

> ⚠️ **HOLD**: `event_tags` 列は DB 全体に存在しない。上の12種は未実装の机上記述。
> **owner（App未実装機能の先行記述か / Research管轄の未構築か）の確認が先。**

### Q7 全文検索の対象範囲

| 種別 | 対象 |
|---|---|
| RIG | `model_name` / `nickname` / `platform_slug` / `product_line` / `manufacturer_name_cache` |
| Parts | `product_name` / `part_number` / `description` / `manufacturer_name_cache` |
| Library | 上記 ＋ `master_aliases.alias_value` |
| Logs | `title` / `body` / `tags` / `location` |
| Users | `username` / `display_name` / `bio` |

**`build_details` JSONB の中身は MVP では対象外。**
「Reefs RC 299 を積んでいるRIG」は全文検索ではなく `rig_parts` 経由で解ける。
構造で解ける検索を全文検索に寄せると誤ヒットが増える。

日本語処理の推奨は `pg_trgm`（Supabase 標準搭載）。`pgroonga` の可用性は未確認（使うなら先に検証）。

### Q8 ファセット件数

**リアルタイム COUNT にしない。**

実測: **`part_masters` は 103,526件**（確度=confirmed / 2026-07-21 / Research DB 実測）。
数十万は将来ではなく現実のレンジに入りつつある。

- 推奨は「**マテリアライズドビュー＋日次更新＋概数表示**」
- 検索語なし・条件なしの初期表示はマテビューの値を「3,400+」形式で出す
- **検索語や条件が乗った状態ではファセット件数を出さない（数字を消す）**
- **総件数 COUNT は上限打ち切り。10,000件を超えたら「10,000+」と表示し、ページャは上限ページで止める**

### Q9 wishlist

- 公開検索の**既定結果から wishlist は除外**する
- `is_public` だけでは足りない。既定を「`current` / `past` は公開・`wishlist` は非公開」とし、
  ユーザーが明示的に切り替える形にする。**`ownership_status` ごとの公開設定を持たせるのが安全**

### Q10 preferred_*

- `preferred_rig_type` は**単一値のままでよい**（MVPは rc-car のみ）
- `preferred_subcategory` は**配列化する**（クローラーとドリフト両方は多数派に近い）。
  `TEXT[]` に `rig_categories.slug` を格納

### Q11 集計キャッシュ

- **MVPはカウントキャッシュを持たない。`EXISTS` で都度判定**（`like_count` / `favorite_count` と同方針）
- 「写真あり」「ログあり」「パーツ登録あり」は `EXISTS` 3本で足りる
- 遅くなってから部分インデックス → それでも足りなければトリガ更新のカウント列。
  最初からカウント列を持つと整合ずれの保守が発生する

---

## 4. App側で今すぐ直すもの（回答待ち不要）

1. `rig_type` の値を `rc` → `rc-car` 等、5値の正本へ置換
2. `miniz` を廃止し、Mini-Z を `size_class='mini-z'` の項目へ移動
3. スケール絞り込みを **`size_class` として正式実装**
4. パワーソースをフィルタから外したままでよいが、**データ層には `power_source` を持つ前提に戻す**
5. 「シャーシ / プラットフォーム」絞り込みを **`platform_slug` ベース**に変更（自由テキスト廃止）
6. RIGカテゴリを **24件**へ統一
7. パーツカテゴリの3階層案を取り下げ、**2階層＋`spec_data`** に変更

## 5. 週次ゲートへ回す事項（DB Research 側）

- App側スキーマへの `size_class` / `scale` / `power_source` / `platform_slug` 追加のDDL設計
- App↔Research 写像表（`cross_ref_db_to_ui.md` / `cross_ref_ui_to_db.md`）の作成
- `rig_masters.myrig_category` の充足率 census
- `part_categories.spec_schema` の実キー定義
- `rig_masters.spec_data` への `waterproof` キー追加可否

## 6. 未定として扱ってよいもの（要確認印のまま進めてよい）

Q5 / Q7の日本語処理 / Q8 / Q9 / Q10 / Q11 / Q12のパーツ側キー
