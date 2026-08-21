# MyRIG Search Contract v1（起票案）

**v1.1 / 2026-08-21 JST / 状態: 主要7件イタヤ裁定済み。残り3件は照会中**
**位置づけ: `mobile-feedback-ledger-v1.md` #19⑥「検索スコープ正典表の新設（5スコープ・プレースホルダ・タブ順の一元定義）」の具体化**

上位: `claude/search-blueprint-v2.md` §B-1
関連: v3.1 §4（軸マトリクス）/ `claude/search-results-ux-v1.md`

---

## 0. この文書は何で、何でないか

### これは「検索語彙・状態・軸の正本」である

PC左サイドバー・モバイル絞り込みオーバーレイ・検索APIの**3つを同じ定義から駆動する**ための契約。

```
              Search Contract（本書）
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
   PC Sidebar    Mobile Overlay      Search API
```

### これは「検索仕様書」ではない

巨大化させない。以下は**本書で決めない**。

| 内容 | 行き先 |
|---|---|
| 別名・日英・型番正規化 | B-2（別文書） |
| ランキングの重み付け | B-3（別文書） |
| 検索エンジン製品の選定 | B-5以降 |
| Semantic / Query Understanding | B-4 |
| PCの具体的レイアウト | P1モック |
| React / RSC / Supabase の実装方法 | Implementation Spec |

### ★HTMLは正本ではない

`pc/myrig-search-v3.html` の `data-for` 属性は、**現時点で最も整理された参照実装**である。
本書の確定後、それは「古い正本」ではなく **本書との照合対象** になる。

---

## 1. Vertical 正本

| key | UI（ja） | UI（en） | entity か | 備考 |
|---|---|---|---|---|
| `all` | すべて | All | **いいえ** | 集約表示。検索entityではない |
| `rig` | RIG | RIG | はい | ユーザーが所有・登録した車体 |
| `parts` | パーツ | Parts | はい | ユーザーが所有・登録したパーツ |
| `product` | 製品情報 | Products | はい | 公式カタログ（`rig_masters` / `parts_masters`） |
| `log` | LOG | LOG | はい | 整備・走行記録 |
| `user` | 人・ガレージ | Users | はい | ユーザー／ガレージ |

### ★`library` は廃止し `product` に統一する ✅**2026-08-21 イタヤ裁定・確定**

PC現物は `All / RIG / Parts / Logs / Library / Users`、モバイル現物は `lib` を使っている。

**Library はページ名・機能領域名であって、検索対象の entity ではない。** 検索対象は「製品」。
`library` のまま API に出すと、将来 Library の構造が変わったときに検索APIが壊れる。

| レイヤー | 値 |
|---|---|
| API / URL | `product` |
| UI（ja） | 製品情報 |
| UI（en） | Products |
| 内部テーブル | `rig_masters` / `parts_masters` |

**正典「『マスター』はUIで使わない → 『製品情報』」とも整合**（内部名 `product`、表示「製品情報」）。

### タブ順（PC / モバイル共通）

```
all → product → rig → parts → log → user
```

※ v3.1 §6 の「型番クエリなら製品情報が先頭」はこの既定順に対する**動的な並べ替え**であり、
Contract が定めるのは既定順のみ。

### 検索窓の placeholder（スコープ文言）

| 面 | 状態 | placeholder（ja） | placeholder（en） |
|---|---|---|---|
| 全面 | 既定 | RIG・パーツ・LOG・製品情報を検索 | Search RIGs, parts, logs & products |
| モバイル | 探し方=型番 | 型番・製品番号を入力（例: TRX-4） | Enter a part or model number |
| モバイル | 探し方=人 | ユーザー名・ガレージ名を検索 | Search users and garages |

**★これも Contract が持つ。** 現在 PC home に2種のプレースホルダが散在しており（ledger #19①「検索スコープ三重分裂」）、それの解消もここで行う。

---

## 2. URL 状態契約

**URL を検索状態の正本とする。**（実装手段は書かない）

### パラメータ一覧

