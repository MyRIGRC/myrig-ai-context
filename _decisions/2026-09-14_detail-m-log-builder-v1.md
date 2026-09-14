# Detail M / LOG の Builder compact deck 廃止 — 裁定原本 v1

- 日付: 2026-09-14 JST
- 対象 revision: MYRIG-20260914-100
- 実装: `myrig-mockup`（SHA は CURRENT 100 を参照）
- きっかけ: **Mac 実機 acceptance（イタヤ目視）**
- 関連: Region Behavior Matrix **v1.8** §3 / §6.10、
  `_decisions/2026-09-14_detail-m-transform-v1.md`（Detail M の本体裁定）、
  CURRENT 099（v1.7 の実装）

---

## 0. 何が起きたか

revision 099（`myrig-mockup` `8bafbcb`）は Gate 上は完全だった。

- `detail_m_transform_check` **2,808 PASS / 0 FAIL**
- 故障 13 種すべて単独検知
- 既存 19 本 0 FAIL
- VISUAL LOCK の W 各幅で pixel 差 0

そのうえで **Mac 実機で目視**したところ、LOG Detail の M（1024px）で
**Builder の compact deck が LOG タイトルと本文の間に入り、読む流れを強く遮断していた**。

🔴 **「契約どおりに動いている」ことと「読める」ことは別。**
情報の優先順位（何を本文の前に置くか）は、assert では判定できない。
⛔ Gate が緑だから設計が正しい、と読まない。⛔ 逆に、実機所見を「Gate が通っているから」で退けない。

---

## 1. 裁定

**LOG M では Builder compact deck を廃止する。**

```
旧（v1.7）  LOG M = Identity → Builder compact deck → Main → RELATED → Footer   ← 失効
新（v1.8）  LOG M = Identity → Main → RELATED → Footer
```

### なぜ LOG だけか

LOG の Identity は **avatar / 投稿者名 / handle / 対象RIG** を持っている。
つまり **人物同定が本文の前に済んでいる**。そこへ Builder カードを重ねるのは同じ情報の二度出しで、
しかも本文の直前という一番高い位置を占める。

RIG / PARTS の Identity は **車両 / 製品の同定**であって人物ではない。
「誰の RIG か」を本文前に知る手段が他に無いので、compact deck が要る。
→ **RIG / PARTS の deck 契約は変えない。**

### 責務ごとの処遇

| 責務 | LOG M での扱い |
|---|---|
| Builder identity（avatar / name） | **Identity の author row へ merge**（`data-dt-section="builder-identity"`） |
| Builder / Public Garage への導線 | 実体確認の結果 author row は `<div>` で**リンクではなかった**ので、`<a>` にして導線にした。⛔ 新しい大型 UI は作らない。行き先は **W の Builder「ガレージを見る」と同一値**（W と M で導線が割れない。Feed M の先例と同じ契約） |
| 公開RIG / 公開パーツ / 公開LOG の stats | **M で明示 drop**（`data-m-drop="stats,actions"`） |
| 大型 Follow / ガレージを見る actions | **M で明示 drop**（同上） |
| W（>=1025）の右レーン Builder カード | **現状維持**。VISUAL LOCK を動かさない |
| LOG 本文の `<dt-actions>` | **現状維持**。⛔ Rail Actions を作らない |
| linked-rig → Identity / entity-feed → RELATED / AD → Main 後方 | **現行 Detail M 裁定を維持** |

---

## 2. 「display:none にするだけ」にしない

⛔ Builder カードを CSS で隠して済ませない。**Detail M transform の責務定義として LOG を deck 対象から外す。**

実装は宣言の変更だけで済ませた（共有 JS に面名分岐を入れていない）。

```
旧: <dt-rail-builder data-m="deck" …>
新: <dt-rail-builder data-m="merge:builder-identity" data-m-drop="stats,actions" …>
```

