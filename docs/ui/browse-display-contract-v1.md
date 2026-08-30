# Browse 表示契約 v1

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
> ただし **§3.2 の禁止事項と §4.3 の定義場所は L1**（今回の事故の再発防止）。
>
> **作成:** 2026-08-29 (JST) / Cowork（Claude・主査）
> **根拠:** Playwright 実測（390 / 1280 / 1440px・2026-08-29）＋ `docs/ui/mobile-component-contract-v0.5.md` §5
> **上位正典:** `mobile-component-contract-v0.5.md` §5 カード契約 / `SoT_card-components.js`
> **対象:** Browse系4面 — Vehicle Category Top / Parts Category Top / RIG Root / Parts Root（PC・モバイル）
> **非対象:** Search Results（CLOSE済み・触らない）、Feed、Garage、Library
>
> 🔴 **2026-08-29 V5裁定 / 2026-08-30 確定により一部が失効している。**
> **末尾「§11 V5差分」と「§12 4軸の表示グループ」を先に読むこと。**
> Sidebar / Breadcrumb / RIG ROOT / WORLD階層 / WORLD selector / Root current は
> `docs/ui/browse-sidebar-v5.md` が正典。
>
> 🔴 **2026-08-30 / revision 037: 036 の CLOSE を一部取り消して再オープン中。**
> 再オープンは「4軸の意味・表記」と「Root current の見せ方」だけ。他は凍結。

---

## 0. なぜこの文書が必要になったか

2026-08-29 の実測で、Browse系の同一部品が **5系統に分裂**していることが判明した。

| カード部品 | 定義場所 | 状態 |
|---|---|---|
| `myrig-*-card variant="browse"` | `SoT_card-components.js`（正典） | PC Category Top / PC Home が使用。**正しい** |
| `.gcard` / `.m-card-*` | `css/mobile-shell.css`（共有） | Search・Garage等が使用。正典 §5 準拠 |
| `.bp-card` | **HTML 2ファイルにインライン重複** | モバイルBrowseが使用。共有CSSに無い |
| `.mec-card` | `js/parts-category-demo.js` 内 | PC Partsのみ。**正典コンポーネントを使わず手書き** |
| `.edit-card` | **PC 4ファイルにインライン重複・既に乖離** | 記事カード。別途処理 |

作業者（人・AI問わず）が「Category Topと揃えて」と指示されても、**揃える先が特定できない**
状態だった。開いたファイルの `<style>` を真似るしかないため、毎回違う結果になる。
これは指示や担当者の問題ではなく、参照先が存在しないという構造的欠陥である。

本書はその参照先を1つに定める。

---

## 1. レーン定義（統一してはならない差）

MyRIGは PC と モバイルで**カードの実装方式が異なる**。これは `mobile-component-contract-v0.5.md`
§5 が定めた設計であり、揃えるべき「ズレ」ではない。

| レーン | 実装方式 | 実体 |
|---|---|---|
| **PC** | Web Components | `<myrig-rig-card variant="browse">` 等。`SoT_card-components.js` |
| **モバイル** | CSS クラス | `.bp-card`。§5「CSSクラス方式・Web Components不使用 — Next.js SSR互換」 |

両者は §5 の対応表で紐づく。`.m-card-rig` → `myrig-rig-card ＋ 用途別variant（sm/md/lg/xl/browse系）`。
**browse系variantは正典が最初から想定していたものであり、新規発明ではない。**

---

## 2. 基準画面（数値の正）

> **Rock Crawler Category Top（PC / モバイル）を基準画面とする。**
> Browse系の寸法・文字組み・色・余白は、すべて基準画面の実測値を正とする。

- 本書は**具体的な数値を固定しない。** 数値を文書に書き写すと、基準画面が変更された
  瞬間に文書が嘘になり、しかも誰も気づかない
- 判定は `_audit/browse_contract_check.py` が**実行時に基準画面を実測し**、
  他面と比較して行う
- 基準画面を意図的に変更した場合、契約は自動追従する。意図しない変更は差分として検出される
- 付録Aに 2026-08-29 時点の実測スナップショットを置くが、**判定には使わない**（参考値）

対象ファイル（**本契約による修正では変更しない**）:

- `browse-category.html`（§4.3 のCSS移送を除く）
- `pc/myrig-browse-category-v3.html`

あわせて HOME（`index-e-roomclip.html` / `pc/myrig-home-v3.html`）も回帰基準として固定する。