| param | 型 | 適用 vertical | 例 |
|---|---|---|---|
| `q` | 単一 | 全 | `q=TRX-4` |
| `type` | 単一 | — | `type=parts` |
| `sort` | 単一 | 全（`all` を除く） | `sort=install_examples_desc` |
| `page` | 単一 | PC のみ | `page=3` |
| `per_page` | 単一 | PC のみ | `per_page=60` |
| `rig_type` | 複数 | rig / parts / product | `rig_type=rc-car` |
| `rig_category` | 複数 | rig | `rig_category=rock-crawler` |
| `part_category` | 複数 | parts | `part_category=tire-wheel` |
| `manufacturer` | 複数 | rig / parts / product | `manufacturer=traxxas` |
| `size_class` | 複数 | rig / product | `size_class=1-10` |
| `platform` | 複数 | rig / parts | `platform=trx-4` |
| `series` | 複数 | rig | `series=...` |
| `ownership` | 複数 | rig | `ownership=owned` |
| `purchased_year` | 複数 | rig | `purchased_year=2024` |
| `condition` | 複数 | parts | `condition=new` |
| `compatible_type` | 複数 | parts | `compatible_type=rc-car` |
| `fitment` | 複数 | parts | `fitment=official` |
| `log_type` | 複数 | log | `log_type=maintenance` |
| `surface` | 複数 | log | `surface=rock` |
| `weather` | 複数 | log | `weather=sunny` |
| `tag` | 複数 | log | `tag=...` |
| `event` | 複数 | log | `event=...` |
| `logged_from` / `logged_to` | 単一 | log | `logged_from=2026-01-01` |
| `duration` | 複数 | log | `duration=60-120` |
| `target` | 複数 | product | `target=rig` |
| `discontinued` | 単一 | product | `discontinued=false` |
| `release_year` | 複数 | product | `release_year=2024` |
| `country` | 複数 | user | `country=jp` |
| `preferred_rig_type` | 複数 | user | `preferred_rig_type=rc-car` |
| `rig_scope` | 単一 | 全 | `rig_scope=<rig_id>`（基準RIG） |

### ★複数選択の表現は「同一key反復型」 ✅**確定（技術判断・主査決定）**

```
?manufacturer=traxxas&manufacturer=axial
```

カンマ区切り（`?manufacturer=traxxas,axial`）は採らない。理由:

- 値そのものにカンマが含まれる可能性を排除できない
- **フレームワーク非依存。** `URLSearchParams.getAll()` で素直に取れる
- HTMLフォームの複数チェックボックスの既定の挙動と一致する

### 値は必ず slug

表示名（「ロッククローラー」）を URL に入れない。日英で URL が変わってしまう。
slug は英字 kebab-case（`rock-crawler` / `tire-wheel` / `1-10`）。

※スケールの `1/10` は URL では `1-10`。`/` はパス区切りと衝突する。

### 未指定の意味

| 状態 | 意味 |
|---|---|
| param 自体が無い | その軸で絞り込まない |
| `q` が無く条件だけある | 条件のみ検索（v2 A-4 の P4 / M4） |
| `type` が無い | `all` |
| `sort` が無い | その vertical の既定ソート |

---

## 3. Facet Registry

各軸が持つ情報。**`value source` が最重要** — UI側で車種カテゴリやメーカー一覧を勝手に持たない。

### 列の定義

| 列 | 意味 |
|---|---|
| key | 軸の識別子。URL param と一致させる |
| ja / en | 表示ラベル |
| vertical | どの vertical で出るか |
| value source | 値の出所（正典・テーブル・カラム） |
| selection | `single` / `multi` |
| operator | 同一軸内の結合（`OR` / `AND`） |
| count | `exact` / `approximate` / `none` |
| facet search | 軸内キーワード検索の有無 |
| dependency | 先に確定が必要な軸 |
| order | UI上の順番（vertical 内） |
| unknown | 値が無いレコードの扱い |

### 共通軸（複数 vertical で出る）

| key | ja | en | vertical | value source | sel | op | count | fsearch | order |
|---|---|---|---|---|---|---|---|---|---|
| `rig_type` | ジャンル | Genre | rig, parts, product | `rig_type` 正本5値（`rc-car` / `mini4wd` / `drone-fpv` / `rc-airplane` / `rc-boat`）。MVPは `rc-car` のみ。他は「予定」表示 | multi | OR | exact | なし | 1 |
| `manufacturer` | メーカー | Manufacturer | rig, parts, product | `manufacturer_id` → manufacturers | multi | OR | exact | **あり**（値が多い） | 4 |
| `size_class` | スケール | Scale | rig, product | `rig_masters.size_class` **★HOLD中**（§5-1） | multi | OR | exact | なし | 3 |

