# Library 設計図（言葉版）v1 — Claude 案（PROPOSAL）

- 日付: 2026-09-19 22:34 JST（生成: Cowork `ZoneInfo("Asia/Tokyo")`）
- 前提: MYRIG-20260919-117（ローカル・未 commit）/ `_decisions/2026-09-19_library-zero-base-v1.md`
- 位置づけ: **PROPOSAL**。裁定ではない。イタヤ判定後に採用分だけ 118 以降で DECISION 化する
- ⚠️ **superseded by `2026-09-19_library-ia_claude-v2.md`**（2026-09-19 22:45 JST）
- 参照した正典: `docs/ui/page-role-matrix-v1.md`（Library 節）/ `docs/schema/myrig_db_schema_v1_6.md` Domain 3 / 3-B / 6 / 7 /
  `docs/schema/db-schema-answers-v1.md` / `_decisions/2026-09-17_research-app-boundary-contract-v1.md` /
  `_decisions/2026-09-07_toplevel-surfaces-v1.md` / `_decisions/2026-08-25_search-closure-v1.md` / `_decisions/p22-b14-library-followup.md`

---

## 0. Library とは何か（一文）

**Register で使っているマスター DB を、「調べる → MyRIG の実例を見る → 公式へ行く → 買う」の順に歩ける参照面。**
Community（人が登録したもの）とは別世界。同じ entity（master）で自然に接続する。
Library は製品を「識別」させる面であって、説明し尽くす面ではない（Lite 方針。詳しくは公式へ送る）。

## 1. 階層（URL）— 既存 7 ＋ 追加 3 ＋ 後段 1

```
/library                            ① カタログトップ
/library/search?q=                  ② Library 検索              【追加】
/library/rigs                       ③ 車種カタログ
/library/rigs/c/[category]          ③' 車種カテゴリ着地          【追加＝③ の URL 化】
/library/rigs/[masterSlug]          ④ 車種 製品情報
/library/parts                      ⑤ パーツカタログ
/library/parts/c/[parent]/[sub]     ⑤' パーツカテゴリ着地        【追加＝⑤ の URL 化】
/library/parts/[masterSlug]         ⑥ パーツ 製品情報
/library/makers                     ⑦ メーカー一覧
/library/makers/[makerSlug]         ⑧ メーカー情報
/library/platforms/[platformSlug]   ⑨ プラットフォーム            【追加・後段候補】
```

作らない面と理由:
- **比較（compare）**: パーツ `spec_data` のキーが未確定（B-6）。軸が無いのに比較面を作ると偽の精度になる
- **ボディ**: Research に `bodies` があるが Library での見せ方は未照会。裁定前に階層を作らない
- **レビュー / 評価**: 意見は Community（LOG）の責務。Library は事実だけ
- **使用例の全件一覧**: Library に置かない。Browse 側（Community）へ渡す（071 の「自然な扉」）
- **価格表示**: 変動・地域差・アフィリエイト規約。購入先リンクのみ

## 2. 全面共通の文法

1. **Library ヘッダー**: サブナビ「トップ / 車種 / パーツ / メーカー」＋ **製品検索ボックス（型番直入力対応）** を全面に固定。Community のヘッダーと役割が違うことをここで示す
2. **画像スロット常設 / NO IMAGE**。Detail は最大 2 枚。実画像は `contain` 寄り（製品識別優先。端を切らない）
3. **色**: カテゴリ色なし・バッジなし。**例外だけ表示**（生産終了）。発売中・未確認・確認中は出さない
4. **一覧カード = 1 リンク（Detail へ）**。購入 CTA はカードに置かない。商取引は Detail の責務
5. **「MyRIG で N 台」= Library 独自価値**。RIG 側は今も算出可（`rigs.rig_master_id` は接続済み）。**PARTS 側は H-1 待ち**
6. **出典行**: Detail 末尾に「メーカー公表情報に基づく／最終確認 YYYY-MM／公式ページ」。説明文は転載しない（自前要約のみ）
7. **publication gate（109 §9）はスロットの中身だけ**を制御。⚠️ publication 行が無い master を一覧に出すか（text_only 扱いか非表示か）は **Research 照会**
8. **日英**: 製品名は Research 表記を主、`*_name_ja` があれば副題。UI 文言は日本語
9. **ページネーション**: 番号式（URL に `page=`）。カタログは SEO と再訪を優先し「さらに表示」にしない
10. **レイアウト**: ≥1280 は 4 列、1025–1279 は 3 列、filter は既存 `SoT_filter-sidebar`（R-05 契約: 980 以下は Drawer）

