# ASTRA 監査指示 — MyRIG PC Library v5（revision MYRIG-20260924-118 / 未 commit）

- 発行: 2026-09-25 JST（Cowork / Claude）
- 対象 revision: **MYRIG-20260924-118**（ローカル未 commit。GitHub main は 116）
- 監査対象: mock `App/MOKUP/myrig_pc_Ver3/pc/` の Library v5 **8 面 ＋ 共有 assets 4 + 4**
- 監査対象外: Mobile 版（未着手）/ 既存 v3 7 面（無変更・35 面から参照）/ Register・Community・Garage / `_archive/20260924_library-v4-atlas-close/`（v4・Atlas v1・explore card）/ 106 面 Front-wide 監査（別 PENDING）

---

## 1. このレーンで作ったもの

**位置づけ（118 D12）**
> MyRIG Library は、MyRIG 内で使用・登録される製品を識別し、Community 実例・Register・公式情報・購入先へ接続する**公開製品索引**である。独立した製品メディアやショップを目的としないが、**製品 Detail における購入導線は主要なマネタイズ導線として明確に扱う**。

**濃さの配分**: Top / Maker は薄く、Product Detail だけ濃く。

### 面（8）
| # | ファイル | 役割 |
|---|---|---|
| ① | `myrig-library-v5.html` | Library Top（検索 ＋ 車種 / パーツの 2 扉 ＋ 新着 1 行 ＋ パーツ索引 ＋ メーカー索引の補助導線）|
| ② | `myrig-library-search-v2.html` | 横断検索（すべて / 車種 / パーツ / メーカー のタブ・完全一致は候補提示で自動転送しない）|
| ③ | `myrig-library-rigs-v5.html` | 車種カタログ（facet = 形態 / 駆動）|
| ④ | `myrig-library-parts-v5.html` | パーツカタログ（親 14 分類 Rail ＋ 子チップ）|
| ⑤ | `myrig-library-makers-v5.html` | メーカー索引（A–Z・名前検索）|
| ⑥ | `myrig-library-maker-detail-v5.html` | メーカー（製品索引として薄い）|
| ⑦ | `myrig-library-rig-master-detail-v5.html` | 車種 製品情報 |
| ⑧ | `myrig-library-parts-master-detail-v5.html` | パーツ 製品情報 |

### 共有 assets（8）
`assets/css/SoT_library-{shell,card,catalog-v5,detail-v5}.css` / `assets/js/SoT_library-{fixture,shell,catalog-v5,detail-v5}.js`
Launcher 反映済み: `index.html` の Library 群 8 カード、`compare.html` の 7 組。

---

## 2. 監査時に前提とする裁定（破ってはならない線）

裁定原本: `_decisions/2026-09-19_library-zero-base-v1.md`（117 D1〜D7）/ `_decisions/2026-09-24_library-shell-hybrid-v1.md`（118 D1〜D17）

**構造・見た目**
- D1 共有 Library Shell 1 本（Context Header ＋ 左 Index Rail）。App Header / lib-hd を Library で使わない
- D2 書体は MyRIG（Barlow Condensed / Inter / Noto）。serif・mono を使わない
- D3 色は Library 中立レイヤー `--lib-*`。カテゴリ色・チップ・ストライプ全廃
- D7 fluid / wide。Shell に固定幅なし。製品 grid だけ fluid、文章ブロックは読み幅（1180px）
- H1 統一: `.lib-pagehead` ＋ `.lib-h1`（32px）＋ lead の 1 契約。面ごとにサイズ・余白を変えない。eyebrow・大きなページ番号を置かない

**データ契約**
- 公開可否は 109 B-4 の `master_publication_effective` のみ。App 側で `effective_*` を再計算しない
- 画像は `display_status='approved_image'` かつ `effective_display_mode='image_enabled'` のときだけ。**1 枚**（117 D3 改訂）。満たさなければ `NO IMAGE`。⛔「IMAGE PENDING」「確認中」は廃止
- Q8: 条件適用後は facet 件数を出さない。総数は上限打ち切り表示（`10,000+`）
- H-1: `parts_master_id` 未接続 → PARTS 使用数は本番で計算不可。fixture 値を「本番で算出可能」と読める実装・記録にしない
- D6 件数は metadata。⛔ ランキング・人気・使用数順ソート・セール棚を作らない
- D8 不正 ID / slug は Not Found。⛔ 先頭 entity への fallback 禁止
- D17 + 追加確認: **`master_aliases` は検索展開専用**。「タミヤ」→ alias から manufacturer を解決 → `manufacturer_id` → 製品は id で取得。⛔ alias_value / name_ja / 表示文字列の一致を identity・relation の境界に使わない

**役割・文言**
- D12 Editorial な自由文（メーカー紹介・沿革・レビュー・評価・特集）を持たない
- D13 Maker Detail は薄く。製品ライン紹介なし。公式サイトは製品一覧より下の**参照リンク**（CTA ボタンにしない）
- D14 Top にメーカーグリッドを置かない。主入口は 車種 / パーツ の 2
- D15 メーカーロゴは MVP では表示・管理しない（将来は許諾を得た社のみ。⛔ NO IMAGE をロゴで埋めない）
- D16 **Product Detail の購入導線を弱めない**。Official（`rail-info`）と Buy（`rail-buy`）を分離、アフィリエイト明示。⛔ 価格を出さない
- ⛔ UI 文言に「マスター」を使わない（mobile contract §4）
- 外部流入向けに「MyRIG で整理した参照データ / 最新はメーカー公式で確認」の注記（Top・Detail 末尾）