---

## 3. PC カード契約

### 3.1 使用コンポーネント

| 用途 | 記述 |
|---|---|
| 標準棚のRIG | `<myrig-rig-card variant="browse" …>` |
| 標準棚のPARTS | `<myrig-part-card variant="browse" …>` |
| 標準棚のLOG | `<myrig-log-card variant="browse" …>` |
| ヒーロー棚 | `variant="browse_hero"` |
| 高密度棚 | `variant="browse_sm"` |

`SoT_card-components.js` L96 に
`/* ── browse card shared CSS (INDEX / Category Top / Search) ── */` と明記されており、
Category Top はこのvariantの想定利用先である。

`browse_md` は `browse` のエイリアス。どちらでもよいが `browse` に統一する。

### 3.2 禁止【L1】

1. **`.mec-card` / `.mec-section` / `.mec-rail` / `.mec-badge` / `.mec-tabs` / `.mec-stage`**
   の使用および存置。正典コンポーネントを迂回する手書き実装であり、
   同じ棚を別の見た目にする直接の原因になった
2. **生タグ `<strong>` / `<small>` によるカード内文字組み。**
   フォントサイズが親からの継承依存になる（実測で `small` が `11.6667px` という
   トークンに存在しない値になっていた）
3. **PC側HTMLの `<style>` ブロックでカード内部を上書きすること。**
   `pc/myrig-browse-parts-v3.html` L19 に既に
   「カード内部は SoT_card-components.js に委譲。上書きしない。」と明記されている
4. **JSによる `main` / `aside` / ページルートの `innerHTML` 全置換で、
   正典コンポーネントで書かれた静的マークアップを破棄すること。**
   `js/parts-category-demo.js` がこれを行い、`<myrig-part-card variant="browse">` で
   正しく書かれていた PC Parts Root を `.mec-card` へ差し替えていた

### 3.3 カード内部仕様の変更

`SoT_card-components.js` が正典。変更は `mobile-component-contract-v0.5.md` §7-1 のとおり
**GPTクロスチェック対象**。本書では扱わない。

---

## 4. モバイル カード契約

### 4.1 使用クラス

| variant | クラス | 用途 |
|---|---|---|
| 標準 | `.bp-card` | 通常の横スクロール棚 |
| コンパクト | `.bp-card.bp-card--sm` | 車種・製品など密度の高い棚 |
| ヒーロー | `.bp-card.bp-card--hero` | 特集棚 |

子要素は `.bp-card__ph`（画像）/ `.bp-card__tag`（種別バッジ）/ `.bp-card__b`（ブランド）
/ `.bp-card__t`（タイトル）/ `.bp-card__m`（メタ）。**この構成と順序を変えない。**

### 4.2 種別バッジ

`.bp-card__tag--rig` / `--parts` / `--log`。色は `--cat-rig` / `--cat-parts` / `--cat-log` と、
対の `--cat-*-on`（文字色）を**必ずセットで**使う（`mobile-component-contract-v0.5.md` §2）。

> ⚠️ 適用範囲は §8-1 の裁定待ち。

---

### 4.3 定義場所【L1】

`.bp-card` 系CSSは **共有CSS（`css/mobile-shell.css`）に1箇所だけ**置く。
HTMLの `<style>` ブロックに書かない。

正典 §5 の互換併記方式に従い、正典命名を併記する。

```css
.bp-card, .m-card-rig--browse { … }
```

> 2026-08-29 時点では `browse-category.html` と `browse-parts.html` の
> 2ファイルにインライン重複していた（内容は一致、`:nth-child(n+5)` の1ルールのみ差）。
> **重複した瞬間に乖離は時間の問題になる。** 実例として `.edit-card` は
> PC 4ファイルに重複し、既に書式が乖離している。

---

## 5. 棚・見出し契約

| 面 | 棚 | セクション見出し |
|---|---|---|
| モバイル | `.bp-shelf` | `.bp-sec` > `.bp-sec__hdr` > `.bp-sec__t` ＋ `.bp-sec__sub` |
| PC | `.shelf` > `.shelf__rail` | `.bs-head` > `.bs-title` / `.bs-sub` / `.bs-more` |

- `.bp-shelf` は `padding-left` と `scroll-padding-left` を**必ず同値**にする。
  片方だけだと、右へスワイプしてから左端へ戻したとき1枚目が見出しと揃わない
