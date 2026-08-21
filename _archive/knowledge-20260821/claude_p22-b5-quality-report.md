# P22 B5 — 品質バッチ（裁定不要分）

**日時:** 2026-07-25 JST
**版:** P22-B3 → **P22-B5**
**変更ファイル:** `css/mobile-shell.css` / `css/sot/SoT_component-catalog-v6.css` / `garage.html` / `garage-rig-detail.html` / `search-results.html` ＋ 版3点
**バックアップ:** `mockup-deploy/_backup/P22-B5_20260725_JST/`
**検証:** B5固有11項目・B1回帰60項目・タップ再ゲート（9ページ×25状態）**全PASS**／360・390・440px × 9ページで pageerror 0

---

## 着手理由

実機レビューで「検索の種別ボタンが分かりづらい。モバイルならホバーが無いから仕方ないか」という指摘があり、そこを実測したところ**仕方ないでは済まない実装不備**が出た。

- `css/mobile-shell.css` の `:hover` **50箇所すべてが `@media (hover: hover)` でスコープされていない**。タッチ端末では**タップ後にホバー状態が張り付く**（iOS Safari / Android Chrome 共通の既知挙動）。
- 一方 `:active`（押した瞬間の反応）は **5箇所のみ**。つまり指で触れたときの手応えが事実上存在しなかった。

「分かりづらい」の実体はチップの見た目だけでなくここにもあり、かつ**裁定なしで直せる**ため先に処理した。

---

## 実施内容

### ① `:hover` をホバー可能環境に限定（51箇所）

`@media (hover: hover) and (pointer: fine)` で囲う。機械変換のため、ブレース深さを追う小さなCSSスキャナを書き、**宣言数が前後で 4448 個のまま一致**することを確認して差分がラッパ挿入のみであることを担保した。

`css/sot/SoT_component-catalog-v6.css` の `a:hover{text-decoration:underline}` も対象。SoTは本来PC正典と共有のため、**モバイル配布コピーのみの調整**としたうえで #19（PC改訂キュー）へ「PC側SoT本体にも同処置が要る」として起票する。

なお SoT には他に30箇所の `:hover` があるが、**全9ページでDOMに一致するセレクタは無い**ことを実測で確認済み（モバイルでは死にルール）。そのため触っていない。

### ② 押下フィードバックの全面付与

```css
a:active, button:active, summary:active, label:active,
[role="button"]:active,
input[type="submit"]:active, input[type="button"]:active { opacity: .6; }
```

個別に `:active` を持つ部品（`.feed-card__cell` の `.92`、`.qa-row--mono` のカテゴリ色表示など）はクラス指定のほうが詳細度が高いため、そちらが従来どおり優先される。

### ③ その他（監査S4・#30の機械的項目）

| 項目 | 内容 |
|---|---|
| safe-area | `.mobile-bottom-nav` / `.mobile-shell-header` の `env(safe-area-inset-*)` にフォールバック値が無く、非対応環境で宣言ごと無効化されていた（他13箇所は `, 0px` 付き）→ 統一 |
| デッドコード | `garage.html` の `wireGptHeader()`（`#gptCreateShortcut` は生成箇所が無く毎回 return するだけ）を削除／`garage-rig-detail.html` の `EDIT_TITLES['all']`（ledger #36 で drop 裁定済み・`data-edit="all"` は0個）を削除 |
| `?view=compact` | P17の裁定で役目を終えた旧比較経路が、**レイアウト崩壊状態のままURLで到達可能**だった。`COMPACT = false` で経路を封鎖（コードの完全な畳み込みは #30⑤ で別途） |
| aria | `garage-rig-detail` BottomNav の `aria-current="page"` 欠落（`is-active` だけ付いていた片肺状態）／`garage.html` の tabpanel に `aria-labelledby`（tab側に id を付与し双方向に）／`search-results` の空状態に `aria-live="polite"` |
| 文言 | メモ編集シートの見出しを「PIT TABLEメモを編集」→「メモを編集」（B1-3でメモをPIT所属から独立させたため） |

---

## 検証（B5固有）

| 項目 | 結果 |
|---|---|
| タッチ端末エミュレーションで `(hover: hover) and (pointer: fine)` が false | PASS |
| 同環境で **DOMに一致する素の `:hover` が 0** | PASS（hover限定ブロック51） |
| マウス環境では従来どおりホバーが効く | PASS |
| `:active` 押下フィードバックの定義 | PASS |
| `?view=compact` で旧経路へ到達しない | PASS |
| tabpanel の `aria-labelledby` が実在tabを指す | PASS |
| デッドコード2件の消滅 | PASS |
| BottomNav `aria-current="page"` | PASS |
| 空状態 `aria-live` | PASS |

---

## 残（未着手・別枠）

- **#30⑤**: `?view=compact` / `?layout` / `?notif` / `?qa` と関連関数の**コード完全畳み込み**（今回は経路封鎖のみ）
- **#30⑩**: Home 360px の横overflow 15px（B3から変化なし・本バッチ由来ではない）
- **コメントいいねの非同期**: プレビュー↔全件シートで状態が同期せず再描画で巻き戻る（rig / parts / garage-rig 共通）。データ構造に手を入れる修正のため、共通部品化の設計と一緒に扱うべきと判断し本バッチから外した
- **#19 追加**: PC側 SoT 本体の `:hover` スコープ化

## 次にやること

B4 裁定会（`claude/p22-b4-decision-queue.md`）