## 3. 各面

### ① カタログトップ `/library`
- 役割: 玄関。「何を調べたいか」を 10 秒で選ばせる。フィードではない
- 構成（上から）
  1. 検索（型番 / 製品名 / メーカー）。型番完全一致は Detail へ直行
  2. 扉: 車種カタログ（N 件）/ パーツカタログ（N 件）/ メーカー（N 社）/（後段）プラットフォーム
  3. パーツ親カテゴリ 14 のタイル — パーツ規模対策として最初の一段をここで踏ませる
  4. ウィジェット棚（`page_blocks` に `page_type='library_top'` を追加提案 = B14 ⑥ を schema に接続）:
     「MyRIG でよく使われている車種」（算出可）/「新しく追加された製品」/「メーカー特集」/（H-1 後）「よく使われているパーツ」
  5. 但し書き 1 行: メーカー公表情報に基づく参照データ。価格は載せない
- 出口: ② ③ ⑤ ⑦（⑨）。棚から ④ ⑥
- 依存: 件数集計 / `page_blocks` 拡張（実装フェーズ）

### ② Library 検索 `/library/search?q=`
- 役割: 製品を **識別** する検索。Community 検索とは母集団を分ける（検索 CLOSE 裁定: 製品情報は Library の責務・件数非合算）
- 入口: Library ヘッダーの検索ボックス / グローバル検索結果の「製品情報は Library で」補助導線
- 挙動: 型番（`primary_sku` / variant の `primary_sku` / `master_aliases.alias_sku`）完全一致 → 即 Detail。
  それ以外 → 3 群（車種 / パーツ / メーカー）を件数付きで。各群 5 件 ＋「もっと見る」→ ③ ⑤ ⑦ に `q` を引き継ぐ
- 依存: `master_aliases` JOIN（正本。旧 `parts_masters.aliases` 列は参照しない = HOLD 尊重）

### ③ 車種カタログ `/library/rigs` ／ ③' `/library/rigs/c/[category]`
- 役割: RIG master の一覧
- 構成: 左 filter（メーカー / RIG カテゴリ 24 / `size_class`（値は data 由来。13 値を固定しない）/ `kit_type` / `power_source` / プラットフォーム / 生産終了を含む）。
  上 toolbar（件数リアルタイム・並び: 名前 / 新着 / **MyRIG 登録数**）。グリッド 4 列 = カード v1 の RIG 版
  （メーカー / 製品名 / Item No. / 1 行 3 語 = スケール · 駆動 · kit_type / MyRIG で N 台）。番号ページネーション
- ③' は同じ面。URL とタイトル・説明 1 行だけを持つ（SEO とブックマーク用）
- 依存: 発売状態の列と語彙（Research 照会）

### ④ 車種 製品情報 `/library/rigs/[masterSlug]`
- 役割: このモデルは何か → MyRIG で誰が使っているか → 公式 → 買う
- 骨格: **既存の Visual Stage（左）＋ 製品情報（右）を維持**。装飾・情報順・購入導線だけ改める
  - 左 Visual Stage: 最大 2 枚 / NO IMAGE
  - 右 Identity: メーカー（→ ⑧）/ 製品名（JA 副題）/ Item No. / 生産終了タグ / 一言分類（スケール · カテゴリ · kit_type · プラットフォーム（→ ⑨））
  - 右下 製品・購入（**Detail 共有の `md-commerce` を使う**。Library 専用実装を持たない）: 公式ページ（`master_external_links` active）/ 購入先（`affiliate_links`。訪問者の国 JP/US/GLOBAL で 1 群・`priority` 順。他国は畳む）/ アフィリエイト注記
