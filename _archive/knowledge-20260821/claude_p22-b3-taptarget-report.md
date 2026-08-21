# P22 B3 — 48pxタップ領域の再是正（ledger #29 ゲート改訂）

**日時:** 2026-07-25 JST
**版:** P22-B1 → **P22-B3**（B2は版管理のみ）
**変更ファイル:** `css/mobile-shell.css` / `feed.html` / `garage-rig-detail.html` ＋ 版3点（VERSION.txt / VERSIONS.md / index.html）
**バックアップ:** `mockup-deploy/_backup/P22-B3_20260725_JST/`
**結果:** **未達 66要素 → 全9ページ×25状態で0**（Homeはイタヤ裁定により r14凍結を部分解除して是正）

---

## 1. ゲート自体の欠陥を2つ修正した

B3の本題に入る前に、**測定ハーネス側に2つの欠陥**が見つかった。#29が「504要素実測で未達0件」と報告しながらP22監査で多数の未達が出た原因は、一部ここにある。

**① `scroll-behavior: smooth` による計測の空振り**
`#mobileViewport` は `scroll-behavior: smooth` が効いており、`scrollTop` への代入が非同期になる。代入直後に `getBoundingClientRect()` を読むと**スクロール前の座標**が返るため、ファーストビュー外の要素は座標が画面外と判定され、**計測対象から丸ごと落ちていた**。実測すると1ページあたり可視108要素中20要素しか測れていない状態だった。計測時に `scroll-behavior: auto` を強制して解消し、**実測数は1ページ20→68要素まで回復**した。

**② 状態が既定の1つしか走査されていない**
シート類・編集モード・アクションシートは既定状態では `hidden` のため、対象から外れる。**全9ページで25状態**（登録シート／絞り込みシート／コメント全件／コメント⋯／編集モードON／設定変更／PIT設定／メモ編集／削除確認／カテゴリシート／ユーザーメニュー）を開いた上で計測するようにした。これで `#gdSetSave` `#gdEditCancel` `.gd-editbtn` `.gd-galedit` `.gd-status-select` など、**シート内・編集モード内の未達7種**が新たに露出した。

**測定基準そのものも改訂した。** 従来の「要素自身のボックスが48px以上か」ではなく、`elementFromPoint` を要素中心から上下左右に1pxずつ走らせ、**他要素に奪われた分を差し引いた実効ヒット領域**で判定する。以後の #29 ゲートはこの基準を正とする。

---

## 2. 是正の内訳

### ① 拡張ヘルパーの統一（2系統併存の解消）

`.tap48::after` は**固定48px角**、P14で追加された一覧側は**実寸拡張**（`width/height:100%` ＋ `min-*:48px`）という2系統が併存していた。前者は元要素が48pxより広い場合に**ヒット領域をむしろ縮める**（`.feed-card__stat` が実効幅43.5pxになる）ため、実寸拡張形へ統一した。

### ② P17以降の新規クラスを拡張リストへ一括登録

P14の是正リストにP17以降のクラスが1つも入っていなかった。以下を登録:

`.btn-follow` / `.rd-subhdr__back` / `.rd-subhdr__edit` / `.rd-ptb__cat` / `.rd-cmt-like` / `.rd-cmt-menu` / `.rd-fchip` / `.ld-idrow__meta a` / `.pit-card__btn` / `.act` / `.gd-pit__btn` / `.gd-action-btn` / `.gd-danger` / `.gd-manage__publink` / `.gd-editbtn` / `.gd-galedit` / `.gd-edit-save` / `.gd-edit-cancel` / `.gd-context a`

`<select>` `<input>` と、拡張リストに載せられない要素は実寸で確保した（`.gd-status-select` 36→48px、`.hdr-search__input` 38→48px、`.page-e3 .qanav .qa` 46→48px）。いずれもヘッダー高60px／器の内側に収まるため、器そのものの高さは変わらない。

### ③ 衝突・切り取りの解消（ボックスではなく中心間距離で設計）

拡張した48px領域どうしが重なると、上に来た側が下の側のタップを奪う。**「中心間距離 ≥ 48px」**を満たすよう実寸の間隔を確保した。

