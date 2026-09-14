# Web Fundamentals Final Consolidation（Phase 2 CLOSE）— 裁定原本 v1

- 日付: 2026-09-14 JST
- 対象 revision: MYRIG-20260914-102
- 実装: `myrig-mockup`（SHA は CURRENT 102 を参照）
- 関連: Region Behavior Matrix **v1.10** §1 / §3 / §6.12 / §8、
  `_decisions/2026-09-14_detail-m-transform-v1.md`（R-06）、
  `_decisions/2026-09-13_web-fundamentals-recovery-batch2-v1.md`（Overlay 横断契約 v1.3）、
  `_decisions/2026-09-13_feed-m-transform-and-footer-v1.md`（法務導線 / 「広告について」の経緯）

---

## 0. 何を閉じたか

Web Fundamentals Phase 2 の残件 A〜G を 1 batch で処理し、**viewport continuity を CLOSE** した。
これで基盤修正レーンは終了し、通常のモック制作・デザイン探索レーンへ戻す。

| | 結論 |
|---|---|
| A. Header ≤538 overflow（R-09） | **解決**。Shared Source（`SoT_app-shell.css` §3.5）で |
| B. Home 棚 clip 外 focus（F-4） | **解決**。Shared Source（`SoT_shelf.js`）で。Home / Browse / Detail 共通 |
| C. Garage Owner Detail 1025〜1050 | **>=1025 W / <=1024 M へ統一**（1025 で成立を実測） |
| D. `css/sot/SoT_app-shell.css` 旧コピー | **削除**（実利用 0 を実測。`SoT_footer.css` の旧コピーも同じ理由で削除） |
| E. Overlay 契約の重複実装 | **1 本化**（`SoT_app-shell.js` §0 `MyRIG.overlay`）。排他も契約へ |
| F. 法務「広告について」 | **非 blocker PENDING**（法務コンテンツ制作待ち）。架空の遷移先は作らない |
| G. Final Gate | `_state/web_fundamentals_final_check.py` 新設。2,050 PASS / 0 FAIL、故障 11 種検知 |

---

## 1. A — Header narrow（R-09）

**現象（実測）**: 共有 Header の中身は hamburger 40 ＋ logo ＋ 検索ピル（`min-width:220`）＋ actions 208 で
**約 540px の固定幅**。それ未満では `.app-header__inner` が document を横に押し広げていた（320px で 218px）。
45 面が同じ Header を共有しているので、page-local では直さない。

**裁定**: 責務は落とさない（hamburger / logo / 検索 / 投稿 / 通知 / アバター は全部残る）。
- `<=1024`: 検索ピルの `min-width:220` を 0 に（幅が足りる帯では今までどおり伸びる。W には当たらない）
- `<=559`: 投稿ボタンの**ラベル文字だけ**畳んでアイコンにする（`.cx__label / .cx__caret{display:none}`）。
  button 自体と accessible name（`aria-label="投稿する"`。無ければ `SoT_app-shell.js` が付ける）は残る
- 隠すものは `display:none`。透明のまま hit-test を奪う overlay を作らない

**⚠️ 538 / 559 は implementation value。** 正典に書くのは「Header はどの幅でも document を押し広げない」
「責務を落とさない」「隠した control が hit-test を奪わない」という契約だけ。

**同伴で直したもの**: Header は 0 になっても document がまだ overflow する面があった。原因は全部同じで
**`1fr` = `minmax(auto,1fr)` なので min-content が下限になる**こと。
Search の `.srch-grid`（<=500）、Library 3 面の `.lib-layout`（<=1024）、Garage `.page`（PNF <=720。Own / Public 共通）
を `minmax(0,1fr)` にした。

## 2. B — 棚の focus（F-4）

**現象（実測）**: Home の横スクロール棚で Tab を押すと、
1. ブラウザ既定の scroll-into-view は `scroll-behavior:smooth` のせいで **数百 ms 遅れて**動く（その間 focus は不可視）
2. 「一部でも見えていれば」動かないので、レール右端の **mask（48px の fade）の下**に入ったカードが focus を持ったまま実質不可視

`shell_interaction_check` の WARN 2 件はこれ（B-off）。

**裁定**: focusin で **カード先頭を snap 位置へ即時に**入れる（`SoT_shelf.js`）。
- ⛔ `outline:none` で隠さない ⛔ 不可視カードを Tab 順から外さない（キーボードで到達できなくなる）
- マウス / wheel / 矢印ボタンの挙動は変えない（focusin のときだけ動く）

**踏んだ罠**:
- `scrollTo({behavior:'auto'})` は **CSS の `scroll-behavior:smooth` に従ってアニメーションする**。即時にするなら `'instant'`
- レールは `scroll-snap-type:x`。「はみ出た分だけ」動かすと snap に引き戻されて結局 fade の下に残る（30px 動かして 0 に戻った）。
  **snap 点そのもの（カード先頭 − scroll-padding-left）へ送る**

## 3. C — Garage Owner Detail の境界

099 では「092 で CLOSE 済み」を理由に旧 `<=1050` stack を隔離保持したが、
**「CLOSE 済みだから触らない」だけを理由に混在帯を温存しない**（本 batch の指示）。

**実測（1025）**: 主列 610 / rail 365 / 横 overflow 0 / 内側 overflow 0 / Context Bar 可視 /
hamburger 不可視 / owner controls 20 点の hit-test 正常 → **2 カラムが成立する**。

