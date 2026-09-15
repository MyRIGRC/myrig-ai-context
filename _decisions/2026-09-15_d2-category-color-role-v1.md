# 裁定原本 — D2 / H2-a「カテゴリ色を文字色・線色・操作色から外す」

記録: 2026-09-15 (JST) / 裁定: イタヤ / 実装: Cowork
拘束力: L1（この裁定の結論は再提案しない）

関連: `docs/design/design-nogo-list.md`（NG-1 / NG-2 / NG-7）、
`docs/design/color-token-v8.md`（§3 職域 / §4 実装上の注意）、正典 077 DECISION 1、081 の H2 棚卸し。

---

## §0 着手前の実測をやり直した理由

081（2026-09-10）の棚卸しは **grep（静的）**で、次を区別できない。

- 後から `!important` で打ち消されている宣言
- 要素が存在しない / 非表示で描画されない宣言
- shadow DOM の中（design-nogo-list「検査の盲点 #1」）

そのため D2 の着手前に **描画された計算値**だけを見る Gate を新設した
（`_state/category_role_check.py`）。方式は grep ではなく
**`--cat-*` / `--cat-*-on` に sentinel 値を注入して計算値の差分を取る**。
color-mix も page-local alias（`--gd-control` / `--master-cta-accent` 等）も自動で追える。
⛔ 色値の一致判定はしない（v7 / v8 が面ごとに違うので一致判定は破綻する）。

### 🔴 検査自体で踏んだ罠（再発防止・数字を信じる前に読む）

1. **継承と currentColor を数えると 1 箇所が数百件に化ける。**
   `color` は継承するので祖先 1 つがカテゴリ色なら子孫全部の計算値が変わる。さらに
   `border-*-color` / `outline-color` / `text-decoration-color` / `caret-color` / `accent-color` は
   **初期値が currentColor**。素朴に差分を数えて **25,835 件**になった（実体は 2 桁）。
   → `color` は親の計算値と違うときだけ、currentColor 系はその要素の `color` と違うときだけ数える。
2. **sentinel を `<style>` で注入すると head に 1 ノード増え、2 パスの要素 index が全部ずれる。**
   `color:#fff` の要素まで「カテゴリ色由来」と誤検出した（home の `.idx-hero__copy`）。
   → `:root` のインライン style へ `!important` で置く。ノードを足さない。
3. **地の色を DOM の backgroundColor 遡りで取ると、絶対配置のシェード / 写真の上で嘘になる。**
   browse の `.category-stage__eyebrow` を「ライト contrast 1.08」と誤判定したが、
   実画素で測ると **2.58**（写真の上）だった。また、要素の外形が小さいと
   クロップの最頻色が**グリフ自身**になるので、地は「文字色から十分離れた画素の最頻値」で取る。
   → 地を持たない要素の contrast を詰めるときは実画素で測り直す。

### 実測（live PC 40面 / light + dark / 1440px / 着手前）

| 職域 | 同型を畳んだ件数 | 判定 |
|---|---|---|
| FILL（塗り） | 156 | 正 |
| BORDER（線色） | 104 | 役割で分類（§3） |
| TEXT_ON（塗りの上の `--cat-*-on`） | 88 | 正 |
| **TEXT（カテゴリ色の文字）** | **29（実体 13 要素）** | NG-2 |
| SHADOW | 20 | — |
| **BAND（角丸＋片側 4px の色帯）** | **4（実体 2 要素）** | NG-1 |
| OUTLINE / CONTROL | **0**（rest 状態） | — |

🔴 **design-nogo-list NG-1 が「PC側に未撤去」と名指ししていた箇所のうち、実際に描画されていたのは
`myrig-library-v3.html` の `.door--rig` / `.door--parts` の 2 件だけ**だった。
`home` の `.dir-card--active` / `SoT_component-catalog-v6.css` の `.ptb__nickname` /
`SoT_detail.css` の `.dt-identity__build` は打ち消し済みか要素が無く、描画されていない。

⚠️ **この Gate は状態依存を見ない。** hover / focus / active / フォーム途中ステップの宣言は
rest では 0 件になる（例: `register-rig` の `accent-color:var(--cat-rig)` は当該ステップが非表示）。
**作業リストは静的 grep、合否はこの Gate**、の二段で扱う。

---

## §1 裁定①（Garage Owner Control）— 077 と NG-7 の衝突

正典 077 DECISION 1「Owner Control の強調色は**その entity のカテゴリ色**に従う」は、
NG-7 の職域表「操作＝中立」と衝突していた。

**裁定: 077 の entity category binding は「Owner 領域の**種別識別**」に限定して維持する。**

- **維持**: `.rail-section[data-gd-owner]` の**塗りヘッダ**（塗り ＋ `--gd-control-on` の文字）。
  Mobile の `.gd-owner__head` / `.gd-owner__manage` も同じ理由で維持。
  Owner Detail の bottom-sheet ヘッダ下線（2px）も、地が形を作っている識別アクセントなので維持。