| 症状 | 原因 | 対応 |
|---|---|---|
| 「履歴を消す」実効36px | 隣の履歴チップ個別削除✕の48px領域が上から被る | `.recent-chip` を実寸42px＋行間6px＝中心間48px、見出し行との間隔を15pxへ |
| feed RIGチップ 実効41px | 上は画像セル、下はリアクション行に挟まれる | チップに上下マージン（9px / 17px） |
| feed リアクション行 実効幅43.5px | stat どうしの拡張領域が横で衝突 | `.feed-card__foot` の gap を 18→28px |
| feed タブ 実効43px | 器 `.seg-tabs` が44pxでタブ48pxがはみ出し、上端がstickyヘッダーの下に潜る | 器を `min-height:49px` へ |
| 詳細4面 戻るボタン 実効43px | 画面最上端(y=0)にあり48px領域の上半分がビューポート外 | `.rd-subhdr` を5px下げる（器ごと） |
| 同 実効幅45px | `opacity:0` のタイトルが当たり判定を持ち右側5pxを奪う | `.rd-subhdr__ttl { pointer-events: none }` |
| LOGフィルタchip 実効43px | 器が `overflow-x:auto`＝縦にも切るため拡張領域が42pxで切られる（P14でいう de-clip 対象） | 器の縦paddingを8pxずつにして32+16=48px |
| garage-rig-detail パンくず 実効20px | 直下のギャラリー画像が拡張領域を覆う | `.gd-context` に上下マージン |
| 同 MANAGE内の各行 実効35〜40px | PIT設定／アクション／フッタ各行が密 | 行間を実寸で確保 |

### ④ Home（r14凍結の部分解除・イタヤ裁定 2026-07-25）

Homeの3件は修正すると視覚が変わるため一度保留したが、**「共通部品をNext.jsへ持ち出すときにHomeだけ48px未満の例外が残ると契約が二重化する」**という判断で凍結を部分解除し、今回まとめて是正した。

| 要素 | 実効 | 原因 | 対応 |
|---|---|---|---|
| `input#headerSearchInput` | 38px | input は擬似要素拡張が効かない | `.page-e3 .hdr-search` / `__input` を48px、角丸を24pxへ。ヘッダー高60pxの内側に収まるため器は不変 |
| `a.qa` ×2 / `button#tileCat.qa` | 40px | `.qa` が拡張リスト未登録＝箱がそのまま当たり判定。器 `.qanav` が `overflow:auto` で縦も切る。加えて直下 `.mytab` の箱が下端を奪う | `.qa` を48px、`.qanav` に下padding 2px、`.mytabs` の上paddingを4→12px |
| `a.ins__more` | 40px | 直下の `.sectab` に奪われる | `.page-e3 .sectabs { margin-top: 10px }` |

---

## 3. 検証

- **ヒットテスト再ゲート**: 9ページ×25状態・440px。**全9ページで未達0**
- **B1回帰**: 60項目 全PASS（機能欠陥の修正が壊れていないこと）
- **横overflow / pageerror**: 360 / 390 / 440px × 9ページで pageerror 0。360pxのHomeのみ overflow 15px が残るが、**変更前の状態でも同じ15px**であり本バッチの影響ではない（ledger #30⑩ の既存項目）

---

## 4. ledger 反映案

- **#29 改訂**: ゲートの測定基準を elementFromPoint によるヒットテストへ。あわせて「新規ページ／新規クラス着工時に拡張リストへ登録する」をチェック項目化。計測ハーネスは `scroll-behavior` の強制無効化と**シート・編集モードを開いた状態の走査**を必須とする。
- **#32 追記**: Home r14凍結は **P22-B3 で48px契約のため部分解除**（ヘッダー検索バー・クイックアクション行・セクションタブ間隔の3箇所）。以後Homeは「r14の設計を維持しつつ48px契約に従う」扱い。
- **#30 追加**: ⑩Home 360px overflow 15px（本バッチで再確認・未着手）。

## 次にやること

B4 裁定会（S3の要裁定項目 ＋ GPT設計論点2件）
