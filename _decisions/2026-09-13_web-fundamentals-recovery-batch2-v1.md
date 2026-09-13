# 裁定記録: Web Fundamentals Recovery Batch 2 — Global Shell M / 共通 Drawer / focus 契約（2026-09-13）

- 裁定: GPT／実装・検査: Cowork（Claude）
- モック: `myrig-mockup` `9a45d50`／正典: CURRENT 095・Region Behavior Matrix v1.3
- 種別: R-02 解消 ＋ Overlay / temporary UI の横断契約 ＋ 実操作 Gate

## 何を踏んでいたか

| | |
|---|---|
| 死ボタン | `initDrawer()` が `.home-dir` 前提で return。Home / Browse 以外の 28 面で hamburger が何も開かない。Matrix v1.1 は「hamburger 実装済み」と書いていたが、それは「ボタンが見える」ことしか意味していなかった |
| 導線 drop | `.app-nav` を ≤1024 で隠す規則は共有 Shell にあったが、受け皿は Home / Browse の directory にしか無かった。「隠す」だけが共有され「代替」が共有されていない |
| focus 漏れ | Drawer は transform で画面外に送るだけ。Tab は DOM 順に従うので、閉じている Drawer の不可視リンクへ focus が入る（Home x≈−248、Search filter x=−320）。Tab 順検査（WM7）は「順序」しか見ておらず「不可視への到達」は見ていなかった |

## 裁定

1. hamburger が見える面は必ず有効な Drawer の受け皿を持つ。無ければ共有 Shell が生成する（page-local に複製しない）。
2. Drawer は最低限 Browse / Feed / Library への導線を持つ。`.app-nav` を単一情報源として複製し、hrefs を別管理しない。
3. 「カテゴリ directory」と「global navigation」は責務を分ける。directory を持つ面では同じ Drawer 内の別 region。directory を全面へ複製しない。
4. closed は inert（Tab 順から除外）。`aria-hidden` だけで focusable を残すのは禁止。open は focus を層内へ入れ、Escape / backdrop で閉じ、trigger へ focus を返す。
5. 1 つの trigger に 2 つの Drawer を結線しない。既存の共有 Drawer（Garage Detail）が受け皿なら開閉はそれに委譲し、global nav だけ注入する。
6. Gate は must-exist と optional を分ける。Shell 必須要素は無ければ FAIL。skip 数は結果で明示する。
7. Recovery Batch 2 が PASS しても MyRIG 全体の viewport continuity は CLOSE しない（Library 721〜980・Detail M・Feed・Garage filter M が残る）。

## 実装で踏んだ罠（次に触る人へ）

- **閉じた Drawer の box-shadow は画面外でも描画される**: translateX(-100%) の要素の 24px blur が x 0〜28px に漏れる。Home / Browse では従来から出ていた artifact で、共通 Drawer を全面へ広げた pixel 比較で初めて見えた。closed では `box-shadow:none`。
- **Garage Detail は既に Drawer を持つ**: `#hamburgerToggle` が `data-garage-drawer-trigger` / `aria-controls="garageDrawer"` で `SoT_garage-drawer.js` に結線済み。共通 Drawer も結線すると 2 つ開いて G16（aria 同期・focus 位置）が落ちる。`aria-controls` が既存要素を指すかで分岐する。
- **inert は W では外す**: `.home-dir` は ≥1025 では通常のサイドバー。`matchMedia('(max-width:1024px)')` の change で同期しないと、W へ広げたときレールが操作不能になる。
- **Tab 循環は keydown で止める**: `focusin` で戻すだけだと Playwright の Tab は一瞬 body を経由して失敗判定になる。last → first の wrap は keydown で preventDefault してから focus する。
- **横スクロール棚の clip 外カードへの focus**（Home）は Shell 契約外の既知事項（Phase 1 F-4 / A4）。Gate では FAIL にせず WARN として件数を出す — FAIL に混ぜると Shell の回帰と区別できなくなる。