- 全件導線が未接続の場合はリンクにせず
  `<span class="ins__more is-pending" aria-disabled="true">全件導線は準備中</span>`
- `.mec-rail` / `.mec-section > header` は使わない（§3.2）

---

## 6. ローカルナビ契約

### 6.1 項目と順序

🔴 **2026-08-30 / revision 037 改訂: 4軸は「投稿種別の切替」ではなく
「現在のスコープを起点にした関係ビュー」である。**
裁定原本: `_decisions/2026-08-30_browse-axis-relation-view-v1.md`

| 面 | スコープ | 項目 |
|---|---|---|
| RIG ROOT（すべてのRCカー） | RCカーWORLDの全RIG | **トップ / RIG / LOG** |
| RIG CATEGORY（Rock Crawler） | Rock CrawlerのRIG | **トップ / RIG / パーツ / LOG** |
| PARTS ROOT（すべてのパーツ） | RCカーWORLDの全パーツ | **トップ / パーツ / LOG** |
| PARTS CATEGORY（モーター・ESC） | モーター・ESCのパーツ | **トップ / パーツ / LOG** |

🔴 **全面共通の「4軸」は失効。面ごとに本数が変わる【L1】**
判断基準は entity の対称性ではなく **その軸を主ナビとして押したくなるか**。

| | 性質 | 置き場所 |
|---|---|---|
| **LOG** | **閲覧モード**（その場所に関する活動を見る） | **全面のナビに常設** |
| RIG↔PARTS 相互参照 | **発見コンテンツ**（眺めて気づく） | **必要な面だけ。原則は棚** |

**軸化の基準は階層差ではなく「閲覧需要の非対称性」。** 構造的な対称性で軸を決めない。

| 方向 | 需要 | 置き場所 |
|---|---|---|
| RIG Category → PARTS | 大量閲覧したい（どんなタイヤ・ESC・サーボが使われているか） | **軸** |
| PARTS Category → RIG | 参考・発見が中心 | **棚** |
| ROOT 同士の相互参照 | 大量閲覧軸としては弱い | **棚** |

**Root は構造上の Hub。** あれもこれも機能を持たせない。

🔴 **PARTS系の LOG軸は UI として採用するが、データ取得方式は PENDING。**
意味は「当該パーツ／パーツカテゴリについて記録されたLOG」。現行DBに part↔log の
直接関係が無いため、**`装着RIG → そのRIGの全LOG` を代替して「関連LOG」と偽装しない**（§6.1-c）。

**ラベルは素の `RIG` / `パーツ` / `LOG` のみ。ナビ側で関係を修飾しない。**
厳密な関係は押した先の棚見出し・サブ・表札が担う。

第1タブの語は **「トップ」**（「ホーム」はMyRIG全体HOMEと衝突、「概要」は説明ページに見える）。

**失効:** 全面共通の4軸 / `使用パーツ`・`装着RIG`・`RIGのLOG`・`装着RIGのLOG` のナビ表記 /
「RIG Root / Parts Root はローカルタブを置かない」（2026-08-29 に失効済み）。

### 6.1-b 関係の定義【L1】

`rig_parts.removed_at IS NULL`（＝**現在装着中のみ**）を関係条件とする。過去の装着は含めない。

| 軸 | 定義 |
|---|---|
| RIG | scope内のRIG数 |
| 使用パーツ | scope内RIGに `rig_parts.removed_at IS NULL` で結合する DISTINCT `part_id` |
| LOG（RIG系） | scope内RIGの `rig_id` に**直接**紐づく DISTINCT log。棚見出しでは `RIGのLOG` |
| パーツ | scope内のPARTS数 |
| 装着RIG | scope内PARTSに `rig_parts.removed_at IS NULL` で結合する DISTINCT `rig_id` |
| LOG（PARTS系） | 上記「装着RIG」の `rig_id` に紐づく DISTINCT log。棚見出しでは `装着RIGのLOG` |

### 6.1-c 🔴【L1】関係を名乗るときは、DBで辿れる経路だけを名乗る

`maintenance_logs` は `rig_id`（**NULLABLE**）を持つが **`part_id` を持たない**
（`docs/schema/myrig_db_schema_v1_6.md`）。よって **PARTS → LOG は直接関係ではない。**

- ❌ `パーツのLOG` / `関連LOG` / `そのパーツを扱っているLOG`
- ⭕ **`装着RIGのLOG`**（`パーツ → 装着RIG → そのRIGのLOG` の2ホップ）