- 本文（上から）
  a. 基本スペック — 承認 11 キー ＋ `power_source`。空の行は出さない
  b. バリエーション — `rig_master_variants`（RTR / Kit / ボディ違い、SKU）
  c. **MyRIG での使用例** — `rigs.rig_master_id`。6 件 →「すべて見る」→ Browse RIG 一覧（base model フィルタ）。0 件は「まだ登録がありません」＋ CTA
  d. 同じシャーシのモデル — `master_relations.same_chassis`（→ ④ 他 / ⑨）
  e. 別名・旧型番 — `master_aliases`
  f. 出典・最終確認
- CTA: 「このモデルで RIG を登録」→ `/register/rig?master=`（要ログイン。Variant 選択は Register 側未実装 = H-9 なので master 単位で渡す）
- 依存: Browse 側の master フィルタ契約（Browse 裁定 037 / 039 と要整合）

### ⑤ パーツカタログ `/library/parts` ／ ⑤' `/library/parts/c/[parent]/[sub]`
- 役割: 数十万点を **2 段で絞る**（B14 ④ への回答）
- 構成: 入口は **親カテゴリ 14 のタイル**（一覧をいきなり出さない）。親を選ぶと **子 90 の chip 列 ＋ 一覧**。
  filter: メーカー / 子カテゴリ / 生産終了を含む /（XREF 後）対応プラットフォーム。**spec facet は対象外**（B-6）。
  toolbar: 件数リアルタイム・並び（名前 / 新着 /（H-1 後）使用数）・**型番直入力を常設**。グリッド 4 列 = カード v1。番号ページネーション
- ⑤' は `/library/parts/c/wheels/1-9` のように URL 化
- 依存: 実 DB は `part_categories` 0 行（Research 構築待ち）。mock は Category v1.4 の 14 / 90 で作る

### ⑥ パーツ 製品情報 `/library/parts/[masterSlug]`
- 骨格: ④ と同じ（Visual Stage ＋ 製品情報 ＋ `md-commerce`）
- Identity: メーカー / 製品名 / Item No.（`primary_sku`）/ 生産終了 / 親 · 子カテゴリ
- 本文
  a. スペック — `master_publication.spec_display_schema`（14 値）の表示定義に従う。自由キー 225 種を全部出さない
  b. 対応 — `compatible_platforms` の **Research 正本値のみ**「メーカー公表の対応」として。推測展開なし。値が無ければ **節ごと出さない**
  c. バリエーション — `part_master_variants`（色 / サイズ / 個数、SKU）
  d. **MyRIG での使用例** — H-1。mock は 0 / N の両状態。本番は H-1 解消後
  e. 別名・旧型番
  f. 出典・最終確認
- CTA: 「このパーツを登録」→ `/register/parts?master_ref=`（`db_register=false` は CTA を出さない）

### ⑦ メーカー一覧 `/library/makers`
- 役割: メーカーから入る
- 構成: 頭文字索引ではなく **「MyRIG 登録数順 → 名前順」の 1 リスト**（日英混在で頭文字が効きにくい）。
  行カード: メーカー名 / 国（列が実在すれば）/ 車種 N · パーツ N / MyRIG で N 台。ロゴは許諾前提なので文字
- 依存: `manufacturers.country` 実在要確認。`is_active` は矛盾あり（B-6）→ 使わない

### ⑧ メーカー情報 `/library/makers/[makerSlug]`
- 役割: このメーカーの製品ライン → 車種 / パーツへ（薄く作る。matrix の Later は維持しつつ索引としては置く）
- 構成: Identity（名 / 国 / 公式サイト）/ 製品ライン（`rig_masters.product_line` の値集合）/ 車種 6 件 → ③ `maker=` / パーツ（親カテゴリ別件数）→ ⑤ `maker=` / MyRIG で N 台。**説明文は書かない**

### ⑨ プラットフォーム `/library/platforms/[platformSlug]`（後段候補）
- 役割: クローラーの実態は「SCX10 III / TRX-4 / CC-02」というシャーシ単位。車種 → プラットフォーム → 同シャーシの別モデル → 合うパーツ、の中心
- 構成: 名（`platform_name` / `_ja`）/ このプラットフォームの車種（`rig_masters.platform_slug` **完全一致**。部分一致禁止）/ MyRIG で N 台 / 合うパーツ（`compatible_platforms` XREF 後のみ。それまで節を出さない）
- 後段の理由: 価値は最大だが、パーツ側の接続が HOLD なので今作ると「車種の再掲」で終わる。RIG 側だけで先行する判断はあり

