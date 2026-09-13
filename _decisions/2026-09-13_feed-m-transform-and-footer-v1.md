# Web Fundamentals Feed M Transform ＋ Feed Footer PENDING 決着 — 裁定原本 v1

- 日付: 2026-09-13 JST
- 対象 revision: MYRIG-20260913-098
- 実装: `myrig-mockup`（SHA は CURRENT 098 を参照）
- 関連: Region Behavior Matrix v1.6 §1 / §3 / §6.8、CURRENT 096（Footer を PENDING にした回）、
  `_decisions/2026-09-07_feed-tabs-v1.md`（3タブ）、`_decisions/2026-09-07_feed-continuity-v1.md`（Feed 内完結）、
  `_decisions/2026-09-13_web-fundamentals-recovery-batch2-v1.md`（Overlay / temporary UI 横断契約）、
  `docs/ui/page-role-matrix-v1.md` §6（Feed 定義・無限スクロール #25）

---

## 0. 実体の棚卸し（裁定の前提）

`pc/myrig-feed-v3.html` を実測した結果、Feed の「左 Rail」は**2つ**あった。

| 領域 | 実体 | 状態 |
|---|---|---|
| `aside.feed-left.myrig-filter-sidebar` | 検索系と同じ filter サイドバー（P22-C19b） | **base に `display:none`。どの幅でも一度も表示されていない死に markup**。`data-feed-panel="all\|following\|trending"` / `data-feed-type="maintenance\|run\|custom\|memo"` を持ち、実際に動いている `.feed-main-tabs`（all/latest/following）・`.feed-rail-types`（all/整備/走行/カスタム/メモ）と**語彙が食い違う第2の定義**だった |
| `aside.feed-left-modern` | LOG投稿ボタン ＋ `.feed-rail-types`（LOG種別）＋ LOG 紹介コピー | 実際に使われている左 Rail |
| `main.feed-center` | `.feed-main-tabs`（3タブ）／`.feed-type-chips`（**`display:none !important` で全幅非表示**・Rail と同じ5値を手書きで複製）／タイムライン／`.pc-feed-ad`（**おすすめタブのみ**） | — |
| `aside.feed-right` | 注目のRIG(3)／おすすめのユーザー(3)／広告枠(2)／page-local mini footer | mini footer は `利用規約 / プライバシー / 広告について` を**すべて `href="#"`** で手書き。Global Footer の法務3本（プライバシーポリシー / 利用規約 / 特定商取引法）とは**語彙も本数も違う第2の法務定義** |

**M（≤900）で実際に起きていたこと**: 左右 Rail がまとめて `display:none` になり、
**LOG投稿導線・LOG種別の絞り込み・注目RIG・おすすめユーザー・右レーン広告が受け皿なしに全部消えていた**。
`.feed-type-chips` は M でも `!important` で隠れたままで、Matrix が「M の受け皿」と書いていた region が機能していなかった。

---

## 1. Feed M の変身（Matrix §3 の既裁定を実装）

| 責務 | W | M | 実装 |
|---|---|---|---|
| LOG投稿 | 左 Rail の `.feed-rail-post` | **既存 Header の投稿導線**（`#cxBtn` → 「ログを投稿」） | Header のその項目が `href="#"` の死にリンクだったので、Rail と同じ composer を指すようにした（Home / Garage v7 6面 / Public Garage 4面と同じ行き先＝既存の慣行）。⛔ Rail を消して投稿機能ごと消さない |
| LOG種別 | 左 Rail の `.feed-rail-types` | **既存 `.feed-type-chips`**（`.feed-controls` 内） | 🔴 chips を手書きで複製しない。`.feed-type-chips` は**空のスロット**にし、`.feed-rail-types` を **DOM ごと移す**。同じ情報の定義は1組だけになり、Tab 順も Main の直前になる |
| 注目RIG / おすすめユーザー | 右 Rail | **Feed-local「発見」Drawer** | 🔴 右 Rail **自体**を Drawer へ変身させるので中身は W と同一 DOM（複製しない）。trigger は `.feed-controls` 内。⛔ Global hamburger にも timeline タブにも混ぜない |
| 広告 | 右 Rail の広告枠2つ | **Feed 内広告 `.pc-feed-ad`** | 既存の受け皿がおすすめタブにしか無く、新着 / フォロー中では M で広告責務ごと消えていた。**全 timeline パネル**に置いた。Drawer 側では広告枠を出さない（発見 Drawer は広告枠ではない） |
| 法務導線 | 右 Rail の mini footer | **Global Footer**（M では出る） | §2 のとおり Shared Source 化。Drawer 側では法務行を出さない |

**PC Narrow Fallback（≤720）まで M の変身先をそのまま維持する。** 専用 Mobile 面（C）とは別物で、混同しない。
timeline の「おすすめ / 新着 / フォロー中」（`_decisions/2026-09-07_feed-tabs-v1.md`）へ
周辺情報（発見 / 広告 / 法務）を混ぜないことは Gate FM2 で恒久化した。

**死に markup の撤去**: `aside.feed-left` とその page-local script・補完 CSS を撤去した。
表示されない第2定義を残すと「どちらが本物か」が判断のたびに揺れる（実際 `.feed-type-chips` の手書き複製も同じ事故だった）。

---

## 2. Footer PENDING の決着 — **裁定 B（W では Footer 本体を出さない）＋ 法務導線の Single Source 化**

### 2-1. 結論