### `rig`

| key | ja | en | value source | sel | op | count | dep | order |
|---|---|---|---|---|---|---|---|---|
| `rig_category` | 車種カテゴリ | Vehicle Category | `rig_categories`（`rig_type='rc-car'` / `level=1`）**正本24件** | multi | OR | exact | — | 1 |
| `platform` | シャーシ / プラットフォーム | Chassis / Platform | `rig_masters.platform_slug` / `platform_name` | multi | OR | exact | — | 2 |
| `series` | シリーズ | Series | `rig_masters.product_line`。**マスターの値のみ許容・ユーザー自由入力禁止**（テーブル化は保留） | multi | OR | exact | — | 3 |
| `size_class` | スケール | Scale | 共通軸 | multi | OR | exact | — | 4 |
| `manufacturer` | メーカー | Manufacturer | 共通軸 | multi | OR | exact | — | 5 |
| `ownership` | 所有状態 | Ownership | `ownership_status` | multi | OR | exact | — | 6 |
| `purchased_year` | 購入年 | Year Purchased | `purchased_at` から年を導出 | multi | OR | exact | — | 7 |
| `has_build` ★ | 作例あり | Has Builds | 集計（未実装） | single | — | exact | — | 8 |

### `parts`

| key | ja | en | value source | sel | op | count | dep | order |
|---|---|---|---|---|---|---|---|---|
| `fitment` ★ | 適合 | Fitment | ①`compatible_platforms`（公式）②`rig_parts` 集計（装着例）③なし | multi | OR | exact | `rig_scope` があるとき有効 | 1 |
| `part_category` | パーツカテゴリ | Part Category | `part_categories`（`level=1`）**正本14件** | multi | OR | exact | — | 2 |
| `condition` | 状態 | Condition | `condition` | multi | OR | exact | — | 3 |
| `compatible_type` | 対応ジャンル | Compatible Genre | `compatible_types` / `rig_type` | multi | OR | exact | — | 4 |
| `manufacturer` | メーカー | Manufacturer | 共通軸 | multi | OR | exact | — | 5 |
| `spec_*` ★ | スペック | Specs | `spec_data` / `part_categories.spec_schema` **★衝突あり（§5-3）** | multi | OR | exact | **`part_category` 確定後のみ** | 6 |
| `has_install` ★ | 装着例あり | Has Installs | `rig_parts` 集計 | single | — | exact | — | 7 |

### `log`

| key | ja | en | value source | sel | op | count | order |
|---|---|---|---|---|---|---|---|
| `log_type` | ログ種別 | Log Type | `log_type` **★4値/5値の衝突（§5-4）** | multi | OR | exact | 1 |
| `surface` | 路面 | Surface | `surface`（CHECK制約） | multi | OR | exact | 2 |
| `weather` | 天候 | Weather | `weather` | multi | OR | exact | 3 |
| `tag` | タグ | Tags | `maintenance_logs.tags` | multi | OR | exact | 4 |
| `event` | イベント | Event | `event_tags` | multi | OR | exact | 5 |
| `logged_from`/`logged_to` | 実施日 | Date | `logged_at` | range | — | none | 6 |
| `duration` | 作業・走行時間 | Duration | `duration_minutes` を離散バケットへ | multi | OR | exact | 7 |
| `linked_rig` | 紐付け | Linked RIG | `rig_id` | multi | OR | exact | 8 |

### `product`

| key | ja | en | value source | sel | op | count | order |
|---|---|---|---|---|---|---|---|
| `target` | 対象 | Target | `rig_masters` / `parts_masters` のどちらか | multi | OR | exact | 1 |
| `manufacturer` | メーカー | Manufacturer | 共通軸 | multi | OR | exact | 2 |
| `rig_type` | ジャンル | Genre | 共通軸 | multi | OR | exact | 3 |
| `size_class` | スケール | Scale | 共通軸 | multi | OR | exact | 4 |
| `discontinued` | 生産状況 | Availability | `is_discontinued` | single | — | exact | 5 |
| `release_year` | 発売年 | Release Year | `release_date` から年を導出 | multi | OR | exact | 6 |
| `has_build` ★ | 作例あり | Has Builds | 集計（未実装） | single | — | exact | 7 |

