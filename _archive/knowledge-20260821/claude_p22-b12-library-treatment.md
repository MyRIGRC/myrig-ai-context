# P22-B12 ライブラリ群 処遇表（PC正典 → モバイル）

作成: 2026-07-27 (JST) / 対象7ページ:
`library.html` / `library-rigs.html` / `library-parts.html` / `library-makers.html` /
`library-maker-detail.html` / `library-rig-master-detail.html` / `library-parts-master-detail.html`
PC正典: `myrig-library-v3.html` ほか `myrig-library-*-v3.html` 7面
根拠: 継承規約 `pc-mobile-spec-inheritance-v1.1` / B9裁定（一覧は無限スクロール）/ ledger #34（PR明示）

## 1. 処遇表

| PC正典の要素 | 処遇 | モバイル写像 |
|---|---|---|
| `lib-hd`（CATALOGブランド＋Top/車種/パーツ/メーカー nav＋ストライプ） | adapt (G2) | カタログ識別バー（戻る＋マーク＋RC PRODUCT CATALOG/MyRIG CATALOG）＋3色ストライプ＋タブ行4本。**ライブラリは独立した「カタログ世界」**なのでこの識別を落とさない |
| hero（eyebrow・見出し・リード・検索・方針文） | carry | 縦積み。「価格・在庫・販売条件は表示しません」の方針文も保持 |
| stats（1,234 / 5,678 / 45） | carry | 3枠グリッド |
| bento doors（車種/パーツ/メーカーの3入口） | carry | 左縁色付きの縦積みカード（収録数つき・実結線） |
| Quick Explore chips | carry | 横スクロールchip行（「将来は管理画面から差し替え」の注記も保持） |
| Catalog Policy | carry | 破線カード |
| Recently Indexed（8枚） | carry | 2列グリッド |
| 左サイドバー `myrig-filter-sidebar` | adapt (G6) | **絞り込みシートへ全群写像**。車種=メーカー(検索窓つき)/カテゴリ/スケール/駆動/STATUS/プラットフォーム/完成度の7群、パーツ=4群、メーカー=地域/種別/取扱カテゴリの3群。**PCのチェックボックス＝複数選択を保持**（地域のみ単一）。件数表示も保持 |
| pagination | adapt | 無限スクロール（1,234 / 5,678 / 45件・B9裁定踏襲・スクロール器は #mobileViewport） |
| `mkc`（モノグラム＋名前＋収録数＋導線） | carry | 縦積みカード。車種収録0のメーカー（Futaba等）はPC同様2導線 |
| `ctx-bar`（戻る＋パンくず＋同メーカー/同カテゴリ/カタログ検索） | adapt / drop | 戻る＋パンくずを carry。右の3ショートカットは **drop**（一覧の絞り込みシートで代替） |
| `master-catalog-box`（ロゴ・kicker・題・メタ・ギャラリー・スペック表8行・REV/MFR-DECLARED） | carry | 縦積み。タミヤロゴはダーク時に白地パネルで可読性確保 |
| `master-buy-panel`（公式/取扱店・注意・情報修正報告） | carry | ボタン2＋非保証文言＋修正報告（toast） |
| `master-modal`（公式/取扱店リンク・地域タブJPN/US/GLOBAL・モールPR） | adapt (G6) | **確認ボトムシート**。公式/取扱店の2面・地域タブ・Amazon/楽天等のPR明示行・「外部リンクは確認画面で開く」方針を保持 |
| `master-activity`（登録状況4指標） | carry | 4枠グリッド（ページ内アンカー） |
| related / UGC RIG / parts / logs | carry | 横スクロールshelf 3本＋ログ行リスト（rig-detail / log-detail / parts系へ実結線） |
| `community-split`（公式情報×ユーザー投稿） | carry | 縦積み2パネル。**pravatarアバターはイニシャル円へ置換**（外部URL排除・B9教訓） |
| IMAGE PENDING プレースホルダ | carry | **偽画像を当てない**（公式根拠 > 推測。パーツ詳細ギャラリー・placeholder カード） |
| disclaimer（非公認・要公式確認） | carry | 全7ページ末尾 |
| `site-footer` | drop (G2) | Bottom Nav＋ユーザーメニュー「情報」ブロックへ集約済み |

## 2. 結線（このバッチで有効化した導線）

- ブラウズ2ページ: ヒーローの Library CTA → `library.html`、lib-section 3行×2ページ → rigs/parts/makers へ
- Home（凍結r14・href変更のみ視覚不変）: クイックアクション「製品情報」→ `library.html`、「製品情報をすべて見る」→ `library.html`、TRX-4製品情報カード → `library-rig-master-detail.html`
- ライブラリ内: doors/chips/タブ/パンくず/mkc導線/master回遊ナビ すべて実遷移

## 3. 是正2件（再発防止メモ）

1. **オーバーレイのクラス誤り**: B10以降の自前シート開閉で overlay に `is-open` を付けていたが、共有CSSの正は **`is-visible`**。背景が暗転せず、オーバーレイタップで閉じられなかった（開閉自体は動くため検証をすり抜けた）。browse 2面・library 7面とも是正。**教訓: 共有部品の状態クラスは mobile-shell.js の実装を正とし、新規ページはそれを参照してから書く。**
2. **シート内訳の合計超過**: 絞り込みシートの grip+ヘッダ＋本文max-height＋適用バーの合計が max-height 78vh を超え、適用バー下端が画面外に切れていた（実効35px）。本文の max-height を再配分して解消。

## 4. 検証

- B12チェック **143項目 全PASS**（識別バー/タブ/免責/画像/シート全群/複数選択/メーカー検索/無限スクロール/メーカーカード/マスター詳細のスペック表・リンクシート・地域タブ/結線）
- タップ48px: **全27ページ 未達0**（絞り込み・公式/取扱店シート展開状態を含む）
- 回帰: 2幅×27ページ overflow 0（Home 360pxの15pxは既知）・pageerror 0

## 5. 残ページ（PC正典比）

| 群 | ページ | 状況 |
|---|---|---|
| マイガレージ | `garage-parts-detail` | 未着手（次） |
| 認証・設定・法務・登録 | 12ページ | 最後にまとめて |
