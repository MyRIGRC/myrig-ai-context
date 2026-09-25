# Library 設計図（言葉版）v2 — Claude 案（PROPOSAL）

- 日付: 2026-09-19 22:45 JST（生成: Cowork `ZoneInfo("Asia/Tokyo")`）
- 前提: MYRIG-20260919-117（ローカル・未 commit）/ `_decisions/2026-09-19_library-zero-base-v1.md`
- 位置づけ: **PROPOSAL**。裁定ではない。v1（`2026-09-19_library-ia_claude-v1.md`）を GPT レビュー（10 点）＋ Claude 自己監査（2 点）で改訂。v1 は supersedes
- 参照正典: v1 と同じ ＋ `docs/ui/page-role-matrix-v1.md` §7（ランキング全廃）/ `docs/ui/mobile-component-contract-v0.5.md` §4（禁止語）

---

## 0. Library は 3 層

```
FIND      製品を探す      ① トップ / ② 検索 / ③ 車種カタログ / ⑤ パーツカタログ / ⑦ メーカー一覧（＋カテゴリ着地 = preset state）
IDENTIFY  製品を理解する  ④ 車種 製品情報 / ⑥ パーツ 製品情報 / ⑧ メーカー情報 /（将来）⑨ プラットフォーム
BRIDGE    次へ渡す        各 Detail から MyRIG の実例 / Community RIG / Register / 公式 / 購入先 へ。Library 内で完結させない
```

Browse が「眺める場所」、Library は「見つける場所」。

## 1. Sitemap（MVP はこれ以上増やさない）

```
/library                                  ① Library Top（製品 DB のコントロールパネル）
/library/search?q=&type=                  ② Catalog Search（すべて / 車種 / パーツ / メーカー）
/library/rigs                             ③ RIG Catalog
/library/rigs/category/[category]           ③ の preset state（専用 UI なし）
/library/rigs/[masterSlug]                ④ 車種 製品情報
/library/parts                            ⑤ PARTS Catalog
/library/parts/category/[parent]/[sub]      ⑤ の preset state（専用 UI なし）
/library/parts/[masterSlug]               ⑥ パーツ 製品情報
/library/makers                           ⑦ Maker Directory
/library/makers/[makerSlug]               ⑧ メーカー情報（製品索引）
FUTURE
/library/platforms                        ⑨ プラットフォーム一覧
/library/platforms/[platformSlug]         ⑨ プラットフォーム（First-Class 候補。compatibility HOLD 解消まで本線化しない）
```

Product Line / Variant / Alias / 対応規格は **filter・state・section** で表現する。ページを増殖させない。
作らない面（v1 と同じ）: 比較 / ボディ / レビュー / 使用例の全件一覧（Browse へ）/ 価格。

## 2. 全面共通の文法

1. Library ヘッダー: サブナビ「トップ / 車種 / パーツ / メーカー」のみ。**検索窓は lib-hd に置かない**。検索は Top / 各 Catalog の見出し側にある同じ検索部品（scope: all / rig / parts）。② はその結果面（09-24 GPT レビューで確定）
2. 画像スロット常設 / NO IMAGE。Detail は最大 2 枚。実画像は `contain` 寄り（製品識別優先）
3. 色: カテゴリ色なし・バッジなし。例外だけ表示（生産終了）。運用状態は出さない
4. **一覧カード = Detail への単一入口**。購入 CTA はカードに置かない
5. **「MyRIG で N 台」は事実の添え物として出す（件数）。並び替え軸・上位 N 棚にはしない**（ランキング全廃方針）。RIG 側は算出可、PARTS 側は H-1 待ち
6. **UI 文言に「マスター」を使わない**（mobile contract §4 禁止語）。「製品情報 / 車種 / パーツ / メーカー」で通す。Master は内部名
7. 出典行: Detail 末尾に「メーカー公表情報に基づく／最終確認 YYYY-MM／公式ページ」。説明文は転載しない
8. 画像スロットの中身は 109 §9 の画像公開判定だけ。Master 自体の一覧掲載可否は B-4 の公開面 gate（publication 行なしは Research 照会）
9. 日英: 製品名は Research 表記を主、`*_name_ja` があれば副題
10. ページネーション番号式。≥1280 は 4 列 / 1025–1279 は 3 列。filter は既存 `SoT_filter-sidebar`（980 以下 Drawer）
11. **Variant = state**。`rig_master_variants` / `part_master_variants` は Detail 内の切替（SKU・画像が変わるなら表示だけ変わる）。Variant 別 URL は作らない
12. **Library Top の CMS 化は今はしない。** mock は自由に並べる。B14 ⑥（並び替え可能）の思想は残し、`page_blocks` への接続は実装フェーズで別設計（`page_type` 追加だけでは済まない。Master 配信の block / data source が要る）

## 3. 各面