### ❌ `price_band`（価格帯）は検索軸に置かない ✅**2026-08-21 イタヤ裁定**

v1 では「希望小売と明示すれば ledger #34 と両立しうる」として残していたが、**落とす。**

**理由: 価格で絞る行為そのものがショップの文法**であり、
ledger #34「MyRIGはショップではない。ユーザーは買いに来ているのではなく参考にしに来ている」
というトーン原則と、明示ラベルでは埋まらない水準で衝突する。

- `msrp` / `currency_code` は**データとしては持ち続ける**（製品情報の属性）
- 製品詳細ページでの表示可否は本書の管轄外。別途判断
- **検索の絞り込み軸としては持たない**

### `user`

| key | ja | en | value source | sel | op | count | order |
|---|---|---|---|---|---|---|---|
| `country` | 国 | Country | `country_code` | multi | OR | exact | 1 |
| `preferred_rig_type` | 得意ジャンル | Focus | `profiles.preferred_rig_type` | multi | OR | exact | 2 |
| `scale_of_garage` | 規模 | Garage Size | **公開RIG数・公開ログ数の集計**（閾値バケット）★集計値が必要 | multi | OR | exact | 3 |

### 軸間の結合

**軸をまたぐと AND。同一軸内は OR。**

```
?rig_category=rock-crawler&manufacturer=traxxas&manufacturer=axial
→ ロッククローラー AND (Traxxas OR Axial)
```

UI では各軸の見出しに「いずれかを含む」を小さく添える（日本語UIでの摩擦が最も少ない）。

### `unknown`（値が無いレコード）の扱い

| 方針 | 内容 |
|---|---|
| 既定 | **絞り込まない限り結果に含める。** 値が無いことを理由に除外しない |
| ファセット値としての「不明」 | **出さない。**「不明」を選ばせる意味がない |
| 例外 | `fitment` のみ「適合情報なし」を明示的な値として持つ（適合は不明であること自体が情報のため） |

**根拠:** 正典「公式根拠 > HOLD > 空欄/unknown > 推測 > 偽値」。
空欄は空欄として扱い、除外も補完もしない。

### 件数の定義（v2 B-5 より再掲・重要）

> 検索結果件数とファセット件数は、**同一 Search Index・同一クエリ文脈**から取得する。RDB COUNT との混在は禁止する。
> **ただし、総件数と各ファセット値の合計が一致することは保証しない。**
> ファセット件数は **「その値を選択した場合に該当する結果数」** と定義する。

- 選択中の軸は **disjunctive**（自分のフィルタを除いて集計）
- 大規模時は `約12,400` のような **概数表示を許容**（`count: approximate`）
- **0件の値は無効化する。UIから消さない**

---

## 4. Sort Contract

**vertical ごとに定義する。`required data` が取れないものは選択肢に置かない。**

| vertical | URL値 | ja | en | required data |
|---|---|---|---|---|
| rig | `relevance` ◎ | 関連順 | Relevance | — |
| rig | `newest` | 新着順 | Newest | `created_at` |
| rig | `likes_desc` | いいね順 | Most Liked | いいね集計 |
| parts | `relevance` ◎ | 関連順 | Relevance | — |
| parts | `install_examples_desc` | 装着例が多い順 | Most Installed | **`rig_parts` の集計値（未実装）** |
| parts | `newest` | 新着順 | Newest | `created_at` |
| log | `relevance` ◎ | 関連順 | Relevance | — |
| log | `newest` | 新着順 | Newest | `logged_at` |
| log | `likes_desc` | いいね順 | Most Liked | いいね集計 |
| product | `relevance` ◎ | 関連順 | Relevance | — |
| product | `registered_rigs_desc` | 登録RIGが多い順 | Most Registered | **その製品を登録しているRIG数の集計（未実装）** |
| product | `newest` | 新着順 | Newest | `release_date` |
| user | `relevance` ◎ | 関連順 | Relevance | — |
| user | `rig_count_desc` | RIG登録数順 | Most RIGs | RIG数の集計 |