**ただしこれを名乗る場所は4軸ラベルではなく、押した先の棚見出し・サブである。**
4軸ラベルは素の `LOG`（§6.1-e）。`rig_id` が NULLABLE で常に部分集合になることも、
棚のサブで導出を書くことで担保する。

### 6.1-e 🔴【L1】ナビは「何を見るか」だけ。厳密な関係は押した先で説明する

**ラベルに定義を全部しゃべらせない。**

| 場所 | 役割 |
|---|---|
| 4軸ラベル | 何を見るかだけを短く示す（`使用パーツ` / `装着RIG` / `LOG`） |
| 押した先の棚見出し・サブ | どういう関係で集まっているかを厳密に説明する |

一度 `RIGのLOG` / `装着RIGのLOG` を4軸ラベルに置いたが、実画面で
**「仕様書としては正解、ナビとしては重い」**と判断して同日中に短く戻した。
`装着RIGのLOG` は1語に「装着」「RIG」「LOG」の3概念が積まれている。
**いちばん重い1語だけを落とし、効いている `使用パーツ` / `装着RIG` は残す。**
`関連LOG` へ丸めるのは §6.1-c 違反なので採らない。

将来 LOG と part を直接紐づける仕様ができた時点で「パーツのLOG」へ昇格できる。それまで昇格させない。

### 6.1-d 🔴【L1】関係件数を面ごとに手打ちしない

- **関係件数は1か所へ集約する。** HTMLごとに数字を散在させない
- 実DB値が無い間はモック集計値でよいが、**MOCK値であることをコード上に明記する**
- **同じ scope では同じ関係件数を返す**
- **使用パーツ ≤ 全パーツ** / **装着RIG ≤ 全RIG** を必ず満たす
- 主役軸の件数は Directory の同カテゴリ件数と**同じ出所から取る**

> 2026-08-30 時点、件数は PC 4か所（HTML 3ファイル ＋ `js/parts-category-demo.js`）に
> 直書きされ、関係で絞られていない値が放置されていた。
> 036 で扱った「visible 判定が5か所に分散」と同じ形である。

### 6.2 挙動

同一ページ内の `data-view` による表示切替。

- **Search Results へ遷移させない**
- **ページ内アンカースクロールにも戻さない**
- **どの棚が残るかは「表示グループ」で決める。§12 を参照**（🔴 2026-08-30 追加）

> 根拠: `search-results.html` L893-903 の `AXES` で `scat` は scope:rig、`pcat` は scope:parts。
> `visibleAxes()`（L919-923）と `dropHiddenFilters()`（L928-934）により、
> `type=log&scat=…` / `type=parts&scat=…` / `type=rig&pcat=…` はカテゴリ条件が
> 削除され全件へ飛ぶ。既存HOLD「カテゴリ条件付きLOG一覧・カテゴリ別Parts一覧は
> 現行Searchで安全に表現できない」のとおり、これらのSearch遷移は作らない。

### 6.3 視覚

基準画面（Rock Crawler）の値を正とする（§2）。`.mec-tabs` は廃止し、
モバイルは `.bp-local-nav`、PCは `.category-side__group` と同一の文法へ揃える。

---

## 7. 検証

`_audit/browse_contract_check.py`（myrig-mockup 側）が機械判定する。

### 7.1 判定内容

1. **基準画面を実行時に実測**し、他のBrowse面と突き合わせる
2. `.mec-*` が1つも残っていないこと
3. カードのDOM構成（子要素のクラスと順序）が基準と一致すること
4. カード幅・画像比率・角丸・文字サイズ・font-weight・line-height・margin が基準と一致すること
5. ローカルナビの項目・順序・高さ・文字・下線が基準と一致すること
6. 棚の `padding-left` と `scroll-padding-left` が同値であること
7. PCサイドバーとモバイルカテゴリシートが同じカテゴリデータを使っていること
8. パーツ14分類・RC24分類がすべて表示されていること
9. 未制作カテゴリが非リンクかつ「準備中」表示であること
10. 自己リンク・`href="#"`・カテゴリ条件が落ちるSearch遷移が無いこと
11. 横スクロールが発生していないこと / pageerror が0件であること

### 7.2 回帰ゲート

`mobile-component-contract-v0.5.md` §7-2 / §7-3 を流用する。