### ① Library Top `/library` — 製品 DB のコントロールパネル
Hero 画像・Editorial なし。開いた瞬間に「検索するか、分類から潜るか」ができる。
1. 検索（製品名・型番・メーカー）— 大きく
2. 扉: 車種を探す（N 件）/ パーツを探す（N 件）/ メーカーから探す（N 社）
3. パーツカテゴリ 14 ／ RIG カテゴリ 24 の Directory（ショートカット。関所ではない）
4. 棚（下段）: 「新しく追加された製品」/ 「メーカー特集」（編集）。⛔ 「よく使われている」「人気」棚は置かない
5. 但し書き 1 行

### ② Catalog Search `/library/search?q=&type=`
- 切替: **すべて / 車種 / パーツ / メーカー**。「すべて」は上位結果を混在。車種 / パーツを選ぶと **③ / ⑤ と同じ一覧部品**で出す（検索結果専用カードを作らない）
- 型番一致（`primary_sku` / variant `primary_sku` / `master_aliases.alias_sku`）は **最上位の強い候補として表示**。⛔ 自動で Detail へ転送しない（SKU は境界キー不適格。NULL / N/A / 重複あり）
- 入口: Top / Catalog 見出しの検索窓（同じ部品。`type` で scope）/ グローバル検索の「製品情報は Library で」補助導線（件数非合算）
- Facet 件数は Q8: 初期状態だけ概数、条件が乗ったら各 Facet の件数を消す。総件数は 10,000+ 打ち切り
- 依存: Search Service の Catalog 論理 index ＋ Library profile（既存設計）/ `master_aliases` JOIN

### ③ RIG Catalog `/library/rigs`（＋ `/category/[category]` preset）
- 上: 検索 / 型番（②へ）。Directory: RIG カテゴリ 24（chip）
- 左 filter: メーカー / カテゴリ / `size_class`（値は data 由来）/ `kit_type` / `power_source` / プラットフォーム / 生産終了を含む
- toolbar: 件数リアルタイム・並び **名前 / 新着**（⛔ 登録数順なし）
- グリッド 4 列 = カード v1 の RIG 版（メーカー / 製品名 / Item No. / スケール · 駆動 · kit_type / MyRIG で N 台）
- preset URL を開いたら「そのカテゴリを選択した状態の ③」。タイトルと説明 1 行だけ差し替え

### ④ 車種 製品情報 `/library/rigs/[masterSlug]`
- 骨格: 既存 Visual Stage（左）＋ 製品 Identity / Spec（右）を維持。装飾・情報順・購入導線を改める
- 右 Identity: メーカー（→ ⑧）/ 製品名（JA 副題）/ Item No. / 生産終了 / スケール · カテゴリ · kit_type · プラットフォーム（→ ⑨ 将来）
- 右 製品・購入: **`md-commerce` 共有（state / behavior / aria / affiliate 注記は共通）＋ Library variant（カタログ寄りの見た目）**。公式ページ（`master_external_links` active）/ 購入先（`affiliate_links` 訪問者の国で 1 群・`priority` 順）。「製品を見る」が主、「買う」を主にしない
- 本文: 基本情報（承認 11 キー ＋ `power_source`。空行なし）→ バリエーション（state 切替）→ MyRIG で使われている（6 件 → すべて見る → Browse RIG 一覧 base フィルタ。0 件は「まだ登録がありません」＋ CTA）→ 関連製品（`same_chassis`）→ 別名・旧型番 → 出典
- CTA: 「このモデルで RIG を登録」→ `/register/rig?master=`（H-9: master 単位で渡す）

### ⑤ PARTS Catalog `/library/parts`（＋ `/category/[parent]/[sub]` preset）
- **同時に置く**: 検索 / 型番 ─ カテゴリ Directory 14（ショートカット）─ 現在の結果一覧。何も選ばなければ全 PARTS（新着順）
- 親を選ぶと子 90 の chip 列が現れて絞られる（関所ではない。製品名・型番を知っている人はカテゴリを触らない）
- 左 filter: メーカー / 子カテゴリ / 生産終了を含む /（XREF 後）対応プラットフォーム。spec facet は対象外（B-6）
- toolbar: 件数リアルタイム・並び **名前 / 新着**（使用数順は置かない）
- グリッド 4 列 = カード v1。番号ページネーション
- 依存: 実 DB `part_categories` 0 行 → mock は Category v1.4 の 14 / 90

### ⑥ パーツ 製品情報 `/library/parts/[masterSlug]`
- 骨格 ④ と同じ。Identity: メーカー / 製品名 / Item No.（`primary_sku`）/ 生産終了 / 親 · 子カテゴリ
- 本文: 基本情報（`spec_display_schema` 14 値の表示定義。自由キーを全部出さない）→ 対応（`compatible_platforms` Research 正本値のみ「メーカー公表の対応」。無ければ節ごと出さない）→ バリエーション（state）→ MyRIG で使われている（H-1。mock は 0 / N）→ 関連製品（要確認）→ 別名 → 出典
- CTA: 「このパーツを登録」→ `/register/parts?master_ref=`（`db_register=false` は出さない）

