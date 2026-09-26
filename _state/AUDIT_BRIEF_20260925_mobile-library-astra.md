# ASTRA 監査指示 — MyRIG Mobile Library v5（revision MYRIG-20260924-118 / mock 未 commit）

- 発行: 2026-09-25 21:52 JST（Cowork / Claude）
- 対象 revision: **MYRIG-20260924-118**（canon は GitHub main と一致。mock の Mobile Library は未 commit）
- 監査対象: mock `App/MOKUP/myrig_pc_Ver3/` 直下の **Mobile Library v5 8 面 ＋ 器 2 ＋ 共有変更 5**
- 監査対象外: 旧 Mobile Library 7 面（`library.html` / `library-rigs.html` … 無変更・採用判定まで残置）/ PC Library v5（監査 CLOSE 済み。**ただし今回の共有変更で PC の挙動が変わっていないかは対象**）/ Register・Community・Garage / 106 面 Front-wide / PENDING P1〜P4 / Research HOLD

---

## 1. 作ったもの

**方針（イタヤ 2026-09-25）**: PC Library v5（118 D1〜D20・ASTRA 最終確認 OK）を**ベースに 8 面を一気に仕上げ、その後 ASTRA 監査で詰める**。
PC の**エンジン・fixture・照合・カード・契約はそのまま**使い、器だけ Mobile。裁定原本 = `_decisions/2026-09-25_mobile-library-v1.md`（M1〜M5 ＋ PENDING P-M1〜P-M5）。

### 面（8）
| # | Mobile | 対応する PC | 備考 |
|---|---|---|---|
| ① | `library-v5.html` | `pc/myrig-library-v5.html` | Top。検索 → パーツをカテゴリから探す → 新着 4 行 → 注記。**扉 2 つ・メーカー補助リンクは置かない**（常設タブと重複） |
| ② | `library-search-v5.html` | `pc/myrig-library-search-v2.html` | 横断検索。照合は同じ `productMatch` / `findBySku` / `matchedVariant`。結果は製品行 |
| ③ | `library-rigs-v5.html` | `pc/myrig-library-rigs-v5.html` | `LIB.catalog5(… view:'list', more:true, makerFilter:false, chips:true)`。常設操作は検索欄 ＋ カテゴリ chip。形態 / 駆動 / 生産終了 / 並び替えは「絞り込み」底シート。**メーカー select 無し** |
| ④ | `library-parts-v5.html` | `pc/myrig-library-parts-v5.html` | 同上（children:true） |
| ⑤ | `library-makers-v5.html` | `pc/myrig-library-makers-v5.html` | 同じ `makerMatch`。A–Z |
| ⑥ | `library-maker-detail-v5.html` | `pc/myrig-library-maker-detail-v5.html` | 抜粋 2 件 |
| ⑦ | `library-rig-master-detail-v5.html` | `pc/myrig-library-rig-master-detail-v5.html` | `LIB.detail5({kind:'rigs'})`。同じ id 契約 |
| ⑧ | `library-parts-master-detail-v5.html` | `pc/myrig-library-parts-master-detail-v5.html` | `LIB.detail5({kind:'parts'})` |

### 器（Mobile だけ）
- `css/mobile-library.css` — レイアウトだけ。⛔ 色の直値 / `--cat-*` / `--lib-*` の再定義なし
- `js/mobile-library-shell.js` — SubHeader ＋ Library タブ ＋ BottomNav ＋ 登録シート（search.html と同じ markup を 1 か所から出す）。**`LIB.shell` を Mobile 向けに定義**（PC と同じ API。index = 親カテゴリ chip 行）