- **第一基準:** 基準画面4面（Rock Crawler PC・モバイル、HOME PC・モバイル）の
  computed value が修正前後で完全一致
- 文字輪郭のみの差分に限り再判定（diff ratio ≤ 0.0005 / bounding box ±0.5 CSS px）
- 文字輪郭外の差分・折返し変更・1px以上の位置差 = **不合格**
- 前提: 同一Chromium版 / 同一DPR / フォント読込完了 / 画像decode完了 / animation停止後

### 7.3 運用【重要】

**修正の前にチェッカーを走らせ、緑であることを確認してから修正に入る。**
修正後も緑であることをもって合格とする。これにより作業者自身の作業も検証対象になる。

> ⚠️ `compare.html` のPNG書き出しは、フォント読込完了を待たず固定250msでキャプチャするため、
> 見出しとサブタイトルが重なって見えることがある。**書き出し画像でレイアウト不具合を
> 判断しない。** 2026-08-29 の実測では、該当箇所の重なりは全面で0件だった
> （見出しとサブの間隔 2.0〜3.0px、右リンクとの水平余白 69〜142px）。

---

## 8. 裁定待ちHOLD

### 8-1. モバイル Rock Crawler カードの種別バッジ欠落

実測（2026-08-29）:

| 面 | バッジ |
|---|---|
| Rock Crawler Category Top（モバイル） | **なし** |
| Parts Root（モバイル） | あり |
| Motor・ESC Category Top（モバイル） | あり |
| Rock Crawler Category Top（PC） | あり（`variant="browse"` が自動付与） |

- **A: モバイルRock Crawlerにバッジを追加**（Cowork推奨）
  PC版と揃う。Category Top は RIG/LOG/PARTS 混在面なので種別表示は必要。
  ただし**基準画面に手を入れる**ため裁定を要する
- **B: 現状維持**（Parts側からバッジを外して揃える）

### 8-2. SoTファイルの二重管理【本バッチ対象外】

`pc/assets/css/` と `css/sot/` に同名のSoTファイルが並存し、**双方向に乖離**している。

| ファイル | 差 | 新しい側 |
|---|---|---|
| `SoT_app-shell.css` | 103行 | **PC側**（P22-C10/C12/C23 の修正がモバイル側コピーに未反映） |
| `SoT_component-catalog-v6.css` | 4行 | **モバイル側**（P22-B5 の `@media (hover:hover)` 対応がPC側に未反映。ソース内に「PC側のSoT本体にも同じ処置が要る（#19 PC改訂キューへ起票）」と記載あり） |
| `SoT_footer.css` | 0 | 一致 |

単純にどちらかを正にできない。Browse整合とは別トラックの横断課題として扱う。

### 8-3. `.edit-card` の4重複【本バッチ対象外】

`pc/preview.html` L194 / `pc/myrig-home-v3.html` L194 /
`pc/myrig-browse-category-v3.html` L189 / `pc/myrig-browse-parts-v3.html` L437 に重複。
既に書式が乖離している。HOMEを含むため別途。

---

## 付録A. 2026-08-29 実測スナップショット（参考・判定に使わない）

> ⚠️ **この節は判定に使わない。** 判定は §7 のとおりチェッカーが実行時に基準画面を実測して行う。
> ここは「契約を作った時点で何が起きていたか」の記録。

### A.1 モバイル 390px

| 項目 | Rock Crawler（基準） | Parts Root | Motor・ESC |
|---|---|---|---|
| カード | `.bp-card` 168px / 3:2 / 画像角丸10px | 同一 | 同一 |
| `__b` | 10px / 800 / lh16 / mt7 | 同一 | 同一 |
| `__t` | 13px / 700 / lh17.94 / mt2 | 同一 | 同一 |
| `__m` | 11px / 400 / lh17.6 / mt3 | 同一 | 同一 |
| `--sm` | 116px / 1:1 / 画像角丸8px / `__t` 12px | — | — |
| バッジ | **なし** | あり | あり |
| 棚 | padL 12px / scrollPadL 12px / gap 10px | 同一 | 同一 |
| 見出し | 17px / 700 | 同一 | 同一 |
| ローカルナビ | `.bp-local-nav` h45 / 12px / **700** / 下線なし | （タブなし） | `.mec-tabs` h40 / 12px / **800** / **赤下線2px** |
| ヒーロー | `.bp-featured` 390×254 | `.mec-stage` 390×254.2 | **なし** |