◎ = 既定

### 規約

1. **`all` にはソートを出さない。** 5種別を横断して並べる基準が定義できない
2. **「人気順」という語を使わない。** 何で並んでいるかを名指しする（既裁定「ランキング表現を使わない」）
3. **既定は必ず `relevance`。** 人気系を既定にしない（Baymard: 上位が同一タイプに偏りカテゴリの幅が伝わらない）
4. **集計値が存在しない選択肢を UI に出さない。**
   「装着例が多い順」と表示するのに集計値が無い、という事故を防ぐ
5. 置き場所は**結果直上**（左サイドバーから出す）

### PC現物との差分

PC は現在 `関連度 / 新着 / いいね数 / コメント数 / 閲覧数` を**全 vertical 共通**で出している。

| 項目 | 判断 |
|---|---|
| 全 vertical 共通 | **不可。**vertical ごとに変える |
| コメント数 | ✅**落とす**（2026-08-21 イタヤ裁定）。LOG以外で意味が薄い |
| 閲覧数 | ✅**落とす**（同上）。ランキング表現に近く、かつ数字の信頼性が低い |
| 置き場所 | サイドバー → **結果直上へ移す** |

※「コメント数はLOGだけ残す」案も検討したが、**両方落とす**で確定。
LOGは `newest` と `likes_desc` で足りる。

---

## 5. 未解決・要照会

### 5-1. `size_class` — HOLD 中。動かさない

2026-07-30 主査裁定「決定済み・固定13値」と 2026-08-20 回答「これから審議」が矛盾。再照会中。
**回答が出るまで値変更・カラム設計・フォーム固定値変更を行わない。**
Contract 上は軸として定義するが、**値リストは確定させない。**

### 5-2. 値の出所 ✅**解決（2026-08-21 PC現物から実測）**

| 軸 | 出所 | 備考 |
|---|---|---|
| `series` | `rig_masters.product_line`（ブランドシリーズ名・表示用） | **マスターの値のみを許容集合とし、ユーザー自由入力は禁止。**テーブル化は保留。例: Trail Finder / Clod Buster / Gelande / Enduro。Mini-Z はここから外して `size_class` へ移した |
| `scale_of_garage` | **公開RIG数・公開ログ数の集計** | 値は閾値バケット（RIG 5台以上 / RIG 10台以上 / ログ 50件以上）。★集計値が必要 |

### 5-3. `spec_data` の自己矛盾 ★実用上の最重要

DB Research 回答内に次の2つが併存している。

- 「絞り込みは `spec_data` 側で行う」
- 「App側は当面 `spec_data` を絞り込み軸に使わない」

**両方守ると、タイヤを 1.9 / 2.2 / 2.6 で絞る手段が消える。**
`part_categories.spec_schema` が存在するので、**カテゴリごとにどのスペックを軸に昇格させるかを定義できる**はず。
**DB Research へ照会する。**

### 5-4. `log_type` の 4値 / 5値 ✅**Contract は5値で確定（主査決定）**

schema v1.6 は4値、**UI と 2026-07-28 裁定はどちらも5値**（`setting/セッティング` を含む）。
**裁定が5値である以上、Contract が4値を採る理由がない。**5値で確定する。

```
maintenance / 整備
custom      / カスタム
run         / 走行
setting     / セッティング
memo        / メモ
```

**残るのは schema 側の追随のみ。**schema v1.7 改訂提案として別途起票する（本書の管轄外）。
旧 `setup` は復活させない。

### 5-5. `fitment` の値定義

| 値 | ja | 出所 |
|---|---|---|
| `official` | 公式適合 | メーカー一次資料（`compatible_platforms`） |
| `reported` | 装着例あり | `rig_parts` の集計。**保証ではない** |
| `unknown` | 適合情報なし | 根拠なし |