## 4. 世界間の扉（リンクマップ）

| 起点 | 行き先 | 文言 | 条件 |
|---|---|---|---|
| Community RIG Detail | ④ | ベースモデル | `rig_master_id` あり（既存 Library Bridge） |
| Community PARTS Detail | ⑥ | 製品情報 | `parts_master_id`（**H-1 後**）。それまで扉なし |
| Owner Garage PARTS / RIG Detail | ⑥ / ④ | 製品情報 | 情報のみ。購入 CTA は Owner 面に出さない（Q-5 既裁定） |
| ④ ⑥ 使用例 | Browse RIG 一覧 | すべて見る | Browse の master フィルタ（要確認） |
| ④ ⑥ | 外部 | 公式ページ | `master_external_links` active |
| ④ ⑥ | 外部 | 購入先 | `affiliate_links` is_active・国別 |
| ④ ⑥ | Register | このモデルで RIG を登録 / このパーツを登録 | 要ログイン・`db_register` |
| ④ | ④ 他 / ⑨ | 同じシャーシ | `master_relations.same_chassis` |
| ④ ⑥ ③ ⑤ | ⑧ | メーカー名 | — |
| ⑧ | ③ ⑤ | 車種 / パーツをすべて見る | `maker=` |
| ① ③ ⑤ ヘッダー | ② | 検索 | — |
| グローバル検索 | ② | 製品情報は Library で | 補助導線・件数非合算（検索 CLOSE 裁定） |
| Home `library_links` block | ① | — | 既存 |
| Browse カテゴリ（Community） | ③' / ⑤' | この種類の製品を調べる | 任意の soft door。Browse の見た目は変えない |

## 5. 依存・HOLD 一覧（Library 側から見た）

| 依存 | 影響する面 | 扱い |
|---|---|---|
| **H-1** PARTS master 接続 | ⑥ 使用例 / ⑤ 使用数ソート / Community PARTS → ⑥ の扉 / ⑨ パーツ節 | mock は両状態。本番は解消後 |
| `compatible_platforms` XREF | ⑥ 対応節 / ⑤ 対応フィルタ / ⑨ パーツ節 | 値が無ければ節・filter を出さない |
| `part_categories` 0 行 | ⑤ ⑤' | mock は Category v1.4 |
| publication 行なし master の一覧表示可否 | ③ ⑤ ② | **Research 照会** |
| 発売状態の列・語彙 | 全面（生産終了タグ） | **Research 照会** |
| `manufacturers.country` / `is_active` | ⑦ ⑧ | 前者は要確認、後者は使わない |
| `master_aliases` 旧列 HOLD | ② ④ ⑥ | 新規参照は `master_aliases` のみ |
| Browse の master フィルタ | ④ ⑥ の「すべて見る」 | Browse 裁定と整合を要確認 |
| `page_blocks.page_type='library_top'` | ① | schema 変更の提案（実装フェーズ） |
| H-9 Variant 選択導線 | ④ ⑥ CTA | master 単位で渡す |

## 6. 既存 matrix / 既存 mock からの変更提案（裁定が要るもの）

1. 一覧の 2-CTA（View Master ＋ 購入先を見る）→ **カード 1 リンク**。購入は Detail の責務
2. Maker Detail は matrix 上 Later だが、⑧ を **薄く** 作る（製品ラインの索引として要る）
3. 追加ページ: ② ③' ⑤'（MVP）/ ⑨（後段）
4. Library トップを `page_blocks` 管理へ（B14 ⑥ を schema に接続）
5. Detail の購入ブロックを Library 専用 `master-buy-*` から **Detail 共有 `md-commerce`** へ
6. `IMAGE PENDING` → `NO IMAGE`（117 D3 で裁定済み）

## 7. 作る順序（PC → Mobile）

カード v1（RIG サムネ撤去）→ ⑤ パーツカタログ（親タイル → 子 chip → 一覧）→ ⑥ パーツ製品情報（既存骨格の再評価）
→ ② 検索（⑤ の toolbar と共通部品）→ ③ ④ 車種（写す）→ ① トップ → ⑦ ⑧ → 既存 14 面収束 → Mobile → ⑨
