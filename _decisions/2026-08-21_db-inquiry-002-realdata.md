# DB Research 照会#2 — 実DB確認結果（App側Cowork実行）

**実行: 2026-08-21 16:34 JST**
**実行者: App側 Cowork（device_bash経由、`_tools/ro_query.sh` / `myrig_research_ro`）**
**位置づけ: 照会#2で依頼したE/F/H-1/I-3/J-2を、DB Research主査への送付前に自前で実行し完了。**

---

## E. spec_data / spec_schema

- **E-1**: `part_masters.spec_data` に実在するキーは225種。上位: `series_name`(2205) / `series_number`(2034) / `diameter_inches`(1792) / `type`(1418) / `msrp_jpy`(1184)。
- **E-2**: カテゴリ別分布を取得。`wheels-tires` は `diameter_inches`(1786件)が圧倒的多数で他を大きく引き離す。
- **E-3**: **`part_categories` テーブルは0行（空）。`spec_schema` というカラム名はDB全体のどのテーブルにも存在しない。** facetable提案（K）の前提インフラ自体が未構築。
- **E-4**: 表記揺れが実在。`diameter_inches` の値に `1.9"`(712件) と `1.90"`(4件) が別トークンとして混在。→ G の読み（表記揺れは正規化の仕事）を裏付ける実データ。
- **E-5**: ホワイトリストの記載は正典内に見当たらず。225種類が事実上自由入力運用と読める（未確認のまま）。

## F. master_aliases

- **F-1**: `entity_type` 実在値4種: `part_master`(190) / `rig_master_variant`(53) / `manufacturer`(31) / `rig_master`(2)。RIG・パーツ両方参照可能。
- **F-2**: `locale` 実在値: `en`(259) / 空欄(16) / `ja`(1)。**日本語別名は実質1件のみ。**
- **F-3**: `alias_kind` 実在値10種（11値中）。`sku_revision`(97)・`reseller_sku`(96) が最多。
- **F-5**: `manufacturer × sub_brand_alias` に `TRX`(Traxxas公式) の実例あり。メーカー別名は保持可能。

## G. 正規化と別名の責務分界

E-4の表記揺れ実データが「`master_aliases` の仕事ではなく検索インデックス構築時の正規化の仕事」という読みを裏付け。

## H. 日英

- **H-1**: `name`/`name_en`/`name_ja`（manufacturers等）は揃う一方、`part_name`/`part_name_ja`（`part_name_en`なし）、`platform_name`/`platform_name_ja`（`platform_name_en`なし）という**非対称構造を実データで確認**。

## I. event_tags

- **I-3**: **`event_tags` という列名はDB全体のどのテーブルにも存在しない（0件）。** Category v1.4「確定値12種」・CURRENT「8値未確定」はどちらも未実装の机上記述と判明。

## J. size_class

- **J-1**: 7/30付の裁定書は正典内に発見できず（再確認）。関連して `MyRIG_Category_Structure_v1.4.md`（2026-06-16改訂、7/30より前）は size_class を13値enumではなく `TEXT` 自由記述カラムとして定義（例示6値のみ）。
- **J-2**: 実データは18パターン（`NULL`含む）。`NULL`(639件・最多) / `1/10`(341) / `1/8`(62) / `1/12`(50) ... `M-chassis`(2) / `mini`(1) など自由記述が混在。**13値という前提の裏付けは実データ上も無い。**

## K. facetable

未着手（週次ゲート裁定待ち、変更なし）。E-3で前提インフラ未構築が判明。

---

## App側の暫定判断（イタヤ裁定待ち）

1. **size_class**: 「13値確定」の出典は正典にもDB実データにも無い。実データ主導（18パターンを土台に確定）へ切替を提案。
2. **event_tags**: 列が物理的に存在しないため、Category v1.4「12種確定」記載は誤り、またはApp側の未実装機能の先行記述。owner確認が必要（Research側から解消する話ではない、との照会#2回答済み見解と整合）。
3. **spec_schema / facetable**: `part_categories`が空でカラムも無い。Kの週次ゲート裁定を待つ前に、App側でこのインフラをどう構築するかの設計判断が別途必要。