- **中立へ**: `hover`（`border-color`）/ `active`（塗り）/ `focus-visible`（`outline`）。
  これらは**操作**なので NG-7 に従う。とくに RIG 黄の focus ring は
  ライト白地 contrast **1.08** で見えていなかった（実害）。

⛔ `--gd-control` を hover / active / focus へ戻さない。⛔ 面ごとに色を足さない。

## §2 裁定②（NG-1 の代替形）

`myrig-library-v3.html` の `.door--rig` / `.door--parts`（`border-radius:14px` ＋
`border-top:4px solid var(--cat-*)`）を撤去し、**カードに既にあるアイブロー `.door__label` を
小型の塗りバッジに変える**。P22-B19c（モバイル library の VEHICLE CATALOG / PARTS CATALOG）と同じ形。

NG-1（色帯）と NG-2（`.door__label` の `color:var(--cat-*)`、ライト実測 2.47）を同時に解消する。
線は 1 本も増やしていない。`.door--maker` の中立 4px は種別色ではないので据え置き
（3 枚の天面の高さを揃えている）。

⛔ 色帯を戻さない。⛔ 線を別の場所へ移さない。

## §3 裁定③（BORDER 104 の分類基準）

**「color-mix だから D2 の対象外」にはしない。役割で判定する。**

| 役割 | 扱い |
|---|---|
| 操作 UI の border（`create-btn--rig` 等）、選択・現在地の線、focus ring | **中立化**（NG-7） |
| カテゴリ色の線**そのものが輪郭を作る**もの（地を持たない要素の枠、線画アイコンの stroke） | **中立化**（NG-2） |
| 別の面・背景で形が成立していて、カテゴリ識別アクセントとしてのみ働くもの | **維持可** |

維持したもの: `home` の `.dir-card--parts` / `.dir-world--parts` / `.dir-parts-grid` の淡色境界、
`library-v3` の `.chip--rig/--parts` の inset ring、`library-maker-detail` の `.lpc__badge--*`。

---

## §4 実装で足したもの — v7 の `--cat-*-on`

`SoT_tokens-v6.css` の v7 ブロックに **`--cat-*-on` を新設した（値の flip ではない）**。

これが無いと「塗り ＋ `var(--cat-*-on)`」が v7 面で fallback の `#fff` に落ち、
**v7 LOG オレンジ ＋ 白 = contrast 2.57**（実測）になる。塗りバッジ化した面が v7 だと即座に効く。
選び方は塗りに対して contrast の高い方（`#151515` / `#ffffff` の 2 値）。

⚠️ H2 の値 flip（D3）とは別件。`--cat-*` の**値は触っていない**。

---

## §5 D2 の CLOSE 条件（Gate）

`_state/category_role_check.py` の CC1〜CC6 がすべて PASS。

| | assert | 結果 |
|---|---|---|
| CC1 | BAND（角丸＋片側 >=3px のカテゴリ色帯）が描画 0 | 0 / PASS |
| CC2 | カテゴリ色（base）を文字色として描画 0 | 0 / PASS |
| CC3 | outline にカテゴリ色 0 | 0 / PASS |
| CC4 | `accent-color` / `caret-color` にカテゴリ色 0 | 0 / PASS |
| CC5 | 塗りの上の文字（`--cat-*-on`）が全件 contrast >= 4.5 | 110 / 0 FAIL |
| CC6 | 線色に base カテゴリ色 0（既知 PENDING のみ許可） | 24 / 0 FAIL |

**134 PASS / 0 FAIL。故障注入 6 種すべて単独検知。**
既存 21 本すべて 0 FAIL。

---

## §6 D2 の対象外として残すもの（⛔ 黙って増やさない）

- **notifications の通知タイプバッジ**（インライン SVG の `stroke="var(--cat-log)"` / `fill="var(--cat-rig)"`）
  → NG-7 の**通知色は未確定（HOLD）**。通知 UI の設計議論とあわせて後日。`BORDER_PENDING` に登録済み。
- **`.app-logo__accent` のブランド色**（`color:var(--cat-rig)`）
  → color-token-v8 §3「ブランド色: 未裁定」。live PC 40 面では未使用（描画 0）。
- **`sg-log-tag--run` の `#22c55e` / `--setup` の `--color-accent`**
  → カテゴリ色ではない別系統。パレット外（NG-7）だが、ステータス・通知色の裁定とあわせて後続。
- **`home` の `!important` 層に残るカテゴリ色宣言**
  → 描画 0。`home` は `!important` 743 個で層が深く、触る利得が無い。横断 Convergence で扱う。

## §7 D3 以降の扱い（イタヤ方針 2026-09-15）

MyRIG はまだモック制作途中なので、**ここで全体を収束させない**。

- D3 を含む cross-surface の完全収束は「次のモック制作を妨げるものだけ必要時に是正」
- 全モックが揃った後の**横断 Convergence** で改めて徹底監査する
- D4〜D8 / D9 / D11 / D13 は改善候補として保留
- **Feed カードの再デザインは再 OPEN しない**（具体的な不具合を根拠にした必須作業ではない）

いまは「全体を完成させる」より「モックを前へ進める」を優先する。