- **`unknown` と「要加工」を混ぜない。**「要加工」はユーザー記録・一次情報で確認できる場合のみ別ラベル
- **既定は3つすべて表示。**`official` のみに絞るのはユーザーの選択
- **MVPでは `rig_scope` を hard filter にせず、ランキング boost として使う**（件数を減らさない）
- **UGC集計から `compatible_platforms` を自動補完しない**

### 5-6. `aliases` の正本

App正典 `parts_masters.aliases TEXT[]` と DB Research `master_aliases` テーブルの二重化。
**B-2（正規化・別名・日英）の前提条件。Contract では扱わないが、B-2 着手前に決着が要る。**

---

## 5-7. ★App依存項目レジスタ（DB Research との境界）

**Search Contract の `value source` 欄は、App が DB に対して「うちはこの列に依存している」と宣言する表でもある。**
その一覧をここに集約する。**これが App と DB Research の境界の正本。**

| 依存先 | 使う軸 | 状態 | 備考 |
|---|---|---|---|
| `rig_categories`（`rig_type='rc-car'` / `level=1`） | 車種カテゴリ | ✅確定 | 正本24件。突合せ済み・不一致ゼロ |
| `part_categories`（`level=1`） | パーツカテゴリ | ✅確定 | 正本14件。親5件のリネームはDB反映待ち |
| `rig_type` | ジャンル | ✅確定 | 正本5値。MVPは `rc-car` のみ |
| `manufacturer_id` | メーカー | ✅確定 | — |
| `rig_masters.platform_slug` / `platform_name` | シャーシ / プラットフォーム | ✅確定 | — |
| `rig_masters.product_line` | シリーズ | ✅確定 | マスター値のみ許容・自由入力禁止。テーブル化は保留 |
| `ownership_status` / `purchased_at` | 所有状態 / 購入年 | ✅確定 | — |
| `condition` / `compatible_types` | 状態 / 対応ジャンル | ✅確定 | — |
| `surface` / `weather` | 路面 / 天候 | ✅確定 | Research管轄外・App側が正本 |
| `is_discontinued` / `release_date` | 生産状況 / 発売年 | ✅確定 | — |
| `country_code` / `profiles.preferred_rig_type` | 国 / 得意ジャンル | ✅確定 | — |
| 公開RIG数・公開ログ数の集計 | 規模 | ⚠集計値が必要 | 閾値バケット。集計の実在を要確認 |
| `rig_parts` の集計 | 装着例あり / 装着例が多い順 | ⚠集計値が必要 | 未実装 |
| `compatible_platforms` | 適合（公式） | ⚠要確認 | UGC集計から自動補完しない |
| **`parts_masters.spec_data` / `part_categories.spec_schema`** | **スペック** | **🔴照会中** | **DB照会#1 A** |
| **`master_aliases` vs `parts_masters.aliases`** | **（検索の入口全体）** | **🔴照会中** | **DB照会#1 B** |
| **`log_type`** | **ログ種別** | **🔴照会中** | **DB照会#1 C**（4値/5値） |
| **`rig_masters.size_class`** | **スケール** | **🔴HOLD** | **DB照会#1 D** |

### 運用ルール

1. **App側は `value source` に無いものを検索軸にしない。** 架空のフィルタを作らない
2. **依存先が変わったら、まずこの表を直す。** 画面を先に直さない
3. **照会は単発で出さず、この表の🔴をまとめて出す。** 往復を減らす
4. **照会には必ず「なぜ必要か」「決まらないと何が作れなくなるか」を添える。**
   正典の優先順位は Charter → Schema → Rules → Category なので、
   **DB Research の出力の方が App の設計より上位。**App側が勝手に決めない
5. 回答が返ったら**この表の状態を更新して閉じる**

### 現在の照会

`claude/db-research-inquiry-spec-data-v1.md` — **DB Research 照会 #1（境界4件）**

---

## 6. 受入条件（Contract が機能しているかの判定）

1. **PC左サイドバー・モバイル絞り込み・検索APIの3つが、本書の1つの定義から生成できる**
2. **同じクエリと同じ条件で、PCとモバイルの結果件数が一致する**（表示形式は違ってよい）
3. **URLを他人に渡すと、同じ結果が再現できる**
4. **UI側のコードに、車種カテゴリ・メーカー・スケールの値リストがハードコードされていない**
5. **ソートの選択肢に、集計値が存在しないものが出ていない**
6. **`library` という語が API / URL のどこにも出てこない**
7. **表示名（日本語）が URL に出てこない**