共有 JS 側は **deck 対象の widget が 1 つも無い面では compact deck 自体を作らない**ようにした
（`needs('deck')`）。空の `<div class="dt-mdeck">` を DOM に置かない。
これは 099 で踏んだ「W に受け皿を残すと隣接セレクタが切れる」と同じ原則の延長で、
**使わない受け皿は DOM に置かない**。

`data-m-drop` は「M で明示 drop する下位責務」の宣言。
**黙って消えたのか裁定して落としたのかを、実体から区別できるようにするため**に置いている。
Gate はこれを読んで「drop が宣言されていること」と「実際に可視でないこと」を両方見る。

---

## 3. author row をリンクにしても W の見た目は変えない

`<div class="dt-identity__author">` → `<a class="dt-identity__author" href="…">` に変えると、
既定の anchor スタイル（色・下線）が効いて **W の VISUAL LOCK が割れる**。

```css
a.dt-identity__author{color:inherit;text-decoration:none;}
a.dt-identity__author:hover .dt-identity__author-name{text-decoration:underline;}
a.dt-identity__author:focus-visible{outline:2px solid var(--color-text);outline-offset:3px;border-radius:8px;}
```

色は子要素（`.dt-identity__author-name` / `-meta`）が持っているので `color:inherit` で足りる。
下線は hover のときだけ名前に出す。**既定状態は 1px も変わらない**ことを実測した（LOG 1440 / 1280 / 1051 で 0）。

`.dt-identity__author` は **LOG Detail だけが使うクラス**なので、他面への波及は無い（実体確認済み）。

---

## 4. Gate の更新

| 追加した assert | 何を見るか |
|---|---|
| DM2 | LOG M に **Builder compact deck が存在しない**（`.dt-mdeck` が DOM に無い・`.dt-mdeck [data-widget="builder"]` が 0） |
| DM2 | LOG M で **Builder カード本体が可視でない** |
| DM2 | LOG M で **stats / 大型 actions が可視でない**（明示 drop の実効） |
| DM2 | **drop が宣言されている**（`data-m-drop` があること＝黙って消していない） |
| DM2 | Identity の author row が **リンクで href を持つ** |
| DM2 | author row の行き先が **W の Builder「ガレージを見る」と同じ** |
| DM2 | **RIG / PARTS の M は stats / actions を deck で維持する**（LOG の是正で共通処理を壊していないことの正の確認） |
| DM1 | W の author row は**リンクでも既定状態で下線を出さない**（VISUAL LOCK） |
| DM1 | W は右レーンの Builder カードを出す（現状維持） |
| DM5 | deck を持たない面の tab 順に deck が現れない |
| DM9 | 往復後、**deck を持たない面に deck が湧かない** |
| DM7 | LOG M では author 導線を hit-test する（deck が無いので対象を差し替え） |

故障注入に 2 種を追加した。

- `log-builder-deck-reinserted`: LOG M へ Builder deck を戻す → DM2 / DM5 / DM1 / DM9 が検知
- `log-author-link-removed`: author row の `<a>` を `<div>` に戻す（導線が消える）→ DM2 / DM1 が検知

**⚠️ 「面が deck を持つか」は宣言（`data-m="deck"` があるか）から決める。**
Gate に面名（ファイル名）で分岐を書かない。書くと、次に deck の有無が変わったとき Gate が嘘をつく。

---

## 5. 検証

| | |
|---|---|
| `detail_m_transform_check` | **3,020 PASS / 0 FAIL** |
| 故障注入 | **15 種すべて単独で検知**（未検知 0） |
| 既存 19 本 | **0 FAIL** |
| VISUAL LOCK | LOG 1440 / 1280 / 1051、RIG 1440 / 1051、PARTS 1440、Garage Owner Detail 1440 / 1040 / 1024 で **揺れで説明できない画素 0**。変化は **LOG の M だけ** |
| resize 1024⇄1025 × 3 往復 | DOM 完全復元・受け皿の増殖 0・LOG では deck が湧かない |

🔴 **R-06（1024/1025 境界）は再 OPEN しない。** 今回は LOG M の情報優先順位の是正であって境界の話ではない。
