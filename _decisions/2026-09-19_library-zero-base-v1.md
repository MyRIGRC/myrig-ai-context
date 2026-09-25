# 裁定 — Library はゼロベース再設計レーン（Library Zero-Base v1）

- 日付: 2026-09-19 JST（生成: Cowork `ZoneInfo("Asia/Tokyo")`）
- revision: MYRIG-20260919-117
- 前提正典: MYRIG-20260919-116 / `_decisions/p22-b14-library-followup.md`（2026-07-27 の継続課題）/ `_decisions/2026-09-17_research-app-boundary-contract-v1.md` §9（publication）
- 裁定者: イタヤ（Claude 提案 → GPT クロスチェック → イタヤ承認。D3 はイタヤ実機所見で改訂）
- 進行: Library レーンは **Claude（Cowork）がプロデュース**（イタヤ指示 {now.strftime('%Y-%m-%d')}）。GPT は読み専・随時レビュー
- ⛔ Production DB 非接触。migration 未実行。物理 DELETE 禁止。Research 所有領域 不変。

---

## 1. 発端

Library 7 ペア（PC 7 / Mobile 7 = 14 面）は 2026-07-27（P22-B14）に「今の段階でこれでいい」とした暫定確定のまま、
Library 専用のデザインレーンは一度も走っていない。以後の変更は Footer / token / filter R-05 / D2 色などの横断バッチのみ。
B14 の継続課題（色付きの縁・色チップの要否・オフィシャル感・パーツ絞り込みスケール）は全件未裁定。

実測（2026-09-19 / 116 時点）:
- PC Master Detail 2 面は inline CSS 約 48KB ずつ。購入ブロックは page-local `master-buy-*`（Detail 共有の `md-commerce` 未使用）
- Mobile 7 面は同一 inline CSS 29,655B をそれぞれ複製。旧 `SoT_component-catalog-v6.css` 依存
- Mobile にリンク切れ 15 件（`/library/makers/tamiya/*` 3 / `/external/*` 6×2）。PC トップ `href="#"` 20 / メーカー一覧 16
- Library 専用 gate 無し

さらに前提が変わった:
- **メーカー画像は使用許諾が要る可能性が高い。** 許諾済み / 条件付き / 不可 / 未確認 に分かれる。
- Library は「Register で使うマスター DB を活かした参照面」であり、Community（ユーザー投稿）とは別系統。
- Library のマネタイズ役割（公式サイト / 購入先へ送る）が強い。

## 2. 裁定（5 点 ＋ D6 / D7 追記）

### D1. Library はゼロベース再設計レーンとして OPEN
既存 14 面は **情報構造・データ項目・導線の参照資料**。見た目の継承義務は無い。
引き継ぐもの: URL 構造（`/library/*` 複数形）/ RIG・PARTS・Maker の分類 / Master Detail に載せる項目（Variants / User Examples / 公式サイト / 購入先）/ 109 のデータ契約。
自由に壊してよいもの: カード形状・余白・写真の扱い・色・一覧密度・Hero・セクション構成・情報の優先順位・ページ全体のトーン。
**ゼロベース ≠ 必ず新しくする。** 探索の結果「既存の骨格が正しかった」と戻ってよい（Master Detail の Visual Stage ＋ 製品情報がその候補）。

### D2. Community とは別デザイン体系
- Library 内では **RIG / PARTS カテゴリ色を識別体系として使わない**（`--cat-rig` / `--cat-parts` の塗り・帯・チップを持ち込まない）。
- **「公式情報」等の強いバッジも設けない**（MyRIG はメーカーではない）。
- Master 情報であることは **メーカー・製品名・型番・発売状態・基本スペック・出典・MyRIG 内使用例** という情報構造の並べ方で示す。
- 狙う語は「オフィシャル感」ではなく **「製品データベース感 / 資料性 / 信頼できるカタログ感」**（B14 ③ の言い換え）。
- B14 ①（色付きの縁・影）② （色チップ要否）は Library の範囲ではこの裁定で解消。Community 側の ① は別論点として残る。

### D3. 画像ありき・画像依存にしない（画像スロット常設 / NO IMAGE）
- Library は **画像ありきのレイアウト**にする。ただし **画像依存にはしない**。
- カード / Master Detail とも **画像スロットを常設**。実画像が無い場合は **`NO IMAGE`** で **同一レイアウトを維持**する。
  No Image = 欠損状態ではなく通常状態（製品 DB は「情報はある。画像はまだない」で成立する）。
- **画像の有無でカード / Detail の構造・高さを変えない。**
- **Library 全体の公開可否は 109 B-4 の `master_publication_effective` に従う。画像スロットについては、その画像公開条件**（`display_status='approved_image'` **かつ** `effective_display_mode='image_enabled'`）**を満たす場合のみ実画像を表示し、それ以外は NO IMAGE とする。Library 独自の画像公開判定は作らない。**
  ⛔ App 側で `effective_*` を再計算しない。publication 行が無い master を一覧に出せるかは Research 照会（本裁定は触れない）。
- ~~Master Detail は最大 2 枚。~~ → **2026-09-25 改訂（イタヤ裁定）: 製品画像は 1 枚。**
  理由: 2 枚だと「どの 2 枚を選ぶか」の基準が要り、面ごとに 1 枚 / 2 枚でブレる。許諾・取得・差し替えの管理も倍になる。**代表画像 1 枚 ＋ NO IMAGE** で全面の構造を固定する。
  ⛔ 複数枚・サムネイル切替を持たない（mock からも撤去済み。gate L33）。⛔ NO IMAGE スロットをメーカーロゴで埋めない（118 D15 改訂）。
  既存 Master Detail の「Visual Stage ＋ 製品情報」骨格は**維持候補**（装飾・情報整理・購入導線だけ改めて評価）。
