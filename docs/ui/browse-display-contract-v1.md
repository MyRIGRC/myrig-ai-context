# Browse 表示契約 v1

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
> ただし **§3.2 の禁止事項と §4.3 の定義場所は L1**（今回の事故の再発防止）。
>
> **作成:** 2026-08-29 (JST) / Cowork（Claude・主査）
> **根拠:** Playwright 実測（390 / 1280 / 1440px・2026-08-29）＋ `docs/ui/mobile-component-contract-v0.5.md` §5
> **上位正典:** `mobile-component-contract-v0.5.md` §5 カード契約 / `SoT_card-components.js`
> **対象:** Browse系4面 — Vehicle Category Top / Parts Category Top / RIG Root / Parts Root（PC・モバイル）
> **非対象:** Search Results（CLOSE済み・触らない）、Feed、Garage、Library

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

| 面 | 項目 |
|---|---|
| Vehicle Category Top | `トップ / RIG / パーツ / LOG` |
| Parts Category Top | `トップ / パーツ / RIG / LOG` |
| RIG Root / Parts Root | **ローカルタブを置かない** |

規則は「トップ → 主役entity → 相互参照entity → LOG」。
第1タブの語は **「トップ」**（「ホーム」はMyRIG全体HOMEと衝突、「概要」は説明ページに見える）。

### 6.2 挙動

同一ページ内の `data-view` による表示切替。

- **Search Results へ遷移させない**
- **ページ内アンカースクロールにも戻さない**

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