| | 裁定 |
|---|---|
| **W（Feed の内部 scroll 帯）** | **Global Footer 本体は出さない。** ただし page-local の偽 `#` リンクは撤去し、法務導線は Shared Source 由来の `<site-legal>` を右 Rail に置く |
| **M** | Matrix どおり **Global Footer へ merge**。Rail 側の法務行は出さない |
| **法務導線の真源** | `pc/assets/js/SoT_footer.js` の `LEGAL` **1本だけ**。Footer 本体も `<site-legal>` も同じ配列を描く。⛔ Feed だけが別の法務リンクを持つことを禁止 |

### 2-2. なぜ W で Global Footer 本体を出さないのか

**A（Footer 本体を W でも出す）を採らなかった理由は2つあり、どちらも実体に基づく。**

1. **無限スクロールの終端に Footer を置くことになる。**
   `docs/ui/page-role-matrix-v1.md` §6 は Feed の追加ロードを**無限スクロール**と定めている（#25・Feed 限定例外）。
   `.feed-center` が scroll container なので、Footer をその中の末尾へ入れると
   **構造上いつまでも到達できない**。「出したが誰も辿り着けない」は法務導線として最悪の状態で、
   いま右 Rail に置いて**到達できている**状態より悪くなる。
2. **内部 scroll を捨てると Feed W の骨格が変わる。**
   `html,body{overflow:hidden}` ＋ `.feed-shell{height:calc(100vh - 60px)}` ＋ 左右 Rail の `height:100%` は
   「左右の文脈は残し、中央のタイムラインだけを送る」閲覧アプリ型レイアウトの本体で、
   R-07 で確立した W の連続性（264 → 170 の compact 化）もこの骨格の上にある。
   Footer を通常のページ末尾へ置くには外側 scroll へ戻す必要があり、W の見た目と Rail の挙動を作り直すことになる。
   **Footer を出すためだけに払うコストとして正当化できない。**

🔴 **「以前そうだったから」で維持したのではない。** 上の2点は今回 W / M 両方で
**実 scroll 到達性を測ったうえでの判断**であり、測定結果は §2-4 に残す。

### 2-3. なぜ page-local の mini footer をやめたのか

- `利用規約 / プライバシー / 広告について` を**すべて `href="#"`** で手書きしていた。行き先の宣言すら無く、
  「法務導線がある」ように見えて**どこにも行けない**。
- 文言と本数が Global Footer（プライバシーポリシー / 利用規約 / 特定商取引法）と**食い違っていた**。
  法務表記が面ごとに違うのは表示事故そのもので、共有UI Single Source（CORE L1）の対象。
- よって Feed は法務リンクを**持たない**。`SoT_footer.js` に `<site-legal>` を追加し、
  Footer 本体と同じ `LEGAL` 配列を描かせた。Feed は `<site-legal>` を1タグ置くだけ。

**「広告について」の扱い（明示）**: これは Shared Source の `LEGAL` に無い項目で、Feed だけが持っていた。
今回**採用しない**。必要なら `LEGAL` へ足す（＝全面に出る）べきもので、Feed だけが管理するのは禁止事項に当たる。
サイト全体の法務項目として要るかどうかは Footer の内容裁定の側の話なので、**PENDING として残す**（黙って落としたのではない）。

### 2-4. 実 scroll 到達性（cloud Chromium 実測）

| 幅 | mode | 法務導線の場所 | 到達 |
|---|---|---|---|
| 1440 / 1200 / 901 | w-rail / compact-rail | 右 Rail の `<site-legal>` | Rail を末尾まで scroll（可視 840 / 内容 1051〜965）→ `elementFromPoint` が当該リンクを返す ✅ |
| 900 / 720 | m | Global Footer の法務3本 | ページを末尾まで scroll（doc 900 / 6703〜6885）→ `elementFromPoint` が当該リンクを返す ✅ |

W では Global Footer 本体は `display:none`（＝ M の受け皿）。M では右 Rail が Drawer になり法務行を出さない。
**どちらの帯でも法務導線はちょうど1か所にあり、実際に到達できる。**

---

## 3. Overlay / temporary UI 契約の実装が3本になったこと（記録）

「発見」Drawer で、横断契約（v1.3）の実装は
`SoT_app-shell.js initDrawer()`（Global Drawer）／`SoT_filter-sidebar.js`（filter Drawer）／
`SoT_feed-rails.js`（発見 Drawer）の**3本**になった。契約自体は Matrix §1 が1つだけ持つが、
コードは3か所にある。**共通ヘルパへの抽出は次バッチ以降の課題として残す**（今回のバッチで
既存2本を作り替えるのは、直前に CLOSE した Gate を巻き込むリスクの方が大きいと判断した）。

---

## 4. 境界について（px は正典化しない）

Feed の変身は CSS が `.feed-shell` に `--feed-mode`（`w-rail` / `compact-rail` / `m`）を宣言し、
`SoT_feed-rails.js` はそれを読むだけ。**JS は breakpoint の px を持たない。**
Gate（`_state/feed_m_transform_check.py`）も宣言を CSS から抽出し、その ±1 と連続スイープで
`w-rail → compact-rail → m` が一方向であること（Matrix 原則 C）を確かめる。
⛔ 現在の値（1200 / 900）を正典へ固定しない。契約は「responsibility の受け皿があること」と単調性。

---

## 5. 今回触っていないもの

Detail M（R-06）／Header ≤538 overflow（R-09）／Home 棚 clip 外 focus（Phase 1 F-4）／
Garage・Library の filter（前バッチで CLOSE）／Feed カード自体の再デザイン。
`css/sot/SoT_app-shell.css` の旧コピーも従来どおり未整理。