- 旧表記 **`IMAGE PENDING` / 「公式画像は確認中です」は廃止**。将来必ず画像が来る含みになるため。通常状態は `NO IMAGE`（補足を付けるなら「表示できる製品画像はありません」程度）。
  **運用状態（確認中 / 許可待ち / 未確認）を Public UI に出さない。** 発売状態も同様に「生産終了」だけ表示し、発売中・未確認は出さない。
- メーカーロゴも同じ扱い（許諾前提）。初期は文字表記。
- メーカー説明文の転載はしない（画像と同じく権利問題になり得る）。自前要約＋出典リンク。

> 経緯（2026-09-19 22:21 JST）: 当初案は「画像が無ければ枠も置かない」（探索 v0）。イタヤ実機で
> 「写真枠があった方が何の商品か直感的に分かる」「データ表に寄りすぎ」→ 画像スロット常設（探索 v1）へ修正。117 は未 commit のため本書内で改訂。
> 2026-09-19 22:43 JST: publication の記述を「画像スロットに限定」へ是正（GPT レビュー指摘。B-4 は Library 全体の gate であり画像だけの判定ではない）。
> 2026-09-24 11:05 JST: D3 を「Library 全体の公開可否 = B-4 / 画像スロット = 画像公開条件」の一文に明文化（GPT 最終レビュー）。

### D4. PARTS から探索開始（PC）
順序: **PARTS Master Card → PARTS Master Detail → PARTS Catalog Top**。
- 新規ファイルで作る。既存版には手を付けない。**最初のカード案は旧版を見ずに白紙で 1 案作り、完成後に旧版と比較する**（改良版へ引っ張られるのを避ける）。
- 一覧 / Detail とも **画像あり・画像なしを混在**させた fixture で作る。
- 実機比較で採否をイタヤ裁定 → 採用後に Library 共通部品化 → RIG カタログへ横展開 → 残り Library（Maker は最後）→ 既存 14 面を収束 → Mobile。
- ⛔ 既存 14 面の修繕（Mobile inline CSS 共通化 / リンク切れ 15 件 / `href="#"`）は **方向決定より後**。先に整備すると「古い Library を綺麗にする作業」になる。

### D5. MyRIG 使用例は重要だが H-1 依存
- 「この製品を使っている RIG N 台」は Library の最大の強み（メーカーサイトに無い情報。Library → Community への扉）。
- ただし本番で PARTS Master と User PARTS を結ぶ集計は **HOLD H-1（`parts_master_id` 未接続）解消が前提**。
- mock では **0 件 / 複数件の両状態**を作り、**0 件でも破綻しない UI** にする。
- ⛔ mock の fixture（「使用 RIG 24 台」等）を見て本番算出可能な機能と誤認しない。

### D6. 件数は metadata として可。Rank には使わない（2026-09-19 22:50 JST 追加）
利用・収録件数は製品やメーカーの補助情報として表示してよい。
ただし件数による順位付け・ランキング・人気表現・ランキング相当の棚や並び順には使用しない。

| 例 | 可否 |
|---|---|
| 「41 台の RIG で使用」を製品の事実情報として表示 | OK |
| メーカーに「車種 248 / パーツ 1,842」の収録件数 | OK |
| 件数部分から、その製品を使っている RIG へ入る | OK |
| 「使用数順」に並べる | NG |
| 「よく使われているパーツ」「人気メーカー」棚を件数順で作る | NG |
| 1 位・TOP・ランキング等の表現 | NG |

根拠: `docs/ui/page-role-matrix-v1.md` §7（ランキング機能全廃）。⛔「ランキング禁止＝数字も全部禁止」と誤読しない。
併せて: **UI 文言に「マスター」を使わない**（`docs/ui/mobile-component-contract-v0.5.md` §4 禁止語）。内部名では Master を使ってよい。Public UI は「製品情報 / 車種 / パーツ / メーカー」。

### D7. Library IA は Find / Identify / Bridge の 3 層（2026-09-19 22:50 JST 追加）
設計図: `_proposals/2026-09-19_library-ia_claude-v2.md`（PROPOSAL。個別面の採否はイタヤ実機で順次）。
本裁定で固定するのは 3 点のみ:
- カテゴリ着地 URL（`/library/*/category/…`）は一覧の **preset state** として扱い、専用 UI を複製しない
- 一覧カードは **Detail への単一入口**。購入は Detail の責務
- Platform（`/library/platforms`）は将来の First-Class Library surface 候補。PARTS compatibility 解消までは本線化しない

## 3. やらないこと（このレーン）
- Register / Detail / Launcher を掘り直さない（116 の禁止を継続）。
- H-12 / Front-wide Consistency Audit / H-7 系は動かさない。
- 公開範囲・SEO の扱い（画像許諾が揃うまで控えめにする案）は **未裁定**。実装フェーズで扱う。
- B14 ④（パーツ数十万点の絞り込み）は D4 の Catalog Top で扱う。裁定はその時点。

## 4. 記録先
- mock: `_state/LIBRARY_EXPLORATION_*.md`（探索案の履歴）/ 新 gate は方向決定後に `library_check.py`
- canon: 本書 + CURRENT NOW 117
