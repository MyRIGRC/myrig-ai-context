# Web Fundamentals Detail M Transform（R-06 決着）— 裁定原本 v1

- 日付: 2026-09-14 JST
- 対象 revision: MYRIG-20260914-099
- 実装: `myrig-mockup`（SHA は CURRENT 099 を参照）
- 関連: Region Behavior Matrix **v1.7** §1 / §3 / §6.9 / §8、
  `_decisions/2026-09-13_feed-m-transform-and-footer-v1.md`（Feed M。責務ごとに受け皿を変える先例）、
  `_decisions/2026-09-02_shared-ui-single-source-v1.md`（共有UI Single Source / L1）、
  CURRENT 069（Entity Actions の Shared Source）、CURRENT 092（Garage 制作レーン CLOSE）

---

## 0. 実体の棚卸し（裁定の前提）

`.dt-grid` / `.dt-rail` は **Public Detail 3 面と Garage Owner Detail 2 面の計 5 面**が共有していた。
そこに 3 世代ぶんの帯規則が**同時に**生きていた。

| 由来 | 規則 | 実際に効いていたか |
|---|---|---|
| v14r4 以前 | `@media(max-width:1279px) and (min-width:961px){.dt-grid{… 350px}}` | ❌ 後続の `!important` に負けて **どの幅でも効いていない死文** |
| v14r4 以前 | `@media(max-width:960px){…}`（Aside を 2 列 deck にして積む） | ❌ `padding-bottom:48px` の 1 行だけが生きていた。他は `<=1050` の `!important` に負けていた |
| v14r4 | `@media(max-width:1050px){…}`（1 列 stack・Aside を Main の**前**へ） | ✅ これが実体 |
| Global Shell | `<=1024` で hamburger（`SoT_app-shell.css`）／ Garage の Context Bar も `<=1024`（`SoT_garage-detail.css`） | ✅ |

→ **1025〜1050 が混在帯**（Shell は M なのに Detail はまだ 2 カラム）。これが R-06。
さらに「350px / 961」は Matrix §3 に**正典として書かれていた**が、実体は 1051〜1279 が 365px だった。
⛔ 正典の数字が実体と違う状態を、実装側だけ直して放置しない。

---

## 1. なぜ「Aside 全部 stack」をやめ、責務単位で merge したか

旧実装（`<=1050`）は **Aside を丸ごと Gallery と Main の間へ積む**ものだった。実測すると:

- RIG Detail の 1024px で、Aside の塊が y≈1030〜2307 を占め、**本文の先頭が y=2335 まで落ちていた**
  （Gallery 直下から本文まで 1,277px の「関連情報の壁」）。
- その壁の中身は **Main に既にある節と同じ責務**だった。
  RIG なら rail の `base-model` / `entity-feed` / `used-parts` は、本文の「ベースモデル」「LOG」「使用パーツ」と同じもの。
  つまり **狭幅では同じ情報を 2 回読ませて本文を押し下げていた**。

したがって裁定は次のとおり。

- **Aside は M では面として存在しない。** 責務ごとに受け皿が違う。
- **merge**: Main / Identity / RELATED に同責務が既にあるものは、M では**表示責務をそちらへ譲る**（Aside ごと `hidden` + `inert`）。
  W へ戻せば元の Aside 位置・DOM 順へ完全復元する。⛔ 二重表示しない。⛔ Main 側を作り直さない。
- **deck**: Builder と Entity Actions だけは Main に同責務が無く、かつ「誰の RIG か」「いいね・保存・共有」は
  本文を読む前に要る。**Gallery の直後に compact deck** として置く。
- **adapt**: 外部リンク・広告・ビルダーの他 RIG は本文の後ろ（回遊）へ送る。⛔ 消さない（無断 drop の禁止）。

⛔ **「Detail は狭幅で Aside を積む」を一般原則に戻さない。** 積むと本文が画面外へ落ちる、が実測の結論。

### 1-b. PARTS の `entity-feed` を LOG 扱いしない