**モバイルのカード本体は3面で完全一致していた。** 不一致はバッジ・ナビ・ヒーローのみ。

### A.2 PC 1440px

| 項目 | Rock Crawler（基準） | Parts Root / Motor・ESC |
|---|---|---|
| カード実装 | `<myrig-rig-card variant="browse">` | `.mec-card` 手書き |
| カード幅 | — | 372px |
| 画像比率 | — | 16:10（1.6） |
| ブランド | — | 10px / 800 / mt8 |
| タイトル | — | `<strong>` 15px / 700 |
| メタ | — | `<small>` **11.6667px** / 400 |
| ローカルナビ | `.category-side__group` 13px（現在地800 / 他700） | `.parts-side__group` **12px** / 700 |
| ヒーロー | `.category-stage` 1216×**320** | `.mec-stage` 1216×**318** |
| 記事カード | `.edit-card` 378.7px / 16:9 / 角丸8px / 枠1px | — |

1280px時: `.edit-card` 325.3px / `.mec-card` 318.7px / `.category-stage` 1056×320。

### A.3 確認できた良好な状態

- 棚の左ガター（`padding-left` / `scroll-padding-left`）は**全棚12pxで揃っており修正済み**
- セクション見出しとサブタイトルの**重なりは全面で0件**
- 「準備中」の非リンク表示は既に実装済み
- 横スクロール発生 0件 / pageerror 0件

---

## 付録B. 変更履歴

### v1（2026-08-29）

初版。2026-08-29 の Playwright 全面実測と正典再発見（`mobile-component-contract-v0.5.md` §5 /
`SoT_card-components.js`）に基づき作成。

`.mec-*` を正典逸脱と認定し、`js/parts-category-demo.js` による
正典コンポーネントの `innerHTML` 破棄を §3.2-4 として L1 禁止化した。

---

## 🔴 §11. V5 差分（2026-08-29 起案 / 2026-08-30 確定 / BROWSE-CONTRACT-003）

**Sidebar / Breadcrumb / RIG ROOT / WORLD階層 / WORLD selector / Root current は
`docs/ui/browse-sidebar-v5.md` が正典。**
本書はカード・棚・カードvariant・棚レイアウト・ローカルナビを担当し、
Sidebar側の意匠はそちらへ委譲する（V5 §17）。

🔴 **2026-08-30 / revision 037: 036 の CLOSE を一部取り消して再オープン中。**
再オープンは **「4軸の意味・表記」と「Root current の見せ方」だけ**。
WORLD selector / Root entry 文法 / Breadcrumb / Directory / 表札 / 色 / 基準画面は**凍結**。
裁定原本: `_decisions/2026-08-30_browse-axis-relation-view-v1.md`

### 11.1 本書で失効した項目

| 旧記述 | 失効理由 |
|---|---|
| §6.1「RIG Root / Parts Root はローカルタブを置かない」 | V5 §6: **Rootにも4軸を置く** |
| §7「HOMEにパンくずを出さない」（v4時点の案） | V5 §10: HOMEは RCカー WORLD ROOT なのでパンくずを出す |
| HOMEをBrowse全体Rootとして扱う前提 | V5 §0: **HOMEは「RCカー」WORLDのトップ**（WORLD階層） |
| Directory 直前のキャプション「他のカテゴリを探す」 | V5 §14: 全Browse面から撤去 |
| 4軸の表示判定を `data-entity-type` の完全一致で行う | **§12: 表示グループで行う**（2026-08-30） |

### 11.2 WORLD階層（本書側で押さえること）

```
Browse › RCカー(WORLD ROOT) › RIG(RIG ROOT) › Rock Crawler(RIG CATEGORY)
                            › PARTS(PARTS ROOT) › モーター・ESC(PARTS CATEGORY)
```

RIG ROOT（`pc/myrig-browse-rigs-v3.html`）は **Rock Crawler Category Top の別バージョンではない。**
本文は特定カテゴリに寄せず、OFF-ROAD / ON-ROAD / SCALE & SPECIAL が
最初の1〜2スクロールで混在して見えること。

- **棚の意味と中身の entity を一致させる。** RIG棚にPARTSカードを混ぜない / LOG棚にはLOGのみ
- PARTS ROOT / PARTS Category 固有のセクション（`Used in Real RIGs`・`Parts Guide` 等）を
  RIG ROOT へ複製しない
- 新規の架空データを作らない。既存 fixture を組み替える

