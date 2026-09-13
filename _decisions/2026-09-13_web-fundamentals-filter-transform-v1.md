# Web Fundamentals Filter Transform Batch — Garage filter M ＋ Library filter（R-05）裁定原本 v1

- 日付: 2026-09-13 JST
- 対象 revision: MYRIG-20260913-097
- 実装: `myrig-mockup`（本文の SHA は CURRENT 097 を参照）
- 関連: Region Behavior Matrix v1.5 §1 / §3 / §6.7、CURRENT 096（次の作業として予告）、
  `_decisions/2026-09-13_web-fundamentals-recovery-batch2-v1.md`（Overlay / temporary UI 横断契約）

## 0. 何を裁いたか（結論）

同型の 2 問題を 1 つの原則で閉じた。

| | 実体（修正前） | 裁定 |
|---|---|---|
| Garage 一覧 8 面（Own 5 ＋ Public 3） | `.main-2col` が 1 列に落ちる幅（共有 CSS の 1 列帯）で右レーンの `aside.filter-panel` が**一覧の下**に積まれる。一覧を通り過ぎないと filter に届かず、狭幅では存在に気づけない | Toolbar の「絞り込み」trigger（一覧本文の**直前**）→ その直後の inline 折りたたみ panel。初期 closed。active 条件数と要約を Toolbar 側に出す |
| Library 一覧 3 面（RIG / PARTS / Makers） | page-local `.lib-layout` が 980px で 1 列に落ち、`aside.myrig-filter-sidebar` が Main の**前**に全幅で積まれる。一覧の先頭が y≈1,230〜1,480（page top 基準）まで落ち、981px では y≈437 に急変（R-05） | Search filter と同じ「フィルター」ボタン → Drawer を **Shared Source** で適用。1 列化の境界は共有 Context Rail の境界（`SoT_filter-sidebar.css`）に揃える |

**原則（Matrix §3 に反映）**: 狭幅では filter を常時展開 Region として維持しない。Toolbar / Filter trigger → temporary panel へ変身させ、
Overlay / temporary UI の横断契約（v1.3: closed は pointer を遮らない・closed 内部へ focus しない・trigger が見えるなら受け皿必須・
open/close・Escape・focus 復帰）を継承する。

## 1. なぜ常時展開 sidebar を狭幅で維持しないか

- 狭幅では 2 列が成立しないので、常時展開の filter は**必ず Main の前か後ろに全幅で積まれる**。前に積めば一覧の先頭が
  1 画面以上下がる（Library: 720px で y≈1,482 / 実測）。後ろに積めば一覧を全部通り過ぎるまで filter が存在しない
  （Garage: 720px で panel top y≈2,505 / 実測）。どちらも「filter が Main への障壁になる」か「filter に到達できない」。
- filter は**一覧に対する操作**で、一覧と同じ画面で往復するもの。常時展開である必要はなく、条件数が分かれば閉じていてよい。
- Search filter は既に「ボタン → Drawer」で成立していた（MR-MOCK-035D）。同じ部品（`myrig-filter-sidebar`）を使う Library だけ
  別の姿になるのは Matrix の趣旨（同じ幅で面ごとに別の姿にならない）に反する。

## 2. なぜ一覧後方の filter を禁止したか

- DOM 順 = Tab 順。一覧の後ろにある filter は、キーボードでは一覧の全カード（PARTS で 20 枚超）を通過しないと届かない。
- 視覚順でも、狭幅では一覧の末尾までスクロールしないと filter の存在が分からない。「絞り込める」ことを知る前に一覧を読み切ることになる。
- Garage の M 裁定（Matrix §3 v1.1〜）は既に「⛔ 一覧の下へ置かない」。今回はそれを実装しただけで、新しい裁定ではない。

## 3. 実装の要点（挙動は共有側・面は属性だけ）

| | Garage 一覧 8 面 | Library 3 面 ＋ Search |
|---|---|---|
| style | `SoT_garage-list.css` §3b / §4 | `SoT_filter-sidebar.css` FS03 |
| 挙動・aria | `SoT_garage-list.js`（既存の filter 挙動と同居） | `SoT_filter-sidebar.js`（**新設**。Search の page-local Drawer を移した） |
| 面側の変更 | **0**（8 面とも markup 不変。Toolbar と panel の移動は JS） | `aside` に `data-fs-drawer`、`<script>` 1 行、`.lib-layout` の 1 列化境界を共有境界へ。Search は page-local の Drawer CSS / JS を撤去し trigger に `data-fs-toggle` |
| panel の形 | inline 折りたたみ（backdrop なし。背景の pointer を遮る理由がない） | Drawer ＋ backdrop（Search の既存形を踏襲） |
| closed | `hidden` ＋ `inert` | `transform` で画面外 ＋ `inert` ＋ `aria-hidden`、backdrop `hidden`、closed は `box-shadow:none`（影の漏れを止める） |
| DOM | 1 列帯では `aside.filter-panel` を Toolbar の直後へ移し、広幅へ戻すと元の位置へ戻す（**Tab 順を動かすため DOM ごと**。grid-row では Tab 順が動かない） | DOM 不変（sidebar は元から Main の前） |
| 境界 | CSS の `--garage-list-mode`（rail / panel）を JS が読む。**JS に px を書かない** | CSS の `--fs-mode`（rail / drawer）を JS が読む。同上 |
| active 条件 | trigger 内 badge（条件数）＋ 要約テキスト（`カテゴリ: Trail · ブランド: RC4WD`） | 同じ（Search は trigger が既存なので badge だけ注入） |

W（rail 帯）は 12 面すべてで**全要素の DOM rect が 0 差**（1440 / 1280 / 1101）。

## 4. 境界について（px は正典化しない）

- Garage の変身は `.main-2col` が 1 列に落ちる幅と同じ境界で起きる（共有 CSS の 1 列帯。現状 1100 だが、この値は正典ではない）。
  Matrix の M（721〜1024）だけでなく、W のうち右レーンが立たない 1 列帯も同じ変身になる。「右レーンが立つなら rail、立たないなら Toolbar → panel」が契約。
- Library は page-local の 980 を捨て、共有 Context Rail の境界（Search filter と同じ）に揃えた。981〜1024 は従来 2 列（sidebar）だったが、
  Matrix の M 帯で Context Rail が sidebar のまま残るのは Search と食い違うため統一した。**数字ではなく「Search filter と同じ境界」が契約**。
- Gate（`_state/filter_transform_check.py`）は境界を共有 CSS から抽出し、その ±1 と 720/721・900・979/980/981・1024/1025・1099/1100/1101 を
  昇順で走査、700→1200 の 10px スイープで mode が 1 回しか遷移しないことを確かめる（原則 C 単調性）。

## 5. 触っていないもの（PENDING のまま）

Feed M 本体・Feed Footer PENDING・Detail M・Header ≤538 overflow・Home 棚 clip 外 focus（Phase 1 F-4）。
Search の filter 内部（facet の state / 件数）は Search 固有のままで、今回は Drawer の開閉・focus・inert だけを共有化した。