PARTS の rail `entity-feed` は「登録 / 装着 / 取り外し」の履歴で、`maintenance_logs` とは別物
（`maintenance_logs` に `part_id` が無いので LOG は根拠が取れない。CURRENT の既存 PENDING）。
そこで **merge 先を作らず、「登録情報」の直後へ adapt** した。
⛔「装着RIGのLOG」をこのパーツの LOG として捏造しない。

### 1-c. LOG の Actions を二重化しない

LOG Detail は本文に `<dt-actions>` を持つ（Feed との連続性のため）。
M で Builder deck を作るときに **Rail Actions を新設しない**。action 面は面に 1 つだけ。
`SoT_entity-actions.js` は `[data-action]` 契約で 1 本なので、面が 2 つ持つと同じ entity に
**2 つの押下面**ができ、どちらを押したかで見た目が割れる（069 の一貫性要件に反する）。

---

## 2. なぜ Detail の breakpoint を Global Shell へ合わせたか

`>=1025` を Detail W、`<=1024` を Detail M とした。理由は 3 つ。

1. **混在帯そのものが事故の温床だった。** 1025〜1050 では Shell が hamburger（M の文法）なのに
   Detail は 2 カラム（W の文法）。「同じ幅で面ごとに別の姿になる」は Matrix を作った理由そのもの。
2. **Garage Owner Detail の Context Bar は既に 1024** で分岐していた（`SoT_garage-detail.css`）。
   Detail 系で 1024 は既に使われている境界であり、新しい数字を増やさない。
3. **1025 でも 2 カラムが成立することを実測した。** 主列 610px / rail 365px / 横 overflow 0 /
   内側 overflow 0（3 面 × 1025・1030・1051・1100・1279）。
   → **帯ごとの rail 幅調整は入れていない。** 入れると 1051〜1279 の VISUAL LOCK が動くため、
   「成立しない場合だけ最小限」という条件に照らして**不要**と判断した。

⛔ Shell の breakpoint を 1050 へ広げない（逆向きの統一はしない）。
⛔ `SoT_detail.css` / `SoT_detail-rail.css` へ 961 / 960 / 1050 を書き戻さない。

### 2-b. Garage Owner Detail は現状保存した（別裁定）

同じ `.dt-grid` を使うが、Garage Owner Detail は **092 で CLOSE 済み**であり、今回の batch は
「Aside Rail: Detail の M」であって Garage の再設計ではない（Matrix §8 の ⛔）。
したがって旧 `<=1050` stack / `961` の rail 規則を `SoT_detail-m.css` の
**`:not([data-dt-transform])` へ隔離して持ち越し**、全 13 幅で pixel 差 0 を実測した。
結果としてこの 2 面だけ 1025〜1050 の混在帯が残る。**揃えるかは別裁定**（Matrix §8 に記載）。

---

## 3. 実装の形（Shared Source 1 本・page-local を作らない）

| ファイル | 役割 |
|---|---|
| `pc/assets/css/SoT_detail-m.css`（新設） | `--dt-mode`（`w` / `m`）の宣言・M の 1 列・compact deck・adapt 受け皿・非対象面の現状保存 |
| `pc/assets/js/SoT_detail-m.js`（新設） | 責務の移動と復元。**px を持たない**（`--dt-mode` を読むだけ） |
| Detail 3 面 | `.dt-grid` に `data-dt-transform`、rail widget に `data-m="…"`、merge 先の節に `data-dt-section="…"` を**宣言するだけ** |

`data-m` の語彙: `deck` / `merge:<key>` / `adapt-main` / `adapt-after:<key>` / `adapt-related`。
これは `SoT_detail-markup.js` の方針（**ページに残るのはデータ・variant 選択・配置だけ**）と同じ扱いで、
merge 先が面ごとに違うのは Main の構造が面ごとに違うからである。⛔ 共有 JS で面名（ファイル名）分岐をしない。

- **clone しない。** 既存 DOM を移動するだけなので、`SoT_entity-actions.js` が結線した listener・
  `__eaKey`・pressed / count / `aria-pressed`・LoginRequired・楽観更新 / rollback・toast・share が
  そのまま移動する。二重 state が原理的に作れない。
