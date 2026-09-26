# 裁定: Mobile Library v5（2026-09-25）— PC 118 をベースに 8 面を一気に実装

> revision: MYRIG-20260924-118 に統合（新 revision は採番しない）。作成: 2026-09-25 21:51 JST / Cowork
> 対象 mock: `myrig_pc_Ver3/library-*-v5.html`（8 面）/ `css/mobile-library.css` / `js/mobile-library-shell.js` / `_state/mobile_library_check.py`
> 前提: PC Library v5（118 D1〜D20・ASTRA 最終確認 OK・canon 118 push 済み）。Mobile は PC の**エンジン・fixture・契約をそのまま使い、器だけ Mobile**。

## 確定（イタヤ裁定 / GPT 合意 / Claude 提案の順で記録）

### M1. Mobile Top の骨格（イタヤ 2026-09-25・GPT 合意）
- 検索 → **パーツをカテゴリから探す**（14 分類） → 新着（製品行 4 件。件数は mock tuning） → 参照データ注記
- ⛔ 常設タブ（トップ / 車種 / パーツ / メーカー）と重複する「車種を探す / パーツを探す」の扉、「メーカー索引を見る」補助リンクを置かない
- ⛔ 車種 24 分類を Top へ足さない（見た目の対称性だけを理由にしない。車種は車種タブで）
- リード文: 「製品名・型番から検索できます。車種・パーツ・メーカーからも探せます。」
- PC Top は再 OPEN しない（PC はタブが無いので扉が要る。違って当然）

### M2. Mobile の一覧文法 = 製品行のみ（GPT 推奨・Claude 同意）
- 車種 / パーツカタログとも **製品行**（左 4:3 画像枠常設 / メーカー ＋ Item No. / 製品名 2 行まで / 仕様 / 使用数）。Grid / List 切替を持たない
- 理由: Library は写真鑑賞でなく製品を識別して Detail へ入る場。製品名が長い（fixture 平均 33 字）・型番が重要・NO IMAGE が多くなり得る
- 行全体が Detail への 1 リンク（PC と同じ「一覧 = Detail への単一入口」）。使用数は非リンク
- 車種カタログも同じ製品行で**確定**（2026-09-26 ASTRA 意見: 375px でもメーカー・型番・製品名で識別でき、NO IMAGE が続いても索引として成立）

### M3. 続きは「もっと見る」24 件ずつ（Community Search の無限スクロールとは役割差で分離）
- `page=N` = N ページ目まで読み込み済み。Detail から戻ると同じ件数まで復元
- 戻り位置の復元は**内部 ID（fixture `id` = 本番 part_masters.part_id）**で行う。⛔ Item No. / SKU を identity に使わない（境界契約 2026-09-17）

### M4. 一覧の操作は「検索欄 ＋ カテゴリ chip」の 2 つだけ（2026-09-26 09:34 イタヤ「検索導線が多すぎる・プルダウンがしつこい・メーカー 1,000 社で破綻」→ Claude 案を採用）
- 親カテゴリ = 横スクロール chip（PC の左 Rail に相当）。初版では sticky にしない（実機で必要なら）
- 子カテゴリも横スクロール 1 列（正本は親 14・子 90。fixture の 1〜4 件を前提にしない）
- **メーカーの select は置かない**（`makerFilter:false`）。メーカーで絞る手段は 2 つで足りる: ①検索欄にメーカー名（別名解決 `resolveMakerIds` が効く） ②メーカー索引 → メーカー詳細 → 「車種 / パーツカタログ N 件」（`maker=` で到達）。`maker=` で来たときは解除ピルを出す
  - 理由: 実データでは数百〜1,000 社になり select では実用にならない見込み（1,000 社での実測はしていない）。検索欄・索引と機能が重複していた
  - ⚠️ PC にも同じ select がある（同じ規模で破綻する = 見た目でなく実害）。PC は CLOSE 済みなので**別バッチ**で Mobile と同じ形へ寄せる候補（PC 再 OPEN の判断はイタヤ）
- **形態 / 駆動 / 生産終了 / 並び替えは「絞り込み」ボタン 1 つ → 底シート**（mobile-shell の Dialog Controller `filterSheet`）に chip 群で（`chips:true`）。使っている条件数をボタンに出す。適用ボタンは「N 件を表示」
- 常時見えるのは 見出し → 検索欄 → カテゴリ chip →（子 chip）→ 件数 ＋ 絞り込み → 製品行 だけ
- 件数は H1 に混ぜず一覧の直前 1 か所（PC の P1 と同じ）。Q8（facet 件数は pristine のみ）継承

