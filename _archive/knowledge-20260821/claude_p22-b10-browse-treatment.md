# P22-B10 ブラウズ群 処遇表（PC正典 → モバイル）

作成: 2026-07-27 (JST) / 対象: `browse-category.html` / `browse-parts.html`
PC正典: `myrig-browse-category-v3.html` / `myrig-browse-parts-v3.html`
根拠: 継承規約 `pc-mobile-spec-inheritance-v1.1` / ledger（AD枠 #34）

## 1. 処遇表

| PC正典の要素 | 処遇 | モバイル写像 |
|---|---|---|
| `app-header`（ロゴ/nav/検索/登録3種/通知/テーマ/アバター） | adapt (G2) | コンテキストバー（戻る＋タイトル＋カテゴリ切替）＋ Bottom Nav 5タブ |
| 左サイドバー `home-dir`（RIGカテゴリ木3群24件・パーツ14件・他ジャンル4件） | adapt (G6) | 「カテゴリ切替」ボトムシートへ**全件**写像。件数表示・現在地強調も保持 |
| `cat-hero` / `parts-hero`（見出し・説明・統計・検索・Open Library） | carry | ヒーロー（背景画像＋スクリム＋見出し＋説明＋統計）＋直下に検索欄・Library導線 |
| `browse-section` `hero_shelf` | carry | 横スクロール shelf（幅268pxカード） |
| `browse-section` `horizontal_shelf` | carry | 横スクロール shelf（幅168pxカード） |
| `browse-section` `compact_shelf` | carry | 横スクロール shelf（幅116px・正方形サムネ） |
| メーカー別3セクション（RC4WD / Axial / Vanquish・Traxxas） | **merge** | メーカーchip切替の1棚に統合（縦積みだと3棚分＝過剰な縦長になるため） |
| `used-in-rigs`（パーツ＋使用RIG一覧） | carry | 縦積みカード（パーツ＋使用RIG2件＋「他のRIGを見る」） |
| `lib-section`（3枚のlib-card） | carry | 3行リスト |
| `lib-cta-row`（3ボタン） | **drop** | 直上の lib-card と行き先が重複するため |
| `ad_slot`（728×90） | adapt | `.ad-slot`（PR明示・高さ92px・1ページ1枠 / ledger #34） |
| `editorial_banner` | carry | 横スクロール shelf（16:9サムネ＋eyebrow＋タイトル＋読了時間） |
| `site-footer`（サービス/カタログ/コミュニティ/SNS/言語/法務） | **drop (G2)** | Bottom Nav ＋ ユーザーメニュー内「情報」ブロック（P22-B6a）へ集約済み |
| `shelf__arrow` prev/next | **drop** | タッチスワイプで代替 |

## 2. 判断の根拠メモ

- **merge を使ったのはメーカー別だけ**。PCは横幅があるので棚を縦に3本並べても破綻しないが、モバイルでは1棚あたり約240pxの縦を消費するため、同種セクションの反復は切替に畳む。切替対象が「同じ形・違う母集団」であることが merge の条件。
- **drop は2件のみ**で、どちらも「モバイル側に既に受け皿がある」もの。受け皿の無い drop は継承規約G2違反（B6a で解消した footer 導線の再発）になるため採らない。
- **カテゴリ木は間引かない**。B9 で「PC右サイドバーの絞り込みが完全な見落とし」と指摘された反省から、件数の多い一覧は縮めずシートへ全件移す方針を継続。

## 3. 検証

- B10 チェック 75項目 全PASS（ヒーロー・全セクション・shelf横スクロール・画像実表示・Library・AD枠・カテゴリ切替シート24/14/4件・メーカー切替・Used in Real RIGs・ダークモード）
- タップ48px: 全20ページ 未達0（カテゴリ切替シート展開状態・メーカー切替後を含む）
- 回帰: 2幅×20ページ overflow 0 / pageerror 0（Home 360px の15pxはP22-B9以前からの既知）

## 4. 実装上の是正（次回以降の再発防止）

1. **検索欄の input が48px未達** — `display:flex; align-items:center` の中の `input` は行ボックス分（15px）しか高さを持たない。器が48pxでも当たり判定は足りないので `align-self: stretch` が要る。
2. **検証ハーネスが完走しない真因は外部フォント** — 各ページの Google Fonts への `https` リクエストがコンテナで必ず失敗し、失敗ハンドシェイクが積もると Chromium 全体が新規タブを開けなくなっていた。全ハーネスに「127.0.0.1 以外を abort する route」を追加し、ブラウザは幅ごとに立て直す方式へ変更。**これ以前の「2幅×10ページ」等の限定的な回帰報告は、意図した範囲ではなく途中で落ちていた可能性がある**。

## 5. 残ページ（PC正典比）

| 群 | ページ | 状況 |
|---|---|---|
| ライブラリ | `library` / `library-rigs` / `library-parts` / `library-makers` / `library-maker-detail` / `library-rig-master-detail` / `library-parts-master-detail` | 未着手（次） |
| マイガレージ | `garage-parts-detail` | 未着手 |
| 認証・設定・法務・登録 | 12ページ | 最後にまとめて |