### 共有の変更（PC にも効く。ここが監査の要）
| ファイル | 変更 | PC への影響（想定） |
|---|---|---|
| `pc/assets/css/SoT_library-base.css`（新） | `--lib-*` と共通の小物を `SoT_library-shell.css` から分離。Shell は `@import` で読む | 値・セレクタ無変更 |
| `pc/assets/js/SoT_library-fixture.js` | 経路表 `ROUTES`（pc / mobile）＋ `imgSrc()`（画像の基点）/ 製品に内部 ID `id`（`rig_NNN` / `prt_NNN`）/ カード・行に `data-id` / `rowHTML5` に `kind` オプション / `safeFrom` の接頭辞を platform 別に | URL・画像パス・表示は同じ |
| `pc/assets/js/SoT_library-catalog-v5.js` | `view`（表示固定）/ `more`（もっと見る）/ `makerFilter:false`（メーカー select 無し）/ `chips`（facet・生産終了・並び替えを chip 群で）/ `onRender` オプション。`lc5View` / `lc5Pager` / `lc5More` / `lc5Sort` を null 安全に。more モードだけ戻り位置を `data-id` で復元 | オプション未指定（PC）は従来どおり |
| `pc/assets/js/SoT_library-detail-v5.js` | 画像 src を `L.imgSrc(p.img)` に | PC は基点 '' で同じ |
| `_state/library_check.py` | `@import` 展開 / L19 の経路表対応 / L33 の文字列更新 | FAIL 0 / selftest 58/58 |

gate: `_state/mobile_library_check.py`（M01〜M14）FAIL 0 / selftest 13/13。

---

## 2. 前提とする裁定
PC 118 D1〜D20（`_decisions/2026-09-24_library-shell-hybrid-v1.md`）はすべて Mobile にも効く。加えて Mobile 固有 = `_decisions/2026-09-25_mobile-library-v1.md` M1〜M5。
特に: D3 中立レイヤー（購入 CTA の青だけ例外）/ D5 from= 戻り状態 / D6 データ契約（publication / H-1 / 「マスター」不使用）/ D8 Not Found / D9 自動転送なし / D12 公開製品索引 / D15 ロゴなし / D16 購入導線 / D17 identity 境界（alias は検索専用・関係は id）/ D18 製品 Detail の H1 / D19 購入 CTA 青 / D20 部品は 1 か所。

---

## 3. 見てほしいこと（優先順）
1. **PC の退行**: 共有 5 ファイルの変更で PC 8 面の表示・URL・戻り状態・Variant・画像が変わっていないか（PC は CLOSE 済み。1 px でも変わっていたら MUST）
2. **Mobile の契約**: PC と同じ操作で同じ着地になるか — `57991` / `PRO10128-03` / 曖昧 `PRO10128`（横断検索・カタログ）→ Detail の ITEM・仕様・購入対象 / `from=` で一覧の状態（q / category / sub / maker / 形態・駆動 / eol / sort / page）が戻るか / もっと見る → Detail → 戻り で件数と位置が戻るか（位置は内部 ID）
3. **Mobile の器**: 375 / 390 / 430 × light / dark で横はみ出し・ヘッダー / タブ / chip の崩れ・入力の自動ズーム（16px 未満）・BottomNav と本文末尾の重なり・購入ボタン（青 #0969da）
4. **Not Found**: 不正 sku / slug が別製品へ倒れないか。H1 が無い件（P-M4）の扱いに意見
5. **一覧文法**: 製品行（左 4:3 画像枠 / メーカー＋Item No. / 製品名 2 行 / 仕様 / 使用数）が 375px で読めるか。NO IMAGE が多い並びで索引として成立するか。車種にも同じ行でよいか（P-M2）に意見
6. **Top**: 骨格（検索 → パーツをカテゴリから探す → 新着 4 行 → 注記）と、扉・補助リンクを置かない判断への意見
6b. **カタログの操作**: メーカー select を置かず「検索欄 ＋ メーカー索引」に任せた判断（M4）。`maker=` 到達時の解除ピル / 絞り込みシート（chip 群・件数バッジ・「N 件を表示」）の挙動。**PC のメーカー select も 1,000 社で破綻する**点への意見（PC 再 OPEN の要否）
7. **gate**: `mobile_library_check.py` の M01〜M14 と `library_check.py` の L01〜L56 で見逃している契約があれば gate 化の提案

## 4. 報告形式
MUST（契約違反・退行・事故）/ SHOULD（改善）/ 意見（裁定が要るもの）/ gate 化可否。再現手順（URL と操作）を付ける。

⛔ ファイルを直接書き換えない。⛔ Production DB 非接触。⛔ commit / push しない。