---

## 7. PC現物との差分一覧（照合表）

Contract 確定後、`pc/myrig-search-v3.html` はこの表に従って改修する。

| # | 項目 | 現物 | Contract |
|---|---|---|---|
| 1 | vertical 名 | `Library` | **`product`（表示は「製品情報」）** |
| 2 | vertical ラベル | 英語（All / RIG / Parts / Logs / Library / Users） | **日英辞書で切替** |
| 3 | ソートの置き場所 | 左サイドバー | **結果直上** |
| 4 | ソートの選択肢 | 全 vertical 共通5種 | **vertical ごと。コメント数・閲覧数は落とす** |
| 5 | ファセット件数 | 「検索語が乗った状態では数字を出さない」 | **Search Index から取る。`約N` を許容** |
| 6 | キーワード入力 | ヘッダー＋結果ヘッダー右の二重 | **ヘッダーのみ。結果面は条件チップ** |
| 7 | 結果の提示 | ブレンド（1本のグリッド） | **種別セクション** |
| 8 | ゼロクエリ状態 | 無い | **`/search` の状態①として作る** |
| 9 | `適合` 軸 | 無い | **`fitment` 3値を新設** |
| 10 | 「メンテ・工具」等の旧カテゴリ名 | 一部旧名が残存（ledger #19） | **`part_categories` 正本14件に一致させる** |

---

## 8. 裁定の状況

### ✅ 裁定済み（2026-08-21）

| # | 項目 | 裁定 | 裁定者 |
|---|---|---|---|
| 1 | `library` → `product` への改称 | **改称する。**API/URL は `product`、UI表示は「製品情報」 | イタヤ |
| 2 | 複数選択のURL表現 | **同一key反復型**（`?manufacturer=a&manufacturer=b`） | 主査（技術判断） |
| 3 | `spec_data` の扱い | **DB Research へ照会する** | イタヤ |
| 4 | `log_type` の値数 | **5値で確定。**schema側の追随は別起票 | 主査（既裁定に従う） |
| 5 | `series` / `scale_of_garage` の出所 | **PC現物から実測して確定**（§5-2） | 主査（事実確認） |
| 6 | ソートの「閲覧数」「コメント数」 | **両方落とす** | イタヤ |
| 7 | `price_band`（価格帯） | **検索軸に置かない。**データとしては保持 | イタヤ |

### ⏳ 未解決（本書の外で決着させる）

| # | 項目 | 行き先 |
|---|---|---|
| A | `size_class` の値リスト | **HOLD中。**軸としては定義するが値は確定させない（§5-1） |
| B | `spec_data` の軸昇格ルール | **DB Research 照会中**（§5-3） |
| C | `aliases` の正本一本化 | B-2（正規化・別名・日英）の前提条件（§5-6） |

---

## 9. 出典・根拠

**MyRIG 正典**
- `mobile-feedback-ledger-v1.md` #19①（検索スコープ三重分裂）/ #19⑥（検索スコープ正典表の新設）
- 同 #24（日英2言語・MVP時点から）
- 同 #26（すべて＝ダイジェスト／種別＝無限スクロール。PC既定は別途）
- 同 #34（購入導線・価格/在庫を出さない）
- `cross-ref-category-names-v4.md`（`rig_categories` 24件 / `part_categories` 14件）
- `db-schema-answers-v1.md`（`size_class` HOLD）
- `pc/myrig-search-v3.html` の開発メモ（各軸の対応カラム。**参照実装であって正本ではない**）

**外部**
- NN/g: Defining Helpful Filter Categories and Values — https://www.nngroup.com/articles/filter-categories-values/
- Baymard: Filtering UX — https://baymard.com/learn/ecommerce-filter-ui
- Baymard: Diversity-Based "Relevance" Sort — https://baymard.com/blog/default-sort-type
- Baymard: Category-Specific Sorting — https://baymard.com/blog/category-specific-sorting
- Algolia: Searching for facet values — https://www.algolia.com/blog/product/search-for-facet-values