**UX 契約**
- D5 戻り状態保持（`from=` で q / category / sub / maker / facet / sort / page / view を復元。`safeFrom` で外部 URL を弾く）
- D9 検索は Variant Item No. も対象。完全一致でも自動転送しない。`?variant=` で該当 state へ着地
- D11 使用例「すべて見る」は Browse の正式 route がある場合だけ（現状なし → 出さない）

---

## 3. すでに機械化してある検査（重複を避けるため）

`_state/library_check.py`（**L01〜L35**）。`python3 _state/library_check.py` で本検査、`--selftest` で 35 件の故障注入テスト。現状 **FAIL 0 / selftest 35/35**。

L01 リンク実在 / L02「マスター」不使用 / L03 カテゴリ色 / L04 NO IMAGE 文言 / L05 ランキング / L06 カード単一入口 / L07 Q8 / L08 画像判定 / L09・L10 Shell / L11 H-1 注記 / L12 禁止部品 / L13 戻り状態 / L14 トークン・書体 / L15 scale facet / L16 country・region / L17 Not Found / L18 Variant 検索 / L19 使用例リンク / L20 fluid grid / L21 メーカー紹介要素 / L22 Top・Maker の薄さ / L23 購入導線 / L24 使用数リンク着地 / L25 法務導線 Single Source / L26 dark の wash / L27 新着 1 行・列数連動 / L28 件数責務 / L29 active pill / L30 装飾見出し / L31 見出し契約 / L32 div 開閉 / L33 画像 1 枚 / L34 メーカー検索 / L35 identity・relation 境界

**ASTRA へは「この 35 項目の再実行」ではなく、ここが見落としている観点を求めたい。**

---

## 4. 監査してほしい観点（優先順）

1. **裁定と実装の乖離** — §2 の裁定に対し、実装が破っている / 骨抜きにしている箇所。特に D12（Editorial を持たない）と D16（購入導線を弱めない）の両立
2. **データ契約の逸脱** — publication / 画像条件 / Q8 / H-1 / identity・relation 境界。fixture の値を本番の保証と誤読させる記述が UI・コメント・記録に無いか
3. **状態の破綻** — 0 件 / 画像なし / 生産終了 / 不正 ID / 長い名前 / 大量ヒット / 戻り状態の復元失敗。8 面 × 状態の組み合わせで壊れる経路
4. **面をまたいだ一貫性** — 見出しの縦位置、カード、パンくず、戻り文言、件数の出し方、dark
5. **アクセシビリティ** — 見出し階層、`aria-current` / `aria-pressed` の同期、キーボード操作、コントラスト（light / dark）、`aria-live` の件数
6. **法務・権利** — 画像 1 枚の扱い、ロゴ不使用、アフィリエイト明示、参照データ注記、メーカー公式リンクの文脈（提携に見えないか）
7. **本番移行の危うさ** — mock 固有の前提（fixture、query state、`?maker=<id>`）が本番の route / DB 契約に移したとき破綻しないか

## 5. 監査しないでほしいもの

- Mobile 版（未着手）/ 既存 v3 7 面 / `_archive/` の v4・Atlas v1 / Register・Community・Garage の内部 / 106 面 Front-wide 監査（別 PENDING）
- **PENDING 4 件**（未実装は既知。実装の是非ではなく「PENDING のままで矛盾が出ていないか」だけ見てほしい）
  - P1 Library への主導線とグローバルナビ上の扱い（Library レーン CLOSE 後の横断ナビ監査）
  - P2 使用例「すべて見る」（Browse の master 単位 route 待ち）
  - P3 メーカーロゴの表示（許諾取得後）
  - P4 日本語 locale の「A–Z / 五十音」切替（Research の読みキー定義が前提）
- **Research 未確定（HOLD）**: `size_class` 値集合 / `manufacturers.country` / part_categories 親14・子90 の正式名称・slug / master_aliases の `alias_kind`・`locale` / 日本語正式表記の持ち方 / `reading_ja`・`sort_key_ja`
  → fixture のこれらの値は**仮**。Research 確定値として扱わないこと

## 6. 報告してほしい形式

1. **MUST FIX**（裁定違反・データ契約違反・壊れる経路）: 事象 / 再現手順（URL と幅）/ 該当ファイル・行 / 根拠にした裁定番号
2. **SHOULD FIX**（一貫性・アクセシビリティ・文言）
3. **意見**（裁定そのものへの異議。ただし「裁定の変更提案」と明示すること）
4. 各項目に **gate 化できるか**（`library_check.py` に L36 以降として足せる形か）を付記

⛔ ファイルを直接書き換えない（Cowork に一本化）。⛔ Production DB 非接触。⛔ commit / push しない。