### M5. 実装原則（118 D20 の適用）
- PC の `catalog5` / `detail5` / fixture / `productMatch` / `matchedVariant` / `canShowImage` を**そのまま呼ぶ**。Mobile 側にエンジンを書かない
- PC / Mobile の差は fixture の**経路表 `ROUTES`**（ファイル名・画像の基点）だけ。面は `<html data-lib-platform="mobile">` で宣言
- `--lib-*` と共通の小物は `pc/assets/css/SoT_library-base.css` に分離（PC Shell は @import で読む。値は無変更）。Mobile の器 = `css/mobile-library.css`
- Mobile Shell = `js/mobile-library-shell.js`（SubHeader ＋ Library タブ ＋ BottomNav ＋ 登録シート。**PC と同じ `LIB.shell` API を Mobile 向けに定義**）
- Next.js では `ROUTES` が route 定義、`LIB.shell` の 2 実装が Shell コンポーネントの platform 分岐に置き換わる（版番号を名前に持ち込まない）

### M6. 製品 Detail の H1 は Mobile では 22px（2026-09-26 10:09 ASTRA 意見を採用）
- 「製品名が唯一の H1・表示は Identity 見出し `.ld5-id__name`」は PC 118 D18 と同じ契約。文字サイズだけ PC 26px / **Mobile 22px**（375px で 2 行に収めるため）。gate M17

### 2026-09-26 ASTRA Mobile 監査（MUST 1 / SHOULD 2）→ 対応済み
- MUST: メーカー索引の戻りで検索語が 1 文字前になる → URL 更新を行リンク生成より先に（**PC にも同じ不具合 → 限定再 OPEN で同修正**。L57 / M15 / B1）
- SHOULD: 長い検索語の横はみ出し（M16 / B2）/ `?theme=dark` が落ちる → 状態 URL は `L.stateUrl` が theme を運ぶ（L58 / M15 / B3）
- 意見: Not Found に H1（notFoundHTML を h1 に。PC / Mobile 共通。M17）/ 車種も製品行で確定（M2）/ D18 の Mobile 差分を M6 に
- 回答書: `_state/AUDIT_RESPONSE_20260926_mobile-library-astra.md` / ブラウザ検査 `_state/library_browser_check.py`（B1〜B5）新設

### M7. `?theme=` は「その面だけの表示指定」（2026-09-26 11:30 再確認 M-1 で確定。PC / Mobile 共通）
- 面が自分の URL を書き換える（replaceState）ときは残す（再読込で同じテーマ）。**リンク（from= / カテゴリ / 行 / タブ）には入れない** = 遷移先は保存値 → OS の通常規則
- 理由: リンクに焼き込むと、テーマボタンで選び直した後の遷移で URL 指定が選択を上書きする（PC 退行 M-1）。gate L59 / B6

### M8. 抜粋も製品行（M2 の適用範囲）
- Mobile のメーカー詳細の抜粋（3 件）・製品 Detail の関連製品も製品行（`rowHTML5`）。PC は既定でカードのまま。gate M07
- Mobile Detail のヘッダー「←」はパンくずと同じ解決結果（行き先・読み上げ名）。referrer が無いときも from= へ（B7）

### 2026-09-26 再確認（Claude 独立監査エージェント・ASTRA 利用制限のため）→ **Mobile Library 監査 OK**
- 回答書 `_state/AUDIT_RESPONSE_20260926_mobile-library-astra.md` の「再確認（第 2 回）」

### CLOSE（2026-09-26 11:48）
- イタヤ実機確認で採用。旧 Mobile Library 7 面は他の Mobile 12 面から参照されているため残置（リンク切替・退避は Front-wide 監査で）

### CLOSE 後の後片付け（2026-09-26 12:53）
- 旧 Library へのリンクを v5 へ切替（Mobile 10 面 / PC 25 面 / 共有 JS 2。PC Garage v6 凍結 8 面は除外）。旧 Mobile 7 面は `_archive/20260926_mobile-library-old-close/` へ mv。PC v3 7 面は Front-wide 検査の fixture として残置（導線からのリンク 0）
- P-M6: PC カタログのメーカー select も廃止（L60）

## PENDING
- P-M1 親カテゴリ chip の sticky 化（実機確認後）
- ~~P-M2 車種カタログの一覧形式~~ → 2026-09-26 製品行で確定（M2）
- P-M3 Top 新着の件数（3〜4 で実機調整）
- ~~P-M4 Not Found の H1~~ → 2026-09-26 notFoundHTML を h1 に（PC / Mobile）
- ~~P-M6 PC のメーカー select~~ → 2026-09-26 廃止（イタヤ「やるべきことは全部」。L60）
- P-M7 PC v3 7 面を Front-wide 検査の fixture から外す（検査を v5 / 他面へ付け替えた後に `_archive/` へ）→ Front-wide 監査で
- ~~P-M5 旧 Mobile Library 7 面の退避~~ → 2026-09-26 リンク切替のうえ退避済み。（旧記述: Front-wide 監査へ移管（browse-* / search-results / rig-detail / parts-detail / garage-*-detail / index-e-roomclip / about の 12 面がリンク中。v5 へ切替後に `_archive/` へ mv）
- 横断: Mobile 全面の BottomNav 複製を mobile-shell 側へ寄せる（Front-wide。Library Shell は同じ API で先行）