### ⑦ Maker Directory `/library/makers`
- **名前順 Default**。検索 ＋（列が実在すれば）地域 filter
- 行: メーカー名 / 車種 N · パーツ N。ロゴなし・文字だけ。⛔ 登録数順・人気順にしない
- 依存: `manufacturers.country` 要確認 / `is_active` は使わない

### ⑧ メーカー情報 `/library/makers/[makerSlug]` — 製品索引
- 「メーカーについて読む」ページではなく「そのメーカーから製品を探す」ページ。沿革・紹介文なし
- 上: 名 / 公式サイト → / 車種 N · パーツ N / MyRIG で N 台（事実として）
- 車種を見る: 主要 Product Line（`rig_masters.product_line` の値集合）→ ③ `maker=`
- パーツを見る: 親カテゴリ別件数 → ⑤ `maker=`

### ⑨ プラットフォーム（FUTURE First-Class）
- 将来: このプラットフォームの車種 ＋ 使える PARTS ＋ MyRIG の RIG ＋ 関連 LOG が繋がった瞬間に最強の面になる
- 今は PARTS compatibility が HOLD → 作ると「車種の再掲」で終わる。設計図に残し本線化しない
- 実装時の規則: `platform_slug` 完全一致のみ（部分一致禁止・318 件事故）

## 4. 世界間の扉（v1 と同じ。要点）

| 起点 | 行き先 | 条件 |
|---|---|---|
| Community RIG Detail「ベースモデル」 | ④ | `rig_master_id`（既存 Bridge） |
| Community PARTS Detail「製品情報」 | ⑥ | H-1 後。それまで扉なし |
| Owner Garage Detail | ④ / ⑥ | 情報のみ。購入 CTA なし（Q-5） |
| ④ ⑥「すべて見る」 | Browse RIG 一覧 | Browse の master フィルタ要確認 |
| ④ ⑥ | 公式 / 購入先 / Register | `master_external_links` / `affiliate_links` / `db_register`・ログイン |
| ④ ⑥ ③ ⑤ | ⑧ | メーカー名 |
| ⑧ | ③ ⑤ | `maker=` |
| グローバル検索 / Home `library_links` | ② / ① | 既存契約 |
| Browse カテゴリ | ③ / ⑤ preset | soft door（任意） |

## 5. 依存・HOLD（v1 から差分のみ）

- 追加: **ランキング全廃**（matrix §7）→ 登録数順ソート・上位棚を置かない。件数表示は添え物として可（イタヤ確認事項）
- 追加: **禁止語「マスター」**（mobile contract §4）→ UI 文言から除外
- 削除: `page_blocks.library_top` → 実装フェーズで別設計
- 変更: SKU 完全一致の自動転送 → 候補表示のみ

## 6. v1 → v2 変更一覧

| # | 変更 | 出所 |
|---|---|---|
| 1 | SKU 一致は候補表示。自動 Detail 遷移なし | GPT |
| 2 | カテゴリ着地 = Catalog の preset state。URL は `/category/` | GPT |
| 3 | PARTS 親カテゴリ 14 は Directory shortcut。関所にしない | GPT |
| 4 | 一覧カード = 単一入口（v1 と同じ。確認） | GPT / Claude |
| 5 | Maker 一覧 Default 名前順 | GPT（＋ランキング全廃） |
| 6 | Maker Detail = 製品索引（v1 と同趣旨。表現を強めた） | GPT |
| 7 | Variant = state。別 URL なし | GPT |
| 8 | Platform = FUTURE First-Class。`/library/platforms` 一覧も将来枠 | GPT |
| 9 | `page_blocks.library_top` は今は schema 化しない | GPT |
| 10 | 117 D3 の publication 記述を画像スロット限定へ是正（実施済み） | GPT |
| 11 | 「MyRIG 登録数」ソート / 「よく使われている」棚を撤去。件数は添え物 | Claude 自己監査（ランキング全廃） |
| 12 | UI 文言に「マスター」不使用 | Claude 自己監査（禁止語） |
| 13 | `md-commerce` 共有 ＋ Library variant 可 | GPT |
| 14 | Library Top = コントロールパネル（Hero / Editorial なし） | GPT（v1 ① と同趣旨） |

## 7. 作る順序（PC → Mobile）

カード v1 確定（RIG サムネ撤去・`contain`）→ ⑤ PARTS Catalog（検索 ＋ Directory ＋ 一覧を同時に置く）→ ⑥ パーツ製品情報（既存骨格の再評価）
→ ② 検索（⑤ と同じ一覧部品）→ ③ ④ 車種 → ① Top → ⑦ ⑧ → 既存 14 面収束 → Mobile → ⑨（HOLD 解消後）