### 11.3 Root current（Sidebar側の詳細は V5 §9-2）

| 面 | Directory current |
|---|---|
| RCカー HOME | **なし** |
| RIG ROOT | すべてのRCカー |
| Rock Crawler | ロッククローラー |
| PARTS ROOT | すべてのパーツ |
| Motor・ESC | モーター・ESC |

反証も同格に重要: **HOME / Category 面で Root entry を current にしない。**

🔴 **2026-08-30 / revision 037: Root current は子カテゴリ current と同一の表示文法。**
Root 専用の全面帯・件数前の縦罫・濃い下罫線は current 時に使わない（036 の専用デザインは失効）。
非current 時の Root entry 文法は維持。詳細は V5 §9-2。

---

## 🔴 §12. 4軸の表示グループ（2026-08-30 イタヤ裁定）

**拘束力: L2。ただし「§12.3 判定を1か所に持てないなら検査で塞ぐ」は L1。**
裁定原本: `_decisions/2026-08-30_browse-axis-display-groups-v1.md`

> 🔵 **本節は revision 037 でも有効（無改訂）。**
> 037 で変わったのは**4軸のラベルと棚の中身**であって、
> 内部の entity グループ分けは変えていない。`§6.1` のラベル定義と本節を併せて読むこと。

### 12.1 グループ定義

4軸（§6）を押したときにどの棚が残るかは、`data-entity-type` の**完全一致ではなくグループ**で決める。

| view | 表示する `data-entity-type` |
|---|---|
| RIG | `rig` / `rig_master` |
| PARTS | `part` / `parts` / `part_master` |
| LOG | `log` |
| トップ | その面の通常構成へ復帰する |

**理由:** master系（`rig_master` / `part_master`）は「その軸の実体そのもの」ではなく
**その軸の集約ビュー**である。RIG を見にきた人にとって「みんなのRIGに多い車種」は
RIG の話であり、RIG で絞ったときに消えるのは意味的に誤り。

### 12.2 やらないこと【L1】

- **`data-entity-type` を `rig` / `parts` へ書き換えて解決しない。**
  棚が「実体」か「集約」かの区別は Next.js 実装でも必要な情報で、
  表示の都合で潰すと後から復元できない。**visible 判定側で吸収する**
- **`part` / `parts` の語彙2系統をここで統一しない**（PENDING。表示判定側で吸収中）
- **Search の `type` 語彙（`rig` / `parts` / `log`）と混ぜない。**
  Search は URL の外部語彙、本項は面内の表示グループ。別物

### 12.3 🔴【L1】判定を1か所に持てないなら、検査で塞ぐ

2026-08-30 時点、この判定は **PC 4か所 ＋ Mobile 1か所**に分散している
（統合は PENDING・Mobile Browse 着手時）。分散している間は、
`_audit/browse_sidebar_v5_check.py` が**実際に4軸を押して**本文を実測することで再発を塞ぐ。

- **「クラスが付いているか」ではなく「見た目に差が出ているか」を検査する**
- **「押せるか」ではなく「押した結果どうなるか」を検査する**
- **検査が空振りしていないことを検査する**（対象の棚が0本なら判定は素通りする）
- **チェッカーを足したら、故障を注入して FAIL することを確認してから採用する**

**この原則が無かったために、master系が全部消えた状態でチェッカーが 140項目 FAIL 0 を出していた。**

> 🔴 **2026-08-30 / revision 037 追加:「空振り防止」は棚の存在だけでなく
> 「その軸を押したときに棚が空にならないこと」まで要求する。**
> 036 のチェッカーは前者しか見ていなかったため、
> 両ROOTの相互参照軸と PARTS ROOT の LOG軸が**実際に空**なのを 174項目 FAIL 0 で通していた。

---

## 🔵 正典化しないもの（2026-08-30 / revision 037）

**文言・余白・表示件数などのUI調整は正典で縛らない。** 実装側で調整可能にしておく。

| 正典化する | 正典化しない |
|---|---|
| どの面にどの閲覧モード（ローカルナビ）が存在するか | 具体的な日本語ラベル（`トップ` `LOG` など） |
| 相互参照をナビにするか棚にするか | 見出し・説明文の言い回し |
| どういう関係を根拠に表示してよいか | 余白・表示件数・棚の並び順 |

後から `トップ` や `LOG` の語を変えたくなっても情報構造は壊れない。
**文言を磨くために構造を開け直さない。**
