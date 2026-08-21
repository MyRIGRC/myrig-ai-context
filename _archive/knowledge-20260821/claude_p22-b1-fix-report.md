# P22 B1（機能不良）修正 — 完了レポート

**日時:** 2026-07-25 JST
**対象:** `mockup-deploy/`（実体P21-r2）
**変更ファイル:** search.html / search-results.html / garage.html / garage-rig-detail.html / rig-detail.html / parts-detail.html / log-detail.html / js/mobile-shell.js（8本）
**バックアップ:** `mockup-deploy/_backup/P22-B1_20260725_JST/`（`.vercelignore` に `_backup` 追記済み）
**検証:** インラインscript全7本＋mobile-shell.js の構文チェックPASS ／ Playwright 440px 実測 60項目 全PASS ／ pageerror 0件 ／ 横overflow 0

---

## 実施内容

### B1-1 検索「パーツ」白画面
`search.html` の種別チップを `?type=part` → `?type=parts` に修正。加えて `search-results.html` の `setType()` 冒頭に `if (!DATA[type]) type = 'all';` を追加し、未知の type でも `DATA[curType].length` の TypeError が起きないようにした。実測: `?type=parts` 21件描画／`?type=part`・`?type=bogus` はダイジェスト29件へフォールバック。

### B1-2 設定変更キャンセルのロールバック
`garage-rig-detail.html` に `revertSetSheet()` を実装。`openSetSheet()` で status / is_public を退避し、キャンセル・×・オーバーレイの3経路すべてから復元してから閉じる。保存のみが確定経路。実測: archived＋非公開→キャンセル→再度開いて保存で「セッティング中／公開中」を維持＝非公開化の誤爆経路が消滅。

### B1-3 PIT未所属時のメモ到達不能
メモ表示行と「メモを編集」を PIT ブロックの外へ切り出し、独立ブロック `#gdNote`（ラベル MEMO）とした。`renderPitState()` からメモの出し分けを撤去し、`data-pit="off"` でも表示・編集可能。PIT設定ボタンのみ「PIT TABLEに追加」へ切替わる。ledger #36「PITから外してもRIG本体とメモは削除しない」に整合。値は garage top の `PIT_RIGS.memo` と同一ソースのまま。

### B1-4 Public Garage誤結線
他ユーザーのガレージ導線から Own Garage（`garage.html`）を全撤去し、`href="#" data-href="/user/crawler_junkie"` へ置換（rig-detail 2箇所・parts-detail 2箇所・log-detail 1箇所）。`search-results.html` のユーザー結果12件にも `data-href="/user/[username]"` を付与。`js/mobile-shell.js` に `a[data-href]` の委譲ハンドラを追加し、未実装であることをトーストで明示する（`href` が実値に置き換われば通常遷移するため、実装時は data-href を href へ移すだけでよい）。既存の garage.html 側 `data-href` 死にリンク2件も同時に解消。

**注:** Public Garage（`/user/[username]`）を今回実装するか否かは B4 裁定マター。本修正は「Own Garage への誤結線を消し、正式ルートを保持したまま未実装を明示する」までに留めた可逆措置。

### B1-5 リアクションON状態の視覚反映
3ページ共通の P3 ハンドラで、素の `is-on` ではなく `is-on--like` / `is-on--fav` / `is-on--pin` / `is-following` へ切替えるよう是正。`aria-pressed` を同期し、フォローはラベルを「フォロー中」へ、OFF復帰時に元へ戻す。トーストもON/OFF双方の文言を持たせた。実測色: いいね `#fa4549` ／ お気に入り `#f88202` ／ ピン `#0969da`。

### B1-6 ログ画像5枚以上のグリッド破綻
`renderMedia()` の表示枚数を `Math.min(n, 4)` に制限し、4枚目に残枚数「+N」オーバーレイを重ねる（`.ld-media__more` / `.ld-media__more-n`）。Lightbox には全枚数を渡す。実測: N=1〜6 すべてでセル数≤4・grid行数≤2・高さ318px上限、N=6でオーバーレイ「+2」・Lightbox「4 / 6」。

### B1-7 garage → garage-rig-detail 前方リンク
`rigCardsHtml(list, href)` に遷移先引数を追加。既定は `garage-rig-detail.html`（自分のRIG 8枚）、保存済み（他ユーザーのRIG）のみ公開版 `rig-detail.html` を渡す。PITカードのメディア領域と「詳細」ボタンも `garage-rig-detail.html` へ結線。往復導線が成立。

---

## 本バッチで実施しなかった項目（意図的）

- **S1-6 削除確認文言**: P0 → P1 に降格。論理削除の復旧仕様（保持期間・関連ログ/パーツの扱い）が未確定のため、文言だけ先に変えると別の不整合になる。仕様確認後に対応。
- **48px タップ領域・隣接ヒット領域の衝突（S2）**: B3 として別バッチ。セレクタ登録・ヘルパー統一・衝突解消を一括で行い、`elementFromPoint` によるヒットテスト基準で #29 ゲートを再走する。
- **S3（R2無断省略）**: 裁定リスト。自動復元しない。

---

## 次にやること

1. B2 版管理正常化（VERSIONS.md=P17 / VERSION.txt=P18 / ランチャー=P20 / 履歴末尾=P21-r2 の4点分裂解消、先頭「見#」除去、3点同時更新を運用規則化）
2. B3 48px一括是正＋ヒットテスト基準での再ゲート
3. B4 裁定会（AD枠・Footer受け皿・Public Garage・rd-subhdr・保存済みURL ほか）