- **merge 先が無いときは責務を落とさない。** adapt-main へ退避し `data-m-error` を刻む（Gate が拾う）。
  ⛔ 黙って消さない。
- listener は `resize` 1 本だけ。受け皿の要素は 1 度だけ作って使い回す。

---

## 4. 🔴 この batch で実際に踏んだ欠陥（同じ改善案を再提案しないための記録）

### 4-1. 「M の受け皿を W にも置いたままにする」は W の見た目を壊す

最初の実装は受け皿 `<div>` を起動時に DOM へ挿し、W では `display:none` にしていた。
これで **VISUAL LOCK が割れた**（PARTS 1440px で 1,024,554px / LOG 1440px で 193,627px の差）。

原因は **隣接セレクタ**。`display:none` の要素も DOM には居るので、

- `.dt-identity + .dt-main{grid-row:2}`（Gallery を持たない LOG の行詰め）が compact deck に遮られた
- `.dt-main .section--flat + .section--flat{margin-top:44px}` が adapt-after の受け皿に遮られ、
  PARTS の節間が 44 → 40px になった

**契約: W では受け皿を DOM から外す。** `display:none` では足りない。
Gate の故障 `w-receivers-left-in-dom` がこれを見る。

### 4-2. 共有 CSS の @media を「ついでに」広げると、共有している別の面が動く

`min-width:1051px` を 1025 へ広げる作業を一律にやったところ、Garage Owner Detail が動いた。

| 広げた規則 | Garage で起きたこと |
|---|---|
| `@media(min-width:961px){.dt-main{margin-top:10px}}` → 1025 | 1024px で本文が 10px 上へ（全体が 1.84M px ずれた） |
| `@media(min-width:1051px){.dt-gallery .gallery{margin-bottom:0}}` → 1025 | 1040px で Gallery が 4px 縮んだ |
| `@media(max-width:1279px) and (min-width:1051px){.rail-model__buttons{1fr 1fr}}` → 1025 | 1040px で rail のボタンが 2 列になった |
| `.dt-grid{--dt-rail-w:340px}`（帯別の rail 幅） | 1051〜1100 で rail が 365 → 340px になった |
| `@media(max-width:960px){.dt-grid{padding-bottom:48px}}` を撤去 | 720px で下端が 12px 伸びた |

**規則: 共有ファイルの @media を動かす前に、その CSS を読んでいる面を全部数える。**
今回は `.dt-grid` を持つ 5 面。3 面だけ見て「Detail の規則」と判断したのが誤りだった。
広げるときは `[data-dt-transform]` のように**対象を名指しして**広げる。

### 4-3. 検査側の落とし穴

- fixture で Aside の中身が変わる面（`?links=0`）があるので、**W の基準は fixture ごとに取る**。
  基本状態の並びを基準にすると「fixture で消えた widget が戻っていない」と誤検知した。
- 縮約走査（`--selftest`）の幅リストを**昇順にしないと**単調性 assert が常に FAIL する。
  全故障で同じ 3 FAIL が出ていて、しばらく「検知した」と誤読していた。
- pixel proof は **native form control（`<select>`）の描画揺れ**を拾う。
  Garage Owner Detail で 444px の偽陽性が出た。`select,input,textarea` を非表示にして再測し、0 を確認してから結論を出すこと。

---

## 5. 検証（全 PASS 後にのみ正典へ反映した）

| | |
|---|---|
| `_state/detail_m_transform_check.py`（新設） | **2,808 PASS / 0 FAIL**。3 面 × fixture 12 × 11 幅 ＋ 1024⇄1025 往復 3 回 ＋ 700→1300 スイープ |
| 故障注入（`--selftest`） | **13 種すべて単独で検知**（未検知 0） |
| 既存 19 regression | **0 FAIL**（`hit_test_check` のみ 373 → 367 PASS。差の 6 は M で Aside 分類が skip になったぶんで、責務の hit-test は新 Gate の DM7 が見る） |
| VISUAL LOCK | Public Detail 3 面 1440/1280/1279/1051、Garage Owner Detail 2 面 13 幅で **揺れで説明できない画素 0**。意図した変化は **1025〜1050 の Public Detail 3 面だけ** |