**裁定**: Garage Owner Detail も **>=1025 W / <=1024 M**。`SoT_detail.css` / `SoT_detail-rail.css` の W 規則の
下限を 1051 → 1025 へ揃え、`SoT_detail-m.css` の `[data-dt-transform]` 向け 1025〜1050 ブリッジは不要になったので撤去。
非変身面の M（`:not([data-dt-transform])` の隔離ブロック）は境界だけ 1050 → 1024。
⛔ Public Detail の変身 DOM（deck / merge / adapt）を Garage へ機械的に持ち込まない。
1024 以下・1051 以上は pixel 差 0。**これで Detail 5 面の境界が Global Shell と 1 本**。

## 4. D — `css/sot/` の旧コピー

**実体確認（runtime）**: 42 Mobile 面（root の `*.html`）が `css/sot/SoT_app-shell.css` と `css/sot/SoT_footer.css` を
読んでいたが、**マッチする規則は `:root` の変数 2 つだけ**（`--shell-header-height` / `--home-sidebar-width`）で、
Mobile 側にそれを消費する規則は無い。`site-footer` を持つ Mobile 面も 0。
→ **実利用 0**。両ファイルを削除し、42 面の `<link>` を撤去した。archive へ複製しない（Git 履歴に任せる）。
42 面 ＋ launcher で pixel 差 0、参照 0、404 0。

残る `css/sot/` の 3 本（tokens / component-catalog / category-tokens）は `@import` shim で値を持たない
（Final Gate WF8 が恒久検査）。

## 5. E — Overlay 契約の 1 本化

4 実装（global Drawer / filter Drawer / Feed 発見 Drawer / Garage Drawer）を比べ、**意味が同じもの**だけを
`SoT_app-shell.js` §0 `MyRIG.overlay.create()` へ抽出した。

| 抽出した（契約） | 各消費側に残した（面固有） |
|---|---|
| closed の inert + aria-hidden（temporary な帯でだけ）／open の `is-open` + aria-expanded + 先頭 focus／Tab 循環／focusin の呼び戻し／Escape／backdrop click／closer／trigger への focus 復帰／scroll lock（body or class）／mode 退出で close／**排他** | global nav の注入・受け皿の生成・breakpoint 判定（app-shell）／Toolbar・badge・要約（filter）／chips の移動・backdrop 生成（feed）／trigger・closer の収集・「トリガ不可視で閉じる」（garage） |

**排他**を契約に足した理由: Library 面は global Drawer と filter Drawer の 2 つを持つ。片方が開いたまま
もう片方を開けると backdrop が 2 枚・scroll lock が食い違う。**A が開いている間に B を開いたら A を閉じる**。
Final Gate WF5 が Library / Search / Feed で実測する。

Overlay 層は Matrix §1 で「Global Shell に付随」なので、置き場所は `SoT_app-shell.js`。
消費側は `MyRIG.overlay` が無ければ **throw** する（黙って別実装へ落ちない）。
既存の `hit_test` / `shell_interaction` / `filter_transform` / `feed_m_transform` / `garage_*` は全部 0 FAIL。

## 6. F — 法務「広告について」

**実体確認**: `SoT_footer.js` の `LEGAL` は privacy / terms / tokushoho の 3 本。`myrig-support-legal-report-pc-v0.1.html`
にも「広告について」の節は無い。Feed が page-local で持っていた `href="#"` は v1.6 で撤去済み。
→ **遷移先も本文も存在しない**。このバッチで法務文面を創作しない。
**「法務コンテンツ制作待ち」の非 blocker PENDING** へ分類し、Phase 2 の CLOSE 条件から外す。
本文ができたら `LEGAL` へ足す（＝全面に出る）。⛔ 架空ページ / `#` リンクを作らない。

## 7. G — Final Gate

`_state/web_fundamentals_final_check.py`。既存 Gate と重複する assert は増やさず、**横断契約**だけを
20 幅（1440〜320）で見る: Header narrow / 横 overflow / visible focus / 棚 F-4 / Overlay（排他を含む）/
Detail 1024/1025 / Garage Owner Detail 境界 / stale source / missing asset・404 / resize 復元。
Mac では `LD_LIBRARY_PATH=$HOME/.local/lib python3 _state/web_fundamentals_final_check.py` の 1 コマンド。
`--selftest` で故障 11 種を 1 つずつ注入し、すべて単独で検知する。

**踏んだ罠**: grid item に `grid-column:2` があると `grid-template-columns:1fr` にしても implicit column が残る
（Garage 境界の故障注入が最初は検知されなかった）。

## 8. CLOSE 判定

CLOSE 条件（本 batch の §11）を全部満たした。
R-09 ✅ / F-4 ✅ / Garage 境界 ✅ / app-shell 旧コピー ✅ / Overlay ✅ / 「広告について」= 非 blocker PENDING に明示分類 ✅ /
全 regression 0 FAIL ✅ / Final Gate 0 FAIL ✅ / W VISUAL LOCK 非回帰 ✅ / narrow 横 overflow 0 ✅ / hit-test・focus・resize 復元 ✅。

**Web Fundamentals Phase 2 / viewport continuity: CLOSE。**
残る技術的負債（「広告について」）は次のモック制作を止める blocker ではない。
