# MyRIG CURRENT

revision: MYRIG-20260924-118
updated: 2026-09-26 11:48 JST（生成: Cowork ZoneInfo("Asia/Tokyo")）

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

---

## 🔴 いまやっていること（NOW）

> **スレッドをまたぐとき最初に読む節。** イタヤは環境（デスクトップCowork / ブラウザCowork /
> ブラウザ通常チャット）を切り替えながら作業するため、**前スレッドの記憶に依存せず
> ここだけ読めば再開できる**状態を保つこと。作業の区切りで必ず更新する。

**最終更新: 2026-09-24 / revision 118（**Library Shell ハイブリッド裁定 — Atlas 型 Shell ＋ v4 契約**）**

> 🟢 **118 = Library の Shell と見た目の方向を裁定。v5（ハイブリッド）を 8 面で作る。**
> 裁定原本: **`_decisions/2026-09-24_library-shell-hybrid-v1.md`**（イタヤ口頭 2026-09-24。Claude v4 全 8 面 × Atlas v1 の 3 面比較）
> ⛔ **Production DB 非接触。migration 未実行。物理 DELETE 禁止。**
>
> - **D1 Shell = Atlas 型を 1 本の共有 Library Shell として採用**（Context Header ＋ 左 Index Rail。App Header / lib-hd は Library で使わない。⛔ 別 Shell を増殖させない）
> - **D2 書体は MyRIG**（Barlow Condensed / Inter / Noto。serif・mono は採らない）
> - **D3 色は Library 中立レイヤー `--lib-*`（新概念・light/dark）。カテゴリ色・チップ・ストライプ全廃**。lib-hd の緑 / 紫は旧 token（v6 `--cat-rig:#66b900`。現行 v8 は RIG 黄 / PARTS 赤）で Library にあってはならない。
>   使用例 = **Library → Community の Bridge Preview**（独立責務。Community Card でもその無彩色 variant でもない。Library 内は中立、遷移先は Community RIG Detail。設計探索による判断更新）。
> - **D4 一覧は簡潔に**（左 Rail 索引 ＋ select 型 filter ＋ グリッド / リスト切替。上部 chip Directory と filter sidebar は Library で使わない）。
>   **RIG catalog に scale filter を置かない**（filter 軸 `size_class` は HOLD。scale は公称の表示用）。
> - **D5 戻り状態保持契約**（`from=` で一覧の q / category / sub / maker / facet / sort / page / view を復元。本番 URL は個別 route）
> - **D6 データ契約（Q8 / publication / H-1 / 「マスター」不使用）と Detail 骨格は v4 維持**。Detail に製品データ表（ゼブラ）とページ内アンカー。
> - **D7 fluid / wide**（09-24 イタヤ裁定）: Library Shell は固定コンテンツ幅を持たない。Rail 固定 / Main は viewport 追随。製品 grid は適正カード幅を守って列数が増減（auto-fill / minmax）。
>   **Product Detail だけ本文を読み幅（内部 max-width）に止める。** 列数・px は正典化しない（mock が仕様）。
> - **D8 不正 ID / slug は Not Found**（先頭 entity への fallback 禁止）。**D9 Search は Variant Item No. も対象**（部分一致 ＋ 完全一致候補。自動転送なし。`?variant=` で該当 state へ）。
> - **D10 Maker の country / region は Research 正本確認まで出さない**（名前順 / 検索 / 頭文字索引で成立）。**D11 使用例「すべて見る」は Browse の正式 route が無いので PENDING**（架空 URL なし）。
>
> **比較案は退避済み（2026-09-24 17:56 イタヤ指示）**: Claude v4（8 面 ＋ 専用資産）/ explore card v0・v1 / Atlas v1（page ＋ 資産 ＋ 検証記録）→ mock `_archive/20260924_library-v4-atlas-close/`（mv のみ・README 同梱）。`pc/` の Library は **v5 8 面 ＋ v3 7 面（現行・Launcher と 35 面から参照、リンク切替まで維持）**。
> **v5 ハイブリッド 8 面（PC）完了（09-24 15:05）**: ① `pc/myrig-library-v5.html`（入口）/ ② `-search-v2` / ③ `-rigs-v5` / ④ `-rig-master-detail-v5` / ⑤ `-parts-v5` / ⑥ `-parts-master-detail-v5` / ⑦ `-makers-v5` / ⑧ `-maker-detail-v5`。
>   Shell `SoT_library-shell.css/js`（1 本）/ `--lib-*` 中立レイヤー / `SoT_library-catalog-v5` / `SoT_library-detail-v5` / card v5 modifier。gate `library_check.py`（v5 既定 L01〜L14）FAIL 0。render 12 状態 × 4 幅 overflow 0。
>   記録: mock `_state/LIBRARY_EXPLORATION_20260919.md` 探索 5。
> **118 commit 前バッチ（09-24 17:14）反映済み**: scale facet 撤去 / Maker country・region 撤去 / 不正 ID → Not Found / Variant Item No. 検索 ＋ `?variant=` / 使用例リンク整理（preview → v15 実在。「すべて見る」PENDING）/
>   fluid grid（1280:3 → 1440:4 → 1600:4 → 1920:5 → 2560:6 列。Detail 本文 max 1180）。gate `library_check.py` L01〜L20 FAIL 0（selftest 20 件全検出）。render 15 状態 × 8 幅 overflow 0。
> **役割裁定（09-24 17:43）反映済み — D12〜D16**: **Library = MyRIG 内部導線から到達する公開製品索引。Top / Maker は薄く、Product Detail だけ濃く。購入導線は主要なマネタイズ導線として弱めない**（Official ≠ Buy、アフィリエイト明示、価格・ランキング・セール棚なし）。
>   Editorial な自由文（メーカー紹介・沿革・レビュー・評価・特集）は原則持たない。外部流入には「MyRIG で整理した参照データ / 最新はメーカー公式で確認」の注記（Top・Detail 末尾）。
>   実装: Maker Detail から製品ライン chips 撤去・公式サイトは最下部の参照リンクへ格下げ / Maker Index の製品ライン列・検索を撤去 / Top のメーカー扉・グリッド撤去（2 扉 ＋ 「メーカー索引を見る →」）/ ロゴ不使用。
>   gate L21〜L23 追加（FAIL 0、selftest 23 件全検出、v4 FAIL 0）。render 4 面 × 6 幅 overflow 0。**PENDING P1**: 主導線・グローバルナビ上の Library の扱い → レーン CLOSE 後の横断ナビ監査。
> **Launcher 反映済み（18:06）**: `index.html` Library 群 8 カード（7 面 v3 → v5 ＋ PC 専用 検索）/ `compare.html` 7 組 → v5。Mobile 面は旧版のまま。
> **Mobile Library v5 CLOSE（2026-09-26 11:48・イタヤ実機確認「見た感じ良い」）**: 8 面を採用。PC は設計レベルの再 OPEN ではなく、Mobile 実装で見つかった共有部の不整合 4 点の限定修正（GPT 同見解）。
>   残置: 旧 Mobile Library 7 面（`library*.html`）は他の Mobile 12 面（browse-* / search-results / rig-detail / parts-detail / garage-*-detail / index-e-roomclip / about）からリンクされているため**移動しない**（PC v3 と同じ扱い）。リンクの v5 切替と `_archive/` 退避は **Front-wide 監査**（横断 PENDING）で行う。
>   PENDING 継続: P-M1 chip の sticky / P-M3 Top 新着の件数 / P-M6 PC のメーカー select（別バッチ）/ 横断 BottomNav 共通化。**次 = `mockup`（mock ＋ canon 118 に統合して commit / push）**。
> **Mobile Library 監査 OK（2026-09-26 11:30）**: ASTRA 利用制限のため、イタヤ指示で **Claude の独立監査エージェント**が再確認（PC CLOSE 時点 base と 900 状態比較 ＋ 操作 37 × 2 ＋ 変異注入 14）。第 1 回再確認で MUST 1（PC 退行: テーマボタン後の遷移で dark に戻る）→ **?theme= は「その面だけ」＝リンクに入れない**（裁定 M7・L59・B6）/ SHOULD（ヘッダー ← が from= を使わない → B7、抜粋も製品行 → M8・M07）を修正 → 第 2 回で **MUST 0・監査 OK**、残 SHOULD 2 も対応済み。
>   gate: PC L01〜L59 FAIL 0 / selftest 61/61。Mobile M01〜M17 FAIL 0 / selftest 17/17。ブラウザ B1〜B7 FAIL 0。PC の許容差分は 4 点（索引の更新順 / 自分の URL の theme / Not Found H1 / from= に theme を入れない）。**次 = イタヤ実機確認 → `mockup`（mock ＋ canon）**。未 commit。
> **ASTRA Mobile 監査 → 対応済み（2026-09-26 10:09）**: MUST 1（メーカー索引の戻りで検索語が 1 文字前 → URL 更新を先に。**PC にも同じ不具合 → 限定再 OPEN 3 点**: 索引の更新順 / 状態 URL に theme / Not Found の H1）/ SHOULD 2（長い検索語のはみ出し / `?theme=dark` が落ちる → `L.stateUrl`）。意見: 車種も製品行で確定（M2）/ Not Found に H1 / D18 の Mobile 22px を M6 に。
>   gate: PC L57 / L58 追加 → FAIL 0 / selftest 60/60。Mobile M15〜M17 → FAIL 0 / selftest 16/16。**ブラウザ検査 `_state/library_browser_check.py`（B1〜B5・Playwright）新設** FAIL 0。回帰 152 状態 issue 0。回答書 `_state/AUDIT_RESPONSE_20260926_mobile-library-astra.md`（再確認依頼文つき）。未 commit。
> **Mobile カタログの操作を整理（2026-09-26 09:34・イタヤ「検索導線が多すぎ・プルダウンがしつこい・メーカー 1,000 社で破綻」）**: 一覧の常設操作は**検索欄 ＋ カテゴリ chip だけ**。メーカー select を廃止（検索欄の別名解決 ＋ メーカー索引が担う。`maker=` 到達時は解除ピル）。形態 / 駆動 / 生産終了 / 並び替えは「絞り込み」ボタン → 底シートの chip 群（catalog5 に `makerFilter:false` / `chips:true` / `onRender` を追加。PC は既定値で無変更）。裁定 M4 を改訂。
>   ⚠️ PC の メーカー select も同じ規模で破綻する（実害）→ 別バッチで同じ形へ寄せる候補（PC 再 OPEN はイタヤ判断）。gate M06 更新。Mobile 12 URL × 3 幅 × light/dark = 72 状態 issue 0 / PC gate FAIL 0。
> **Mobile Library v5 — 8 面を一気に実装（2026-09-25 21:51・イタヤ「PC 版をベースにまず一気に仕上げる → ASTRA 監査」）**: `library-v5 / -rigs-v5 / -parts-v5 / -makers-v5 / -maker-detail-v5 / -rig-master-detail-v5 / -parts-master-detail-v5 / -search-v5`。
>   PC の catalog5 / detail5 / fixture / 照合 / カード をそのまま使い、器だけ Mobile（`css/mobile-library.css` / `js/mobile-library-shell.js`。`SoT_library-base.css` を PC Shell から分離 = 値は 1 か所）。
>   裁定 `_decisions/2026-09-25_mobile-library-v1.md`: Top 骨格（検索 → パーツをカテゴリから探す → 新着 4 行 → 注記。扉・メーカー補助リンク無し）/ 一覧 = 製品行のみ / もっと見る 24 件 / 戻り位置は内部 ID / 親カテゴリ chip（sticky 無し）。
>   gate: `_state/mobile_library_check.py` M01〜M14 FAIL 0 / selftest 12/12。PC gate FAIL 0 / selftest 58/58（PC は無変更動作: 8 面 × 5 幅 × light/dark = 80 状態 issue 0）。Mobile 14 URL × 3 幅 × light/dark = 84 状態（Not Found 2 面の H1 無しを除き issue 0）。
>   Launcher / compare.html を Mobile v5 へ切替（旧 library*.html 7 面は採用判定まで残置）。**次 = ASTRA 監査（指示文 `_state/AUDIT_BRIEF_20260925_mobile-library-astra.md`）→ イタヤ実機確認 → mockup**。未 commit。
> **ASTRA 最終確認 OK → PC Library v5 監査 CLOSE（2026-09-25 20:54）**: 再確認で MUST / SHOULD 残件なし（57991・58691・PRO10128-03・PRO10128 の 3 入口一致、D18 記述＝実表示）。gate FAIL 0 / selftest 58/58 / 160 状態 issue 0。次 = commit / push（`mockup`・イタヤ判断）。未 commit。
> **ASTRA 最終確認 → 対応済み（2026-09-25 20:45）**: 前回指摘は解消確認。追加 MUST 1 = Variant カードの仕様が代表のまま（`57991` で ITEM＝XB・仕様＝Kit）→ `cardSpec(p,v)` で一覧の仕様も Variant 優先（L54 拡張・selftest L54c）。SHOULD 1 = D18 の記述を実表示に合わせ明確化（720px 以下 26px / メーカー詳細はパンくず下 Y=136）。gate FAIL 0 / selftest 58/58。未 commit。
> **D18〜D20（2026-09-25 20:28・イタヤ裁定）**: **D18** 製品 Detail の H1 は別の表示契約（製品名 = H1、表示は Identity 見出し 26px。P5 確定・gate L49）／ **D19** 購入 CTA を青（`--lib-buy` = #0969da、Community / Garage の購入色と同値。中立レイヤーの唯一の例外・gate L56）／ **D20** リフォームしやすい土台（トークン・部品 1 か所・name/id/slug 分離・gate。Next.js では版番号を名前に持ち込まない。CORE 候補）。
>   gate FAIL 0 / selftest 57/57。回帰 80 状態 0。ASTRA 以外の残りはイタヤ「Astra と Claude に任せる」。未 commit。
> **ASTRA 再監査 → 対応済み（2026-09-25 20:14）**: 再監査は MUST 1 / SHOULD 2（前回 M1〜M8 は解消確認）。MUST = Variant 型番検索で通常カードが代表へ着地 → `matchedVariant` でカード（検索・カタログ grid/list）も Variant へ（L54）。SHOULD = 並び・表示の操作後に Rail を更新（L45 を操作順で検査）／ HANDOFF J を失効扱い。取り残しの `p.maker` 参照も修正（L55）。
>   gate FAIL 0 / selftest 55/55。操作順チェック 8/8・前回分 24/24・回帰 80 状態 0。P5 は「製品 Detail の H1 は別の表示契約」の文言で裁定待ち。未 commit。
> **ASTRA 監査 → 対応済み・再監査待ち（2026-09-25 17:23）**: ASTRA は MUST FIX 8 / SHOULD 7 / 移行注意 3 を指摘（監査 CLOSE 見送り推奨）。**MUST 8・SHOULD 6・移行 2 を修正**、対応中に検出した誤マッチ 1 件（X1: 正規化で `.` を消し「1.9」が「1979」に当たる）も修正。
>   主な修正: 購入先は URL があるものだけリンク・mock は「未接続」明示（M1）／ 検索照合を `L.productMatch` 1 か所に集約（M2）／ Variant を ITEM・仕様・購入対象・URL の single source に・既定は master と同じ SKU・無ければ未選択（M3）／ 根拠なしの「対応」を節・仕様表とも非表示（M4）／ 仕様をキー付きに（M5）／ Q8 に eol・ページャ上限 10,000 と省略表示（M6）／ 掲載を保証しない文言（M7）／ メーカー索引の q 復元（M8）／ manufacturer の id・slug・name を別値に（URL = slug・関係 = id）。
>   gate **L36〜L53**（L49 欠番）追加 → FAIL 0 / selftest 52/52。ASTRA 再現 24 項目 PASS / 回帰 80 状態 0。回答書 = `_state/AUDIT_RESPONSE_20260925_library-astra.md`（再監査依頼文つき）。**PENDING P5**（H1 契約の適用範囲・製品 Detail）はイタヤ裁定待ち。未 commit。
> **⑧ メーカー詳細 CLOSE → PC Library 8 面 一周完了（2026-09-25 15:09・イタヤ実機確認）**: 面別詳細レビュー ①〜⑧ すべて CLOSE。次は **ASTRA 監査**（指示文 = `_state/AUDIT_BRIEF_20260925_library-astra.md`）→ 結果を見て commit / push（`mockup`）を判断。未 commit のまま。
> **⑦ メーカー索引 CLOSE（2026-09-25 15:07・イタヤ実機確認）**: 正式名 ＋ 根拠のある日本語副表示 / master_aliases 展開の名前検索 / `A–Z` のモード表示 / 国・地域なし。PENDING P4（A–Z / 五十音切替）。→ 次は ⑧ メーカー詳細で PC Library 一周。
> **D17 追加確認 — identity / relation の境界（2026-09-25 14:08・イタヤ）**: `master_aliases` は**検索展開専用**。「タミヤ」→ alias から manufacturer を解決 → **`manufacturer_id`** を得る → 製品は id で取得。⛔ alias_value / name_ja / 表示文字列の一致を identity・relation に使わない。
>   mock も同じ責務に修正: 製品 61 行を `maker_id` 参照へ（表示名フィールドを廃止）／ `makerCounts` `byMakerId` `resolveMakerIds` を id ベースに ／ カタログの `?maker=` は **maker_id**（select の value も id、ラベルだけ表示名）／ Detail の関連も id。gate **L35** 追加。**mock fixture は Research 確定値ではない**（alias_kind / locale / 日本語正式表記 / 読みキーは PENDING）。
> **D17 メーカーの日本語表記と検索（2026-09-25 13:59・イタヤ裁定）**: 「検索」と「並び」を分離。検索対象 = `manufacturer.name` ＋ **`master_aliases`(entity_type='manufacturer')**（⛔ 専用 alias 配列を新設しない）。正規化は NFKC / casefold / 記号 / ひらがな→カタカナまで、⛔ ローマ字→カナ・発音揺れの推測吸収はしない。表示は正式名が主、**根拠のある日本語表記だけ副表示**（⛔ 海外メーカーへ推測カナを生成しない）。索引は A–Z 維持 ＋ モード表示。
>   mock 反映: `タミヤ / たみや / ﾀﾐﾔ / tamiya / TAMIYA / 田宮` がすべて同一メーカー ＋ その製品 11 件へヒット。`トラクサス` → Traxxas 製品 6 件。gate **L34** 追加（v5 FAIL 0 / selftest 34 / 回帰 80 状態 0）。**PENDING P4**: 日本語 locale の「A–Z / 五十音」切替 → Research へ読みキーの持ち方を照会。
> **⑥ RIG Product Detail CLOSE ＋ D3 改訂（2026-09-25 13:42・イタヤ裁定）**: RIG Detail は 5 状態で OK。あわせて **製品画像を「最大 2 枚」→「1 枚」に確定**（2 枚は選定基準が要り面ごとにブレる／許諾・差し替えの管理も倍）。代表画像 1 枚 ＋ NO IMAGE で構造を固定。mock からサムネイル切替を撤去、gate **L33** 追加。裁定原本 = 117 D3 に改訂を追記。→ 残り ⑦ メーカー索引・⑧ メーカー詳細。
> **⑤ RIG Catalog CLOSE（2026-09-25 13:40・イタヤ実機確認）**: facet は形態 / 駆動の 2 つで確定（スケールは size_class 整備後）。Rail の 24 分類は 0 件も表示したまま（現状維持）。見出しの縦位置は PARTS と一致。→ 次は ⑥ RIG Product Detail。
> **④ Library Search CLOSE（2026-09-25 13:26・イタヤ実機確認）**: 6 状態（製品名 / Variant 型番 / 旧型番・別名 / メーカー名 / 大量ヒット / 0 件）で OK。自動転送しない D9 の方針も実機で成立。→ 次は ⑤ RIG Catalog。
> **D15 改訂（2026-09-25 13:21・イタヤ裁定）**: メーカーロゴは **MVP では表示しない / 将来は表示する**。画像類の使用許可を取るときに**ロゴ許諾も同時取得**し、取れたメーカーだけ表示（全社一律でない）。⛔ NO IMAGE スロットをロゴで埋めない。解禁条件 = 書面許諾（提携を示すものでない旨を明記）／ 公式アセットのみ ／ フラグ 1 つで即時撤去。**表示箇所は解禁時に別途裁定（PENDING P3）**。Research の画像許諾フローに「ロゴ許諾も同時取得」を申し送り。
> **③ PARTS Product Detail CLOSE（2026-09-25 13:14・イタヤ実機確認）**: 6 状態（標準 / Variant 着地 / NO IMAGE / 生産終了＋画像なし＋使用例 0 / 使用例 0 / Not Found）で OK。D16「購入導線を弱めない」は実機でも成立。→ 次は ④ Library Search。
> **② PARTS Catalog CLOSE（2026-09-25 13:08・イタヤ実機確認）**: 「探しやすさは担保されている」。確定形 = H1 ＋ lead / 検索 / メーカー select ＋ 生産終了品を除く / 左 Rail 14 分類 ＋ 子チップ / 件数は toolbar 1 箇所 / グリッド・リスト / 24 件ページング。P4・P5 もこのまま。→ 次は ③ PARTS Product Detail。
> **① Library Top CLOSE（2026-09-25 13:04・イタヤ実機確認）**: MVP はこの簡素な形で確定（検索 ＋ 2 扉 ＋ 新着 1 行 ＋ パーツ索引 ＋ メーカー索引の補助導線）。情報量を増やす変更は MVP では行わない。運営開始後の見直しは **POST-MVP PENDING**。→ 次は ② PARTS Catalog 詳細レビュー。
> **H1 統一 — ページ見出しの共通契約（2026-09-25 12:58・イタヤ実機指摘）**: 面ごとに H1 の Y（100 / 106 / 127 / 163）とサイズ（40 / 30 / 36px）が違い、ページ推移で縦位置が動いていた。共有 `SoT_library-shell.css` に **1 契約**を置いた: `.lib-pagehead` = H1（32px）＋ lead 1 行、固定高 68px ＋ 下マージン 24px、⛔ eyebrow・大きなページ番号を重ねない、⛔ 面ごとにサイズを変えない。
>   Top / RIG / PARTS / メーカー索引 / 検索 は **H1 Y=100・操作部 Y=192 で一致**。メーカー詳細と製品 Detail は crumb ぶん下がる（下位面の正しい差）。メーカー索引の lead からも件数を撤去（P1 と同じ文法）。gate L30 を全面へ拡張、**L31 見出し契約 / L32 div 開閉一致**を追加（v5 FAIL 0 / selftest 32 / 回帰 80 状態 0）。
> **Library Top CLOSE 候補 ＋ PARTS Catalog P1〜P3（2026-09-25 11:06）**: Top は T13 をもって CLOSE 候補（情報量は増やさない）。PARTS Catalog は **P1 件数責務を toolbar へ一本化**（lead から総数撤去。絞り込み後に 36 と 8 が並ぶ矛盾を解消）/ **P2 active pill は追加条件だけ**（H1 = 親カテゴリ / sub chips = 子 / pill = それ以外。「すべて解除」もカテゴリを外さない）/ **P3 本文の eyebrow・大きなページ番号を撤去**（Rail の選択状態が現在地。RIG Catalog / Maker Index は当該レビューで追従 = PENDING）。
>   P4 page size 24 は現状維持（Catalog は端数行を許容・viewport から独立）/ P5 breadcrumb は追加しない。修正は共有 `SoT_library-catalog-v5.js` 側で実施（page-local コピーを増やさない）。gate L28〜L30 追加（v5 FAIL 0 / selftest 30）。PARTS Catalog 10 状態 ＋ 回帰 80 状態 issue 0。
> **T13（2026-09-25 10:43・Library 専用スレッド）**: Top 新着を **常に 1 行・件数は列数連動**に（旧: 4 列以上 1 行 / 3 列以下 2 行 → 1320px 6 件 → 1330px 4 件と幅を広げて件数が減っていた）。孤立行なし ＋ 単調の唯一解。gate L27 追加（v5 FAIL 0 / selftest 27）。80 状態 render issue 0。**Top は実機確認で OK なら CLOSE → 次は PARTS Catalog**。
> **面別詳細レビュー開始（18:00）**: 順 = Top → PARTS Catalog → PARTS Detail → Search → RIG Catalog → RIG Detail → Maker Index → Maker Detail。① Top: 第 1 回（09-24 18:00: 統計ブロック撤去 / 扉 26px / 新着は列数ぶんの整った行 / 文章ブロック読み幅 / メーカー索引は検索欄脇）→ **第 2 回（09-25・T1〜T12）反映 = CLOSE 候補（イタヤ実機確認待ち）**: 使用数リンク → Detail 使用例節（from 保持）/ dark の wash を紙と別 token に / 列数を幅に対して単調に（card-min を clamp）/ Detail の戻り文言を from 元に / Library footer に法務行（`<site-legal>` = 既存 Single Source）/ 混在 grid だけ VEHICLE・PART の中立テキスト / hero eyebrow 撤去 / aria-pressed / no-op 撤去 / 注記 12.5px。すべて共有側（fixture / shell / detail / card CSS）。gate L24〜L26 追加（FAIL 0・selftest 26）。**PENDING**: T11（≤1024 の横タブ表現）は Mobile Library 設計時の入力。記録: mock `_state/LIBRARY_EXPLORATION_20260919.md`。
> **PENDING P2（118 では動かさない）— 後続 Front-wide Functional Repair**（イタヤ裁定 2026-09-25）: 2026-09-24 の 106 面監査（mock `_state/XC_AUDIT_20260924_frontwide-106.md`）で見つかった Library 外の問題は 118 に混ぜず、Library PC CLOSE 後に独立レーンで処理する。
>   優先: Mobile 9 面の bottom nav「＋」createSheet 欠落 / 廃止 LOG 種別 setup・非正典種別の残存 / PC の invalid・nonexistent route / shared footer の `#` link / theme persistence 欠落（PC Register・Composer・Auth）/ PC Detail の user menu・通知無反応。
>   ⛔ ページ個別パッチで大量修正しない。CORE L1 Shared UI Single Source に従い Header / Footer / Bottom Nav は共有部品側で解く。構造・整合性項目（AppHeader 複製 / Mobile nav 複製 / category token page-local / font・token・title・brand 表記差）は機能欠陥の後。旧比較面（Library v3 / garage v6 等）は原則対象外（active 採用面を確認してから）。
> **次**: イタヤ実機確認（未 commit）→ OK なら `mockup`（canon 117 / 118 も push）→ 採用後に v4 / Atlas を `_archive/` へ mv・Launcher / compare 更新 → Mobile。

---

**最終更新: 2026-09-19 / revision 117（**Library レーン OPEN — ゼロベース再設計**）**

> 🟢 **117 = Library Design Exploration OPEN。既存 14 面の修繕は後回し。**
> 裁定原本: **`_decisions/2026-09-19_library-zero-base-v1.md`**（Claude 提案 → GPT クロスチェック → イタヤ承認。D3 はイタヤ実機所見で改訂）
> **進行: Library レーンは Claude（Cowork）がプロデュース。** GPT は読み専・随時レビュー。
> ⛔ **Production DB 非接触。migration 未実行。物理 DELETE 禁止。** mock 変更はまだ無い（117 は裁定の記録のみ）。
>
> **DECISION（5 点 ＋ D6 / D7）**
> - **D1 ゼロベース再設計**: 既存 14 面（PC `pc/myrig-library-*-v3.html` 7 / Mobile `library*.html` 7）は
>   情報構造・データ項目・導線の**参照資料**。見た目の継承義務なし。URL / 分類 / Master Detail の項目 / 109 データ契約は引き継ぐ。
> - **D2 Community とは別デザイン体系**: Library 内で RIG / PARTS カテゴリ色を識別体系として使わない。「公式情報」等の強いバッジも設けない。
>   メーカー・型番・スペック・出典・使用例の**情報構造**で Master 情報だと示す（狙いは「製品データベース感 / 資料性」）。
> - **D3 画像ありき・画像依存にしない**: カード / Detail とも**画像スロット常設**。実画像が無ければ **`NO IMAGE`** で同一レイアウト。
>   画像の有無で構造・高さを変えない。**Library 全体の公開可否は 109 B-4 の `master_publication_effective` に従う。画像スロットは、その画像公開条件を満たす場合のみ実画像、それ以外は NO IMAGE。Library 独自の画像公開判定は作らない**
>   （publication 行なし master の一覧掲載可否は Research 照会）。Master Detail は最大 2 枚。
>   旧 `IMAGE PENDING`（確認中）は廃止 — **運用状態を Public UI に出さない**（発売状態も「生産終了」のみ表示）。
>   既存 Master Detail の「Visual Stage ＋ 製品情報」骨格は**維持候補**（ゼロベース ≠ 必ず新しくする）。
> - **D4 PARTS から探索（PC）**: **Master Card → Master Detail → Catalog Top** の順で新規ファイルに作る。
>   最初のカード案は**旧版を見ずに白紙で 1 案** → 完成後に旧版と比較 → イタヤ実機裁定。画像あり / なし混在 fixture。
>   採用後: Library 共通部品化 → RIG → 残り Library（Maker 最後）→ 既存 14 面収束 → Mobile。
>   ⛔ 既存 14 面の修繕（Mobile inline CSS 29,655B×7 の共通化 / リンク切れ 15 件 / `href="#"` 36）は**方向決定より後**。
> - **D5 MyRIG 使用例は H-1 依存**: 「この製品を使っている RIG N 台」は本番では **HOLD H-1（`parts_master_id` 未接続）解消が前提**。
>   mock は 0 件 / 複数件の両状態を作り、0 件でも破綻しない UI にする。⛔ fixture を見て本番算出可能と誤認しない。
> - **D6 件数は metadata として可。Rank には使わない**: 利用・収録件数は製品やメーカーの補助情報として表示してよい。
>   ただし件数による順位付け・ランキング・人気表現・ランキング相当の棚や並び順には使用しない（matrix §7 ランキング全廃）。
>   ⛔「ランキング禁止＝数字も全部禁止」と誤読しない。併せて UI 文言に「マスター」不使用（mobile contract §4 禁止語。内部名は可）。
> - **D7 Library IA = Find / Identify / Bridge の 3 層**（`_proposals/2026-09-19_library-ia_claude-v2.md`）。固定 3 点:
>   カテゴリ着地 URL は一覧の preset state（専用 UI 複製なし）/ 一覧カードは Detail への単一入口（購入は Detail）/
>   Platform は将来の First-Class 候補（compatibility HOLD 解消まで本線化しない）。
>
> **実測（116 時点の Library 現在地）**: 2026-07-27 P22-B14 の暫定確定以降 Library 専用レーンは未実施。B14 継続課題は全件未裁定
> （①②は D2 で Library 範囲を解消。③は D2 で言い換え。④は Catalog Top 時点で裁定）。Library 専用 gate 無し。
>
> **PENDING（117 では動かさない）**: H-12 / Front-wide Consistency Audit / H-7 系 / H-1 / 公開範囲・SEO の扱い（画像許諾が揃うまで控えめにする案は未裁定）。
> ⛔ Library の作業中に Register / Detail / Launcher を掘り直さない（116 継続）。
>
> **探索の現在地**: mock `pc/myrig-library-explore-parts-card-v0.html`（画像枠なし → 不採用方向）/ **`-v1.html`（画像スロット常設。1280px 4 列で 8 枚 394px 均一）**。
>   記録: mock `_state/LIBRARY_EXPLORATION_20260919.md`。⛔ 117 はカード採用の revision ではない（採否はイタヤ実機）。
> **設計図**: `_proposals/2026-09-19_library-ia_claude-v2.md`（PROPOSAL。Find / Identify / Bridge の 3 層。v1 は GPT レビューで改訂）。
> **カード**: v1 は RIG サムネ撤去 ＋ 製品画像 `contain` で**収束候補**（イタヤ判定 2026-09-19）。
> **⑤ PARTS Catalog v1 = mock `pc/myrig-library-parts-v4.html`**（検索 ＋ 14 カテゴリ Directory ＋ 共有 filter ＋ 全件一覧。preset state は `?category=&sub=`）。
>   カード共有 CSS `assets/css/SoT_library-card.css`。記録: mock `_state/LIBRARY_EXPLORATION_20260919.md` 探索 3。⛔ 既存 v3 は無変更。
> **⑤ の設計処遇（09-24 GPT レビュー → 反映済み）**: Facet 件数は Q8 に従う（初期状態だけ概数。検索語・条件が乗ったら各 Facet の件数は消す。総件数は 10,000+ で打ち切り）。
>   検索窓は Catalog 見出し側に置く（lib-hd に 2 本目を置かない。② は同じ検索部品の scope 違い）。0 件文言「MyRIG での使用例はまだありません」。Detail リンクは TEMP（⑥ v1 後に差し替え）。
>   Research 照会の文言: 「親14・子90 で正しいか」ではなく **「現在有効な親14・子90 の正式名称・slug 一覧」＋ 子 90 の命名基準点検の先出し**（cross-ref v4 §子カテゴリ）。
> **Library 全 8 面（PC）通し build 完了（09-24、イタヤ指示「止めずに最後まで」）**: ① `pc/myrig-library-v4.html`（入口）/ ② `-search-v1` / ③ `-rigs-v4` / ④ `-rig-master-detail-v4` /
>   ⑤ `-parts-v4` / ⑥ `-parts-master-detail-v4` / ⑦ `-makers-v4` / ⑧ `-maker-detail-v4`。共有 `SoT_library-{{card,catalog,detail}}.css` ＋ `SoT_library-{{fixture,catalog,detail}}.js`。
>   gate `_state/library_check.py`（L01〜L11 ＋ selftest）FAIL 0。render 12 状態 × 4 幅 overflow 0。記録: mock `_state/LIBRARY_EXPLORATION_20260919.md` 探索 4。
>   既存 v3 14 面 / Launcher / compare.html は無変更。⑨ Platform / Mobile は未着手。
> **次**: `mockup` 実行 → イタヤが 8 面を通しで評価（① から辿る。状態 URL は探索記録 §探索 4）→ 採否 / 修正 → 採用後に Launcher 導線・compare ペア更新 → Mobile。

---

**最終更新: 2026-09-19 / revision 116（**Register / Relationship レーン CLOSE → 次レーン = Library**）**

> 🟢 **116 = 区切り。ここまで CLOSE。次は Library 系。**
> 114 → 115 → 116 は連続する 1 レーン（Relationship MVP → Multi-RIG → Register Family 完成 → 横断監査 → Launcher 整理）。
> ⛔ **Production DB 非接触。migration 未実行。物理 DELETE 禁止。** 116 で新しい裁定は無い（状態の締めだけ）。
>
> **116 で CLOSE と確定したもの**
> - **Relationship MVP（114）/ Multi-RIG Relation（115）**: 1 PARTS が複数 RIG に関連できる。schema **v1.6-r7**（`idx_rig_parts_active_pair` のみ）。
>   裁定原本 `_decisions/2026-09-18_relationship-mvp-v1.md` / `_decisions/2026-09-19_multi-rig-relation-v1.md`。H-2 / X-1 / X-2 / X-4 解消。
> - **Mobile PARTS Register**（`register-parts-v2.html`。113 → 115 で採用）。
> - **PC LOG Composer 再構成**: 本文 → 写真 → 種別 → 実施日 → 任意項目（2 列 × 2 段: タイトル｜場所 / 関連RIG｜関連パーツ）。
>   「対象RIG」上部 UI 撤去。RIG 既定は未指定、`?rig=<id>` / `?part=<id>` 入口で初期選択。MVP は 1 RIG（`maintenance_logs.rig_id`）。
>   本文は見出し無し・placeholder「記録しておきたいことを書く」。Modal 内スクロール無し。記録: mock `_state/LOG_COMPOSER_ORDER_TREATMENT.md`
> - **Mobile LOG Register**: `register-log-v2.html` + `js/register-log-v2.js`。PC と同じ順序・意味論。`[LOG] ログを書く`。
>   写真 3 / cover 無し / caption 無し / 章・Advanced 無し / 離脱ガード無し（下書き自動保存。PC と同じ）。記録: mock `_state/MOBILE_LOG_REGISTER_TREATMENT.md`
> - **Register Family PC / Mobile 6 面 = 採用版**:
>   PC `pc/myrig-register-rig-v3.1.html` / `pc/myrig-register-parts-v1.1.html` / `pc/myrig-log-composer-v1.html`
>   Mobile `register-rig-v2.html` / `register-parts-v2.html` / `register-log-v2.html`
> - **Detail / Register 横断監査（16 面）CLOSE**: MUST FIX 4 件を同日修正。記録: mock `_state/XC_AUDIT_20260920_detail-register.md`
>   ① Create 入口（PC 29 面 `href="#"` / Mobile 28 面 `/register/*` 404）→ 採用版へ結線
>   ② Owner Detail 4 面「…のログを記録」stub → Composer / Register の `?rig=` `?part=` 実リンク
>   ③ PC Register「ガレージに戻る」404 → `back-href` ④ PC PARTS Register `data-field` を `entity_links` / `rig_parts` へ
>   新 gate `_state/entry_link_check.py`。
> - **Launcher cleanup**: `index.html` は短い現在地 + 採用版のみ。「旧 v6 を見る」導線 8 本撤去。見比べ / 幅 / テーマは維持。
>   旧 Mobile Register B15 / LOG Composer 比較 / Detail v6 / 探索案 / preview は `_archive/20260920_pre-library-cleanup/` へ **mv**
>   （⛔ 削除なし。`_archive/` は gitignore のため **`git add -f` で追跡**し、git 上も「退避」として残す）。
>   garage v6（8 面）は gate 凍結 baseline として `pc/` に残置。記録: mock `_state/LAUNCHER_CLEANUP_20260920.md`
>
> **PENDING のまま（116 では動かさない）**
> - **H-12** Advanced トリガー色（暫定中立。フロント主要面完成後の横断デザイン監査で裁定）
> - **Front-wide Consistency Audit 候補**: 選択後サムネ無し / 通知色と PARTS 色の値衝突 / `entity_links` の UI 文言（外部リンク / 関連リンク）/ 件数 fixture の食い違い
> - **H-7 系**: Owner Edit（まとめて編集 / 削除 / 節編集 stub）+ Owner LOG Detail（未設計。LOG は Composer のみ）
> - H-1 / H-3〜H-6 / H-8 は変更なし
> - gate の既知: mock `mobile_garage_list_check` MG16 が Public LOG 一覧に「セットアップ」チップを要求するが、`setup` は LOG 種別から**廃止済みの旧仕様**。
>   ⛔ UI に setup を復活させない。gate 側を旧仕様として扱う（Library のブロッカーにしない）。
>
> **次のレーン: Library 系**（カタログトップ / 車種カタログ / パーツカタログ / メーカー / 製品情報）。
> ⛔ Library の作業中に Register / Detail / Launcher を掘り直さない。

---

**最終更新: 2026-09-19 / revision 115（**イタヤ実機確認 3 点 — 中立 Advanced / Mobile 種別識別 / Multi-RIG Relation**）**

> 🟢 **115 は mock 実装まで完了。Register / Relationship バッチは CLOSE。**
> 🟡 **ただし Advanced トリガーの色だけは最終 CLOSE にしない → `HOLD H-12`。**
> 裁定原本: **`_decisions/2026-09-19_multi-rig-relation-v1.md`** / schema **v1.6-r7**
> ⛔ **Production DB 非接触。migration 未実行（索引 1 本の DROP も未実行）。物理 DELETE 禁止。**
>
> **発端**: 114 の Relationship MVP を実機で触って出た 3 点。
>
> **① Advanced トリガーを中立へ揃えた**
> - Mobile Register の「さらに詳しく記録する」が **カテゴリ色の全面塗り**（113 の D-F）だった。
>   PC v3.1 は 青ベタ → 中立 → カテゴリ色ベタ → **中立**（2026-09-17 差し戻し）と辿っており、**Mobile だけ残っていた**。
> - → **Mobile も中立**（`--ms-bg-subtle` ＋ 1px 罫線 ＋ Chevron / inset 角丸は維持）。
>   ⛔ `background: var(--reg-cat)` に戻さない。**同じ理由で 2 回戻している。**
> - 🟡 **ただしこれは暫定。最終裁定ではない。**（イタヤ 2026-09-19）
>   RIG=黄 / PARTS=赤 を PC / Mobile 共通で使った方が良い可能性は残っている。
>   ここだけ **`HOLD H-12`（フロント主要面完成後の横断デザイン監査候補）** として残す。
>   ⛔ 単独バッチで再修正しない。⛔ 「CLOSE 済み」として扱わない。
> - ⚠️ **D-F（113）は実装としては CLOSE**（`?adv=cat` の比較は終了）。**色の最終裁定は H-12 へ移した。**
> - ⚠️ イタヤの指摘文は「PC だけ強い色が残っている」だったが、**実測では逆**（PC は中立・Mobile が塗り）。
>   どちらの読み方でも結論は同じ（強い塗りをやめて中立へ揃える）なので、そのまま進めた。
>
> **② Mobile Register Header に種別バッジ**
> - RIG / PARTS は骨格がほぼ同じで、文字タイトルだけでは現在地が読めなかった。
> - → **`[RIG] RIGを登録` / `[PARTS] PARTSを登録`**。PC Register Header の `.mr-reg-header__cat` と**同じ語彙**。
> - 共有 `css/mobile-register.css` の `.rr-hdcat` 1 本。色は面が宣言する **`--reg-cat` / `--reg-cat-on` だけ**を見るので、
>   **Mobile LOG Register は `--reg-cat: var(--cat-log)` を 1 行足すだけ**で同じ文法になる。
> - バッジは `aria-hidden`（読み上げは h1 が持つ）。⛔ ページ全体をカテゴリ色で塗らない（NG-7）。
>
> **③ Multi-RIG Relation（114 からの方針変更）**
> - **1 PARTS = 同時 1 RIG をやめた。**「このRIGへ移す / 同じ製品をもう1個登録する / キャンセル」の確認は **撤去**。
> - 根拠: **MyRIG は厳密な物品在庫管理ではなく、どの RIG でどの PARTS を使っているかを記録できればよい。**
>   ユーザーに「物理的に同じ個体か」「本当に移動したか」「もう1個所有しているのか」を判断させない。
> - **schema v1.6-r7**: `idx_rig_parts_active_part` を **撤去**。残るのは `idx_rig_parts_active_pair` だけ。
>   ⛔ UI だけ隠して索引を残さない（2 台目の INSERT が UNIQUE 違反で落ちる）。
> - **取り下げたもの**: 裁定 114 §3-3（別 RIG 装着中の移す確認）/ schema r6 の `parts` 説明文「順次（同時は 1 台）」。
> - **HOLD H-2（共有機材の複数 RIG 同時関連）は解消**。送信機・バッテリーも `rig_parts` に RIG ごとの `active` 行で持つ。
>   ⛔ 専用テーブルも「共有機材」分類も作らない。**H-1（`parts_master_id` 未接続）は変わらない。**
> - **X-1 も解消**: PC PARTS Register v1.1 の複数行リストが索引と食い違っていた件は、索引側が無くなって一致した。
> - 文言: 「装着RIGを変更」→ **「装着RIGを追加」**。状態ラベルに **`○○ ほか N 台に装着中`** を追加。
>   ⛔「移しました」と言わない（ほかの装着は外れないので嘘になる）。
> - 伝播規則（RIG 削除 / 手放す / PARTS `released` → `active` を `removed`）と Q4 の一括確認は **残す**。
>   根拠だけ「索引の帰結」→「手放した RIG に `active` が残ると表示が嘘になるから」へ差し替えた。
>
> **検査**: 新規 `_state/register_identity_check.py`（static / behave）。
> `relation_check` B2 は「確認が出ないこと・『移しました』と言わないこと」を見る側へ反転。
> `mobile_parts_register_check` は「2 台目が追加されること・選択済みが候補から外れること」を見る。
>
> **⛔ やっていないもの**: 過去装着タイムライン / 厳密な在庫・個体管理（数量・シリアル）/
> 「同じ製品をもう1個」を表現する仕組み / 既存データの意味推定変換 / migration 実行。
>
> **このバッチの状態（2026-09-19 / イタヤ）**
> - **Register / Relationship 機能バッチは CLOSE。**
> - **例外 1 件**: Advanced トリガーの色だけ最終 CLOSE にしない → **`HOLD H-12`**。
>   いまの中立表示は**暫定**。⛔ 今は追加修正しない。
> - **次の本線**: Register Family の残りは **Mobile LOG Register**（RIG / PARTS は 113 / 115 で採用済み）。
>   共有 `.rr-hdcat` は `--reg-cat: var(--cat-log)` を 1 行足すだけで使える。
>   relation は共有 `SoT_relation-picker.js` の `openMyParts({mode:'reference'})` をそのまま読む。

---

**最終更新: 2026-09-18 / revision 114（**Relationship MVP — RIG / PARTS / LOG の関連付け**）**

> 🟡 **114 は裁定と正典の起票まで。mock 実装はこれから。**
> 裁定原本: **`_decisions/2026-09-18_relationship-mvp-v1.md`** / schema **v1.6-r6**
> ⛔ **Production DB 非接触。migration 未実行。物理 DELETE 禁止。Research 所有領域 不変。**
>
> **発端**: Mobile RIG Register（113）を「実際に自分で登録するつもりで」触った結果、
> **RIG / PARTS / LOG が互いに孤立している**箇所が見つかった。仕様書だけでは出なかった穴。
>
> **実測で分かった現在地**
> - RIG Register（PC / Mobile）からパーツを足すと **必ず新しい `parts` 行を作る**。
>   既存の自分の PARTS を選ぶ経路は **0 件**（grep 実測）
> - 「関係だけを張る」経路は **PARTS Register の『搭載RIG』1 本だけ**だった
> - Detail のリンク先は **すでに User Entity（PARTS Detail）が主**で、そこから Library へ降りる二層
>   （v15 の使用パーツ 13 リンクが全て PARTS Detail 宛て）→ **Detail 側は直す必要がない**
> - **LOG ↔ PARTS はデータも表示も両側とも無い**（111 §12 で v1 非搭載）
>
> **DECISION（裁定 Q1〜Q6）**
> - **Q1** LOG は **RIG のみ / PARTS のみ / 両方 / どちらも無し の 4 状態すべて許可**
> - **Q2** `maintenance_log_parts` に **`rig_parts_id` を持たない**。`log_id ↔ part_id` の単純 relation が SoT。
>   🔴 将来追加しても**過去行は遡って埋められない**（推定禁止・日付からも復元不能）。**恒久的に NULL。** schema に明記
> - **Q3** 非公開 PARTS は公開面に **出さない・数えない**。→ **Owner と第三者で「使用パーツ」件数が変わるのが正常**。
>   `section__count` は静的値ではなく**閲覧者に見える PARTS だけ**で計算する
> - **Q4** RIG を手放すとき、装着中 PARTS は **1 回だけ一括確認**（一緒に手放した / 手元に残した。既定は後者）
> - **Q5** picker は **`ownership_state='owned'` のみ**。⚠️ 既に張られた relation は `released` でも消えない
> - **Q6** 関連 PARTS は **UI 暫定 10 件。DB 制約にしない**
>
> **追加で確定したもの**
> - **パーツ追加は 3 経路**: **マイパーツから選ぶ → 製品から探す → 手入力する**。
>   ⚠️ 1 番目に置く根拠は **H-1（`parts_master_id` 未接続）で Master 一致の重複検出が構造上できない**こと。
>   MVP で使える重複防止は**この経路そのもの**
> - ~~**「別 RIG に装着中」を選んだら必ず確認**（移す / もう 1 個登録する / キャンセル）~~
>   🔴 **115 で取り下げ**。確認は出さず、そのまま関連付ける（`_decisions/2026-09-19_multi-rig-relation-v1.md`）
> - **共有 `SoT_relation-picker.js` を新設**し 5 面が同じ 1 本を読む（CORE 共有UI Single Source L1）。
>   ⚠️ 実測で mock に `MY_PARTS` が**存在せず**、`MY_RIGS` は PARTS Register にローカル定義されていた → **共有へ引き上げる**
> - **「装着 RIG の LOG」を「このパーツの LOG」と見せない**（111 維持）
>
> **schema v1.6-r6（列は 1 本も変えていない）**
> `maintenance_log_parts` **新設 1 表のみ** / `rig_parts` に **親の状態変化の伝播規則** /
> `parts` 説明文「複数RIGに装着可能」→ **「順次（同時は 1 台）」**（索引と食い違っていた）/
> `parts.nickname` に「`rigs.nickname` と可視性が逆」/ RLS の `rig_parts` 行が **r3 の `status` 化を反映していなかった**のを是正。
>
> 🔴 **伝播規則が無いと何が起きるか**: ~~`idx_rig_parts_active_part` があるため二度と装着できなくなる~~
> 🔴 **115 で根拠を差し替え**（索引は撤去した）。規則は残る。理由は
> **手放した RIG に `active` な装着が残り続けると「いまこの RIG に付いている」が嘘になる**から。
>
> **111 §12 の再 OPEN**: 111 は「両側に表示面が無く死蔵入力になる」ことを理由に非搭載とし、
> **「PARTS Detail 側の表示契約とセットで裁定する」**と条件を書いていた。
> 今回は LOG Detail と PARTS Detail を**同時に**作るので、**111 が予告した経路**。113 との矛盾ではない。
>
> **VISUAL LOCK（裁定）**: 今回触る 5 面（Public Detail 3 / Garage Owner Detail 2）は
> **canon 114 → mock 実装 → diff 確認 → 意図した差分だけと確認 → 新 baseline 採用 → 以後 pixel diff 0** の順。
> ⛔ **旧 baseline は削除しない**（適用前の履歴）。
> **触らない面（Home / Feed / Library / Search / Browse / Garage Top / Public Garage）は既存 baseline に対し pixel diff 0 を維持。**
>
> **⛔ 今回やらないもの**: PARTS の過去装着タイムライン / LOG 時点の装着エピソード保存 /
> LOG シリーズ・ツリー・`parent_log` / ~~共有機材の複数 RIG 同時関連（HOLD H-2 のまま）~~（🔴 **115 で解消**）/
> 自動関連推定 / 数量列。
>
> **次の作業（実装レーン）**
> 1. 共有 picker（`SoT_relation-picker.js`）→ Mobile RIG Register で実測
> 2. PC RIG Register / PC PARTS Register
> 3. Garage RIG / PARTS Detail の stub 結線 ＋ Q4 確認
> 4. LOG Composer ＋ LOG Detail ＋ PARTS Detail
> 5. Mobile 4 面追随 → 新規 gate `_state/relation_check.py` ＋ 既存 gate 全回し ＋ 反証確認
> 6. VISUAL LOCK baseline 更新（上の順序）
>
> **⛔ 再 OPEN しないもの**: PC RIG / PARTS Register の章立て・写真・保存意味論 /
> LOG Composer の種別・本文・写真・投稿後動線 / Mobile RIG Register の flow・chrome・Advanced トリガー（113 の裁定）。

---

**最終更新: 2026-09-18 / revision 113（**Mobile RIG Register 採用 — progressive × focus × inset**）**

> 🟢 **113 で Mobile RIG Register の UI が決まった。** 実体 `register-rig-v2.html` ＋ `js/register-rig-v2.js`（mock）。
> 処遇表 `myrig_pc_Ver3/_state/MOBILE_RIG_REGISTER_TREATMENT.md`（v3）、検査 `_state/mobile_register_check.py`。
> ⛔ **Production DB 非接触。migration 未実行。PC Register 3 本は触っていない。**
>
> **DECISION（実モック比較 ＋ イタヤ実機裁定 2026-09-18）**
> - **D-A** P22-B17「登録フロー最小化」を根拠に 02〜04 を登録後へ送ることは **しない**。
>   02 メカ・パーツ / 03 所有情報 / 04 公開・リンクは **Advanced「さらに詳しく記録する」の中に残す**。
>   実機判定の理由は「登録後へ送る必要性より、**トリガーが目立たず展開できると気づけない**ことの方が問題だった」。
>   → **defer 0 行**。PC の入力 14 種はすべて Mobile に実在する（gate C3 で実測）。
> - **D-B** **progressive 採用**（1 ページ段階開示）。**steps（4 面ウィザード）は不採用**。
>   理由: 02〜04 が任意であることを形で言えるのは progressive だけ／作成後の編集は連続 Editor なので
>   「登録だけウィザード」の二重文法を作らない。
> - **D-C** **focus 採用**。**登録中は BottomNav を出さない**（markup ごと撤去）。契約 §3.2 の Register 例外として明文化した。
> - **D-D** 写真の並び替えは **写真 Sheet 内の「前へ移動 / 後ろへ移動」**。**drag を必須にしない**。
>   Cover は `is_primary` ではなく **選んだ 1 枚を id で追う**／表示順 SoT は `sort_order`（契約不変）。
> - **D-E** **`window.beforeunload` は採用しない**。**未作成 ＋ dirty のときだけ**、SubHeader の戻る と
>   ブラウザ / PWA Back を route-leave guard に通して確認（「入力内容を破棄しますか？／まだ RIG は作成されていません。」
>   ［編集を続ける］［破棄して戻る］）。**作成後は autosave を信頼して出さない。**
>   履歴 marker は **常に高々 1 つ**（clean → dirty で 1 回積む。Back に消費されたら「編集を続ける」で 1 つだけ積み直す）。
> - **D-F** 🔴 **115 で差し戻し・CLOSE**（中立へ。⛔ カテゴリ色の全面塗りに戻さない）。旧採用内容は以下。
>   ~~Advanced トリガーは **RIG 黄 `--cat-rig` の inset 角丸ヘッダー**~~
>   （左右 `--ms-gutter` / `border-radius 13px` / 全面塗り ＋ `--cat-rig-on`。⛔ border / shadow / gradient / 片側色線なし。
>   open / closed で地色は変えず Chevron だけ回す）。02 / 03 / 04 は番号バッジ文法のまま、操作色は中立のまま。
>   ⚠️ **PC v3.1 は 2026-09-16 に同形（全面帯）を採用し 09-17 に「強すぎる」として中立へ戻した前例がある。**
>   113 の inset は**横いっぱいの帯ではなく独立した角丸コントロール**で役割が違う。**PC への横展開は未裁定**。
>
> **単一化（比較分岐の撤去）**
> 裁定後、`?flow` / `?chrome` / `?adv` を本体から撤去し **progressive × focus × inset の 1 本**にした。
> 比較版の実体は `myrig_pc_Ver3/_archive/20260918_mobile-register-compare/`（README に checkout 手順つき）。⛔ 削除していない。
> 残すオプションは `?debug=1` と `?theme=dark` のみ。
>
> **契約への追随（113 で canon を更新した箇所）**
> - `docs/ui/page-role-matrix-v1.md` — `/register/rig` の「ステップ式」を **連続 Editor / progressive** へ（D-B）
> - `docs/ui/mobile-component-contract-v0.5.md` §3.2 — **Register 例外**（登録・編集系は BottomNav 非表示。
>   Register は Utility 面なので現在地も持たない）を追記（D-C）
> - `docs/ui/pc-mobile-spec-inheritance-v1.1.md` #28 / #29 / #30 — ファイル名を現行へ。
>   #28 特記の「離脱警告はモバイル再利用要件」を **実体と不一致**として D-E の設計へ差し替え。
>   #30 の 111 前のモーダル構成を失効として明示
> - `docs/design/design-nogo-list.md` NG-7 職域表 — **「セクション見出し・展開コントロールの種別識別」行を追加**（D-F）
>
> **実測（Chromium headless / `_state/mobile_register_check.py`）— 4 gate すべて FAIL 0**
> `static`（処遇表 ↔ `data-treat` 43 行の機械突合 / 裁定なき merge・defer・drop 0 / 禁止語 0 / 失効色 0 /
> 枚数直書き 0 / nested interactive 0 / 比較分岐 0 / 退避先の実在）・`render`（360・390・430px × light / dark）・
> `behave`（28 項目）・`counter`（反証確認 ＋ inset 形状 ＋ D-E 11 項目）。
> **実 hit 48×48 未満の control 0**（`getBoundingClientRect` ＋ 絶対配置 `::after` の合成で判定。CSS の grep ではない）。
> ⚠️ **R49 キーボード退避（`visualViewport` で Dock を持ち上げる）だけは headless で再現できない。iPhone 実機で確認する。**
>
> **PENDING / 未裁定**
> - PC Register 側の同型不具合 3 件（本レーンでは直していない）:
>   ① `pc/myrig-register-rig-v3.1.html` の `#fOwn` が **「欲しい / 計画中」**のまま（108 契約では `wishlist` = 欲しい。
>      `planned` は MVP 非構造化）② 同ファイルの `button#phEmpty > span#phSample[role=button]` が **interactive の入れ子**
>   ③ D-F の inset を PC Register Family へ横展開するかどうか
> - `<myrig-register-header save-status="saved-draft">` を RIG / PARTS へ揃えるか（111 からの PENDING）
> - Shell の `toast(msg)` は action を取れない。R9「取り消す」は page-local 実装のまま（契約 §7-1 のクロスチェック対象）
>
> **次のレーン: Mobile PARTS Register → Mobile LOG Register。**
> Mobile LOG は 111 の契約差分（種別任意 / location のみ / 写真 3 枚 / Cover なし）が全部乗るので最後に回す。

---

**最終更新: 2026-09-17 / revision 112（**LOG Detail Compatibility Gate ＋ LOG Register PC CLOSE**）**

> 🟢 **112 で LOG レーンの PC が閉じた。**
> **⛔ これは Detail の再設計ではない。111 契約への整合修正。** 067 の LOG Detail CLOSE と
> 既存デザイン骨格はそのまま維持している（骨格・情報階層・写真文法は変更なし）。
>
> **① LOG Register PC = CLOSE**
> 採用版 `pc/myrig-log-composer-v1.html`。canon **8cb8389** / mock **a1a6820** とも **push 済み・
> Vercel production READY 済み**（イタヤ確認）。111 作成時点の「push 未実行 / Vercel 未確認 /
> CLOSE ではない」という記述は**当時の状態**であり、**112 で解消**した。
>
> **② LOG Detail Compatibility Gate = 実施・整合済み**
> 111 の Composer / データ契約と既存 LOG Detail を E2E で突き合わせた。
> 対応表 12 フィールド × nullable matrix 11 状態を実測し、**PASS 9 / MINOR_FIX 2 / DECISION_REQUIRED 2**。
> DECISION_REQUIRED 2 件はイタヤ裁定を得て実装済み（下の C-1 / C-2）。
> ⚠️ **Gate で見つけた事実の訂正**: 111 バッチで「?title=none → h1 0」と報告したのは**計測ミス**だった。
> 合成 sr-only h1 は実在していた（`.dt-log-title` だけを数えていた）。C-1 はその是正でもある。
>
> **③ C-1 裁定: title=NULL 時の H1**
> `log_type + logged_at + author` から見出しを組み立てる**合成 sr-only h1 を廃止**
> （例「LOG 整備 · 2026.03.18 — crawler junkie」）。111 の「⛔ ユーザーが入力していない title を
> 偽生成しない」を優先する。**ページ構造上の h1 は維持**し、title=NULL のときだけ
> `<h1 class="sr-only">ログ詳細</h1>` を置く。**ユーザー投稿の title ではなく、ページ種別を示す
> 固定の構造見出し。** ⛔ 文言を状態から組み立てない（固定文字列 1 つ）。
> title あり → visible `.dt-log-title` が h1、sr-only は足さない。
>
> **④ C-2 裁定: location-only の Log facts**
> 111 以降、新規 LOG が structured facts として持てるのは `location` だけになる。
> - `location` だけ（`surface` / `weather` / `duration_minutes` 全 NULL）＝**新規 LOG の通常形**
>   → 「ログ情報」section を作らず **compact metadata row 1 本**（見出しなし・フル幅）
> - legacy が 1 つでも非 NULL → **従来の「ログ情報」section を維持**。`location` もその中へ入れる
> - 全 NULL → 何も出さない（既存どおり）
> ⛔ `location` を上部 context へ移す再設計はしない（位置は actions の下・comments の上のまま）。
> ⛔ map icon / link 化 / GPS / 地図 / place entity を作らない。`location TEXT` をそのまま出す。
> ⛔ LOG 専用の新 component を作らない。既存の中立語彙 ＋ 1 列化 modifier `.spec-grid--single` だけ。
> ⛔ 既定の `.spec-grid` / `.spec-grid--quiet` は触っていない（consumer は RIG v15 / PARTS v1-open ほか）。
> ⛔ **既存 DB 値は捨てない。** legacy 3 項目は Composer v1 で入力させないだけで、値があれば表示する。
>
> **⑤ Gate の実測（Chromium headless）**
> LOG Detail 33 状態（nullable matrix A〜K ＋ 写真 0/1/2/3 ＋ 16:9 / 4:3 / 縦 / mix ＋ light / dark /
> 1280・820・420px）と RIG / PARTS / garage RIG Detail × 3 幅で
> **console error / pageerror / 非画像 404 = 0**。孤立 separator・空 heading・空 shelf・横溢 = 0。
> 写真は全パターンで**元比率と表示比率が一致**（crop なし・歪みなし）。
> Composer → toast「見る」→ Detail の E2E も、title / type / rig / date / photo なしで通る。
> ⛔ **Production DB 非接触。migration 未実行。** `maintenance_logs` は App 所有で 109 の境界契約に未接触。
>
> ⚠️ **残り 1 手（112 時点で未了）**: C-1 / C-2 を入れた mock の
> **`b14590c` と `77b2614` が未 push**（Cowork のデバイス VM に GitHub 認証情報が無い）。
> イタヤが Mac ターミナルで push → **Vercel production READY と production 上での
> 通常 / 最小 / location-only の再確認**が必要。それが済めば LOG レーン PC は完全に閉じる。
>
> 🟢 **STATE（113 で解消・上の記述は 112 作成時点のもの。⛔ 履歴として残す）**:
> mock `77b2614` は **push 済み**、Vercel production **READY 確認済み**（2026-09-17 イタヤ）。
> **PC LOG レーン（Composer / Detail / Compatibility Gate）は完全 CLOSE。**
>
> **次のレーン: Mobile Register 3 本（RIG / PARTS / LOG）。**

> 🟢 **111 で LOG Composer PC の正式採用版が決まった。**

> 🟢 **111 で LOG Composer PC の正式採用版が決まった。** `pc/myrig-log-composer-v1.html`
> （探索 A → A/D/E → **A2** を採用。比較モック `pc/myrig-log-composer-v1-compare.html` は Exploration として
> active tree に残すが**通常導線からは外した**。旧 `myrig-log-composer-modal-v0.3.9.html` は
> `_archive/20260917_log-composer-close/` へ **mv**。⛔ 削除していない）。
> 裁定原本: **`_decisions/2026-09-17_log-composer-contract-v1.md`**（理由つき・14 節）。
> ⛔ **Production DB 非接触。migration 未実行。既存行の意味推定変換なし。Research 所有 schema 未変更。**
>
> **LOG 契約の主要差分（schema v1.6-r5）**
> - `log_type` を **任意分類**へ: `NOT NULL` / `DEFAULT 'maintenance'` を撤廃。**NULL＝分類していない**。
>   `memo`＝ユーザーがメモとして分類した。**別物**。CHECK は 4 値のまま（PostgreSQL の CHECK は NULL を通す）。
>   ⛔ 5 値目 `other` を復活させない（`other` / `setup` は v1.2 廃止済み slug）
> - `title` を **NULLABLE** へ（実 UI と逆転していた）。⛔ 本文冒頭から偽 title を生成しない
> - `body` を **NOT NULL DEFAULT ''** ＋ 公開時 CHECK（`is_public=false OR btrim(body)>=1 文字`）。
>   **投稿ゲートの 10 文字は App の UX 値。⛔ DB 契約値として固定しない**
> - 補助入力は **title / location の 2 つだけ**。`duration_minutes` / `surface` / `weather` は
>   **列を維持したまま Composer v1 から新規入力させない**（DROP しない・既存データ不変）
> - 写真 **最大 3・Cover なし・caption 入力なし・crop なし**・`sort_order` が表示順 SoT。
>   共有 `SoT_register-family.js` の `photoMax('log')` を **7 → 3** へ追随、`hasCover('log')=false` を新設
> - 関連パーツ / 外部リンクは **v1 非搭載**。⛔ `maintenance_log_parts` を作らない。⛔ 本文 URL を linkify しない
> - 投稿後は **LOG Detail へ自動遷移しない**。modal を閉じ scroll 維持 → toast「投稿しました　見る」
>
> **共有部品への昇格（⛔ LOG 専用コピーを作らない）**
> `<myrig-register-header variant="modal">` 新設（ロゴ・戻る無し／閉じるが `myrig:register-close` を dispatch）/
> `save-status="saved-draft"`（「下書き保存済み」）を共有語彙へ追加 / `.bar--inset`（Action Bar の container variant）。
> ⚠️ **保存文言は RIG / PARTS へ波及させていない。** あちらは公開後も同じ画面で autosave するため
> 「下書き」が偽になりうる。意味が同じか確認してから揃える（PENDING）。
>
> **横断追随（semantic guard のみ。デザインは変えていない）**
> `SoT_card-components.js`（list / sm-meta の偽 fallback `'LOG'` 撤去・pc / list の title NULL で h3 ごと出さず
> excerpt 繰り上げ・Feed の空 type span / 空 data 属性を出さない）/ `myrig-log-detail-v1.html` ＋
> `js/detail-state-demo.js`（`?type=none` 追加。種別と separator を両方落とす）/
> `docs/search/search-page-plan-v2.md`（LOG の検索対象へ **body** を追加）。
> フィルタは **「指定なし」専用 UI を MVP で作らない**（「すべて」に NULL LOG を含む）。
>
> 🔴 **過去記録の是正（削除せず併記）**: 下の「✅ 完了（2026-08-22 モック是正A）」に
> **「廃止値 setup の撤去 … これで log_type 絞り込み UI は全ページ 4 値に揃った」**とあるが、
> **実体は揃っていなかった。** 111 の再 grep で `pc/myrig-public-garage-logs-v3.html`（フィルタチップ ＋
> カード `data-type`）と `user-garage-logs.html`（フィルタチップ）に「セットアップ」が残存していた。
> **111 で撤去した**（カードの `data-type` は v1.2 の移行先 `custom`＝「カスタム」へ是正）。
> ※ 本文中の「セットアップを見直した」等の普通の日本語と `tags="セットアップ,Tires"`（ユーザー自由入力タグ）は
> `log_type` ではないので対象外・残置。
>
> ⚠️ **push は Cowork から実行できない**（デバイス VM に GitHub 認証情報が無い）。
> **canon / mock とも local commit まで。イタヤが Mac ターミナルで push → Vercel 反映確認が必要。**
> それが済むまで **LOG Register PC は CLOSE ではない**（§CLOSE 条件の残り: canon push / mock push /
> remote 反映確認 / Vercel production READY）。

> 🟢 **110 で RIG / PARTS Register の PC 版が CLOSE。**

> 🟢 **110 で RIG / PARTS Register の PC 版が CLOSE。** イタヤ実機確認により
> `pc/myrig-register-rig-v3.1.html` / `pc/myrig-register-parts-v1.1.html` を**正式採用版**とした
> （`-compare` を外して昇格。旧 v3.0 / v1.0 は active tree から削除。履歴は Git に残る）。
> ⛔ schema v1.6-r4 / 109 のデータ契約は変更していない。Production DB 非接触。LOG は未着手。

> 🔴 **109 で「Research Master ↔ App」の境界を閉じた。** 108 が閉じたのは App 内
> （Register ↔ Detail ↔ App schema）で、その**一段外側**が残っていた。
> Research 主査の `RIG_PARTS_CONTRACT_EXPORT_20260917` と App 実体を突き合わせた
> E2E 監査（`_state/E2E_CONTRACT_AUDIT_20260917.md`）で **Next.js 前 BLOCKER 6 件**が出たため、
> 裁定原本 **`_decisions/2026-09-17_research-app-boundary-contract-v1.md`** で確定し、
> `docs/schema/myrig_db_schema_v1_6.md` を **v1.6-r4** へ更新した。
> ⛔ Production DB 非接触・migration 未実行・Research schema 未変更・cross_ref 未生成。

> 🔴 **108 で「データ契約」を閉じた。** 107 までは画面の採用記録で `docs/` は無変更だったが、
> 108 は **Register ↔ Public Detail ↔ Owner Detail ↔ App DB を同じ契約へ収束**させ、
> `docs/schema/myrig_db_schema_v1_6.md` を **v1.6-r3** へ更新した。
> 裁定原本: **`_decisions/2026-09-16_field-contract-rig-parts-v1.md`**（4 層モデル・全 12 項目 ＋ HOLD 7 件）。
> ⛔ Production DB への migration は行っていない。既存値の意味推定変換もしていない。

### 現在地（mock）

| | |
|---|---|
| PC の RIG 登録 | **`pc/myrig-register-rig-v3.1.html`（正式採用版・CLOSE）** |
| PC の PARTS 登録 | **`pc/myrig-register-parts-v1.1.html`（正式採用版・CLOSE）** |
| 旧版 | `myrig-register-rig-v3.0.html` / `myrig-register-parts-v1.0.html` は **active tree から削除**（履歴は Git。⛔ `_archive` へ複製していない） |
| 共有 UI | `pc/assets/css/SoT_register-family.css`（Advanced / 章見出し / 下端バー / form chrome / 操作の中立語彙）<br>`pc/assets/js/SoT_register-family.js`（写真枚数の Single Source。`photoMax('rig')=7 / photoMax('part')=5`） |
| PC の LOG 投稿 | **`pc/myrig-log-composer-v1.html`（正式採用版・111 / 112 で CLOSE）** |
| PC の LOG 詳細 | **`pc/myrig-log-detail-v1.html`（067 CLOSE 維持。112 で 111 契約への整合修正のみ）** |
| LOG の探索履歴 | `pc/myrig-log-composer-v1-compare.html`（A / A2 / B / C / D / E。**Exploration。通常導線からは外した**。⛔ 再オープンしない） |
| LOG の旧版 | `myrig-log-composer-modal-v0.3.9.html` は `_archive/20260917_log-composer-close/` へ **mv**（⛔ 削除していない） |
| Launcher / 各面 | `compare.html` と各面の「＋投稿する」導線 **18 ファイル**を新正式版へ更新済み。旧名の参照は 0 件 |

### レーン

- 基盤修正レーンは終了済み。D3 を含む cross-surface の完全収束はやらない（イタヤ方針 2026-09-15）。
  Feed カードの再デザインは再 OPEN しない。D4〜D8 / D9 / D11 / D13 は改善候補として保留。
- **106 の交通整理**（イタヤ）: MVP 本線は「Smart Assist なしでも完成した登録画面」。
  自然文の整理台は Register へマージしない。Exploration として保存のみ。
- **Register PC は RIG / PARTS とも CLOSE（110）。**
- **LOG Register PC は CLOSE（112）。** LOG Detail Compatibility Gate も整合済み。
- ⚠️ mock `b14590c` / `77b2614` は **未 push**。イタヤが push → Vercel 確認で完全に閉じる。
- ~~次のレーン: Mobile Register 3 本（RIG / PARTS / LOG）~~ → 113 / 115 で 3 本とも採用済み。**次のレーン = Library 系**（116）→ **117 で OPEN**（ゼロベース再設計）。

### ✅ RIG Register PC 採用（2026-09-16 / イタヤ裁定）

> ⚠️ **107 時点の記録。採用版は 110 で `pc/myrig-register-rig-v3.1.html` に置き換わっている**（上の 110 節が現在地）。
> 以下の画面文法・意味論そのものは v3.1 に継承されている。

- 採用版 **`pc/myrig-register-rig-v3.0.html`**（制作中の呼び名は v3 Concept R2 / -fixed）。
  旧 baseline v2.9.5 の後継として v3.0 とした。
- 画面文法: 写真 → 01 基本情報 → 青帯「さらに詳しく記録する」→ 02 メカ・パーツ / 03 所有情報 /
  04 公開・リンク → 下端固定帯。**手動入力だけで完成する**（Smart Assist を持たない）。
- 主要な意味論: 作成＝**非公開で実体作成** → autosave → 明示的に公開（作成前に公開状態を作らない）。
  写真は Cover を **id** で保持（削除・並び替えで動かない）。候補は**入力してから**出す。
  Master 紐付きは Master 側の分類が正。「変更」のキャンセルは完全に無変更。
- 旧版は `_archive/20260916_register-rig-close/` へ **mv**（⛔ 削除していない。`_archive` は
  `.gitignore` / `.vercelignore` 対象なのでディスク上のみ保全）:
  v2.9.5 / v3-concept（第一バッチ）/ v3-concept-r2 / **v3-smart-explore（Smart Assist Exploration）** /
  当時の Gate 2 本。
- ⛔ **入口 A / B / C の比較は行わないまま終了した。** 第一バッチの入口比較は採用されていない
  （R2 で共通 Editor 1 本に寄せたため、A/B/C の裁定自体が不要になった）。

### ✅ PARTS Register PC 採用（2026-09-16 / イタヤ裁定）

> ⚠️ **107 時点の記録。採用版は 110 で `pc/myrig-register-parts-v1.1.html` に置き換わっている**（上の 110 節が現在地）。
> 以下の画面文法・意味論そのものは v1.1 に継承されている。

- 採用版 **`pc/myrig-register-parts-v1.0.html`**。RIG v3.0 と**同じ画面文法を継承**。
- PARTS 固有: 01 は 製品（パーツ Master）/ **型番**（Detail の MODEL 行）/ 表示名 / 紹介文。
  02 は **搭載RIG**（rig_parts / M:N）。03 は 所有状態 / 入手時期 / 入手価格 / 入手先 / メモ。04 は RIG と同一。
  ⚠️ 採用時点の 03 は「コンディション / 保有状況 4 値」だったが **108 の裁定で置き換え済み**。
- **写真上限 5 枚は PARTS Detail 実体と照合済み**（2026-09-16）。公開 `myrig-parts-detail-v1-open.html` /
  ガレージ `myrig-garage-parts-detail-v7.html` とも ギャラリー **1 / 5**・フォトノート **4 枚**
  ＝ カバー 1 ＋ サブ 4。RIG は 1 / 7・フォトノート 6 枚で 7 枚。「Detail のギャラリーと同数」は共通、枚数だけ違う。
- **PARTS 識別色を是正**: カバーバッジと選択済みチェックが RIG の黄（`--cat-rig`）のままだった。
  `--cat-parts` `#D92D20` / `--cat-parts-on` `#fff` へ。PARTS Detail の PARTS バッジと同値。
- **搭載RIG の関係意味論**: ⚠️ **108 で再裁定済み**。`rig_parts.status`（active / removed）で事実状態を持ち、
  日付は分かるときだけ入れる。取り付け予定とガレージに無い RIG 名は**入力導線ごと外した**。
- ⛔ `compatible_platforms` はこの画面で**一切さわらない**（Master 継承のみ。Research が上流）。
- 旧 baseline `myrig-register-parts-v0.1.10.html` は `_archive/20260916_register-parts-close/` へ mv。

### ✅ 110: RIG / PARTS Register PC 採用・CLOSE（2026-09-17 / イタヤ実機確認）

**採用版**: `pc/myrig-register-rig-v3.1.html` / `pc/myrig-register-parts-v1.1.html`

| 項目 | 確定した状態 |
|---|---|
| 共通 shell | **900px 一本**（`--col:900px`）。RIG / PARTS で同じ箱。⛔ 二重幅は廃止 |
| カテゴリ色 | **Identity だけ**。Header の RIG / PARTS チップ ・ 01〜04 の章番号 ・ Cover の種別バッジ ・ Master 選択済みアイコン。⛔ 操作・focus・hover・装飾には使わない（NG-7） |
| Advanced「さらに詳しく記録する」 | **RIG / PARTS 共通の中立**（`--color-bg-subtle` ＋ 1px 罫線）。⛔ カテゴリ色にしない |
| 写真枚数 | **RIG 最大 7 枚 / PARTS 最大 5 枚**（カバー 1 ＋ 追加 6 / 4）。出所は `SoT_register-family.js` の `photoMax()` **1 箇所**。Detail 4 面の実体（ギャラリー 1/7・1/5、フォトノート 6・4）と一致 |
| 空状態 | 「最大 N 枚（カバー 1 枚 ＋ 追加 N-1 枚）」を明記 ＋ **空スロットを最初から表示** ＋ カウンタ「写真 0 / N」 |
| 実写真 preview | **3:2**（cover / thumb）。Detail 4 面の実測（cover img `3/2`・thumb 1.52・フォトノート img `3/2`）に一致。⛔ 空の Drop Zone は操作領域なので 3:2 に固定しない |
| 追加写真 | **RIG 6 枚横 1 列 / PARTS 4 枚横 1 列**。列数を落とさず縮め、セルが最小幅を割る幅だけ横スクロール。⛔ 狭めて大きくなる挙動は作らない |
| caption | **1 枚ずつ展開**（写真を押した 1 枚だけ編集欄を開く。別写真で切替）。**placeholder なし**（用途を限定しないため）。ラベル「写真の説明 任意 ・ 公開ページのフォトノートに出ます ・ 90 字まで」は維持 |
| responsive | 狭くなったら PARTS も RIG と同じ縦文法へ収束（カバー → 写真一覧 → 01 基本情報）。⛔ `order` による特殊な並べ替えはしない |
| Register Family | 共有 CSS `SoT_register-family.css` ＋ 共有 JS `SoT_register-family.js` を**共有実装として維持**。「片方だけ直ってもう片方が古い」を構造で止める |
| データ契約 | **Research ↔ App E2E 契約は 109 を維持**（schema v1.6-r4 無変更） |

**旧版の扱い**: `myrig-register-rig-v3.0.html` / `myrig-register-parts-v1.0.html` は
active tree から**削除**（履歴は Git）。⛔ `_archive` へ新たに複製していない。

### ✅ 109: Research ↔ App 境界契約（2026-09-17 / イタヤ裁定）

裁定原本 **`_decisions/2026-09-17_research-app-boundary-contract-v1.md`** / schema **v1.6-r4**。
入力は Research 主査 `RIG_PARTS_CONTRACT_EXPORT_20260917`。

> **「UI に入力欄が無い＝不整合」ではない。誰がその値を書くのか（write authority）まで決めるのが 109。**

| # | 確定したこと |
|---|---|
| `build_tags` 同名別義 | App 側を **`rigs.user_build_tags`** へ改名。Research `rig_masters.build_tags`（Master 固定の分類・ユーザー編集不可）とは別概念。UI 表示名「ビルドタグ」は維持。⛔ Master 値を自動コピーしない |
| FK 境界 | 参照先を Research 実 PK へ是正。`rig_masters.rig_master_id` / `manufacturers.manufacturer_id`（⛔ `.id` は誤記）。PK の再採番・改名を禁止 |
| Category | **`category_id UUID` を廃止**し `rigs.rig_category_slug` / `parts.part_category_slug` ＋ `part_subcategory_slug` へ。Research の PK は **slug**。⛔ 偽 UUID 変換禁止。親は `parent_slug` から導出し重複保存しない |
| RIG Variant | **`rigs.rig_master_variant_id UUID NULL` 新設**。Master だけ選択 / Custom は NULL。⛔ `base_model` で代用しない。Public Detail の「バリエーション」はここから取る |
| PARTS Master ID | **HOLD H-1 継続**。唯一の source ID は `part_masters.part_id`（⛔ `.id` ではない）。Register は `master_ref` まで |
| 型番の write authority | Master / Variant 紐付き → Research `primary_sku` が権威・**server-derived・ユーザー上書き不可**（Register で read-only）。**Custom PARTS のときだけ**入力可 |
| Master 継承値 | **client は master identity だけを送る / server が同期 Master を引いて FK と cache を作る**。⛔ hidden field を増殖させない。`manufacturer_id` / `platform` / `product_line` / category slug / Master 型番は **server-derived** |
| `db_register` | `false` → **Resolver / Picker の候補から除外**（確定）。⛔ App 側で物理 DELETE しない |
| publication | Public Detail / Library / Search / 公式画像・説明・リンクは **`master_publication_effective` が唯一の公開判定源**。⛔ App 側で `effective_*` を再計算しない。**Picker には publication gate をかけない**（publication 行が無い part が 64% あり Register が機能しなくなるため。公開表示の契約と登録内部検索の資格を別契約とする） |
| compatibility | `compatible_platforms` = Research 正本・入力禁止・推測展開禁止・**部分文字列照合禁止**・XREF は HOLD。`compatible_types` = Research に同名列が**存在しない**。App 所有列として扱い⛔ Research 値として同期しない |

**境界キー禁止**（Research 主査裁定）: `part_slug` / `primary_sku` / `part_name` / `canonical_url` /
`evidence_url` / `scraped_from` / `master_aliases.alias_value` / `alias_sku` / `manufacturers.slug` /
`platform_slug` / `variant_slug` / これらの組合せハッシュ / App 生成 UUID。

**109 の追加 HOLD**: **H-9** Register の Variant 選択導線（受け皿は確定・Resolver が variant を返せてから）/
**H-10** RIG 側の cross_ref 文書（PARTS と同時に作る）/ **H-11** Picker への publication gate（現時点では不要と裁定）。

### ✅ 108: RIG / PARTS Field Contract 統合裁定（2026-09-16 / イタヤ裁定）

裁定原本 **`_decisions/2026-09-16_field-contract-rig-parts-v1.md`** / schema **v1.6-r3**。要点だけ:

| # | 確定したこと |
|---|---|
| 基本モデル | **Master / User Entity / Relation・History / Public・Owner data** の 4 層 |
| PARTS の定義 | 「製品」ではなく**ユーザーの管理単位**。同じ Master を参照する複数 PARTS を作れる。物理 1 個 = 1 レコードとは限らない |
| 管理名 | `parts.nickname`（UI「管理名」）を新設。**Owner-only**。Public の主タイトルは `product_name` のまま |
| `condition` | **意味論を廃止**（3 つの軸が 1 列に混ざっていた）。Register の入力も Public Detail の「状態」行も撤去。`DEFAULT 'new'` 撤廃。⛔ 列は DROP しない・既存値は変換しない |
| 所有と装着 | 別の事実。`parts.ownership_state`（owned / released）を新設。⛔ active relation が無いことを「予備」と自動判定しない（正しい表示は「装着記録なし」まで） |
| `rig_parts` | **実際に装着した／していた事実だけの SoT**。`status`（active / removed）を新設し**事実状態と日付を分離**（「外したが日付は不明」を偽値なしで表現）。再装着は上書きせず新しい行。active 一意を `(rig_id,part_id)` と `(part_id)` の 2 本で保証 |
| `build_details` | **設定値・加工・自由項目だけ**（Drag Brake / Shock Oil / Body Paint）。装着製品は `rig_parts` のみ。旧 mechanics 系キー廃止 |
| RIG の状態 | **2 軸**。`ownership_status`（current / past / wishlist）＋ `usage_status`（building / active / stored / NULL、current のときだけ意味を持つ）。⛔「整備中」は LOG、⛔「アーカイブ」は混ぜない。Public Detail は「所有」「利用状況」の 2 行 |
| 購入 / 入手 | RIG / PARTS とも任意で持つ。RIG の価格は**ベース車両・キットの入手価格**（総製作費ではない）。価格・入手先は **Owner-only** |
| 日付精度 | `purchased_period TEXT`（`YYYY` / `YYYY-MM` / `YYYY-MM-DD`）を新設。⛔ 年月入力に**架空の 1 日を補完しない**。`purchased_at DATE` は非推奨（DROP しない） |
| `rig_type` | MVP の Register では **rc-car 固定・Selector を出さない**。schema の将来 5 タイプ対応は維持。⛔ パーツ互換性とは無関係 |
| 公開文 / Owner-only | `images.caption`（公開フォトノート）/ `rigs.tagline` / `rigs.build_tags` / `rigs.private_note` / `parts.private_note` を列として新設。⛔ `build_details` へ押し込まない。`images.alt` は**今回の対象外** |
| 公開範囲 | 非公開 entity の情報を **relation 経由で公開面へ漏らさない**（名前・画像・リンク・推測できる件数表示を含む）。RLS 節に追記 |
| `entity_links` | **新設**。RIG / PARTS / LOG 共通のユーザーリンク。`moderation_status`（not_required / pending / approved / rejected）。個人サイトの **URL 変更で `approved` を引き継がず `pending` へ戻す**。Master 公式リンクは Research `master_external_links` が正本で ⛔ コピーしない。表示は 1 ブロックへ合成してよい。`rigs.external_links` は移行対象（恒久二重管理禁止） |
| `planned` | MVP では**構造化保存しない**。購入予定 / 取り付け予定 / ガレージに無い RIG 名だけの relation は**入力導線ごと撤去**。「以前使用」は実際の履歴なので `rig_parts` に残す |

### 🔴 HOLD（108 では決めなかった）

- **H-1 PARTS の Master ID を App FK へ接続すること。** CORE 上 Research = `part_masters`（単数）/
  App = `parts_masters`（複数）で**同名別義**、cross_ref 写像表が未作成。実体を確認したところ
  Register が持っているのは Resolver の `kind:'part_master'`（Research 側の語彙）の **mock slug** であり、
  App の `parts_masters.id`（UUID）ではない。⛔ **名前だけ schema に合わせて ID を刺さない。**
  Register は `parts_master_id` を出さず `pending.master_ref`（出所付き）として保持する。
  → **F-3「`part_master_id` → `parts_master_id`」は HOLD のまま。**
- ~~**H-2 複数 RIG で共有して使う機材**（送信機・バッテリー等）の受け皿~~
  → 🟢 **2026-09-19 / 115 で解消。** `idx_rig_parts_active_part` を撤去したので、
  送信機・バッテリーも `rig_parts` に RIG ごとの `active` 行として持てる。
  ⛔ 専用テーブルも「共有機材」分類も作らない。
- **H-3 `rigs.external_links` → `entity_links` のデータ移行手順。** Production DB migration は未着手。
- **H-4 `build_details` 旧キーの新キーへの寄せ方。** ⛔ 意味推定での自動変換は禁止。
- **H-5 `images.alt` の追加要否。** `caption` とは別概念。
- **H-6 `condition` の将来設計。** 意味論は廃止済み。再設計の余地は残す。
- **H-7 Owner Detail「まとめて編集」の実体。** `data-g-edit` は現在モックの器のみで、
  Register を開くのか独自フォームかが未定義。UI 契約であってデータ契約ではない。
- **H-8 RIG の設定値・加工メモ（`build_details`）の入力導線。** 受け皿は確定したが Register に UI が無い。
- 公開中 RIG / PARTS 編集の「確定して反映」保存モデルは未裁定（`data-save-mode` で切替できる構造のみ）。
- Mobile の Register 3 本は未着手。PC が揃ってから。

#### 🟡 H-12 Advanced トリガー「さらに詳しく記録する」の色 — **横断デザイン監査候補**

**起票: 2026-09-19 / 115 / イタヤ裁定。⛔ このバッチの中立化を最終裁定にしない。**

- **いまの状態（暫定）**: PC / Mobile とも **中立**（Mobile は `--ms-bg` ＋ 1px 罫線 ＋ Chevron、
  PC は透明 ＋ Chevron）。115 で Mobile のカテゴリ色全面塗りをやめて PC に揃えた。
- **残っている可能性**: **RIG=黄 / PARTS=赤 を PC / Mobile 共通で使った方が、
  現在地・種別識別として良いかもしれない。** 115 の中立化はそれを否定していない。
- **なぜ今やらないか**: ここを触ると Register / Detail / Garage を含む**横断修正が続く**ため。
  主要面が揃う前に色の職域を動かすと、揃った後にもう一度全部やり直すことになる。
- **いつ判断するか**: **フロント主要面が完成したあとの横断デザイン監査でまとめて裁定する。**
  単独バッチで再修正しない。
- **一緒に見るもの**（バラバラに決めない）:
  Register Header の種別バッジ（PC `.mr-reg-header__cat` / Mobile `.rr-hdcat`）/
  章番号バッジ（`.chap__num` `.grp__n` `.rr-chap__n` `.rr-grp__n`）/
  写真タグ（`.rr-ph-tag`）/ color-token-v8 の NG-1・NG-2・NG-7（カテゴリ色＝種別識別・操作は中立）。
- **裁定が「カテゴリ色にする」へ動いたとき、同時に直すもの**:
  `css/mobile-register.css` の `.rr-adv__tg` / `pc/assets/css/SoT_register-family.css` の `.adv__tg` /
  `_state/register_identity_check.py` の I1 / `_state/mobile_register_check.py` の D-F 断言。
  ⛔ CSS だけ変えて gate を置き去りにしない（どちらも「中立であること」を FAIL 条件にしている）。
- **履歴**（同じ所を何度も往復している。⛔ 経緯を消さない）:
  PC 青ベタ → 中立 → カテゴリ色ベタ（2026-09-16）→ **中立へ差し戻し**（2026-09-17）/
  Mobile はカテゴリ色ベタ（113 D-F）→ **中立へ差し戻し**（2026-09-19 / 115）。

### NEXT

**LOG 投稿 PC（`myrig-log-composer-modal-v0.3.9.html`）を次のレーンにする。**
ただし LOG は「写真＋種別＋本文」が主役で 01〜04 の構成がそのままは合わないため、
着手時に構成案から出す。その後 Mobile 3 本 → 全モック揃ってからの横断 Convergence。

> ## 🟢 110: RIG / PARTS Register PC 採用・CLOSE（2026-09-17 / 現在地・採用状態の変更）
>
> イタヤ実機確認により `-compare` の 2 本をそのまま**正式採用版**へ昇格した。
> ファイル名から `-compare` を外し、旧 v3.0 / v1.0 を active tree から削除。
> Launcher（`compare.html`）と各面の「＋投稿する」導線 **18 ファイル**を新名へ更新（旧名の参照 0 件）。
>
> ⛔ `docs/` は無変更。schema は **v1.6-r4 のまま**、109 のデータ契約も動かしていない。
> 本 revision が変えたのは**現在地と採用状態**だけ。
>
> 109 までの「PARTS v1.1 採用判断未了」「mock push 待ち」の記述は現在状態へ更新済み。

> ## 🟢 109: Research ↔ App 境界契約（2026-09-17 / 裁定 ＋ docs 変更あり）
>
> 108 は App 内のデータ契約。109 はその**一段外側**（Research Master ↔ App）。
> Research 主査から `RIG_PARTS_CONTRACT_EXPORT_20260917` が返り、App 実体と突き合わせたところ
> **Next.js 前 BLOCKER が 6 件**出た（`build_tags` 同名別義 / FK 参照先の誤り / category が UUID vs slug /
> RIG Variant の保存先なし / publication 制御ゼロ / `db_register` 未受領）。
>
> **変更した正典**: `docs/schema/myrig_db_schema_v1_6.md`（**v1.6-r3 → v1.6-r4**）、
> 新規 `_decisions/2026-09-17_research-app-boundary-contract-v1.md`。
>
> **モックにも反映**: Register 4 本の `mapToSchema()` を「ユーザー値 ＋ master identity」だけに絞り、
> Master 継承値を `server_derived` として宣言に降格。PARTS 型番は Master 選択時 read-only。
> Resolver mock に `db_register` 除外契約を実装（除外対象を 1 件混ぜて実機で確認できるようにした）。
> RIG Detail の「バリエーション」「ビルドタグ」に出所コメントを追加。
>
> ⛔ Production DB 非接触・migration 未実行・Research schema 未変更・cross_ref 未生成・
> `compatible_platforms` の推測解決なし・SCHEMA_TRUTH 未確認値の確定なし。

> ## 🟢 108: RIG / PARTS Field Contract 統合裁定（2026-09-16 / 裁定 ＋ docs 変更あり）
>
> 107 までは「画面の採用」の記録で `docs/` を変更していなかった。108 は Register / Public Detail /
> Owner Detail / App schema を**同じデータ契約**へ収束させた最初の revision。
>
> **変更した正典**: `docs/schema/myrig_db_schema_v1_6.md`（**v1.6-r2 → v1.6-r3**）、
> 新規 `_decisions/2026-09-16_field-contract-rig-parts-v1.md`。
>
> **106 / 107 の PENDING のうち解消したもの**: 取り付け予定の受け皿（→ 構造化保存しない）/
> ガレージに無い RIG 名の受け皿（→ relation を作らない）/ `private_note`・購入価格・購入店・
> キャッチコピー・ビルドタグ・写真の説明の保存先（→ **実列として確定**）/ RIG 5 状態・PARTS 4 状態
> （→ 2 軸へ再設計）/ 個人サイトの承認フロー（→ `entity_links.moderation_status`）。
> **`condition` は「不整合を直す」ではなく「意味論を廃止する」**という結論になった。
>
> ⛔ Production DB へは接触していない。migration も実行していない。Research 所有 schema も変更していない。
> ⛔ cross_ref 未解決の `parts_master_id` は推測で埋めず HOLD H-1 として残した。

> ## 🟢 107: Register PC 2 本の採用を記録（2026-09-16 / 裁定の記録 ＋ STATE）
>
> 106 の NOW は「Register v3 Concept の実機比較・A/B/C 裁定待ち」で止まっていたが、実際には
> R2 / UX Reset → MVP 本線（Smart Assist なし）→ 実用修正 → **イタヤが 2 本とも採用裁定**、
> まで進んでいた。モックの HTML 冒頭に「採用版」と書いてある一方 CURRENT が未採用のままで
> **正典と実体がズレていた**ため、現在地と裁定結果をここへ入れた。
>
> ⛔ 本 revision は `docs/` を変更しない。採用したのは**画面**であって、保存先列や
> `rig_parts` の受け皿といった**データ側の裁定は含まない**（NOW の PENDING のとおり）。
>
> 106 本文の「PC 入口 A / B / C の採否＝イタヤ実機裁定待ち」「Launcher に v3 Concept を登録していない」
> 「baseline 3 本は未変更で維持」は**現在地としては失効**（入口比較は不要になり、Launcher は差し替え済み、
> baseline は archive へ退避）。106 の**裁定内容そのものは有効**。

> ## 🟢 104: NOW の運用状態を実態へ更新（2026-09-15 / STATE のみ）
>
> 103 の NOW は「`b18f74c` push 待ち」で止まっていたが、実際は `b18f74c` / `bbaab40` / `4423ac7` とも push 済み・
> Vercel READY だった。revision は一致していたので `CURRENT_STALE` ではないが、
> **次スレッドが「push 待ち」を見て `mockup` を打ちに行く**状態だったため、現在地だけを更新した。
>
> ⛔ 本 revision は仕様裁定を含まない。`docs/` は無変更。Register v3 Concept は比較モックのままで、
> ACTIVE 仕様へ昇格していない。A/B/C の採否も未裁定。

> ## 🟢 103: D2 / H2-a — カテゴリ色を文字色・線色・操作色から外す（2026-09-15 / Cowork 実装）
>
> モック: `myrig-mockup` **`b18f74c`**（**push 待ち**）。開始時 HEAD / `origin/main` とも `579af22`。
> 正典: この 103。裁定原本: `_decisions/2026-09-15_d2-category-color-role-v1.md`。
> 開始時 canon: `e0aa3e6` / revision.txt・CURRENT 冒頭とも `MYRIG-20260914-102` で一致確認済み。
>
> ### 102 の未了はすべて解消
> push（canon `e0aa3e6` / mock `579af22` とも ahead 0）・**Vercel production READY**
> （`dpl_9htzsygjme1Ub3nDvPGF2EgBFKdD` / 同 SHA / author `MyRIGRC <admin@myrigrc.com>` 正常）・
> **Mac 実機の Final Gate 2,050 PASS / 0 FAIL ＋ selftest 11/11 検知**（イタヤ実行）。
> 102 本文の「push 待ち / Vercel 未了 / Mac 実機未実施」は失効。
>
> ### 🔴 081 の棚卸しは使えなかった（grep は打ち消し・非表示・shadow DOM を区別できない）
> 着手前に**描画された計算値だけを見る** Gate を新設した。方式は grep ではなく
> **sentinel トークンを注入して計算値の差分を取る**（color-mix も page-local alias も自動で追える）。
> 実測（live PC 40面 / light+dark / 1440・同型を畳んで 409 件）:
> FILL 156 / BORDER 104 / TEXT_ON 88 / **TEXT 29（実体 13 要素）** / SHADOW 20 / **BAND 4（実体 2 要素）** /
> OUTLINE 0 / CONTROL 0。
> **NG-1 が「PC側に未撤去」と名指ししていたうち、実際に描画されていたのは `library-v3` の 2 件だけ。**
>
> 🔴 **検査自体で 3 回踏んだ罠**（裁定原本 §0。数字を信じる前に読む）
> ① `color` の継承と currentColor 初期値を数えると 1 箇所が **25,835 件**に化ける／
> ② sentinel を `<style>` で注入すると head に 1 ノード増えて 2 パスの index が全ずれし、
>    `color:#fff` の要素まで誤検出する（`:root` のインライン style へ置く）／
> ③ 地の色を DOM の backgroundColor 遡りで取ると**絶対配置のシェード/写真の上で嘘になる**
>    （browse の `.category-stage__eyebrow` を 1.08 と誤判定 → 実画素では 2.58）。
>    小さい要素はクロップの最頻色が**グリフ自身**になるので、地は「文字色から離れた画素の最頻値」で取る。
>
> ### イタヤ裁定（2026-09-15）と実装
> | | 裁定 | 実装 |
> |---|---|---|
> | **① Garage Owner Control** | 077 の entity category binding は **Owner 領域の「種別識別」に限定して維持**。塗りヘッダ ＋ `--gd-control-on` はカテゴリ色のまま。hover / active / focus-visible は**操作**なので NG-7 に従い中立へ。とくに RIG 黄の focus ring はライト白地 **1.08** で見えていなかった | `SoT_garage-detail.css` / `css/mobile-garage-detail.css`。PC / Mobile の両方を同時に直した（片方だけ直る事故を作らない） |
> | **② NG-1 の代替形** | `library-v3` の `.door--rig` / `.door--parts` の 4px 色帯を撤去し、既存 `.door__label` を **P22-B19c と同じ小型塗りバッジ**へ。NG-1 と NG-2 を同時に解消 | 線は 1 本も増やしていない。`.door--maker` の中立 4px は据え置き |
> | **③ BORDER 104 の分類** | 「color-mix だから対象外」にしない。**役割で判定**。操作 UI の枠 / 選択・現在地の線 / 線そのものが輪郭を作るもの ＝ 中立化。別の面・背景で形が成立し識別アクセントとしてのみ働くもの ＝ 維持可 | 維持: `home` の `.dir-card--parts` 系淡色境界・`library-v3` の `.chip--*` inset ring・`.lpc__badge--*` |
>
> ### 主な変更
> - **塗りバッジ化**: `category-stage__eyebrow`（browse 3面）/ `door__label` / `user-post-item__handle-badge` /
>   `about` の `pillar__label`・`pillar__icon` / `support-us`・`welcome-tour` の線画アイコン
> - **中立操作色へ**: `category-side__cta`（NG-7 が名指ししていた CTA 流用）/ `category-cta` /
>   `parts-hero` の focus ring / チップの選択下線 / Feed の種別フィルタ選択 / `create-btn` の枠 /
>   `register-rig` の選択・ドラッグ枠・`accent-color`・主操作ボタン / `log-composer` の公開範囲ピル /
>   `lib-hd__nav` の現在地
> - **中立境界へ**: `feed-rail-about`（地を持たない＝線が輪郭）/ `dir-subheader` / `ptb__nickname` /
>   `dt-identity__build` / `act-item`（打ち消し済みだったものも**供給元で是正**して再発を止めた）
> - `register-parts` の `--usage-installed` は**使用状態**なのでカテゴリ色の流用をやめ `--rig-status-active` へ
> - 🔴 `SoT_tokens-v6.css` に **v7 の `--cat-*-on` を新設**（値の flip ではない）。これが無いと
>   「塗り ＋ `var(--cat-*-on)`」が v7 面で `#fff` に落ち、**v7 LOG オレンジ ＋ 白 = 2.57**（実測）になる
>
> ### 検証
> 新設 `_state/category_role_check.py`: **134 PASS / 0 FAIL**（CC1 BAND 0 / CC2 TEXT 0 / CC3 OUTLINE 0 /
> CC4 CONTROL 0 / CC5 TEXT_ON 110 全件 CR>=4.5 / CC6 BORDER 24 は既知 PENDING のみ）。故障注入 **6 種**すべて検知。
> 既存 21 本 **0 FAIL**: detail_contract 51 / entity_actions 36 / footer 4 / image 63 / launcher 177 /
> hit_test 367 / shell_interaction 418 / web_meaning 1290 / garage_check 513 / garage_top 291 /
> garage_list 749 / garage_integrity 610 / mobile_garage_list 804 / mobile_garage_detail 134 /
> mobile_detail 59 / mobile_feed 63 / feed_w_gap 108 / feed_m 525 / filter_transform 3,328 /
> detail_m 3,152 / web_fundamentals_final 2,050。
>
> ### 残す PENDING（⛔ 黙って増やさない）
> - **notifications の通知タイプバッジ**（インライン SVG の `stroke`/`fill` がカテゴリ色）→ NG-7 の**通知色は HOLD**。
>   通知 UI の設計議論とあわせて後日。Gate の `BORDER_PENDING` に登録済み
> - **`.app-logo__accent` のブランド色** → color-token-v8 §3「ブランド色: 未裁定」。live 40 面では未使用（描画 0）
> - **`sg-log-tag--run` の `#22c55e`** 等 → カテゴリ色ではない別系統。パレット外（NG-7）だがステータス/通知色の裁定と同時に
> - **`home` の `!important` 層に残るカテゴリ色宣言** → 描画 0。`!important` 743 個で層が深く触る利得が無い
>
> ### 🔴 未了
> - push: mock `b18f74c` / canon（この 103）とも **local commit のみ**。イタヤの `mockup` コマンドで
>   canon push → mock push → Vercel deploy。
>
> ### 次
> **通常のモック制作・デザイン探索レーンへ。** D3 を含む cross-surface の完全収束はやらない
> （次のモック制作を妨げるものだけ必要時に是正 ＋ 全モックが揃った後の横断 Convergence）。

> ## 🟢 102: Web Fundamentals Final Consolidation — Phase 2 / viewport continuity CLOSE（2026-09-14 / Cowork 実装）
>
> モック: `myrig-mockup` **`579af22`**（**push 待ち**）。開始時 HEAD / `origin/main` とも `f02239a`。
> 正典: この 102。裁定原本: `_decisions/2026-09-14_web-fundamentals-final-consolidation-v1.md`。Matrix **v1.10**。
> 開始時 canon: GitHub main `4036fa4` / revision.txt・CURRENT 冒頭とも `MYRIG-20260914-101` で一致確認済み。
>
> ### 101 の stale 記述の補正
> 101 本文の「push 待ち / Vercel 未了 / Mac acceptance 未実施」は失効。`f02239a` / `4036fa4` は push 済み・Vercel READY・
> Mac 実機（Mac Studio / Chrome）で `detail_m_transform_check` 3,152 PASS / 0 FAIL・selftest 17 種全検知 ＝ **Detail M 最終 acceptance 完了**。
>
> ### 残件の結論（A〜G）
> | | 結論 | 実装 |
> |---|---|---|
> | **A. Header ≤538 overflow（R-09）** | **解決**。320〜538 の横 overflow 218 → 0。責務は落とさない（検索ピルが縮む・投稿ボタンはラベル文字だけ畳む。button と accessible name は残る）。px は implementation value | `SoT_app-shell.css` §3.5 ＋ `SoT_app-shell.js`（`#cxBtn` に aria-label）。同伴: Search / Library 3 面 / Garage PNF の `1fr` → `minmax(0,1fr)` |
> | **B. Home 棚 clip 外 focus（F-4）** | **解決**。focus したカードを snap 位置へ**即時**に入れる。⛔ outline:none / Tab 順除外は不採用 | `SoT_shelf.js`（Home / Browse / Detail 共通）。`shell_interaction` の WARN 2 → 0 |
> | **C. Garage Owner Detail 1025〜1050** | **>=1025 W / <=1024 M へ統一**（1025 で 2 カラム成立を実測: 主列 610 / rail 365 / overflow 0 / Context Bar・owner controls の hit-test 正常）。Detail 5 面が Global Shell と 1 本 | `SoT_detail.css` / `SoT_detail-rail.css` の W 下限 1051 → 1025、`SoT_detail-m.css` の隔離ブロックを <=1024 へ。1024 以下・1051 以上は pixel 差 0 |
> | **D. `css/sot/SoT_app-shell.css` 旧コピー** | **削除**（`css/sot/SoT_footer.css` も）。42 Mobile 面で runtime にマッチする規則は `:root` の変数 2 つだけ・消費元なし・`site-footer` 0 ＝ 実利用 0 | 42 面の `<link>` 撤去。42 面 ＋ launcher で pixel 差 0。参照 0・404 0。archive しない |
> | **E. Overlay 契約の重複** | **1 本化**。closed inert / open focus / Tab 循環 / Escape / backdrop / focus 復帰 / scroll lock / mode 退出 / **排他**を `MyRIG.overlay.create()` が持ち、4 消費側は面固有だけ | `SoT_app-shell.js` §0。`SoT_filter-sidebar.js` 192→153 行 / `SoT_feed-rails.js` 164→98 / `SoT_garage-drawer.js` 165→99 |
> | **F. 法務「広告について」** | **非 blocker PENDING（法務コンテンツ制作待ち）**。遷移先も本文も存在しない（`LEGAL` は 3 本・support-legal 面に節なし）。架空ページ / `#` は作らない | — |
> | **G. Final Gate** | `_state/web_fundamentals_final_check.py` **新設**。横断契約だけを 20 幅で見る（Header narrow / overflow / visible focus / 棚 / Overlay 排他 / Detail 境界 / Garage 境界 / stale source / 404 / resize 復元） | **2,050 PASS / 0 FAIL**。故障 **11 種**すべて単独検知 |
>
> ### 検証（cloud Chromium 相当＝デスクトップ Cowork の Linux VM）
> 既存 19 本 **0 FAIL**: detail_contract 51/0/0・entity_actions 36/0・footer 4/0・image 63/0・launcher 177/0・hit_test 367/0・
> shell_interaction 418/0（**WARN 0**）・web_meaning 1290/0・garage_check 513/0・garage_top 291/0・garage_list 749/0・
> garage_integrity 610/0・mobile_garage_list 804/0・mobile_garage_detail 134/0・mobile_detail 59/0・mobile_feed 63/0・
> feed_w_gap 108/0・feed_m 525/0・filter_transform 3,328/0。＋ `detail_m_transform_check` **3,152 / 0**。
> Final Gate **2,050 / 0**（WF1 1520 / WF2 260 / WF3 24 / WF4 7 / WF5 109 / WF6 75 / WF7 30 / WF8 6 / WF9 14 / WF10 5）。
> VISUAL LOCK: Home / Feed / Library / Search / Browse / Garage Top / Public Garage / Detail 3 面 / Garage Owner Detail 2 面 の
> W 幅で **揺れで説明できない画素 0**。42 Mobile 面 ＋ launcher（390px）で 0。
>
> ### 🔴 踏んだ罠（裁定原本 §1〜§7）
> `behavior:'auto'` は CSS の `scroll-behavior:smooth` に従ってアニメーションする／scroll-snap のレールは「はみ出た分だけ」動かすと
> snap に引き戻される／`1fr` = `minmax(auto,1fr)` で min-content が下限になり狭幅で document を押し広げる／
> grid item に `grid-column:2` があると template を 1 列にしても implicit column が残る。
>
> ### CLOSE 判定
> §11 の条件をすべて満たした。**Web Fundamentals Phase 2 / viewport continuity: CLOSE。**
> 残る技術的負債は「広告について」（非 blocker）だけ。Feed カードの再デザインは DESIGN レーン。
>
> ### 🔴 未了
> - push: mock `579af22` / canon（この 102）とも **local commit のみ**。イタヤの `mockup` コマンドで canon push → mock push → Vercel deploy。
> - Final Gate の Mac 実行: `LD_LIBRARY_PATH=$HOME/.local/lib python3 _state/web_fundamentals_final_check.py`（＋ `--selftest`）。
>
> ### 次
> **通常のモック制作・デザイン探索レーンへ戻る。** 基盤修正レーンは終了。

> ## 🟢 101: Detail M / LOG の Builder card を drop から defer へ（2026-09-14 / 100 の裁定修正 / Cowork 実装）
>
> モック: `myrig-mockup` **`f02239a`**（**push 待ち**）。100 の `06b582c` / canon `dcbdecd` は **push 済み**。
> 正典: この 101。裁定原本: `_decisions/2026-09-14_detail-m-log-builder-v2.md`。Matrix **v1.9**。
> 開始時 canon: GitHub main `dcbdecd` / revision.txt・CURRENT 冒頭とも `MYRIG-20260914-100` で一致確認済み。
>
> ### 何を撤回したか
> 100 の「**LOG M で full Builder card / stats / 大型 actions を明示 drop**」は **Mac 実機確認で過剰**と判明した。
> イタヤの元の意図は「Builder カードを LOG タイトルと本文の間に挟まない」であって、
> 「Builder カード自体を M から消す」ではなかった。
>
> 🔴 **「本文の前に置かない」と「消す」は別の裁定である。** 実機所見から契約を書くときは、
> **遮断の是正**と**責務の削除**を取り違えない。⛔ 位置の問題を drop で解こうとしない。
>
> ### 新しい LOG M
> ```
> Identity → Main（body / photos / tags / dt-actions / Log facts / comments）→ Builder card → RELATED → Footer
> ```
> - Builder card は **M で drop しない**。W の右 Rail から **Main 読了後・RELATED 直前へ defer**（`adapt-main`）。
>   **同一 DOM のまま移動**し、clone しない。
> - **avatar / name / 公開RIG・公開パーツ・公開LOG の stats / Follow / ガレージを見る をすべて維持。**
> - Identity 上部の author row と Public Garage リンクも**維持**。
>   **人物 identity の一部重複は許容**（上部＝投稿者の同定 / 読了後＝作者への回遊で責務が違う）。
> - ⛔ 本文の前へ戻さない。⛔ RIG / PARTS の M を変更しない。⛔ W の右 Rail を変更しない。⛔ R-06 を再 OPEN しない。
>
> ### 実装（宣言 1 行の差し戻し。共有側に LOG 専用分岐は無い）
> | | |
> |---|---|
> | 100 | `<dt-rail-builder data-m="merge:builder-identity" data-m-drop="stats,actions" …>` |
> | 101 | `<dt-rail-builder data-m="adapt-main" …>` |
>
> `adapt-main` は**既存の adapt / defer 文法**（受け皿は `.dt-main` 末尾＝本文の後・RELATED の前）。
> LOG 専用の DOM 複製も専用受け皿も作っていない。`data-m-drop` は使う面が無くなったので語彙ごと撤去した
> （空振りする検査を残さない）。
>
> 🔴 **受け皿は Aside の variant クラス（`.dt-rail--quiet` 等）を引き継ぐ**ようにした。
> `.dt-rail--quiet .rail-section{…}` のような**子孫セレクタは祖先が変わると切れる**ので、引き継がないと
> 「同一 DOM を移動しただけ」なのに M で Widget の見た目（header の帯・border の濃さ）が変わる。
> Builder card が M で最も目立つ要素になったことで顕在化した。⛔ 移動先ごとにスタイルを書き直さない。
>
> ### 検証（cloud Chromium 相当＝デスクトップ Cowork の Linux VM）
> `detail_m_transform_check` **3,152 PASS / 0 FAIL**（DM0 312 / DM1 350 / DM2 960 / DM3 396 / DM4 108 /
> DM5 72 / DM6 678 / DM7 42 / DM8 24 / DM9 60 / DM10 150）。
> 故障 **17 種**すべて単独検知（`log-builder-dropped` / `log-builder-cloned` を追加。
> 本文前へ戻す故障は `log-builder-deck-reinserted` が見る）。
>
> 既存 19 本 **0 FAIL**: detail_contract 51/0/0・entity_actions 36/0・footer_single_source 4/0・image_integrity 63/0・
> launcher 177/0・hit_test 367/0・shell_interaction 418/0・web_meaning 1290/0・garage_check 513/0・garage_top 291/0・
> garage_list 749/0・garage_integrity 610/0・mobile_garage_list 804/0・mobile_garage_detail 134/0・mobile_detail 59/0・
> mobile_feed 63/0・feed_w_gap 108/0・feed_m_transform 525/0・filter_transform 3,328/0。
>
> VISUAL LOCK: LOG 1440 / 1280 / 1051、RIG 1440 / 1051、PARTS 1440 / 1280、
> Garage Owner Detail 1440 / 1040 / 1024 で **揺れで説明できない画素 0**。変化は **LOG の M だけ**。
>
> 実測（LOG 1024）: title 281 → 本文 333（**直後**）／コメント 1157〜1654 → **Builder 1687〜1941** → RELATED 1990。
> Builder は document 内 1 つ（clone 0）・stats 1・Follow 可視・「ガレージを見る」の href は author row と一致。横 overflow 0。
> resize 1024⇄1025 × 3 往復で DOM 完全復元・受け皿の増殖 0・deck が湧かない。
>
> ### 🔴 未了 → **102 で補正: すべて解消**
> - push: mock `f02239a` / canon `4036fa4` とも **push 済み**。**Vercel production READY**。
> - Mac 実機 acceptance（Mac Studio / Chrome）: `detail_m_transform_check` **3,152 PASS / 0 FAIL**、selftest **17 種全検知**。
>   **Detail M の最終 acceptance 完了。**
>
> ### 次
> **Header ≤538 overflow（R-09）**。⛔ R-06 は再 OPEN しない。⛔ viewport continuity はまだ CLOSE しない。

> ## 🟡 100: Detail M / LOG の Builder compact deck 廃止（2026-09-14 / Mac 実機 acceptance の是正 / Cowork 実装）
> 🔴 **101 で一部失効**: 「full Builder card / stats / 大型 actions を M で明示 drop」は**過剰な裁定**だったので撤回した。
>    現行は **Main 読了後・RELATED 直前へ defer**（101 と `_decisions/2026-09-14_detail-m-log-builder-v2.md`）。
>    「本文の前に置かない」「deck を作らない」「clone しない」「author row のリンク化」は**有効**。
>
> モック: `myrig-mockup` **`06b582c`**（**push 待ち**）。099 の `8bafbcb` は **push 済み・Vercel READY**。
> 正典: この 100。裁定原本: `_decisions/2026-09-14_detail-m-log-builder-v1.md`。Matrix **v1.8**。
> 開始時 canon: GitHub main `14df2b2` / revision.txt・CURRENT 冒頭とも `MYRIG-20260914-099` で一致確認済み。
>
> ### きっかけ（Gate が緑でも設計が正しいとは限らない）
> 099 は Gate 上は完全だった（`detail_m_transform_check` 2,808 PASS / 0 FAIL・故障 13 種検知・既存 19 本 0 FAIL・
> VISUAL LOCK 0）。そのうえで **Mac 実機の目視**で、LOG Detail の M（1024px）において
> **Builder の compact deck が LOG タイトルと本文の間に入り、読む流れを強く遮断**していた。
> 🔴 **「契約どおり動く」ことと「読める」ことは別。** 情報の優先順位は assert では判定できない。
> ⛔ Gate が緑だから設計が正しい、と読まない。⛔ 実機所見を「Gate が通っているから」で退けない。
>
> ### 裁定
> | | 旧（099 / Matrix v1.7）| 新（100 / Matrix v1.8）|
> |---|---|---|
> | LOG M の骨格 | Identity → **Builder compact deck** → Main → RELATED → Footer | **Identity → Main → RELATED → Footer** |
>
> **なぜ LOG だけか**: LOG の Identity は avatar / 投稿者名 / handle / 対象RIG を持ち、**本文前に人物同定が済んでいる**。
> RIG / PARTS の Identity は車両 / 製品の同定であって人物ではないので deck が要る。⛔ RIG / PARTS の契約は変えない。
>
> | 責務 | LOG M での扱い |
> |---|---|
> | Builder identity（avatar / name） | **Identity の author row へ merge**（`data-dt-section="builder-identity"`） |
> | Public Garage 導線 | 実体確認の結果 author row は `<div>` でリンクではなかったので `<a>` 化。⛔ 新しい大型 UI は作らない。**行き先は W の Builder「ガレージを見る」と同一値** |
> | 公開RIG / パーツ / LOG の stats | **M で明示 drop**（`data-m-drop="stats,actions"`） |
> | 大型 Follow / ガレージを見る | **M で明示 drop**（同上） |
> | W（>=1025）の右レーン Builder カード | **現状維持**（VISUAL LOCK を動かさない） |
> | LOG 本文 `<dt-actions>` / linked-rig / entity-feed / AD | **現行 Detail M 裁定を維持** |
>
> ### 実装（宣言の変更だけ。共有 JS に面名分岐を入れない）
> - `data-m="deck"` → `data-m="merge:builder-identity"` ＋ `data-m-drop="stats,actions"`。
> - 共有 JS は **deck 対象の widget が 1 つも無い面では compact deck 自体を作らない**（`needs('deck')`）。
>   ⛔ 「カードを `display:none` にするだけ」にしない。⛔ 使わない受け皿を DOM に置かない（099 の罠と同じ原則）。
> - `a.dt-identity__author{color:inherit;text-decoration:none}` ＋ hover で名前だけ下線 ＋ `:focus-visible` の outline。
>   **既定状態の見た目は不変**（`.dt-identity__author` は LOG Detail だけが使うクラス＝他面への波及なし）。
> - `data-m-drop` は「黙って消えたのか裁定して落としたのか」を実体から区別するための宣言。Gate が両方見る。
>
> ### 検証（cloud Chromium 相当＝デスクトップ Cowork の Linux VM）
> `detail_m_transform_check` **3,020 PASS / 0 FAIL**（DM0 312 / DM1 350 / DM2 840 / DM3 396 / DM4 108 /
> DM5 72 / DM6 678 / DM7 30 / DM8 24 / DM9 60 / DM10 150）。
> 故障 **15 種**すべて単独検知（`log-builder-deck-reinserted` / `log-author-link-removed` を追加）。
>
> 既存 19 本 **0 FAIL**: detail_contract 51/0/0・entity_actions 36/0・footer_single_source 4/0・image_integrity 63/0・
> launcher 177/0・hit_test 367/0・shell_interaction 418/0・web_meaning 1290/0・garage_check 513/0・garage_top 291/0・
> garage_list 749/0・garage_integrity 610/0・mobile_garage_list 804/0・mobile_garage_detail 134/0・mobile_detail 59/0・
> mobile_feed 63/0・feed_w_gap 108/0・feed_m_transform 525/0・filter_transform 3,328/0。
>
> VISUAL LOCK: LOG 1440 / 1280 / 1051、RIG 1440 / 1051、PARTS 1440、Garage Owner Detail 1440 / 1040 / 1024 で
> **揺れで説明できない画素 0**。変化は **LOG の M だけ**。
> resize 1024⇄1025 × 3 往復で DOM 完全復元・受け皿の増殖 0・LOG に deck が湧かない。
>
> ### 🔴 未了
> - **push**: mock `06b582c` / canon（この 100）とも **local commit のみ**。Cowork の実行シェルから GitHub 認証不可。
> - **Vercel**: push 後に READY 確認が要る。
> - **今回分の Mac 実機 acceptance**: 未実施。
>
> ### 次
> **Header ≤538 overflow（R-09）**。⛔ R-06 は再 OPEN しない。⛔ viewport continuity はまだ CLOSE しない。

> ## 🟢 099: Web Fundamentals Detail M Transform Batch（R-06 CLOSE）（2026-09-14 / Cowork 実装）
>
> モック: `myrig-mockup` **`8bafbcb`**（**local commit のみ・push 未了**）。開始時 HEAD / `origin/main`（ローカル ref）とも `ff8855b`。
> 正典: この 099。裁定原本: `_decisions/2026-09-14_detail-m-transform-v1.md`。Matrix **v1.7**。
> 開始時 canon: GitHub main `ced4be6` / revision.txt・CURRENT 冒頭とも `MYRIG-20260913-098` で一致確認済み。
>
> ### 何をしたか
> **Public Detail 3 面（`myrig-rig-detail-v15` / `myrig-parts-detail-v1-open` / `myrig-log-detail-v1`）** の
> Aside Rail を M（`<=1024`）で解体し、責務ごとに受け皿を変えた。⛔「Gallery → Aside 全部 → Main」は不採用。
>
> | | RIG / PARTS | LOG |
> |---|---|---|
> | 骨格 | Identity → Gallery → **Builder＋Actions の compact deck** → Main → RELATED → Footer | 🔴 **失効（100 で是正）**: Identity → **Builder の compact deck** → Main → RELATED → Footer。現行は **Identity → Main → RELATED → Footer**（Mac 実機 acceptance の裁定。理由は 100 と `_decisions/2026-09-14_detail-m-log-builder-v1.md`） |
> | merge（M で出さない・W へ戻すと復元） | base-model→ベースモデル / entity-feed→LOG / used-parts→使用パーツ（RIG）、parts-master→製品情報 / used-by-rigs→このパーツを使っているRIG（PARTS） | linked-rig→Identity の RIG chip / entity-feed→RELATED「このRIGの他のLOG」 |
> | adapt | external-links・AD → Main 後方／builder-rigs・builder-parts・小AD → RELATED／**PARTS の entity-feed は「登録情報」の直後**（登録/装着/取り外しの履歴であって LOG ではない） | rail AD → Main 後方（＝ RELATED の直前） |
> | Actions | rail Actions を deck へ移動 | **本文 `<dt-actions>` のみ。Rail Actions を新設しない** |
>
> - **clone しない。** 既存 DOM を移動するだけなので `SoT_entity-actions.js` の listener / `__eaKey` /
>   pressed / count / `aria-pressed` / LoginRequired / 楽観更新 / rollback / toast / share がそのまま移る（二重 state が作れない）。
> - M で空になった Aside は `hidden` + `inert` + `aria-hidden`。layout・hit-test・Tab 順から消える。
> - **W では受け皿を DOM から外す**（`display:none` では隣接セレクタが切れて W の見た目が変わる。§ 罠を参照）。
> - **明示 grid-row を捨てて DOM 順の auto-placement** にしたので、Gallery を持たない LOG に空 row / 空 gap が出ない。
>
> ### R-06（1025〜1050 の混在帯）を閉じた
> **>=1025 は Detail W / <=1024 は Detail M。** Global Shell（`<=1024` で hamburger）と 1 本に揃えた。
> `SoT_detail.css` / `SoT_detail-rail.css` から旧 `350px/961`・`<=960`・`<=1050` を撤去。
> 1025 でも 2 カラムが成立することを実測（主列 610px / rail 365px / 横 overflow 0 / 内側 overflow 0）したので、
> **帯ごとの rail 幅調整は入れていない**（入れると 1051〜1279 の VISUAL LOCK が動く）。
> ⛔ Shell の breakpoint を 1050 へ広げない。⛔ 961 / 960 / 1050 を書き戻さない。
>
> ### 🔴 Garage Owner Detail 2 面は現状保存（別裁定）
> `.dt-grid` / `.dt-rail` は Public Detail 3 面と Garage Owner Detail 2 面の**計 5 面**が共有している。
> Garage Owner Detail は **092 で CLOSE 済み**なので今回は触らず、旧 `<=1050` stack / `961` の rail 規則を
> `SoT_detail-m.css` の `:not([data-dt-transform])` へ**隔離して持ち越した**。全 13 幅で pixel 差 0 を実測。
> → この 2 面だけ 1025〜1050 の混在帯が残る。揃えるかは **別裁定**（Matrix §8）。
>
> ### 追加・変更したファイル
> | ファイル | 変更 |
> |---|---|
> | `pc/assets/css/SoT_detail-m.css` | **新設**。`--dt-mode` の宣言（JS に px を持たせないための唯一の入口）・M の 1 列・compact deck・adapt 受け皿・非対象面の現状保存 |
> | `pc/assets/js/SoT_detail-m.js` | **新設**。責務の移動と復元。`--dt-mode` を読むだけで px を持たない。listener は `resize` 1 本 |
> | `pc/assets/css/SoT_detail.css` / `SoT_detail-rail.css` | 旧帯規則を撤去し、W の下限を 1051 → 1025 へ（rail 幅 365px は据え置き） |
> | Detail 3 面 | `.dt-grid` に `data-dt-transform`、rail widget に `data-m="deck\|merge:<key>\|adapt-main\|adapt-after:<key>\|adapt-related"`、merge 先の節に `data-dt-section` を**宣言するだけ**。⛔ page-local の JS / CSS は 1 行も足していない |
> | Garage Owner Detail 2 面 | `SoT_detail-m.css` の `<link>` のみ追加（`data-dt-transform` は付けない＝変身しない） |
> | `_state/detail_m_transform_check.py` | **新設** Gate |
>
> ### 検証（cloud Chromium 相当＝デスクトップ Cowork の Linux VM）
> `detail_m_transform_check` **2,808 PASS / 0 FAIL**（3 面 × fixture 12 × 11 幅 ＋ 1024⇄1025 往復 3 回 ＋ 700→1300 スイープ）。
> 故障 **13 種**すべて単独検知（breakpoint を 1050 へ戻す / Builder clone / Actions clone / LOG へ Rail Actions 追加 /
> 空 Aside を focusable のまま / W 復元失敗 / **W で受け皿を DOM に残す** / M で Aside 全体 stack /
> PARTS entity-feed を LOG 扱い / LOG linked-rig 二重 / merge 先欠落 / deck を Main の後ろ / adapt 受け皿ごと drop）。
>
> 既存 19 本 **0 FAIL**: detail_contract 51/0/0・entity_actions 36/0・footer_single_source 4/0・image_integrity 63/0・
> launcher 177/0・**hit_test 367/0**・shell_interaction 418/0・web_meaning 1290/0・garage_check 513/0・garage_top 291/0・
> garage_list 749/0・garage_integrity 610/0・mobile_garage_list 804/0・mobile_garage_detail 134/0・mobile_detail 59/0・
> mobile_feed 63/0・feed_w_gap 108/0・feed_m_transform 525/0・filter_transform 3,328/0。
> `hit_test` は 373 → **367 PASS**。差の 6 は 3 面 × 900/720 の `.dt-rail` 分類が M で skip になったぶんで、
> 責務の hit-test は新 Gate の DM7 が compact deck 側で見る（**空振りではない**）。
>
> VISUAL LOCK: Public Detail 3 面の 1440 / 1280 / 1279 / 1051 と Garage Owner Detail 2 面の
> 1440 / 1280 / 1100 / 1051 / 1040 / 1025 / 1024 / 961 / 900 / 834 / 720 / 640 / 540 で
> **揺れで説明できない画素 0**。意図した変化は **1025〜1050 の Public Detail 3 面だけ**。
>
> ### 🔴 この batch で踏んだ罠（裁定原本 §4 に全文）
> 1. **M の受け皿を W にも置いたままにすると W が壊れる。** `display:none` では隣接セレクタは切れたまま。
>    `.dt-identity + .dt-main`（LOG の行詰め）と `.section--flat + .section--flat`（PARTS の節間 44px）が失効し、
>    PARTS 1,024,554px / LOG 193,627px の pixel 差が出た。**W では DOM から外す。**
> 2. **共有 CSS の @media を一律に広げると、共有している別の面が動く。** `.dt-grid` を持つのは 5 面。
>    Garage Owner Detail が 1024 / 1040 / 1051〜1100 / 720 の 4 か所で動いた。
>    広げるときは `[data-dt-transform]` のように**対象を名指しして**広げる。
> 3. 検査側: fixture で Aside の中身が変わる面があるので **W の基準は fixture ごとに取る**。
>    縮約走査の幅リストを昇順にしないと単調性 assert が常に FAIL する。
>    pixel proof は **native `<select>` の描画揺れ**を拾う（444px の偽陽性を実測・分離）。
>
> ### 触っていない（PENDING）
> Header ≤538 overflow（R-09・次）／Home 棚 clip 外 focus（F-4）／法務項目「広告について」／
> Overlay 契約の実装 3 本の共通化／**Garage Owner Detail の変身境界**（別裁定）。
>
> ### 🔴 未了（この revision では達成していない）→ **100 で解消**
> - **push**: 🔴 **100 で補正**: mock `8bafbcb` / canon `14df2b2` とも **push 済み**（イタヤの手元で実行）。Vercel READY。
> - **Mac 実機 Chrome（実フォント・DPR2）での acceptance**: 🔴 **100 で補正**: 実施済み。
>   **その結果 LOG M の Builder deck が不採用と裁定された**（100 を参照）。数値自体は旧契約の PASS として扱う。
>
> ### 次
> **Header ≤538 overflow（R-09）**。⛔ MyRIG 全体の viewport continuity はまだ CLOSE しない。

> ## 🟢 098: Web Fundamentals Feed M Transform Batch ＋ Footer PENDING 決着（2026-09-13 / Cowork 実装）
>
> モック: `myrig-mockup` **`ff8855b`**。🔴 **099 で補正**: 本文の「push 待ち」は失効（`ff8855b` は push 済みで Vercel READY、Mac 実機 acceptance も完了済み＝ feed_m 525/0・selftest 18 種検知・既存 19 本 0 FAIL）。正典: この 098。
> 開始時: GitHub main は canon `039ee89`（097）／mock `d071dbb` で一致確認済み（両 repo tree clean）。
> 裁定原本: `_decisions/2026-09-13_feed-m-transform-and-footer-v1.md`。Matrix v1.6。
>
> ### 097 の実績補正
> - mock `d071dbb` / canon 097 `039ee89` は **push 済み**（097 本文の「push 待ち」は失効）。
> - Filter Transform Batch の **Mac 実機 acceptance**（イタヤ報告）: filter_transform 3,328/0・selftest 13 種すべて狙った FAIL を検知・
>   garage_list 749/0・mobile_garage_list 804/0・garage_check 513/0・garage_top 291/0・garage_integrity 610/0・launcher 177/0・
>   image_integrity 63/0・mobile_feed 63/0・mobile_detail 59/0・detail_contract 51/0/0 WARN・**entity_actions 合計 36 / PASS 36 / FAIL 0**・
>   footer_single_source 4/0・mobile_garage_detail 134/0・web_meaning 1290/0・hit_test 373/0・shell_interaction 418/0・feed_w_gap 108/0。
> - Mac の `_state/garage_list_check_result.json` は 734→749 へ変わっただけの実行結果だったため commit せず restore 済み。
>
> ### Feed 実体の棚卸し（裁定の前提・実測）
> | 領域 | 実体 |
> |---|---|
> | `aside.feed-left.myrig-filter-sidebar` | **base に `display:none`。どの幅でも一度も表示されない死に markup**。`data-feed-panel="all\|following\|trending"` / `data-feed-type="maintenance\|run\|custom\|memo"` を持ち、実際に動く `.feed-main-tabs`（all/latest/following）・`.feed-rail-types`（all/整備/走行/カスタム/メモ）と**語彙が食い違う第2定義**だった |
> | `aside.feed-left-modern` | 実際の左 Rail。LOG投稿 ＋ `.feed-rail-types` ＋ 紹介コピー |
> | `main.feed-center` | 3タブ／`.feed-type-chips`（**`display:none !important` で全幅非表示**・Rail と同じ5値を手書き複製）／`.pc-feed-ad`（**おすすめタブのみ**） |
> | `aside.feed-right` | 注目RIG(3)／おすすめユーザー(3)／広告枠(2)／page-local mini footer（`利用規約 / プライバシー / 広告について` を**すべて `href="#"`**。Global Footer の法務3本と語彙も本数も違う第2定義） |
>
> **M（≤900）で起きていたこと**: 左右 Rail がまとめて `display:none` になり、**LOG投稿・LOG種別・注目RIG・おすすめユーザー・右レーン広告が受け皿なしに全部消えていた**。
> `.feed-type-chips` は M でも `!important` で隠れたままで、Matrix が「M の受け皿」と書いた region が機能していなかった。
>
> ### 是正（責務ごとに受け皿へ。挙動は `SoT_feed-rails.js`・面は属性だけ）
> | 責務 | M の受け皿 | 単一情報源の守り方 |
> |---|---|---|
> | LOG投稿 | 既存 Header の投稿導線（`#cxBtn` →「ログを投稿」） | Header 側が `href="#"` の死にリンクだったので Rail と同じ composer へ（Home / Garage v7 6面 / Public Garage 4面と同じ既存の行き先） |
> | LOG種別 | 既存 `.feed-type-chips` | 手書きの5個を撤去して**空スロット**にし、Rail の `.feed-rail-types` を **DOM ごと移す**（定義は1組だけ。Tab 順も Main 直前） |
> | 注目RIG / おすすめユーザー | Feed-local「発見」Drawer | **右 Rail 自体が Drawer へ変身**するので中身は W と同一 DOM。⛔ Global hamburger にも timeline タブにも混ぜない |
> | 広告 | Feed 内広告 `.pc-feed-ad` | 受け皿がおすすめタブにしか無く、新着 / フォロー中では M で広告責務ごと消えていた → **全パネル**に配置。Drawer では広告枠を出さない |
> | 法務導線 | Global Footer（M で出る） | `SoT_footer.js` に `<site-legal>` を追加。**真源は `LEGAL` 配列 1 本**で Footer 本体と共用。Drawer では法務行を出さない |
>
> 死に markup（`aside.feed-left` ＋ page-local script ＋ 補完 CSS ＋ 不要になった `SoT_filter-sidebar.css` の読み込み）を撤去した。
> PC Narrow Fallback（≤720）まで M の変身先をそのまま維持する（専用 Mobile 面 C とは別物）。
>
> ### 🔴 Footer PENDING の決着 — **裁定 B ＋ 法務導線の Single Source 化**
> | | 裁定 |
> |---|---|
> | W（内部 scroll 帯） | **Global Footer 本体は出さない。** 代わりに Shared Source 由来の `<site-legal>` を右 Rail に置く |
> | M | Matrix どおり **Global Footer へ merge**（Rail 側の法務行は出さない） |
> | 真源 | `SoT_footer.js` の `LEGAL` **1本**。⛔ Feed だけが別の法務リンクを持つことを禁止 |
>
> **なぜ A（W でも Footer 本体）を採らなかったか**: ① Feed の追加ロードは**無限スクロール**（page-role-matrix §6 / #25）なので、
> `.feed-center` の末尾に置いた Footer は**構造上いつまでも到達できない**（「出したが辿り着けない」は今より悪い）。
> ② 内部 scroll を捨てると `html,body{overflow:hidden}` ＋ Rail の `height:100%` という W の骨格ごと作り替えになり、R-07 で確立した W の連続性も巻き込む。
> 🔴 「以前そうだったから」ではなく、**W / M 両方で実 scroll 到達性を測ったうえで**の判断（下表）。
>
> | 幅 | 法務導線の場所 | 到達 |
> |---|---|---|
> | 1440 / 1200 / 901 | 右 Rail の `<site-legal>` | Rail を末尾まで scroll（可視 840 / 内容 1051〜965）→ `elementFromPoint` が当該リンクを返す ✅ |
> | 900 / 720 | Global Footer の法務3本 | ページ末尾まで scroll（doc 900 / 6703〜6885）→ 同 ✅ |
>
> **page-local mini footer をやめた理由**: `href="#"` だけで行き先の宣言すら無く、文言・本数が Global Footer と食い違っていた（表示事故）。
> **「広告について」は採用しない**（Shared `LEGAL` に無い項目を Feed だけが持つのは禁止事項）。サイト全体で要るなら `LEGAL` へ足す話なので **PENDING として残す**（黙って落としていない）。
>
> ### Gate（新設 `_state/feed_m_transform_check.py`）
> 18 幅（`--feed-mode` 宣言を **CSS から抽出**した境界 ±1 ＋ 720/721・899/900/901・1024/1025・1119/1120/1121・1199/1200/1201・1280・1440）＋ 700→1280 スイープ。
> FM0 must-exist（必須欠落を SKIP にしない）／FM1 transform completeness（意味集合に drop 無し）／FM2 no duplicate（discovery を timeline タブへ混ぜない）／
> FM3 closed（inert・aria・実 Tab 45 回で leak 無し・backdrop 非表示）／FM4 実 click → open → focus in → 実 Tab → Escape → backdrop → focus 復帰／
> FM5 posting・chips（Header 投稿導線へ実 click・chip の実 click で絞り込みが効く）／FM6 ad（全パネルに受け皿・Main を遮らない）／
> FM7 legal・Footer（`#` だけの偽リンク禁止・Shared Source 一致・**実 scroll 到達 ＋ hit-test**）／FM8 scroll（container・double scroll trap 無し・close で復帰・畳んだ Rail へ focus しない）／
> FM9 geometry（overflow 0・Main overlap 0・**空 grid 列 = 列数 vs in-flow の子**）／FM10 monotonic（`w-rail → compact-rail → m` の一方向）。
> **525 PASS / 0 FAIL。修正前ツリー 123 FAIL。** 故障 18 種を 1 種ずつ注入し**全種単独 FAIL**。
>
> ### regression（cloud・変更前 baseline `d071dbb` と比較）
> feed_w_gap **108/0 維持**（R-07 非回帰）／filter_transform 3,328/0／garage_list 749/0／mobile_garage_list 804/0／garage_check 513/0／garage_top 291/0／
> garage_integrity 610/0／launcher 177/0／detail_contract 51/0/0 WARN／**entity_actions 合計 36 項目 / PASS 36 / FAIL 0**／footer_single_source 4/0／
> web_meaning 1290/0／hit_test 373/0／shell_interaction 418/0。
> `mobile_feed_check` は **60/3**（cloud 依存のみ・baseline と同一集合）。同検査の「PC: グループ名 ×3」は**重複定義の個数**を固定していたので
> 「role=group がちょうど1組・可視・重複定義なし」へ**精密化**（緩和ではない。修正前ツリーではこの assert が FAIL する）。
> image_integrity 44/19・mobile_detail・mobile_garage_detail 132/2 は cloud 環境依存の FAIL 集合が baseline と**同一**。
> pixel / DOM: W（1440〜901 の 9 幅）は撤去・差し替えた領域を除いて**全要素 rect 0 差**。
>
> ### 触っていない（PENDING）
> Detail M（R-06・次）／Header ≤538 overflow（R-09）／Home 棚 clip 外 focus（F-4）／Feed カード自体の再デザイン／`css/sot/SoT_app-shell.css` 旧コピー／
> 法務項目「広告について」／**Overlay 契約の実装が3本になった**（`SoT_app-shell.js` / `SoT_filter-sidebar.js` / `SoT_feed-rails.js`）。共通ヘルパへの抽出は別 batch。
>
> ### 次
> **Detail M**（Aside Rail: Detail — Gallery → Builder＋Actions の compact deck → Main、残りは Main の該当節へ merge。R-06 の 1025〜1050 混在帯と Matrix §3 の旧値 350px/961 の訂正も同時に）→ Header ≤538。
> ⛔ MyRIG 全体の viewport continuity はまだ CLOSE しない。
> 🔴 **099 で補正**: 「Mac 実機での `feed_m_transform_check`（＋ selftest）と 19 本の再走は未実施」は**失効**。実施済み。

> ## 🟢 097: Web Fundamentals Filter Transform Batch — Garage filter M ＋ Library filter R-05（2026-09-13 / Cowork 実装）— **push 済み（098 で補正）**
>
> モック: `myrig-mockup` **`d071dbb`**（push 済み）。正典: この 097（`039ee89`・push 済み）。Mac 実機 acceptance は 098 の「097 の実績補正」。
> 開始時: GitHub main は canon `59387d0`（096）／mock `ee323de` で一致確認済み（両 repo tree clean）。
> 裁定原本: `_decisions/2026-09-13_web-fundamentals-filter-transform-v1.md`。Matrix v1.5。
>
> ### 096 の実績補正
> - mock `ee323de` / canon 096 `59387d0` は **push 済み**（096 本文の「push 待ち」は失効）。
> - Recovery Batch 2 の **Mac 実機**再走（イタヤ報告）: garage_list 734/0・mobile_garage_list 804/0・garage_check 513/0・garage_top 291/0・garage_integrity 610/0・launcher 177/0・
>   image_integrity 63/0・mobile_feed 63/0・mobile_detail 59/0・detail_contract 51/0・footer_single_source 4/0・mobile_garage_detail 134/0・web_meaning 1290/0・hit_test 367/0・
>   shell_interaction 418/0・shell_interaction 故障 10 種すべて検知。entity_actions は当時 `tail -1` が区切り線を拾い Mac の要約値は未確認（cloud 36/0）。
>   → 以後 runner は「合計 … PASS … FAIL」行を明示取得する（今回 cloud: **合計 36 項目 / PASS 36 / FAIL 0**）。
>
> ### 原因（同型の 2 問題）
> | | 実体 | 到達の実測（page top 基準・修正前） |
> |---|---|---|
> | Garage 一覧 8 面（Own 5 ＋ Public 3） | 共有 `SoT_garage-list.css` の 1 列帯（`.main-2col` 1 列）で右レーンの `aside.filter-panel` が**一覧の下**に積まれる（Matrix §3 で「⛔ 一覧の下へ置かない」と裁定済み・未実装） | RIG 一覧 720px: 一覧先頭 y=1,061 に対し filter は y≈2,505（一覧を全部通過しないと届かない） |
> | Library 一覧 3 面 | page-local `.lib-layout` が 980px で 1 列に落ち、`aside.myrig-filter-sidebar` が Main の**前**に全幅で積まれる（R-05） | 一覧先頭 y: 980px = 1,413 → 981px = 437（RIG）。PARTS 1,230 → 437。Makers 852 → 437 |
> 共通原因: filter は存在するが狭幅でそのまま大きな block として積まれ、Main / 一覧への到達を大幅に遅らせる。
>
> ### 是正（挙動は共有側・面は属性だけ）
> | | Garage 8 面 | Library 3 面 ＋ Search |
> |---|---|---|
> | 変身 | 1 列帯: Toolbar「絞り込み」（一覧本文の**直前**）→ 直後の inline 折りたたみ panel。初期 closed（`hidden`＋`inert`）。backdrop なし | 「フィルター」ボタン → Drawer（backdrop あり）。Search の page-local Drawer（MR-MOCK-035D）を **`SoT_filter-sidebar.js`（新設）＋ `SoT_filter-sidebar.css` FS03** へ移し、Library 3 面へ同じものを適用 |
> | active 条件 | trigger 内 badge（条件数）＋ 要約（例 `カテゴリ: Trail · ブランド: RC4WD`）。閉じていても分かる | 同じ badge（Search は既存 trigger に badge だけ注入） |
> | DOM | 1 列帯では panel を Toolbar 直後へ DOM ごと移し、広幅で元へ戻す（Tab 順のため。grid-row では動かない） | 不変 |
> | 境界 | CSS の `--garage-list-mode` を JS が読む（**JS に px を書かない**）。W の 1 列帯（右レーンが立たない幅）も同じ変身 | `.lib-layout` の page-local 980 を捨て、共有 Context Rail の境界（Search filter と同じ `--fs-mode`）へ統一。**数字ではなく「Search と同じ境界」が契約** |
> | 面の変更 | **0**（markup 不変） | `aside` に `data-fs-drawer`・`<script>` 1 行・1 列化境界。Search は page-local の Drawer CSS / JS を撤去、trigger を `data-fs-toggle` に |
> | W | 12 面とも全要素 DOM rect **0 差**（1440 / 1280 / 1101）。Feed 不変 | 同左（closed Drawer の影の漏れ 0〜51px を止めた差だけ = Batch 2 と同じ artifact） |
>
> ### Gate（新設 `_state/filter_transform_check.py`）
> Garage 8 ＋ Library 3 ＋ Search × 14 幅（境界は**共有 CSS から抽出**、その ±1 ＋ 720/721・900・979/980/981・1024/1025・1099/1100/1101・1280・1440 を昇順）＋ 700→1200 の 10px スイープ。
> FT0 must-exist（trigger 可視・受け皿・filter 項目数が W と同数）／FT1 closed（aria-expanded=false・不可視・inert・backdrop hidden・実 Tab 30 回で panel 内へ入らない）／
> FT2 一覧本文の先頭が trigger の直後（DOM 順・視覚順）／FT3 overflow 0／FT4 panel × Main 重なり 0／FT5 active 条件（適用 → badge・要約、閉じても残る、解除で消える）／
> FT6 実 click → open → focus in → 実 Tab で不可視要素へ飛ばない → Escape → focus 復帰／FT7 closed で Main 先頭リンクの hit-test ＋ 実 click／FT8 単調性（mode が境界どおり・遷移 1 回）。
> **3,328 PASS / 0 FAIL。修正前ツリー 1,882 FAIL。** 故障 13 種（closed panel focusable / trigger handler 無効 / 受け皿削除 / 初期 open / panel を一覧の後ろ / 全積みへ戻す /
> overlay で pointer 遮断 / Escape 無効 / focus return 無効 / active indicator 消失 / Library 980・981 に穴 / Garage 境界 ±1 に穴 / 非単調）を 1 種ずつ注入し**全種単独 FAIL**。
>
> ### regression（cloud・変更前 baseline と比較）
> garage_list **749/0**（GL13 を「カード（[data-list-items] の子）を DOM 再接続しない」の本来の不変条件へ精密化: 静的は sortItems に限定、動的に並べ替え後の DOM 順不変を追加。旧 734）／
> mobile_garage_list 804/0／garage_check 513/0／garage_top 291/0／garage_integrity 610/0／launcher 177/0／detail_contract 51/0／**entity_actions 合計 36 項目 / PASS 36 / FAIL 0**／
> footer 4/0／web_meaning 1290/0／hit_test **373/0**（HT3 の Search toggle selector を共有名へ。旧 367）／shell_interaction 418/0（WARN 2 = Home 棚 F-4 従来どおり）／feed_w_gap 108/0。
> image_integrity 44/19・mobile_feed 60/3・mobile_detail・mobile_garage_detail 132/2 は cloud 環境依存の FAIL 集合が baseline と**同一**。
>
> ### 触っていない（PENDING）
> Feed M 本体／Feed Footer PENDING／Detail M／Header ≤538 overflow／Home 棚 clip 外 focus（F-4）／`css/sot/SoT_app-shell.css` 旧コピー。Search の filter 内部（facet state）は Search 固有のまま。
>
> ### 次
> **Feed M**（Aside Rail: Feed の M — 左は Header 投稿導線と `.feed-type-chips` へ merge、右は「発見」Drawer / Feed 内広告 / Global Footer へ merge。Footer PENDING の裁定を同時に）→ Detail M → Header ≤538。
> ⛔ MyRIG 全体の viewport continuity はまだ CLOSE しない。Mac 実機での `filter_transform_check`（＋ selftest）と 17 本の再走は未実施。

> ## 🟢 096: Web Fundamentals Feed W Gap Recovery — R-07（2026-09-13 / GPT 裁定・Cowork 実装）— **push 済み（097 で補正）**

> モック: `myrig-mockup` **`ee323de`**（push 済み・097 で補正）。正典: この 096（`59387d0`・push 済み）。
> 開始時: GitHub main は canon `bd5b2d8`（094）／その後 Mac から 095 `cfb06f7` と mock `9a45d50` が push され origin と一致。
> Recovery Batch 2 の Mac 実機再走の結果は 097 の「096 の実績補正」に記録。
>
> ### 原因（R-07）
> `pc/myrig-feed-v3.html` の page-local に旧世代の `@media(max-width:1200px){ .feed-shell{2 列} .feed-right{display:none} }` が残り、
> FEED STREAM R2 の 3 列化（264 / 640 / 264、≤1120 で 170 / 640 / 170）のあとも生き残っていた。1121〜1200px で
> 右レール（注目RIG / おすすめユーザー / 広告）が受け皿なしに消え（空の 264px 列だけ残る）、1120px 以下で再出現する非単調状態。
>
> ### 是正（最小）
> 旧 ≤1200 の drop 規則を撤去（理由コメント付き・書き戻し禁止）。compact 3 列の境界を 1120 → 1200 にし、1121〜1200 も
> 同じ compact 3 列で連続させる。変身は **レール 264（≥1201）→ 170（≤1200）→ 消える（≤900・Matrix §3 の裁定済み M）** の単調な一方向。
> 1100 以下と ≥1201 の見た目は不変（pixel 一致）。Feed M の最終変身（Header 投稿導線 / feed-type-chips / 発見 Drawer / Feed 内広告 / Global Footer）は別 batch のまま。
>
> ### Footer / overflow の実体調査（今回は触らない → PENDING）
> | | |
> |---|---|
> | 発動幅 | `@media(min-width:901px)` — `html,body{overflow:hidden}`・`.feed-shell{height:calc(100vh−60px)}`・`.feed-center{overflow-y:auto}`・`.site-footer{display:none}`。≤900 では Global Footer が出る |
> | 意図 | page-local コメント「PC Feed は閲覧アプリ型。左右の文脈は残し、中央のタイムラインだけを送る」。右レール下部の mini footer（利用規約 / プライバシー / 広告について、href は `#`）が Global Footer の代替 |
> | 裁定根拠 | **なし**（feed-continuity / feed-tabs の裁定原本・CURRENT に記載なし）。Matrix §3 Feed M 行の「法務導線 → Global Footer・menu へ merge」は M 側の裁定で、W 側で Footer を消す根拠ではない |
> | 判断 | Global Footer を復旧するには feed-center を scroll container にしている構造（Footer を scroll 内へ入れるか shell の高さを変える）に手を入れる必要があり、R-07 の W gap 修正と分離。**「Feed だけ Global Footer を消す」を既定仕様として固定しない。** Feed M batch で法務導線の受け皿と一緒に裁定する |
>
> ### Gate（新設 `_state/feed_w_gap_check.py`）
> 14 幅（900 / 901 / 1024 / 1025 / 1100 / 1119 / **1120 / 1121** / 1150 / 1199 / **1200 / 1201** / 1280 / 1440）＋ 880→1440 の 10px 連続スイープ。
> FW1 存在／FW2 可視／FW3 Main 非重なり／FW4 overflow 0／FW5 Header・Footer 非衝突／FW6 右レール代表 control の `elementFromPoint` ＋ **実 click 到達**／
> FW7 「表示 → 消失 → 再表示」なし／FW0 ≤900 は非表示（裁定済み M）。**108 PASS / 0 FAIL。修正前ツリーでは 17 FAIL（1121〜1200）。**
> 故障 6 種を 1 種ずつ注入 → 旧 drop 復活: FW1・2・5・6・7／旧 1120 競合復活: FW0・1・2・5・6・7／opacity:0: FW2・7／pointer-events:none: FW6／Main 上へ重ねる: FW3／1121 だけ穴: FW1・2・5・6。全種単独 FAIL。
>
> ### regression（cloud）
> shell_interaction 418 / hit_test 367 / web_meaning 1290 / footer 4 / launcher 177 / garage_check 513 / garage_top 291 / garage_list 734 /
> garage_integrity 610 / detail_contract 51 / entity_actions 36 / mobile_garage_list 804 — 0 FAIL。image_integrity / mobile_feed / mobile_detail /
> mobile_garage_detail は cloud 環境依存の FAIL 集合が baseline と同一。共通 Drawer（Batch 2）は非回帰。
>
> ### 次
> **Garage filter M ＋ Library filter（721〜980）**: Garage 一覧の filter panel（Toolbar の Filter → 一覧本文の直前に折りたたみ panel・初期 closed、Matrix §3 裁定済み）と、
> Library 一覧 3 面の 721〜980 未所有帯（filter sidebar 全積みで一覧先頭 y≈1,108 = R-05。Search filter と同じ `myrig-filter-sidebar` 部品）。
> ⛔ MyRIG 全体の viewport continuity はまだ CLOSE しない（Detail M・Feed M・Header ≤538 が残る）。

> ## 🟢 095: Web Fundamentals Recovery Batch 2 — Global Shell M / Common Drawer（2026-09-13 / GPT 裁定・Cowork 実装）
>
> モック: `myrig-mockup` **`9a45d50`**（push 済み）。正典: この 095（`cfb06f7`・push 済み）。Mac 実機の再走結果は 097 の「096 の実績補正」。
> 開始時: GitHub main は canon `bd5b2d8`（094）／mock `17f4210` で一致確認済み。
>
> ### 原因（R-02）
> | | |
> |---|---|
> | 死ボタン | `SoT_app-shell.js initDrawer()` は `.home-dir`（Home / Browse 4 面のカテゴリ directory）が無いと return。≤1024px で見えている hamburger を押しても何も開かない（現行 28 面） |
> | 導線 drop | `.app-nav`（Browse / Feed / Library）が ≤1024px で `display:none`。代替のグローバル導線なし |
> | focus 漏れ | Home 等の閉 Drawer は transform で画面外にあるだけで Tab 対象から外れておらず、x≈−248 の不可視リンクへ focus が入る。Search の filter panel（page-local）も同型（x=−320） |
>
> ### 実装（共有 Shell のみ。page-local 複製なし）
> | | |
> |---|---|
> | 共通 Drawer の受け皿 | hamburger のある面は必ず Drawer を持つ。`.home-dir` 無し → `<nav class="app-drawer" id="appDrawer">` を生成、overlay 無し（Detail 等）→ 生成 |
> | global navigation | `.app-drawer__nav`（Browse / Feed / Library）を `.app-nav` から複製（単一情報源）して Drawer 先頭へ。directory を持つ面では**同じ Drawer 内の別 region**（directory と global nav の責務分離）。≥1025 では非表示（W の見た目不変） |
> | hamburger 契約 | closed: aria-expanded=false・Drawer は **inert**（Tab 順から除外・pointer 不可）・背景は通常操作可／open: aria-expanded=true・focus を Drawer 内へ・Tab は Drawer 内で循環・Escape / backdrop click で閉じ **trigger へ focus 復帰** |
> | 既存 Drawer との共存 | Garage Detail 2 面は hamburger が `[data-garage-drawer]`（`SoT_garage-drawer.js`）を aria-controls で受け皿にしているので開閉を委譲し、global nav だけ注入（1 trigger に 2 Drawer を結線しない。G16 維持） |
> | 付随 | closed Drawer の `box-shadow` を切った（translateX(-100%) でも影が画面左端 0〜28px に漏れていた artifact。Home / Browse では従来から出ていた）。Search filter panel にも closed inert / focus return |
>
> ### 実操作 Gate（新設 `_state/shell_interaction_check.py`）
> 実 keyboard（Playwright `keyboard.press`）と実 click。11 面 × {900, 720, 1100}。
> **SI0 must-exist**（hamburger / `.app-nav`。無ければ skip ではなく **FAIL**）／A open → Escape → focus 復帰／B closed Tab が Drawer 内へ入らない／
> C open Tab が Drawer 内の可視 control だけ／D global nav 実 click で遷移先が href と一致／E closed の背景 hit-test と backdrop click／F 正の tabindex 無し。
> **418 PASS / 0 FAIL・skip 0・WARN 2**（Home 棚の clip 外カードへの focus = Phase 1 F-4 の範囲、Shell 契約外として件数明示）。
> Astra 指摘（`hit_test_check` の skip は PASS ではない）を受け、must / optional を分けて skip 数を出力する形にした。
>
> 故障 10 種を 1 種ずつ注入 → 狙った assert で単独 FAIL:
> handler 無効化 → A／receiver 削除 → A・B／closed focusable → B／aria-expanded 停止 → A／Escape 停止 → A／
> focus return 停止 → A・E／global nav 1 項目 drop → A／opacity:0 のみ → B／正 tabindex → F／overlay pointer 再発 → E
>
> ### regression（cloud・Batch 2 ツリー）
> garage_check 513 / garage_top 291 / garage_list 734 / garage_integrity 610 / mobile_garage_list 804 / launcher 177 /
> detail_contract 51 / entity_actions 36 / footer 4 / web_meaning 1290 / hit_test 367 / shell_interaction 418 — 0 FAIL。
> image_integrity / mobile_feed / mobile_detail / mobile_garage_detail は cloud 環境依存の FAIL 集合が Batch 1 と同一。
> pixel: W は run-to-run 差のみ、M は Home / Browse の閉 Drawer 影漏れが消えた差だけ。⚠️ Mac 実機再走は未実施。
>
> ### 状態
> | | |
> |---|---|
> | Garage Context Rail | Batch 1 で修正済み（W / M / PNF static） |
> | Global Shell M | **本 batch で成立**（共通 Drawer・global nav・focus 契約） |
> | MyRIG 全体の viewport continuity | 🟢 **102 で CLOSE**（R-05 / R-06 / R-07 / Garage filter M / R-09 / F-4 / Garage Owner Detail 境界 すべて解消。残る「広告について」は非 blocker PENDING） |
> | Mac 実機 | 16 本（14 ＋ hit_test ＋ shell_interaction）の再走が未実施 |
>
> ### 次
> **Feed 1121〜1200 の CSS 競合修正（R-07）**: page-local `@media(max-width:1200px){.feed-right{display:none}}` と
> `@media(max-width:1120px){.feed-right{display:block}}` が競合し、右レーンが 1121〜1200 で消えて 1120 以下で再出現する。

> ## 🔴 094: Web Fundamentals Recovery Batch 1 — 093 CLOSE 失効・REOPEN（2026-09-12 / GPT 裁定・Cowork 実装）
>
> モック: `myrig-mockup` **`17f4210`**（**push 待ち**。`origin/main` は `b78a2f4`）。正典: この 094。
>
> ### 失効
> **093 の「Garage M / Web Fundamentals batch CLOSE」は失効。** Cowork の独立反証レビュー
> （`MyRIG_Responsive_CounterEvidence_Audit_20260912`・cloud 34 面 × 95 幅 ＋ Mac 内蔵ブラウザで再現）と、
> Astra（GPT Work）が途中まで独立に再現した R-01 相当・Public 狭幅により、新証拠で REOPEN。
>
> | ID | Sev | 何が起きていたか | 起因 |
> |---|---|---|---|
> | **R-01** | P0 | PC ≤1024px で**閉じたままの透明 `.drawer-overlay`（全画面 fixed・z 65）が本文・レール・カードの pointer 操作をすべて奪う**。Mac 実機 900px で Nav「パーツ」クリック → 遷移せず、1100px → 遷移。Header だけ操作可、キーボードは通る | **Garage 変更起因ではなく既存 Shell 欠陥**（`SoT_app-shell.css` §3.4 が閉状態でも `display:block`、`pointer-events` 未指定）。現行 27 面に影響 |
> | **R-03** | P1 | Garage M の sticky 64px Nav が defer 領域（RECENT ACTIVITY / LATEST LOGS）へ重なる。Chrome では grid item の sticky 拘束が grid area ではなく grid container まで伸びる | 093 の M 契約「Nav sticky」そのもの。W で案A（sticky）を落とした理由と同系統 |
> | **R-04** | P1 | Public Garage の PC Narrow Fallback（≤720）で aside（Profile＋Nav 約 520px）が sticky のまま本文と LATEST LOGS を覆う | 093 の「Public は従来挙動を維持」例外 |
>
> **4,089 PASS では hit-test（操作できるか）を検査していなかった。** 「見える」「DOM にある」「位置が正しい」を
> 「操作できる」と誤認したのが今回最大の失敗。M 帯を assert していたのは 14 本中 WM7（550）だけで、それも幾何だけだった。
>
> ### 是正（Recovery Batch 1 / `17f4210`）
> | 対象 | 内容 |
> |---|---|
> | R-01 | `SoT_app-shell.css` §3.2: 閉状態 `pointer-events:none` / 開状態（`.is-open`）`auto` を契約化。open/close の opacity transition と backdrop click は維持 |
> | R-08 | Home / `preview.html` の drawer-overlay・home-dir drawer・Shell responsive の page-local 複製を撤去（宣言単位で `SoT_app-shell.css` §3.2〜3.4 と完全同義だったことを実体比較で確認）。共有側 §3.4 の drawer 規則は `body` 前置で特異度を 1 段上げ、page-local の `.home-dir.home-dir--framed{position:sticky!important}`（同特異度・後勝ち）に負けないようにした |
> | R-03 | `SoT_garage-page.css` §5: M の Nav を **sticky → static**（GPT 裁定: sticky は利便性であって主要機能ではない。wrapper で守るより static が単純・予測可能・回帰リスク低） |
> | R-04 | 同 §5: Public も PC Narrow Fallback で `static`。**static・1 列を Own / Public 共通契約**に。Public だけ従来 sticky を維持する例外は廃止 |
> | 付随 | `hit_test_check` が拾った既存の page-local 欠陥を最小修正: `myrig-settings-pc-v0.2.5.html` の `.cover-change-btn` が後続 `.profile-lower` に下 2/3 を覆われていた → `z-index:1` |
>
> ### 新契約（Region Behavior Matrix v1.2 / L2）
> | | |
> |---|---|
> | **W / M / PC Narrow Fallback** | Context Rail / Nav は **すべて static**（**M nav sticky 契約は失効**） |
> | **M（721〜1024）** | Profile 横長 ＋ Nav 約 64px（件数はバッジ・ラベル可視）＋ Main 横 ＋ Activity / LATEST LOGS defer。DOM: Profile → Nav → Main → defer。Own / Public shared source — すべて維持 |
> | **PC Narrow Fallback（≤720）** | **static・1 列（Own / Public 共通）**。Main はレールの下、defer は Main の後 |
> | **Overlay 層（Global Shell）** | **閉状態は content の pointer 操作を遮らない**（hit-test で検査）。z-index 等の具体値は正典化しない |
> | **Global Shell M** | 🔴 「hamburger 実装済み」は誤り → **共通 Drawer 未実装**（2026-08-30 残タスク #1）。hamburger が drawer を開くのは Home / Browse 4 面だけ。他の面は `.app-nav`（Browse / Feed / Library）が M で消え、代替導線が無い（R-02・裁定なしの drop 状態） |
>
> ### Gate（新設・更新）
> | 検査 | 内容 | 結果（cloud Chromium） |
> |---|---|---|
> | **`_state/hit_test_check.py`（新設）** | PC 現行 40 面 × {900, 720, 1100} で Header 外の主要操作点（Context Nav / Main primary / filter / 代表リンク）の中心を `elementFromPoint()` が自身または子孫で返すこと。HT0 で page-local `.drawer-overlay` 規則の再発も静的検知 | **修正前ツリー 164 FAIL（R-01 を検知）→ 修正後 367 PASS / 0 FAIL**。selftest（overlay pointer-events:auto / 透明層追加）を 1 種ずつ注入して両方検知 |
> | **`_state/web_meaning_check.py`（更新）** | WM1 static 化、WM3-b/c バッジ・ラベル可視、WM4-b 視覚順、**WM9** M scroll 中の Nav × defer / footer / Header（scroll: 0 / Main 中盤 / defer 先頭 / 末尾）、**WM10** PNF 720 / 640 / 540（static・1 列・defer 後・overflow 0・Own / Public）、**WM11** hit-test | **1290 PASS / 0 FAIL**。selftest は故障 **12 種を 1 種ずつ**注入し全種検知（対応表出力） |
>
> 故障 → 検知 assert（対応表）:
> overlay pointer-events:auto → WM11 ／ M nav sticky → WM1・WM9 ／ Public PNF sticky → WM10・WM11 ／
> M nav が Header 帯へ潜る → WM9・WM2-b ／ defer を Main の前へ視覚移動 → WM4-b ／ PNF 2 列 → WM10・WM6 ／
> W/M nav display:none → WM1・2・3・6・7・8・9・10・11 ／ badge / label drop → WM3-b・WM3-c ／
> rail 幅 → WM1・WM2-a ／ defer drop → WM4 ／ Main 全積み → WM2-b ／ W sticky → WM8・WM6
>
> ### 既存 regression（cloud 再実行・変更前 baseline と同一）
> garage_check 513 / garage_top_check 291 / garage_list_check 734 / garage_integrity_check 610 /
> mobile_garage_list_check 804 / launcher_link_check 177 / detail_contract_check 51 / entity_actions_check 36 /
> footer_single_source_check 4 — すべて 0 FAIL。image_integrity / mobile_feed / mobile_detail /
> mobile_garage_detail は cloud 環境依存（外部画像・ローカル server の接続 reset・M9 baseline）の FAIL 集合が
> **変更前後で同一**。⚠️ **Mac 実機での 14 本＋新 Gate の再走は未実施**（下記コマンド）。
> pixel: W（1440 / 1280 / 1100 × light / dark）Garage 10 面 **完全一致**。M / PNF は全要素の小数 px rect が
> 変更前後で 0 差、pixel 差は sticky 層解除に伴うラスタライズ差のみ。
>
> ### NOW / PENDING
> 🔴 **Garage M / Web Fundamentals は Recovery Batch 1 が PASS しても再 CLOSE しない**（Global Shell が M で閉じていない）。
>
> | 項目 | 状態 |
> |---|---|
> | **Recovery Batch 2 — Global Shell M / Common Drawer（R-02）** | **次**。hamburger 死ボタン・`.app-nav`（Browse / Feed / Library）の M drop を共通 Drawer で閉じる。Feed M の「発見 Drawer」と同じ器 |
> | Mac 実機で 14 本 ＋ `hit_test_check` を再走 | 未実施（cloud は font 差あり） |
> | Library 一覧 3 面の 721〜980 未所有帯（R-05） | 未着手（Search filter と同じ部品。Feed M より先が安い） |
> | Detail M（Garage Detail を含めるか裁定要）／ Feed M（1121〜1200 の右レーン非単調 drop = R-07 を含む）／ Garage filter M | 裁定済み・未実装（093 のまま） |
> | Header ≤538px overflow（R-09）／ page-role-matrix の Breakpoint 行（R-10・本 094 で参照先だけ整合） | 未着手 |
>
> ⛔ 093 の GT13 / G20 / P1（DOM 順）/ P2（現在地色）の裁定は生きている。失効したのは **CLOSE 判定と M nav sticky 契約**だけ。

> ## 🟡 093: Garage M / Web Fundamentals batch CLOSE（2026-09-12 / イタヤ裁定）— **🔴 094 で CLOSE 失効・REOPEN**（R-01 / R-03 / R-04。M nav sticky 契約は失効。GT13 / G20 / P1 / P2 は有効）
>
> モック: `myrig-mockup` **`b78a2f4`（push 済み・`origin/main` と同期）**。正典: この 093。
>
> 721〜1024px（M 帯）に**持ち主がいなかった**問題への対応。Web Fundamentals Audit Phase 1 で
> 見つけた「PC / Mobile の 2 点でしか QA しておらず、連続 viewport を Gate に持っていない」
> という根本原因のうち、Garage 系を最初のレーンとして閉じた。
>
> ### commit
> | SHA | 内容 |
> |---|---|
> | `786777e` | responsive 本体（共有 CSS 3 ＋ HTML 10 ＋ `garage_check.py` G20 ＋ `web_meaning_check.py` 新設） |
> | `6e1d83c` | cleanup（`Claude outputs/` を active tree から除去・`.gitignore` へ追加） |
> | `b78a2f4` | GT13 を P1 契約へ追従 ＋ 検査結果 JSON を実測値へ |
>
> ### 確定契約（Region Behavior Matrix v1.1 / **L2 = `docs/ui/region-behavior-matrix-v1.md`**）
> | | |
> |---|---|
> | **W** | Context Rail / Nav は **`static`** |
> | **M（721〜1024）** | Nav は **約 64px のアイコンレール・`sticky`**。Profile は横長カード。Activity / LATEST LOGS は本文の下へ **defer** |
> | **PC Narrow Fallback（PC HTML ≤720）** | **`static`**・1 列 |
> | **DOM / keyboard order** | **Profile → Nav → Main → defer** |
> | **Activity / LATEST LOGS** | ⛔ **drop しない。defer**（消さずに位置だけ後ろへ送る） |
> | **W の defer** | CSS Grid で**左列の Nav の下へ視覚復帰**させる |
> | **C** | **専用 Mobile 面だけの契約。幅だけで C にしない** |
>
> 🔴 **PC HTML を 720px 以下へ縮めた状態は C ではない**（= PC Narrow Fallback）。
> BottomNav にもならず、C の契約を実装する帯でもない。1 列に積むだけの保険。
> ⛔ 「PC の HTML も 720px で BottomNav になるはず」と読まない。
>
> ### 🔴 GT13 の旧契約は失効
> 旧: 「左レーン内が **Profile → Nav → RECENT ACTIVITY**」（2026-09-09 裁定）。
>
> **失効の理由 — CSS Grid の視覚並び替えだけでは keyboard / DOM 順は変わらない。**
> RECENT ACTIVITY を aside の中に置いたまま `grid-row` で本文の下へ送っても、Tab は
> **Nav → Activity → Main** と回り、視覚順と操作順が逆転する（WCAG 2.4.3 / 1.3.2）。
> 外部独立監査（GPT Work / Astra・Mac 実機 160 条件）が検出し、Cowork 側でも再現した。
>
> 現契約: Context Rail の中は Profile → Nav。Activity は消えず **main の後方の defer**。
> W では CSS Grid で左列の Nav の下へ視覚復帰。
> ⛔ **緩めた置き換えではない**: P1 以前の DOM へ戻すと GT13 は 8 FAIL する（新設 3 件が落ちる）。
>
> ### 検査（Mac 実機）
> | | |
> |---|---|
> | 全 14 regression | **4,089 PASS / 0 FAIL** |
> | `garage_check` | **513 PASS / 0 FAIL**（G20 を 3 → 11 へ是正後） |
> | `garage_top_check` | **291 PASS / 0 FAIL**（GT13 追従後） |
> | `web_meaning_check`（**新設 / WM7 Viewport Continuum**） | **550 PASS / 0 FAIL**・故障注入 5 種で 290 FAIL |
>
> `_state/web_meaning_check.py` は「作ったものが変わっていないか」ではなく
> **「Web として成立しているか」**を見る初の検査。既存 13 本は W と PC Narrow Fallback しか
> assert しておらず、M 帯の @media を戻されても検知できなかった。
>
> ### G20 の是正（3 → 11）
> 091 で Launcher へ足した Public Garage の確認導線が検査へ反映されておらず、
> **`e71a700` でも FAIL していた**（本 batch の回帰ではない）。実体から内訳を列挙して突合:
> Top 5 本（ライト / ダーク ＝ 091、未ログイン閲覧 / 公開0件 / 検索のユーザー結果 ＝ 086）
> ＋ 一覧 3 面 × 2 本（091）＝ **11 本**。⛔ 数字だけ合わせず、内訳ごと検査へ書いた。
>
> ### Shared Source
> W / M / PC Narrow Fallback の**物理正本は `SoT_garage-page.css` §5 の 1 本だけ**。
> `SoT_garage-sidebar.css` の Responsive 節は撤去、`SoT_public-garage-sidebar.css` へ複製しない。
> Own / Public の差は data・variant のみ（数 = `.g-stats` / `.gs-profile-stats`、
> 操作 = `.gs-profile-actions` / `.gs-visitor-actions` の 2 か所）。
>
> ### NOW
> 🟢 **Garage M / Web Fundamentals batch は CLOSE。** 見た目の再設計として再オープンしない。
> 次は **Region Behavior Matrix の残り**を別 batch で:
>
> | 領域 | 状態 |
> |---|---|
> | **Feed M** | 裁定済み・未実装。左 = Header 投稿導線と `.feed-type-chips` へ merge／右 = 「発見」Drawer・Feed 内広告・Global Footer へ merge。⛔ 3 タブへ周辺情報を混ぜない |
> | **Detail M** | 裁定済み・未実装。Gallery → Builder＋Actions の compact deck → Main。残りは Main の該当節へ merge |
> | **Garage filter M** | 裁定済み・未実装。Toolbar の Filter から一覧本文の**直前**に折りたたみ panel（初期 closed） |
>
> ⛔ これらの実装のついでに Garage 行（CLOSE 済み）を開け直さない。

> ## 🟢 092: Garage制作レーン全体CLOSE — Launcherを card--done へ同期（2026-09-11 / イタヤ裁定）
>
> モック: `myrig-mockup` HEAD `e71a700`（push待ち。`origin/main` は `7d7a893`）。
> 正典: この 092（push待ち）。091 の commit（ai-context）は push 待ちのまま。
>
> ### 裁定
> 「Garage系は制作・監査ともCLOSE済みなので、Launcherも最終状態へ揃えてほしい」。
> `card--review`（黄・今回更新＝レビュー待ち）は091で仮に付けたものであり、
> CLOSE済みのGarage制作レーンには残さない。`card--done`（緑・確定）へ最終昇格した。
>
> ### 対象（計12面。href / data-mo / data-pc / altrow等の確認導線は変更なし）
> - My Garage 6面: garage / garage-rigs / garage-parts / garage-logs /
>   garage-favorites / garage-pins
> - Owner Manage 2面: garage-rig-detail / garage-parts-detail
> - Public Garage 4面: user-garage / user-garage-rigs / user-garage-parts /
>   user-garage-logs
>
> 通常Detail 3面（RIG Detail / PARTS Detail / LOG Detail）は既存の `card--done`
> のまま維持（091のPENDINGとして残っていた「今回更新へ一時的に上げるか」は、
> イタヤ裁定により**据え置き**で決着）。
>
> ### 構造確認
> 編集前後で `<a>` 98/98・`<div>` 52/52・`<details>` 21/21 が一致（`class`属性と
> `group__wip` の手書きラベルのみの変更）。`launcher_link_check.py` はこの環境が
> playwright未導入のため実行不能（Mac実Terminalでの実行が必要）。
>
> ### NOW
> 🟢 **Garage制作レーン（My Garage / Owner Manage / Public Garage）全体CLOSE。**
> Launcher表示も実体に同期済み。次は Web Fundamentals Audit。
>
> ### 要 push
> ```
> myrig-mockup      e71a700   （fast-forward 可）
> myrig-ai-context  092       （fast-forward 可）
> ```
> ⛔ force push 禁止。

> ## 🟡 091: Launcher是正 — Public Garage 4面の card--plain 放置を修正（2026-09-11）
>
> モック: `myrig-mockup` HEAD `a9e6841`（push待ち。`origin/main` は `7d7a893`）。
> 正典: この 091（push待ち）。090 の commit（ai-context）は push 待ちのまま。
>
> ### 発見経緯
> イタヤが実画面（Launcher `index.html`）でPublic Garage CLOSEを最終確認した際、
> My Garage / Owner Manage には黄色「今回更新」バッジが付くのに、
> Public Garage 4面にだけ付いていない、RIG一覧・パーツ一覧・ログ一覧には
> ライト/ダーク確認導線すら無いことを発見。「ランチャーが変わってない」と指摘。
>
> ### 原因
> Launcher のバッジ・件数はすべて `card--review`（今回更新）/ `card--plain`
> （今回は触っていない）/ `card--done`（確定）のクラスから JS が自動生成する
> （手書きの `group__wip` テキストは実行時に上書きされる）。
> Public Garage 4面は 2026-09-08 時点で `card--plain` を付けたまま、086〜090の
> 実体変更（@trail_builder fixture分離・Garage骨格統一・D-PUBDETAIL・13検査CLOSE）
> の間ずっと放置されていた。Launcher自体は今回のPublic Garage作業の対象に
> 一度も入っていなかったため気づけなかった。
>
> ### 対応
> - 4面（Top / RIG一覧 / パーツ一覧 / ログ一覧）を `card--plain` → `card--review` へ変更
> - RIG一覧・パーツ一覧・ログ一覧の3面に `cardwrap` + `altrow`（ライト/ダーク）を追加
>   （旧バージョンファイルが存在しないため「旧を見る」導線は追加せず。
>   guest/empty のデモクエリもこの3面には実装が無いため追加していない）
> - Top（`user-garage.html`）の altrow は既存の 未ログイン/公開0件/検索経由 に
>   ライト/ダークを追加
> - `href` / `data-mo` / `data-pc` は変更していない（リンク先の契約は不変）
>
> ### 影響範囲の切り分け
> これはLauncherの表示（レビュー用メタ情報）のみの是正であり、
> 090で確定した実体（13検査 3525 PASS / 0 FAIL、実機390px確認）には影響しない。
> **Public Garage CLOSEの判定自体は維持**。
>
> ### PENDING（未確認・要判断）
> - `rig-detail.html` / `parts-detail.html` / `log-detail.html` の3枚は
>   D-PUBDETAILで内容を大きく変えたが、Launcher上は既存の `card--done`（確定）
>   のまま。「今回更新」に一時的に上げるべきかはイタヤ判断待ち
>   （どちらも既に別サイクルでCLOSE済みの面なので、据え置きでも矛盾はない）。
>
> ### NOW
> Launcher表示の是正のみ。push待ち。次は Web Fundamentals Audit。
>
> ### 要 push
> ```
> myrig-mockup      a9e6841   （fast-forward 可）
> myrig-ai-context  091       （fast-forward 可）
> ```
> ⛔ force push 禁止。

> ## 🟢 090: Public Garage CLOSE（2026-09-11 / イタヤ裁定）
>
> モック: `myrig-mockup` HEAD `fc0df27`（push待ち）。
> 正典: この 090（push待ち）。089 の `7289a80` は push 済み。
>
> ### CLOSE判定
> 086〜089 で積み上げた是正・確認がすべて揃い、イタヤが Public Garage の CLOSE を裁定した。
>
> - 086: 人物・公開範囲・route を Own から分離（`@trail_builder` fixture／P3 auth）
> - 087: Own と同じ Garage 骨格へ揃える（カード寸法・grid・breakpoint 一致／RIG status／D-PUBDETAIL着手）
> - 088: 最終レビュー2点（ローカルナビ1行固定／Launcher の Owner Manage 改称）
> - 089: M9（Public Detail baseline）を旧版と全文diffし、D-PUBDETAIL起因以外の差分0件を確認・確定
> - 090（今回）: Mac実Terminalで**残り12検査を本走** — 3393 PASS / 0 FAIL
>   （13検査合計 **3525 PASS / 0 FAIL**）。**実機390pxのローカルナビ折り返しもイタヤ確認済み**。
>
> ### 検査（Mac実Terminal本走・2026-09-11）
> | 検査 | PASS | FAIL |
> |---|---|---|
> | `garage_list_check` | 734 | 0 |
> | `mobile_garage_list_check` | 804 | 0 |
> | `garage_check` | 505 | 0 |
> | `garage_top_check` | 287 | 0 |
> | `garage_integrity_check` | 610 | 0 |
> | `launcher_link_check` | 177 | 0 |
> | `image_integrity_check` | 63 | 0 |
> | `mobile_feed_check` | 63 | 0 |
> | `mobile_detail_check` | 59 | 0 |
> | `detail_contract_check` | 51 | 0（WARN 0） |
> | `entity_actions_check` | 36 | 0 |
> | `footer_single_source_check` | 4 | 0 |
> | `mobile_garage_detail_check`（089でMac実行済み） | 132 | 0 |
> | **合計 13本** | **3525** | **0** |
>
> 実行に伴い `_state/garage_check_result.json`（504→505）／
> `_state/mobile_garage_list_result.json`（789→804）の記録カウンタを実測値へ更新（`fc0df27`）。
> 検査項目数の増加（G21・MG16 等）に記録が追いついていなかっただけで、fail は 0 のまま変化なし。
>
> ### PENDING（CLOSE後へ持ち越し・blockerではない）
> - **Web Fundamentals Audit**: 500〜1280px の responsive を MyRIG 全体で横断（本題）
> - Public fixture に残存する廃止済みLOG種別「セットアップ」のcleanup（M9・D-PUBDETAILとは無関係）
> - D-PUBNUM: Follow 49 / Followers 312 が Own と同値（誤りの根拠も代替値も無いため現状維持）
>
> ### NOW
> 🟢 **Public Garage CLOSE。** 次のスレッドは Web Fundamentals Audit から開始する。
>
> ### 要 push
> ```
> myrig-mockup      fc0df27   （fast-forward 可）
> myrig-ai-context  090       （fast-forward 可）
> ```
> ⛔ force push 禁止。

> ## 🟡 089: M9 — Public Detail baseline を承認済み回帰基準として確定（2026-09-11）
>
> モック: `myrig-mockup` HEAD `a52f705`（**未 push**。GitHubへのegressがこの環境で403 Forbidden。
> `origin/main` は `ecff2a0`）。
> 正典: この 089（未 push）。088 の `a90397a` は push 済み。
>
> ### 経緯
> 918d6e7（087）で D-PUBDETAIL 適用時に `_state/mobile_garage_detail_public_baseline.json` を
> 意図的に削除（Public Detail の中身を @crawler_junkie → @trail_builder へ変えたため）。
> `mobile_garage_detail_check.py` の M9 は baseline 不在時に自動再生成 → 自動 PASS する仕様なので、
> 「再生成されたら即正常扱い」にはせず、旧baseline（削除前コミットの親）と全文diffして確認した。
>
> ### 確認内容
> - 3面（rig-detail / parts-detail / log-detail）とも、差分は `data-widget="builder"` 区間
>   （アバター・氏名・@handle・rig/parts/log件数・ガレージ導線・M11 LOG・M12 使用パーツ・
>   R7 このビルダーの他のRIG・コメント欄owner表示）に限定。
> - 区間の前後（ヘッダー〜本体スペック、RELATED棚 `data-treat="S1"` 以降）は旧新で完全一致。
> - **D-PUBDETAIL起因以外の差分は0件。** イタヤ確認済み。
>
> baselineを `myrig-mockup` へ commit し、次回実行時からの回帰比較基準として確定した
> （未追跡のままだと別環境で自動再生成され、回帰検査として機能しないため）。
>
> ### 既知の別件（M9とは無関係・CLOSE blockerでもない）
> 「セットアップ」はスキーマ v1.2 で廃止済みの LOG 種別（`myrig-feed-v3.html` の注記参照）だが、
> PC Public LOG ページ（`myrig-public-garage-logs-v3.html`）に既存の値として残存しており、
> @trail_builder のPublic fixtureへそのまま継承されていた。M9の異常でもD-PUBDETAIL起因でもない。
> 後続cleanupのPENDINGとして別管理する。
>
> ### PENDING
> - 新 `mobile_garage_detail_public_baseline.json` の push（この環境のegress 403待ち）
> - Public fixture の「セットアップ」廃止値cleanup（Public Garage CLOSEとは分離）
>
> ### NOW
> M9はほぼ片付いた。残るのは Mac実機での他12検査、および実機390px（ローカルナビ折り返し）の
> 最終確認。通ったら Public Garage CLOSE を判定 → その後 Web Fundamentals Audit。
>
> ### 要 push
> ```
> myrig-mockup      a52f705   （fast-forward 可。現在 403 でリトライ待ち）
> myrig-ai-context  089       （fast-forward 可。現在 403 でリトライ待ち）
> ```
> ⛔ force push 禁止。

> ## 🟡 088: 最終レビューの2点 — ナビの1行固定と Launcher の分類（2026-09-11）
>
> モック: `myrig-mockup` HEAD `ecff2a0`（**未 push**。`origin/main` は `918d6e7`）。
> 正典: この 088（未 push）。087 の `004a0c2` は push 済み。
>
> 🔴 **CLOSE ではない。** 087 の実画面レビューで「大きな構造問題なし・PC / Mobile とも
> 087 の方向で OK」との評価。CLOSE 前の残り2点をここで閉じた。
>
> ### 1. Mobile Garage ローカルナビ（Shared Garage UI の不具合修正）
> 390px の実機で「概要」「パーツ」「ログ」、および「絞り込み」が**2行に割れていた**。
> Public 固有ではなく**共有部品**なので、Own（`garage-*.html`）にも同じ修正が効く。
> ⛔ Own の再設計はしない。共有部品の不具合修正として同時反映した。
>
> ```
> css/mobile-garage.css       .g2-nav__item / .g2-nav__n   → white-space: nowrap
> css/mobile-garage-list.css  .gl-filter-btn               → white-space: nowrap
>                             .gl-filter-btn svg           → flex: 0 0 14px
>                             .gl-toolbar                  → flex-wrap: nowrap
>                             .gl-sorts                    → min-width: 0
> ```
> 器は横スクロールするので、**縮めず・折らず・はみ出したぶんはスクロールで見せる**形に固定。
> ⛔ 文字を小さくして詰め込まない。⛔ Public 専用の規則を別に作らない。
>
> ⚠️ 🔴 **この環境では症状を再現できなかった。**
> 外部フォント（Noto Sans JP / Barlow Condensed / Inter）が読めず実機より字幅が狭い。
> `letter-spacing` を **+5px** まで足して広い書体を再現しても、`flex:0 0 auto` ＋
> `overflow-x:auto` のため折り返しは起きなかった。
> **したがって今回の修正は「折れない契約を明示する」防御**であって、
> イタヤが見た折り返しの**引き金そのものを特定して直したものではない**。
> 実機でまだ折れるなら、ブラウザ名とスクリーンショットが要る。
>
> ### 2. Launcher の分類
> 「**Owner Detail**」→「**Owner Manage**」（`data-g` も `garage-owner-manage` へ）。
>
> | | 置き場所 |
> |---|---|
> | RIG Detail `/rig/[rigId]` / PARTS Detail `/parts/[partId]` / LOG Detail `/log/[logId]` | **「詳細ページ」グループのまま**。Browse / Search / Feed / Public Garage など**複数入口から到達する通常のユーザーコンテンツ Detail**なので Public Garage 配下へ移さない |
> | RIG管理（オーナー）`/garage/rigs/[id]` / パーツ管理（オーナー）`/garage/parts/[id]` | **Owner Manage**（Garage 配下の管理ページ） |
>
> カード名も「RIG詳細（オーナー編集）」→「**RIG管理（オーナー）**」、
> 「パーツ詳細（オーナー編集）」→「**パーツ管理（オーナー）**」。
> ⛔ 三種類を揃えるためだけに **Own LOG 管理詳細を新設しない**
> （`/garage/logs/[id]` が無いのは現行仕様どおり。画面カードは 2 枚のまま）。
>
> ### 検査
> | 検査 | 087 | 088 |
> |---|---|---|
> | `garage_integrity_check` | 486 | **610 PASS / 0 FAIL** |
> | `garage_check` | 505 | 505（グループ名の変更に追随） |
> | 他11本 | — | 増減なし・新規 FAIL 0 |
>
> **GI5 を 20 → 144 項目へ拡張。** ラベルの実測行数（素 ＋ `letter-spacing +2.5px` の
> 広い書体再現）に加えて、**computed `white-space` が `nowrap` である契約**も検査する。
> 🔴 実測だけでは、この環境の狭い書体で「たまたま折れていない」としか言えない。
> Public 一覧3面も同じ検査対象へ入れた（**Own だけ直して満足しない**）。
> 故障注入（`white-space` を外す）で GI5 が FAIL することを確認済み。
>
> ### 実画面監査 — 360 / 390 / 1280 / 1440 × Light / Dark（44組）
> **087 と完全同一。** はみ出し 0 / 重なり 0 / pageerror 0 / 新規の低CR 0。
>
> ### NOW
> 🔴 **イタヤの最終実画面確認待ち。** とくに 1 の折り返しが実機で直っているか。
> 通ったら Public Garage CLOSE を判定 → その後 **Web Fundamentals Audit**
> （500〜1280px の responsive を MyRIG 全体で横断）。
>
> ### 要 push
> ```
> myrig-mockup      ecff2a0   （fast-forward 可）
> myrig-ai-context  088       （fast-forward 可）
> ```
> ⛔ force push 禁止。

> ## 🟡 087: Public Garage — Own と同じ Garage 骨格へ揃える（2026-09-11 / イタヤ実画面レビュー）
>
> モック: `myrig-mockup` HEAD `918d6e7`（**未 push**。`origin/main` は `05c0c09`）。
> 正典: この 087（未 push）。086 の `2314b78` は push 済み。
>
> 🔴 **CLOSE ではない。** イタヤの最終実画面確認待ち。
>
> ### 基本概念（2026-09-11 イタヤ裁定・単純化）
> **Public Garage は「Own Garage とは別の UI 体系」ではない。**
> 未ログインの人、またはログイン済みユーザーが他人の Garage を見に来たときの
> **Garage 公開ビュー**（`page-role-matrix`: 「Own Garage との表示分岐。別ページではなくビュー切替」）。
>
> > 同じ Garage の骨格・カード・サイズ・レスポンシブ文法を使い、
> > **viewer context によって見せる情報と操作だけを変える**。
>
> ⛔ 086 の @trail_builder fixture 分離 / private・owner data 除去 / Public route 分離 /
> P3 auth / Shared Source 化は**維持**する。戻す話ではない。
>
> ### 1. カード寸法・grid の是正（実測 → 一致）
> | | 着手前 Own | 着手前 Public |
> |---|---|---|
> | ページ器 | `.page` `min(1400px)` | `.pg-wrap` `min(1240px)` → 1440 で本文 **828 / 668** |
> | RIG grid | `.grid-md--top-rig` `auto-fit minmax(280px)` gap20 | `.grid-rig` `auto-fill minmax(200px)` gap14 |
> | RIG 1280px | **2列・カード 328px** | **3列・カード 213px** |
> | PARTS | gap 20 | gap 14 |
> | LOG | `.list-stack` | `.log-list{gap:0}` |
>
> 是正後は RIG / PARTS / LOG とも `pageW` / `pageCols` / `twoCols` / `contentW` /
> `filterW` / `itemsCols` / `gap` / カード幅 / 列数が **1280・1440 で完全一致**。
> 残る差は**サンプル件数だけ**（Own 7・8・12 / Public 5・8・6 ＝ データの違い）。
>
> 撤去した page-local: `.pg-outer` / `.pg-wrap` / `.pg-main` / `.grid-rig` / `.grid-parts` /
> `.log-list` / `.section*` の上書き / reset の重複。器は `SoT_garage-page.css` ＋
> `SoT_garage-list.css` へ。**PC Public の page-local CSS は `button` / `svg` の2規則だけ**になった。
> ⛔ reset にあった無条件 `a:hover{text-decoration:underline}` も撤去。正本は 2026-09-10 に
> `@media(hover:hover)` で囲ってあり、面へ書き戻すとタッチ端末の :hover 張り付きが戻る。
>
> ### 2. breakpoint（Public 専用 responsive は作らない）
> 900〜1500px の **13幅 × 3対**で実測し、折り返しの挙動が全幅で一致。
> `1100px` = filter が本文下 / `1024px` = 左レーンが下。どちらも共有側の既存規則。
> ⚠️ 500〜1280px 全域の responsive 設計は **Public CLOSE 後の Web Fundamentals Audit**
> で MyRIG 全体を横断して扱う（今回は広げない）。
>
> ### 3. RIG status を Public でも出す
> `SoT_card-components.js` に **`context="public"`** を追加し、owner と同じ
> 「中立チップ + 8px status ドット」を描く。filter に status があるのに
> カードに出ていない状態を解消した（出さないなら filter からも外す、という判断の前者を採用）。
> ⛔ `mixed` / `model` / `normal` / `usage` の分岐は触らない
> （081 の「`.gs` は owner 分岐でしか描かれないので非 Owner へ波及しない」安全性は維持）。
>
> 🔴 **副次的に見つかった欠陥**: Public RIG カードは `context` 無し ＝ `mixed` に落ちており、
> `title` が **空**（`model-name` 未指定）で **RIG 名がカードに出ていなかった**。これも解消。
>
> ### 4. D-PUBDETAIL 解消
> `rig-detail` / `parts-detail` / `log-detail` を **@trail_builder の公開 Detail** にした。
> Public 一覧から降りても人物・content context が切れない。
> 対象: `md-builder`（名前・@・5/198/67・ガレージ導線）/ M11 LOG 3件 / M12 使用パーツ 4点 /
> R7 このビルダーの他のRIG 4台 / コメントの owner 返信。
> ⛔ Owner Detail の再デザインはしない。Detail 構造・共有部品はそのまま。
> ⛔ RELATED 棚（S1〜S7）は「他人 / カテゴリ全体」なので対象外。
> `js/mobile-detail.js` のアバター判定に `svg` を追加（ローカル svg が頭文字フォールバックへ
> 落ち、ダークで CR 3.08 になっていた）。
> **M9 baseline は意図的変更のため削除。次回実行時に自動再生成される**（検査に明記あり）。
>
> ### 5. Public 固有として維持するもの
> | Own | Public |
> |---|---|
> | 公開ページをプレビュー / プロフィール編集 / お気に入り / ピン留め / 設定 / PIT TABLE / owner analytics・RECENT ACTIVITY | Follow / … menu / Block・Report / LATEST LOGS / Garage・RIG・PARTS・LOG のみ |
>
> 「Public を Own そっくりに戻す」のではなく、**管理者専用部分だけを viewer 用へ差し替える**。
>
> ### 検査
> | 検査 | 086 | 087 |
> |---|---|---|
> | `garage_integrity_check` | 395 | **486**（**GI14 view parity** 66項目 新設） |
> | 他12本 | — | 増減なし・新規 FAIL 0 |
>
> ```
> GI14 view parity  同じ viewport で一覧の器・カード寸法・grid・gap・列数・
>                   breakpoint が Own と一致する（データ件数は対象外）
> ```
> `--selftest` の故障注入で **110 FAIL** を検出（GI14 は 24/66 ＝ 新設分も生きている）。
>
> ⚠️ 検査は 086 と同じく cloud で実行し、着手前 `0321160` の baseline との差分で判定。
> 外部ホストが塞がれる 4本（`image_integrity` 45/18・`mobile_feed` 60/3・
> `mobile_detail` 55/4）は baseline と同値。`mobile_garage_detail` は M9 baseline を
> 削除したため 132/0。**Mac 側で13本流し直すこと**（初回に M9 baseline が作られる）。
>
> ### 実画面監査 — 360 / 390 / 1280 / 1440 × Light / Dark（44組・公開 Detail 3面を追加）
> はみ出し **0** / 重なり **0** / pageerror **0** / **新規の低CR 0**。
> Public Garage 8面は**低CR 0**。PC 左レーンの3件（`MAINTENANCE` 2.57 / `RUN` 2.28 /
> `♥` 3.58 dark）と公開 Detail の既存分は**着手前と同一**で、**D2（H2-a）の対象**。
>
> ### NOW
> 🔴 **イタヤの最終実画面確認待ち。** ここを通してから Public Garage CLOSE を判定する。
> その後に **Web Fundamentals Audit**（500〜1280px の responsive を MyRIG 全体で横断）。
>
> | # | 項目 | 状態 |
> |---|---|---|
> | D-PUBNUM | フォロー 49 / フォロワー 312 が Own と同値（PC の原文ママ）。誤りの根拠も代替値も無いので数字を作らず現状維持 | **CLOSE blocker にしない** |
> | D2 / D3 | H2-a / H2-b。Public だけ先行させない | 未着手 |
> | D-IMG / D-PARTS / D-TRAP / D-SHELL / D4〜D8 / D9 / D11 / D13 | 085 のまま | 継続 |
> | D-PUBDETAIL | **087 で解消** | 完了 |
>
> ### 要 push
> ```
> myrig-mockup      918d6e7   （fast-forward 可）
> myrig-ai-context  087       （fast-forward 可）
> ```
> ⛔ force push 禁止。

> ## 🟡 086: Public Garage — 人物・公開範囲・route を Own から分離（2026-09-11 / イタヤ裁定）
>
> モック: `myrig-mockup` HEAD `05c0c09`（**未 push**。`origin/main` は `313cd0c`。`0321160` と2本）。
> 正典: この 086（**未 push**。`origin/main` は `9fae794`）。
>
> 🔴 **これは CLOSE ではない。** 構造・データ・route・検査までで、実画面レビューは未了。
>
> ### DECISION（2026-09-11 イタヤ裁定）
> | # | 裁定 |
> |---|---|
> | 1 | **Public Garage の identity は `@trail_builder`。** Own（`@crawler_junkie`）と fixture ごと分ける。`fav / pin / PIT / owner reactions` は Public Source へ持ち込まない。⛔ 分けるのは**人物・公開範囲・route**であって、カード部品ではない |
> | 2 | **D-PERIOD = rolling period。** ラベルは「直近7日 / 直近1か月 / 直近3か月 / 直近6か月」。Production は**現在日**、Mock は fixture / 面が明示した **`referenceDate`**。PC の手書き `data-period` は廃止し、`data-date` だけを根拠にする |
> | 3 | **PC Public RIG / PARTS / LOG に共有 filter / sort を導入。** ⛔ **無限スクロールは入れない**（PC→Mobile 正典でも限定例外）。共有するのは filter / sort / count semantics / aria state / empty state まで。Own 管理UIへ寄せない |
> | 4 | **PC の `--cat-*` v8 rollout は今回やらない。** Public だけ例外化すると「どこまで v8 済みか」の棚卸しが再び要る。**H2-a / H2-b（D2 / D3）レーンに残す** |
> | 5 | **公開ページは未ログインで閲覧できる。** ログインを求めるのは**操作**（フォロー / 報告 / ブロック）だけ ＝ P3 Login Modal。共有は求めない |
>
> ### 着手前の棚卸しで判明していた実測（すべて是正済み）
> - Mobile Public 一覧3面が **Own と同じ fixture** を読み、`@crawler_junkie` の 7 / 312 / 84 を「他人のガレージ」として出していた。PC Public は `@trail_builder` の 5 / 198 / 67 で、**PC と Mobile で人物そのものが違っていた**
> - PC Public 4面の相互リンクが**実装URL**（`/user/trail_builder/rigs`）で、モック内に実体が無かった
> - Mobile Public Top から一覧3面への**前進リンクが 0本**（「すべて見る」は `href="#"`）。往復が閉じていなかった
> - PC Public の filter は**見た目だけ**。`aria-pressed` も無く、選択状態を `--color-accent-fill` で塗っていた（NG-7）
> - `?guest=1` が公開ページで**全面 authwall** を出し、しかも `next` が **Own の `/garage`** を指していた
>
> ### STATE — 実装
> | 面 | 変更 |
> |---|---|
> | `user-garage.html`（Mobile Public Top） | **1059行 → 113行**。page-local style 293 / script 572 → **0 / 0**。描画は `js/mobile-public-garage-top.js`、器は `css/mobile-garage.css` ＋ 差分だけ `css/mobile-public-garage.css` |
> | `user-garage-{rigs,parts,logs}.html` | `data-garage-list-store="MYRIG_PUBLIC_GARAGE"` を宣言。総数 5 / 198 / 67。チップを Public の実データへ |
> | PC Public 4面 | 実装URL → 実体。ロゴ・カード・「もっと見る」の行き先を公開 Detail へ。page-local script **4×16行 → 0**（⋯ は `SoT_public-garage-sidebar.js` へ昇格）。左レーンは `data-active` 以外 **4面完全一致**（drift 0） |
> | PC Public 一覧3面 | `.pg-sub-2col` / `.pg-title-bar` / `.flt-*` を撤去し `SoT_garage-list.css` / `.js` の契約へ。無限スクロールは無し |
>
> **新設（物理正本は1本）**
> ```
> js/mobile-public-garage-data.js      @trail_builder の fixture（RIG 5 / PARTS 8 / LOG 6）
> js/mobile-public-garage-top.js       Public Top の描画
> css/mobile-public-garage.css         Public 固有の差分だけ（cover / 名前＋@ / bio / フォロー行）
> pc/assets/js/SoT_period.js           rolling period の物理正本。Mobile も直読み
> pc/assets/js/SoT_public-garage-sidebar.js  Public 左レーンの ⋯（4面で同一だった16行）
> img/avatar_trail_builder.svg         外部ホストを1件減らすためのローカル資産（pc/img/ にも配置）
> ```
>
> ### STATE — データの是正（抽出時に見つかった PC 内の不一致）
> 🔴 **より具体的な面（一覧面）を正とした。** ラベルが複数箇所で一致することは正しさの証明にならない。
> - LOG の rig 表記が Top と一覧で **3通り**（`RC4WD TF2 Mojave` / `RC4WD TF2 Mojave II` / `Trail Finder 2 MOJAVE II`）→ RIG 一覧の title `TF2 Mojave II Trail Build` へ統一
> - PARTS「SCX10 III Aluminum Skid Plate」の装着 RIG が Top `SCX10 III` / 一覧 `Cliffhanger HD` → 一覧側（`SCX10 III` は @trail_builder の5台に無い）
> - LOG の `likes` / `comments` は **PC Public に存在しない**。⛔ 無い数字をモックへ作らない。よって Public LOG の並び替えに「人気順」を置かない
>
> ### 検査
> | 検査 | 085 | 086 |
> |---|---|---|
> | `garage_integrity_check` | 196 | **395**（GI11 / GI12 / GI13 / GIP3 新設） |
> | `mobile_garage_list_check` | 789 | **804**（store を面ごとに読む形へ） |
> | `garage_check` | 504 | **505** |
> | 他10本 | — | 増減なし・新規 FAIL 0 |
>
> ```
> GI11 owner leak       Public に PIT / お気に入り / ピン留め / 編集・登録 / owner counts が出ない
> GI12 public identity  PC / Mobile の Public 全面が @trail_builder。Own と混線しない。件数も一致
> GI13 public round trip Public Top → 一覧3面 → Public Detail → Public Top。owner route へ1本も入らない
> GIP3 public auth      公開ページは未ログインで閲覧できる。求めるのは操作だけ。共有は求めない
> ```
> `--selftest` の故障注入で **68 FAIL** を検出（新設分も含めて検査が生きていることを確認）。
>
> ⚠️ **検査は cloud コンテナで実行した。** デスクトップ VM に Playwright が無く、
> 網も無いため install できなかった。着手前の HEAD `0321160` で同じ環境の
> baseline を取り、**その baseline との差分**で判定している。
> 外部ホスト（`i.pravatar.cc` / `placehold.co` / Google Fonts）が cloud では塞がれるため、
> `image_integrity_check` 45/18・`mobile_feed_check` 60/3・`mobile_detail_check` 55/4・
> `mobile_garage_detail_check` 132/2 は **baseline と同値**（＝環境要因で、変更由来ではない）。
> イタヤの Mac で 13本を流し直すこと。
>
> ### 実画面監査 — 360 / 390 / 1280 / 1440 × Light / Dark（32組）
> はみ出し **0** / 重なり **0** / pageerror **0** / **新規の低CR 0**。
> `user-garage.html` の低CR 2件（旧・文字アバター CR 3.08）は解消。
> PC 左レーンに残る低CR 3件（`MAINTENANCE` 2.57 / `RUN` 2.28 / `♥` 3.58 dark）は
> **着手前と同一**。カテゴリ色を文字色に使っている分で **D2（H2-a）の対象**。
>
> ### NOW
> 🔴 **次は Public Garage の実画面レビュー**（PC 4面 / Mobile 4面）。ここを通してから CLOSE を判定する。
>
> | # | 項目 | 状態 |
> |---|---|---|
> | D-PUBDETAIL | ✅ **087 で解消**。（以下は当時の記録）**公開 Detail 3面（`rig-detail` / `parts-detail` / `log-detail`）が まだ `@crawler_junkie` の内容**。Public 一覧から降りると人物が変わる。今回は導線だけ中身に合わせて Own 側へ向け直した（GI2 / GI3 / GI8 が中身と遷移先を突き合わせるため）。Detail レーンの契約（`detail_contract` / `mobile_detail` / `mobile_garage_detail` の M9 baseline）にかかるので分離は別バッチ | **未裁定** |
> | D-PUBNUM | `@trail_builder` のフォロー 49 / フォロワー 312 が Own と同値（PC の原文ママ）。数字を作らずに残してある | 未裁定 |
> | D2 / D3 | H2-a / H2-b。Public だけ先行させない（裁定 4） | 未着手 |
> | D-IMG / D-PARTS / D-TRAP / D-SHELL / D4〜D8 / D9 / D11 / D13 | 085 のまま | 継続 |
>
> ### 要 push
> ```
> myrig-mockup      0321160, 05c0c09   （fast-forward 可）
> myrig-ai-context  9fae794 の次（086） （fast-forward 可）
> ```
> ⛔ force push 禁止。

> ## ✅ 085: Mobile Own Garage 6面 CLOSE（2026-09-10 / イタヤ裁定・外部再監査 通過）
>
> モック: `myrig-mockup` `313cd0c`（push 済み）。正典: `6141415`（084・push 済み）。
>
> ### 裁定
> 🔴 **Own Garage は PC 6面・Mobile 6面とも CLOSE。** 見た目の再設計として再オープンしない。
> 外部再監査（簡易）で 084 の残存5点がすべて解消と判定され、新しい重大問題も出なかった。
>
> | 確認項目 | 結果 |
> |---|---|
> | LOG 遷移 | TF2 条件が保持され、詳細・一覧とも 8件 |
> | 駆動系 | PC / Mobile とも 3 items |
> | ESC 画像 | 一覧 / 詳細 / 右欄とも placeholder |
> | 作業台 | 追加・解除・空状態からの復帰を検査で確認 |
> | Mobile の重なり | 検出なし |
> | `garage_integrity_check` | 196 PASS / 0 FAIL |
>
> ⚠️ 再監査は**簡易**。全画面・全条件の再走査と 3097件の再実行は行っていない。
> CLOSE は「この範囲で重大問題が無い」ことの裁定であって、全条件の無欠陥証明ではない。
>
> ### CLOSE の範囲
> ✅ Own Garage 6面（Top / RIG / PARTS / LOG / お気に入り / ピン留め）PC・Mobile
> ✅ 併せて直した Owner RIG Detail / Owner PARTS Detail の**整合項目**（再設計は未着手）
> ⛔ Garage 全体の CLOSE ではない。Public Garage が残る。
>
> ### CLOSE 後も残っている PENDING（再オープンではなく次の作業）
> | # | 項目 | 種別 |
> |---|---|---|
> | D9 / D11 / D13 | 監査 #9 保存一覧の整理操作 / #11 RIG status filter・PARTS 管理情報 / #13 Owner Detail の status 表示・ページ内 navigation | 改善提案 |
> | D-IMG | 実写差し替え 5件（SCX10 III Skid / Warn Winch / LCG Battery Tray / 25T Servo Horn / AXE R2 ESC） | 素材待ち |
> | D-PERIOD | 期間フィルターの意味 | **086 で裁定済み**（rolling period） |
> | D-TRAP | 一覧の focus trap / 開閉を Owner Detail の共有 Shell へ集約 | 実装統合 |
> | D-PARTS | TRX-4 15点 / Cliffhanger 22点 のパーツ数に導出元が無い | データ待ち |
> | D-SHELL | `css/sot/SoT_app-shell.css` が PC 正本の fork（mobile 460行 / PC 666行） | 082 から継続 |
> | D2 / D3 | H2-a / H2-b（`--cat-*` の cross-surface rollout） | 未着手 |
> | D4〜D8 | 081 のまま | 未裁定 |
>
> ### NOW
> 🔴 **次は Public Garage。** 着手前に棚卸し報告で一度止まる（079 / 080 と同じ進め方）。

> ## 🟡 084: 再監査の残存5点を修正 — 導線とデータをつなぐ（2026-09-10）
>
> モック: `myrig-mockup` HEAD `313cd0c`（未 push。`origin/main` は `1db9092`）。
> 正典: 083 も未 push。GitHub main は 082 のまま（再監査レポートの指摘どおり）。
>
> ### 前提の訂正
> 083 に「Integrity 修正 11件を一括実施」と書いたが、外部再監査で **5点の取り残し**が出た。
> 🔴 **「押せるようになった」と「目的を達成できる」は別物。** リンクを直しても
> 遷移先に条件とデータが無ければ、ユーザーの目的（このRIGの続きを読む）は達成できない。
>
> | # | 残っていた問題 | 修正 |
> |---|---|---|
> | 1 | RIG詳細の「全12件を見る」が**全ガレージ84件**へ飛ぶ | 詳細と一覧が同じ LOG データを見るよう fixture を統合し、`?rig=` の初期条件を実装。遷移先は TF2 の 8件だけになる |
> | 2 | 駆動系 3 items / 内訳 4点、写真説明の SAMIX | **PC のラベルが誤り**だったので 3 items へ。写真説明は「【交換前】…現在は Reefs」と履歴を明示 |
> | 3 | RIG詳細の ESC 画像がタイヤのまま | 詳細本文・右欄を是正。代替に使っていた写真は XR10 Justock で **AXE R2 ではない**ため、一覧も含め全面 placeholder へ |
> | 4 | 作業台の「追加」が一覧へ飛ぶだけ | **RIG 選択シート**を実装。追加 ↔ 解除 が閉じ、空にしても戻せる。行き先が別物だった「並べ替える」は撤去 |
> | 5 | PIT の「…」が最終更新時刻に重なる | absolute をやめ、status / 最終更新 / … を同じ flex 行へ。360/390px で重なり 0 |
>
> ### DECISION — 件数はラベルではなく実データから導く（2026-09-10 更新）
> 083 では「2箇所以上で一致する値を採る」とした。再監査の指摘どおり
> 🔴 **値が複数箇所に出ることは、正しい値の証明にはならない。**
> 実データが取れる場合は**実データを唯一の根拠**にする。ラベルの一致は根拠にしない。
>
> 適用結果（fixture の実数へ）:
> - TF2 の LOG: 12 → **8**（fixture の TF2 ログ実数）。PIT / 詳細見出し / md-logs total すべて 8
> - TRX-4 の LOG: 4 → **1** / Cliffhanger の LOG: 7 → **1**
> - ⚠️ TRX-4 15点 / Cliffhanger 22点 の**パーツ数は導出元が無い**（BUILD PARTS MAP は TF2 だけ）。
>   単独出典のまま残している。RIG ごとの構成データが増えたら同じ規則で置き換える
>
> ### DECISION — 画像は「メーカー一致」で確定させない（2026-09-10）
> AXE R2 に当てていた `hobbywing-esc-combo.jpg` は Hobbywing XR10 Justock の商品写真で、
> **該当製品ではない**。カテゴリ・メーカーが合うだけの写真を製品写真として確定させない。
> 実写が用意できないものは placeholder のままにする。
>
> **素材待ち（5件）**: SCX10 III Aluminum Skid / Warn 9.5cti Winch / LCG Battery Tray /
> 25T Metal Servo Horn / **AXE R2 Sensored ESC**
>
> ### 恒久検査（153 → 196 PASS）
> | ID | 追加した検出 |
> |---|---|
> | GI8 | 詳細の LOG 導線が RIG 条件を渡し、**遷移先の件数が詳細と一致する** |
> | GI9 | カード内の要素どうしが重ならない（**ページの横はみ出し検査では出ない種類**） |
> | GI10 | 作業台の 追加 ↔ 解除 が閉じている（空にしても画面から戻せる） |
> | GI2+ | PIT の件数が fixture 実数と一致 / build group の件数が行数と一致 / 過去の caption が現在と区別できる |
> | GI3+ | 詳細の LOG が一覧と同じ集合（drift 検出） |
>
> **検査 13本 合計 3097 PASS / 0 FAIL。** 故障注入 selftest 46 FAIL ＋ 個別注入も確認。
> 横断確認: PC 8面 × 1280/1440 × Light/Dark ＝ 32、Mobile 8面 × 360/390 × Light/Dark ＝ 32。
> はみ出し・画像切れ・pageerror・カード内の重なり いずれも 0。
>
> ### 再監査が「CLOSE 判断とは分けて残す」とした項目
> - **期間フィルターの意味**: Mobile は「サンプル最新日から N 日」、PC は手書きの `data-period`。
>   暦の今週／今月ではない。ラベルを「直近7日」等にするか、モック基準日を固定するかは未裁定
> - 一覧の focus trap / 開閉は Owner Detail の共有 Shell とは別実装のまま（機能は同水準）
> - 値が複数箇所に出ることは製品仕様・互換性の根拠には転用しない
>
> ### NOW
> 🔴 **次は Mobile Own Garage 6面の実画面レビュー（イタヤ）。** 通れば **Mobile 6面 CLOSE**。
> Public Garage へはまだ進まない。
>
> | # | 項目 | 状態 |
> |---|---|---|
> | D1' | Mobile Own Garage 6面の実画面レビュー | 未了（CLOSE 候補） |
> | D9 / D11 / D13 | 監査 #9 / #11 / #13 | PENDING（再監査でも合格条件に含めない） |
> | D-IMG | 5パーツの実写差し替え | 素材待ち |
> | D-PERIOD | 期間フィルターの意味（暦 or 直近N日） | 未裁定 |
> | D2 / D3 | H2-a / H2-b | 未着手 |
> | D4〜D8 | 081 のまま | 未裁定 |

> ## 🟡 083: Own Garage CLOSE前 Integrity 一括修正（外部監査 2026-09-10）
>
> モック: `myrig-mockup` HEAD `11cda8f`（未 push。`origin/main` は `1db9092`）。
>
> ### なぜこの節があるか
> 082 時点で恒久検査は **2795 PASS / 0 FAIL** だった。それでも外部監査（GPT Astra）は
> **v7 面から旧 v6 へ戻るリンク 33本**と**同じ RIG の status の食い違い**を見つけた。
> 🔴 **PASS 数を完成根拠にしない。** 検査していない種類の穴は、何本 PASS しても見つからない。
>
> ### 採用した修正（#1〜#8 / #10 / #12 / #14）
> | # | 内容 |
> |---|---|
> | 1 | **v6 逆流の全廃**。PC v7 8面の通常導線 33本を v7 へ統一。旧版の入口は Launcher の「旧 v6 を見る」だけ |
> | 2 | **状態確認リンクを実装**。`?guest=1` / `?empty=1` / `?pit=0` を別状態として描く。Launcher の確認導線 20→21 |
> | 3 | **データ横断整合**（下記の規則） |
> | 4 | **PARTS 画像**。確認できた4件を実写へ、確認できない4件は placeholder へ退避 |
> | 5 | **Owner RIG Detail の PC / Mobile 整合**。gallery 5→7 / フォトノート 4→6 / md-logs total 14→12 / 使用パーツ 11→10 |
> | 6 | **PC PARTS 1280px の回帰**。カード footer を折返し可に。文字は小さくしない |
> | 7 | **Mobile nav の現在地**を初期表示で可視領域へ |
> | 8 | **絞り込みシートを draft / commit / cancel** へ。focus trap ＋ trigger 復帰 |
> | 10 | **Mobile PIT の管理入口**（既存裁定 Q-4 の実装。語彙は「作業台」） |
> | 12 | **Mobile LOG 検索**（keyword / 期間）を filter sheet へ adapt |
> | 14 | **件数を total / 絞り込み結果 / 読込済み の3つに分離**（PC・Mobile 両方） |
>
> ### DECISION — 数字が食い違ったときの採り方（2026-09-10）
> 🔴 **同じ対象の値は1つ。2箇所以上で一致している値を採り、単独出典で意味の説明が無い値は落とす。**
> 意味の違う集計が実在する場合だけ、ラベルで区別する（「現在装着中」「登録パーツ」等）。
> ⛔ 意味を推測して新しい数字を作らない。
>
> 適用結果:
> - TF2 / TRX-4 の status → **セッティング中**（PIT ＋ Detail MANAGE の2箇所と一致。一覧の「運用中」を是正）
> - Cliffhanger の status → **ビルド中**（PIT と一致。一覧の「保管中」を是正）
> - TF2 のパーツ数 → **10**（BUILD PARTS MAP の内訳 3+4+2+1 と一致。PIT の 38 と 使用パーツの 11 は単独出典のため落とす）
> - TF2 のサーボ → **Reefs RC 360HD**（ビルド詳細 ＋ LOG と一致。RIG説明の SAMIX を是正）
> - 使用パーツの ESC → **AXE R2**（現在構成と一致。QuicRun 1080 は旧 ESC）
>
> ### DECISION — 件数の意味（2026-09-10）
> | 語 | 意味 | 例 |
> |---|---|---|
> | total | そのガレージの登録総数（表示上の総数） | 全 312 点 |
> | loaded | **この面に用意されているサンプル数** | サンプル 8 点 |
> | result | 絞り込み後の該当数 | サンプル 8 点中 1 点が該当 |
>
> ⛔ total に対して loaded しか無いのに「すべて表示しました」を出さない。
> ⛔ 同じカードを総数まで複製して数字を埋めない。
> 面内に残りがあるときだけ「もっと見る（+N件）」を出し、total までの残りは一覧へのリンクで渡す。
>
> ### DECISION — 語彙（2026-09-10）
> **PIT ＝「作業台」。保存2面の「ピン留め」と語を混ぜない。**
> PC の「ピン留めを外す」「ピン留めするRIGを選ぶ」も「作業台から外す / 作業台に置くRIGを選ぶ」へ。
>
> ### 素材が足りていないもの（イタヤ確認事項）
> 次の4パーツは**写真が素材に存在しない**ため placeholder で退避している。
> 実写が用意できれば差し替えるだけで直る。推測で別画像を当てていない。
> - SCX10 III Aluminum Skid / Warn 9.5cti Winch / LCG Battery Tray / 25T Metal Servo Horn
>
> ### 恒久検査
> `_state/garage_integrity_check.py` を新設（**153 PASS / 0 FAIL**・故障注入 selftest で 40 FAIL を確認）。
>
> | ID | 検出するもの |
> |---|---|
> | GI1 | 通常導線から旧版へ戻らない / リンク先が実在する |
> | GI2 | 同じ RIG の status・件数が面をまたいで一致する |
> | GI3 | Mobile 共有 fixture が PC v7 の実体と一致する（画像の対応表を含む） |
> | GI4 | guest / empty / PIT未設定 が別状態として描かれる |
> | GI5 | 現在地タブが初期表示で可視領域に入る |
> | GI6 | draft / commit / cancel / focus 復帰 |
> | GI7 | total / 結果 / 読込済み を混同しない |
>
> 既存検査の期待値も新契約へ更新した（`mobile_garage_list_check` の MG6→MG8 draft/commit、
> `mobile_garage_detail_check` の M9 baseline、`garage_check` の G20 確認導線 20→21、
> `mobile_detail_check` の「もっと見る」契約）。
>
> **検査 13本 合計 3054 PASS / 0 FAIL。**
> 横断確認: PC 8面 × 1280/1440 × Light/Dark ＝ 32、Mobile 8面 × 360/390 × Light/Dark ＝ 32。
> はみ出し・画像切れ・pageerror いずれも 0。
>
> ### NOW
> 🔴 **次は Mobile Own Garage 6面の実画面レビュー（イタヤ）。** 通れば **Mobile 6面 CLOSE**。
> Public Garage へはまだ進まない。
>
> | # | 項目 | 状態 |
> |---|---|---|
> | D1' | Mobile Own Garage 6面の実画面レビュー | 未了（CLOSE 候補） |
> | D9 | **監査 #9** お気に入り / ピン留めの行メニュー・整理操作 | PENDING（今回スコープ外） |
> | D11 | **監査 #11** RIG status filter / PARTS の管理情報強化 | PENDING（今回スコープ外） |
> | D13 | **監査 #13** Owner Detail の status 表示・ページ内 navigation | PENDING（今回スコープ外） |
> | D-IMG | 4パーツの実写差し替え | 素材待ち |
> | D2 | H2-a: カテゴリ色を文字色・線色・操作色から外す | 未着手（081 の棚卸しをそのまま使う） |
> | D3 | H2-b: `--cat-*` の v8 版上げ本体 | 未着手。D2 の後 |
> | D4〜D8 | 081 のまま | 未裁定 |

> ## 🟡 082: Mobile Own Garage を PC v7 の実体で作り直し ＋ PC 6面 CLOSE（2026-09-10）
>
> モック: `myrig-mockup` HEAD `0a581db`（未 push。`origin/main` は `07d604a`）。
>
> ### DECISION（2026-09-10 イタヤ裁定）
> | # | 裁定 | 原文 |
> |---|---|---|
> | 1 | **Own Garage PC 6面は CLOSE**。081 の D1（残り4面の実画面レビュー）は実施せずに閉じる | 「pc 版は一旦これクローズでいいと思います」 |
> | 2 | Mobile は PC の対象面であり、**PC に入っているサムネイル・投稿数をそのまま反映**する | 「pc 版に対しての対象のモバイル版なんで 現在 pc ページに入っているサムネイルとか投稿の数とか含めて 反映してほしい」 |
> | 3 | **同一カードの連打で件数を埋めるのを禁止** | 「同じやつを全部不必要に連打して並べているだけじゃん パーツとかログとか そういうのやめてください」 |
> | 4 | 小分けの確認待ちをやめ、M3→M1→M2→M4 を1回で仕上げる | 「保険をかけすぎて、ちょっといろんな進みが遅すぎる」 |
>
> ### STATE — Mobile Own Garage（Top ＋ 一覧5面）＋ Public 3面
> ✅ **データを PC v7 の card 属性から取る**（`js/mobile-garage-data.js`）。
> RIG 7 / PARTS 8 / LOG 6 / お気に入り 12 / ピン留め 6。面へ複製しない。
> 表示件数（312点 / 84件）は `owner.counts` の**表示上の総数**であって配列長ではない、と
> ファイル先頭に明記した。旧実装は同じカードを件数ぶん複製しており、これが裁定3の対象。
> ✅ **Top の Garage 独自ヘッダー（ロゴ＋色付き登録3本＋ベル）を撤去**し、Home / Feed と同じ
> 共有 Mobile Shell Header に統一。owner identity を作り直し（単独の「編集」ボタンは廃止／
> RIG・PARTS・LOG 件数／フォロー数／公開ページを見る／owner menu）。nav は「概要」始まりの6本。
> ✅ **サブページ 8面の上部を圧縮**。cover ＋ プロフィール全部の再掲をやめ、
> App bar → Garage nav → page title / 並び順・絞り込み → content にした。
> 先頭カードの上端 **454px → 300px 未満**（390×844 のうち 54% が器だった状態を解消）。
> ✅ **面固有 App bar を新造しない**。共有 `.mobile-shell-header` の SubHeader variant
> （`mobile-component-contract-v0.5` §3.9・`__back` + `__pagettl`）を使う。
> 🔴 その過程で**契約違反を1件発見して撤去**した: サブページのヘッダーに置いていたアバターは
> 契約 §3.1「通知アイコン・アバターは置かない」／§3.2「ガレージタブのアバターは
> `.mobile-bottom-nav__avatar`」に反する。しかも実画面では**イニシャル円と img が二重に出て
> 円からはみ出す壊れ方**をしていた（`overflow` 指定なし）。
>
> ### STATE — card 文法（PC list variant との突合で決めた）
> | 面 | 種別 badge | 根拠 |
> |---|---|---|
> | RIG / PARTS / LOG 一覧 | **出さない** | その面に1種しか無く情報を足さない。PC Own Garage と同じ |
> | お気に入り / ピン留め | **v8 badge を出す** | RIG / PARTS / LOG が混在する |
>
> ⛔ badge を消した代わりに色線・色帯を足していない（NG-1 / NG-2 を実測で検出）。
> ✅ 保存2面の badge は **meta 行の先頭へ inline**（PC `.list__meta` と同じ）。写真の上に載せない。
> ✅ meta は PC `.list__meta-text` と同じく**行全体で1回だけ ellipsis**。
> 旧実装は区切りごとに切っており「Hobbywi… · Motor & E… · TF2 Mojav…」と三重に切れていた。
> ✅ PARTS の装着 RIG は meta ではなく独立行（PC `.list__rig` 相当）。
> ✅ LOG 行の大きな青帯を撤去し、サムネ幅を固定して本文の折返しを安定させた。
> ✅ PIT / RIG の status は**中立チップ + status ドット**（PC Owner の `.gs` と同じ思想）。
>
> ### STATE — Shared Source
> | 部品 | 実体 | 面への複製 |
> |---|---|---|
> | データ | `js/mobile-garage-data.js` | 0 |
> | card 文法 | `js/mobile-garage-cards.js` | 0 |
> | Top 描画 | `js/mobile-garage-top.js` | 0 |
> | 一覧の挙動 | `js/mobile-garage-list.js` | 0 |
> | 器 | `css/mobile-garage.css` / `css/mobile-garage-list.css` | 0 |
>
> 9面とも page-local `<style>` 0 / 実行される page-local `<script>` 0。
>
> ### 検査（全て 0 FAIL・合計 2749 PASS）
> | 検査 | 結果 |
> |---|---|
> | `mobile_garage_list_check`（MG1〜MG16） | **639 PASS**（selftest で故障注入 → FAIL 106 を確認） |
> | `garage_list_check` | 734 PASS |
> | `garage_check` | 503 PASS |
> | `garage_top_check` | 287 PASS |
> | `launcher_link_check` | 177 PASS |
> | `mobile_garage_detail_check` | 134 PASS |
> | `image_integrity_check` | 63 PASS |
> | `mobile_feed_check` | 63 PASS |
> | `mobile_detail_check` | 58 PASS |
> | `detail_contract_check` | 51 PASS |
> | `entity_actions_check` | 36 PASS |
> | `footer_single_source_check` | 4 PASS |
>
> 082 で新設した検査（いずれも故障注入で FAIL が出ることを確認済み）:
> - **MG15** サブページ 8面が共有 Shell Header の SubHeader variant を使い、
>   面固有 App bar を新造せず、ヘッダーにアバター・通知を置かない（契約 §3.1 / §3.2 / §3.9）
> - **MG16** 絞り込みチップ ↔ 共有データの不一致検出（markup と data が黙ってズレない）
>
> ### PC 側の同時修正
> `pc/myrig-garage-parts-v7.html` の maker 選択肢ラベル **`Rc4Wd` → `RC4WD`**。
> slug（`rc4wd`）を title case した文字列がそのまま表示に出ていた。value は変えていない。
> モバイル側の同じラベルも合わせた。
>
> ### NOW
> 🔴 **次は Mobile Own Garage 9面の実画面レビュー（イタヤ）。** ここを通してから Public Garage へ進む。
> Light / Dark のスクリーンショットは `_state/shots_mobile/` にコミット済み（14枚 ＋ 絞り込みシート2枚）。
>
> | # | 項目 | 状態 |
> |---|---|---|
> | D1 | ~~PARTS / LOG / お気に入り / ピン留め の実画面レビュー~~ | ✅ 裁定1で CLOSE |
> | D1' | **Mobile Own Garage 9面の実画面レビュー** | 未了 |
> | D2 | H2-a: カテゴリ色を文字色・線色・操作色から外す | 未着手（081 の棚卸しをそのまま使う） |
> | D3 | H2-b: `--cat-*` の v8 版上げ本体 | 未着手。D2 の後 |
> | D4〜D8 | 081 のまま | 未裁定 |
>
> ### 継続 PENDING（082 追加）
> - 🟡 **`css/sot/SoT_app-shell.css` が PC 正本の fork のまま**（mobile 460行 / PC 666行）。
>   同じ `css/sot/` の他3本（tokens-v6 / component-catalog-v6 / category-tokens-v8）は
>   `@import` の再輸出へ寄せたが、app-shell だけは中身が違う（mobile 側は PC にある
>   `.app-auth` / `.app-avatar--btn` / `.app-usermenu` / `.app-theme-opt` 系を持たない部分集合）。
>   42面が読んでいるので、寄せるなら独立バッチで実測してから。
> - 🟡 `css/sot/SoT_footer.css` は PC 正本と **md5 一致の物理コピー**（261行×2）。
>   値は同じなので事故は起きていないが、二重管理であることは変わらない。

> ## 🟡 081: Own Garage PC 6面 実装完了 ＋ 色の職域収束 ＋ 横断機能監査の修正（2026-09-10）
>
> モック: `myrig-mockup` HEAD `6a861e7`。
> 🔴 **これは CLOSE ではない。** 実装と機能不具合の是正が済んだ状態であって、
> 残るデザイン判断項目の実画面確認が終わるまで Garage 全体 CLOSE とは書かない。
>
> ### STATE — 実装
> ✅ **Own Garage PC 6面すべて v7 実装済み**（Garage Top / RIG一覧 / PARTS一覧 / LOG一覧 /
> お気に入り / ピン留め）。Launcher・`compare.html` とも 6面すべて v7 を既定参照にし、
> 確認導線（ライト / ダーク / 旧 v6）は 20本。
> ✅ Garage Top（079）と RIG一覧（080）は**イタヤ実画面レビュー通過済み・再オープンしない**。
> 🟡 **残り4面（PARTS / LOG / お気に入り / ピン留め）は実画面レビュー未了。**
>
> | 面 | 実装 | レビュー |
> |---|---|---|
> | Garage Top | v7 | ✅ CLOSE（079） |
> | RIG一覧 | v7 | ✅ CLOSE（080） |
> | PARTS一覧 / LOG一覧 / お気に入り / ピン留め | v7 | 🟡 未了 |
>
> 保存2面（お気に入り / ピン留め）の表示切替は `pc/assets/css/SoT_garage-saved.css` /
> `pc/assets/js/SoT_garage-saved.js` へ昇格（v6 は2面に同一の block と同じ14行の
> page-local script を複製していた。md5 一致 `bb3a3afe6813`）。
> 面の page-local CSS / JS は 6面とも **0**。収益導線 0。
> 080 PENDING の `.view-toggle` はここで閉じた。
>
> ### STATE — 色の職域収束（Own Garage 6面）
> ✅ 旧 v7 カテゴリ色（緑 / 紫 / 橙）は **v7 6面ともに 0件**（疑似要素・SVG・影・枠まで走査）。
> レビューで見えた緑紫橙は **Favorites v6 / Pins v6**。v6 は `SoT_category-tokens-v8.css` を
> 読まないため `SoT_tokens-v6.css` の旧値へ落ちる。v6 は比較の基準なので是正しない（H2 の対象）。
> ✅ Owner の状態チップ `.gs` を **中立チップ + 8px status ドット**へ収束（079 の PIT 文法）。
> 旧実装は status 色の全面塗り + `color:#fff` で active CR 2.28 / building 3.68、かつ
> `--rig-status-*` を参照せず light 値を直書きしていた（ダークでも light 値）。
> `.gs` は rig / part / gc の3部品とも **owner 分岐でしか描かれない**ので非 Owner へ波及しない。
> ✅ `.cat-badge--*` の文字色を正本（`SoT_component-catalog-v6.css`）で `--cat-*-on` へ是正し、
> browse×3 / feed / search / Detail の **`!important` 打ち消し6箇所（page-local 15行）を全廃**。
> 実測: その5面は `.cat-badge` を markup に1つも持たず、15行は完全な死蔵だった。
> ✅ accent blue に残った責務は **link のみ**。現在地 / 選択 filter / 選択 view / filter 見出しは 0件。
> ✅ 低コントラスト: Garage Top light 27→22 dark 6→1 ／ RIG一覧 light 19→14 dark 6→1。
> 新規の低CR は 0。
>
> ### STATE — 横断機能監査の修正（外部監査 2026-09-10・全件追試して再現を確認）
> | # | 症状 | 原因と是正 |
> |---|---|---|
> | ③ | **Garage 一覧5面の日付ソートが壊れていた**（080 の回帰） | `parseFloat('2026.03.20')` が `2026.03` を返し日が落ちていた。値**全体**が数値のときだけ数値比較へ。LOG 古い順は新しい順と完全一致、保存2面は月だけ昇順・日は降順、RIG/PARTS は各月1件という fixture の偶然で正しく見えていた |
> | ⑤ | 画像切れ5件 | カード部品が属性値を素通しで補間（`src="${img}"` 48箇所）。`_h()` は `"` を逃がさないため実ファイル名のインチ記号で src が切れていた → 属性用 `_at()` を新設。ほか `image:null` の `../img/null`、モバイル欠損ファイルを是正 |
> | ② | モバイル公開ガレージに自分用UI | 「保存済み / お気に入り / ピン留め」が他人のガレージに出て、押すと可視 tabpanel 0 で本文が消えた。`IS_OWNER` で出し分け／不明キーで `switchTab` を no-op ／見出しを「RIG」へ／自分用ヘッダーは markup ごと除去（`hidden` は `.cw-bar{display:flex}` に負けていた） |
> | ① | PC ヘッダー検索が全面で無反応 | 送信が検索結果ページの page-local にしか無かった。`SoT_app-shell.js` へ集約（契約: `.app-search input` / `[data-search-entry]` / `[data-search-local]` / `[data-search-action]`）。HOME 中央検索と候補チップも同経路。文言を現行の検索対象へ |
> | ⑥ | 検索面だけ `?theme` が効かない | 面が `?q=` 管理で URL を書き換え、shell が読む前に `location.search` を空にしていた。検索状態でないパラメータを持ち越す |
> | ④ | カードのピン留めが無反応 | `event.stopPropagation()` だけで `aria-pressed` も無かった。押された見た目は部品が既に持っている（`_ic_pin_push_fill` / `.br__pin--pinned`）ので**状態と aria state だけ**を追加。見た目は不変 |
>
> ### DECISION（2026-09-10 イタヤ裁定）
> - **並べ替えのキーが空・欠損の項目は、昇順でも降順でも常に末尾。** 向きで反転させない
> - **`myrig:pin` は暫定の内部イベント。** Entity Actions の確定契約ではない。
>   名前も detail の形も変わりうるので、面・他部品・検査がこの名前に依存しない。
>   `detail.provisional = true` を持たせてある
> - Garage Top の節見出しの RIG ドットは **ライトで CR 1.02（実測）＝ NG-2 該当だが、
>   イタヤ裁定により現状維持**（2026-09-10）
>
> ### 検査
> `garage_list_check` **734**（GL9 を「並んだ結果の単調性」＋「欠損は末尾」まで見る形へ拡張。
> 68 → 137項目。従来は件数しか見ておらず日付ソートの破損を PASS させていた）／
> `garage_top_check` 287（GT17 新設: 旧 v7 色 0・状態チップの token 整合）／
> `garage_check` 503 ／ **`image_integrity_check` 63（新設**: II1 画像切れ0・II2 属性補間と
> `_at()` の往復を実画面で検証・II3 参照実体）／ `detail_contract` 51 ／ `entity_actions` 36 ／
> `launcher_link` 177 ／ `footer_single_source` 4 ／ `mobile_garage_detail` 134 ／
> `mobile_detail` 58・55・47・23 ／ `mobile_feed` 63 — **すべて 0 FAIL**。
> 故障注入 selftest も全本で FAIL を検知（garage_list 56 / garage_top 14 / image_integrity 10）。
>
> 🔴 **検査の教訓（再発防止）**: DOM 走査は必ず `shadowRoot` を再帰すること。
> 画像切れを最初「0件」と誤判定した。design-nogo-list「検査の盲点 #1」に名指しされている落とし穴。
>
> ### NOW
> 🔴 **次は「残るデザイン判断項目」の実画面確認。** ここを通してから Garage 全体 CLOSE を判定する。
>
> | # | 項目 | 状態 |
> |---|---|---|
> | D1 | **PARTS / LOG / お気に入り / ピン留め の実画面レビュー** | 未了 |
> | D2 | **H2-a: カテゴリ色を文字色・線色・操作色から外す** | 未着手。棚卸し済み（下記） |
> | D3 | **H2-b: `--cat-*` の v8 版上げ本体** | 未着手。D2 の後 |
> | D4 | サンプル写真と名称の不整合（S15 Drift King にクローラー写真 等） | 未裁定 |
> | D5 | 表示密度（モバイル一覧の上部 ~450px / RIG詳細 375px で ~7,500px / 検索の0件メーカー / FEED 右レール） | 未裁定 |
> | D6 | お気に入りとピン留めの使い分けが操作地点で伝わりにくい | 未裁定 |
> | D7 | 保存の永続化（ピン留めを押した後の状態保持）と Entity Actions の正式契約 | 未裁定 |
> | D8 | Own Garage の Users 保存対象（`myrig-user-card` が存在しない） | 未裁定（`page-role-matrix v1` との差分） |
>
> ### 🔴 H2 の棚卸し実測（着手前に必ず読む・2026-09-10）
> `color-token-v8.md` の「**部分適用は禁止**」はこの状態を指している。いま flip すると悪化する。
>
> - live PC 40面のうち **v8 は 17面のみ / 旧 v7 が 23面**
> - `--cat-*` 参照は PC で **445箇所**、`color-mix` によるカテゴリ色の淡色化が **92箇所**
> - **塗りは安全**。カテゴリ色の塗りの上の白文字 15種はすべて PARTS / LOG（正しい）。
>   RIG の塗りは全面すでに黒文字で、1.08 の事故は起きない
> - **危ないのは文字色と線色**。旧 v7 緑 `#66b900` は白地 2.47 だが v8 黄 `#FBFF00` は **1.08 で消える**。
>   該当: about / library-v3 / library-maker-detail / library-rig-master-detail / support-us /
>   register-rig / browse-category / browse-rigs / home / feed
> - `register-rig` は `accent-color: var(--cat-rig)` でラジオ、`border-color` でドラッグ枠まで category 色
> - **NG-1 の 4px 色帯が実画面に残存**: `myrig-library-v3.html` の `.door--rig` / `.door--parts`
>   （canon が「PC側に未撤去」と名指ししているもの）
> - `SoT_browse-shell.css` の `.category-side__cta--parts` は CTA をカテゴリ色で塗っている（NG-7 流用）
>
> 順序: **D2（NG-1/NG-2/NG-7 の是正）→ D3（flip 本体 ＋ page-local `--cat-*` コピー10ファイル撤去
> ＋ 中立操作色の直書き 54箇所を `--color-action-primary` へ移送）**。
> `--color-action-primary` / `-text` / `--color-action-quiet-bg` は `SoT_tokens-v6.css` に
> **敷設済み・未参照**（挙動不変）なので、D3 はそこへ寄せるだけでよい。
>
> ### 閉じた PENDING
> - `.view-toggle`（保存2面で昇格・081 で解消）
> - `.cat-badge--*` の二重管理（正本を `--cat-*-on` へ是正し打ち消し6箇所を全廃・081 で解消）
> - PC フォトノート 8枚問題（現物は6枚。`pc/myrig-rig-detail-v15.html:145` を実測確認。HOLD H1 を閉じる）
>
> ### 継続 PENDING
> - **H2 `--cat-*` cross-surface rollout**（上記 D2 / D3）
> - PIT の中立 status と カード Web Component の色 status の2系統
>   → 081 でカード側を中立へ寄せたため**差は縮んだ**が、PIT は暗いスクリム地・カードは淡色地で
>     地の作り方が違う。最終 Convergence で1つに畳むか判断する
> - Public Garage（Own Garage の CLOSE 後）

> ## ✅ 080: Garage RIG一覧 PC v7 CLOSE — 一覧5面の器が確定（2026-09-09 / イタヤ実画面レビュー通過）
>
> モック: `myrig-mockup` `pc/myrig-garage-rigs-v7.html`（HEAD `b534f8d`。
> Vercel production `dpl_6xSZ8JDBDUaj1E5C3XGE2taoYfC2` = 同 SHA / READY で **origin 反映を独立確認**）。
> 恒久検査: `_state/garage_list_check.py`（**176 PASS / 0 FAIL**・故障注入 selftest あり）。
>
> ### STATE
> ✅ **RIG一覧（/garage/rigs）は PC visual / structure CLOSE。**
> ✅ **Own Garage 一覧5面の器がここで確定した。**3〜6面目は器を再利用し、中身の差分だけを扱う。
> 🔴 **Garage Top / RIG一覧は再オープンしない。** v6 はファイルとして残すが Launcher の参照先ではない。
>
> ### DECISION 1 — 一覧の器を Shared Source 化（棚卸しの実測が根拠）
> `.main-2col` / `.page-title-bar` / `.filter-panel` 系は **v6 の一覧5面すべてが page-local に複製**
> していた（定義本体の md5 は 5面中4面が一致 ／ page-local CSS は5面合計 **1,158行** ／
> `myrig-garage-rigs-v6.html` の中だけでも `.filter-panel` が **2回**定義）。
> CORE 共有UI Single Source（L1）の昇格条件をとうに超えていた。
>
> | ファイル | 責務 |
> |---|---|
> | `pc/assets/css/SoT_garage-page.css` | **Garage 6面共通** … canvas / cover / 節見出し / **`.grid-md`** / 左レーンの v7 差分 |
> | `pc/assets/css/SoT_garage-list.css`（080 新設） | **一覧5面専用** … `.main-2col` / `.main-content` / `.page-title-bar` / `.filter-panel` 系 / 空状態 |
> | `pc/assets/js/SoT_garage-list.js`（080 新設） | filter / sort の**挙動と aria state** |
>
> 🔴 `.grid-md` は `SoT_garage-top.css` → **`SoT_garage-page.css` へ昇格**（イタヤ裁定。Top と一覧の
> 両方が使うため）。一覧側で作り直さない。Garage Top は filter rail を持たないので **`SoT_garage-list.css` を読まない**。
> 面の page-local CSS / JS は **0**。
>
> ### DECISION 2 — filter は style だけでなく behavior / aria state も共有側（イタヤ指示）
> 契約は **data 属性だけ**。id 決め打ちにしない。
> `[data-garage-list]` / `[data-list-items]` / `[data-list-count]` / `[data-list-empty]` /
> `[data-filter-reset]` / `[data-filter-chips="<key>"]` / `[data-filter-select="<key>"]` / `[data-sort]`。
> 対象要素は `data-<key>`（「,」区切りで複数可）。単一選択と `aria-pressed`、件数同期（`aria-live`）、
> 0件の空状態、条件クリアと focus 復帰、並べ替えを**部品が持つ**。
> v6 の filter は見た目だけで、押しても何も起きず `aria-pressed` も無かった。
>
> 🔴 **罠の記録: 並べ替えで DOM を動かしてはいけない。**
> `appendChild` で付け替えると、カード（Web Component）の `connectedCallback` が再実行され
> `attachShadow` が「already hosts a shadow tree」で throw する（実測 pageerror 7〜35件）。
> **grid の `order` だけを書き換える。** 検査 GL13 が `appendChild` の再発を縛る。
>
> ### DECISION 3 — filter の色と、一覧の責務分離
> | 対象 | 決定 |
> |---|---|
> | filter の見出し | v6 の `--color-accent`（青）を撤去し **本文色**（NG-6「行の名前は本文色」） |
> | 選択中のチップ | accent 塗りを撤去し **中立の選択面＋太字**（NG-7 の職域表。Garage Top の「ピットを編集」押下と同じ文法）。実測 Light 地 `#eff1f3` / Dark 地 `#06080c`・weight 800 |
> | 流用禁止 | **カテゴリ色 / accent を操作状態へ流用しない。** 色値は hardcode せず既存 semantic token を参照 |
> | 責務分離（PC 確定） | **左 = Garage identity / navigation ／ 中央 = 一覧本体 ／ 右 = その一覧に対する filter・sort**。実測 x 座標 sidebar 100 < content 384 < filter 1240 |
>
> Library / Search の `SoT_filter-sidebar.css` は**使わない**。冒頭に「**非適用: Garage 系**」と
> 明記があり、あちらは「左が filter」の構造で Garage とは前提が違う。
>
> ### DECISION 4 — カード
> `variant="md" context="owner"` を維持。🔴 **browse variant へ変えない**（Own Garage は発見面ではない）。
> 絞り込み用に `data-category` / `data-maker` / `data-status` / `data-date` / `data-likes` を付与する。
> 一覧なので**全 7 台**を並べた（v6 は 6枚で「7台」と表示し、Sidebar の RIG 7 と食い違っていた）。
>
> ### 非回帰
> `garage_check` **491**（garage グループの確認導線 5→8 本で +3）／ `garage_top_check` 247 ／
> `detail_contract` 51 ／ `entity_actions` 36 ／ `launcher_link` 177 ／ `footer_single_source` 4 ／
> `mobile_garage_detail` 134 ／ `mobile_detail` 58・55・47・23 ／ `mobile_feed` 63 — すべて 0 FAIL。
> pageerror 0 ／ 横 overflow 0（1280・1440・1600・1920 × Light / Dark）。
> 低コントラストは v6 比 light 17→19 / dark 6→6。増分2件は**カードが 6→7 枚**になった分で、
> 今回新設した要素は 1 件も出ていない。Garage Top のページ高は 4090px で不変（`.grid-md` 移送は無影響）。
>
> ### 検査の範囲（先回りしない）
> `garage_list_check.py` が拘束するのは **RIG一覧＋共有器まで**。
> 未移行の PARTS / LOG / Favorites / Pins の将来 UI は固定していない（`.view-toggle` 等は書かない）。
> GL12 は **未移行4面が Launcher / compare で v6 のまま**であることも見る。
>
> ### NOW → ✅ **3〜6面は 081 で実装完了**（レビューは未了）
> 残り: ~~2 RIG一覧~~（080 CLOSE）→ ~~3 PARTS一覧~~ → ~~4 LOG一覧~~ → ~~5 お気に入り~~ →
> ~~6 ピン留め~~（**すべて 081 で v7 実装済み**）。
> **Public Garage は Own Garage の CLOSE 後。**
>
> 🔴 **既存 PENDING のまま最終 Convergence へ**（このバッチでも扱わない）:
> H2 `--cat-*` cross-surface rollout ／ PIT の中立 status と カード Web Component の色 status の2系統 ／
> `.cat-badge--*` が catalog では `color:#fff` で Detail だけ `!important` で黒へ戻している二重管理 ／
> `.view-toggle`（Favorites / Pins のグリッド⇄リスト切替。その2面で昇格させる）。

> ## ✅ 079: Garage Top PC v7 CLOSE（2026-09-09 / イタヤ実画面レビュー通過）
>
> モック: `myrig-mockup` `pc/myrig-garage-top-v7.html`（HEAD `219f63e`。
> Vercel production `dpl_Af2ivenf4S49bk38giHAD2e1VBJ9` = 同 SHA / READY で **origin 反映を独立確認**）。
> 恒久検査: `_state/garage_top_check.py`（**247 PASS / 0 FAIL**・故障注入 selftest あり）。
>
> ### STATE
> ✅ **Garage Top（/garage）は PC visual / structure CLOSE。**
> v6 の情報構造を維持したまま、現行の Shell / Shared Source / token / component 文法へ載せ替えた。
> 🔴 **再オープンしない。** v6（`myrig-garage-top-v6.html`）はファイルとして残すが Launcher の参照先ではない
> （`garage_check` G1 が sha256 を凍結しているので v6 は触らない）。
>
> ### DECISION 1 — Own Garage の器と中身を分ける
> | 責務 | 正本 |
> |---|---|
> | カテゴリ色 v8（`--cat-*` / `--cat-*-on`） | **`pc/assets/css/SoT_category-tokens-v8.css`（079 新設・物理正本）**。`SoT_detail.css` は @import で読む。値・宣言・詳細度は移送前と同一 |
> | Owner 面の統計の目盛り | **`pc/assets/css/SoT_garage-stats.css`（079 新設）**。Owner Detail（`SoT_garage-detail.css`）と Garage 一覧の左レーンが**同じ物理ファイル**を読む |
> | Garage 一覧6面の器（canvas / cover / 節見出し / 左レーンの v7 差分） | **`pc/assets/css/SoT_garage-page.css`（079 新設）** |
> | Garage Top 固有（PIT TABLE / SAVED / FOLLOWING） | **`pc/assets/css/SoT_garage-top.css` ＋ `pc/assets/js/SoT_garage-top.js`（079 新設）** |
> | Sidebar / Header / Footer / カード | 既存 SoT をそのまま読む（複製しない） |
>
> page-local CSS **197行 → 0** ／ インライン JS **22行 → 0**（いずれも非空行）。
> 🔴 `SoT_garage-sidebar.css` **本体は触らない**（v6 の5面も読む）。v7 の差分は `SoT_garage-page.css` で上書きし、
> **remove after: Garage 一覧6面がすべて v7 になったとき**を同ファイルに明記した。
>
> ### DECISION 2 — Own Garage の色の職域（実画面レビュー3往復の結論）
> | 対象 | 決定 |
> |---|---|
> | **節見出し** | **全節そろって `.section__title`**（catalog-v6 素の 20px / Barlow Condensed / 800 / uppercase）。カテゴリ色は**見出し左の 8×8px マーカー**だけが持つ。件数は `.section__count`（12px / tertiary）。**色の面積を増やさない・同じラベルを二重表示しない** |
> | **status** | Owner 面は**色の面で出さない**。PIT TABLE は中立の黒半透明チップ＋白文字＋**8px の状態ドット**。Owner Detail が中立 `<select class="g-select">` で status を扱うのと同じ思想 |
> | **主操作** | **中立ソリッド**（color-token-v8 §3）。既存の `--color-text` / `--color-bg` の対を再利用し、**新しい値も token も足さない**（`.app-theme-opt.is-on` / `.filter-btn.is-active` / `.shelf__arrow:hover` と同じ文法）。実測 Light 15.80 / Dark 15.62 |
> | **選択・押下** | 淡いピル＋太字。塗りで反転させない（NG-7 の職域表）。黒を塗るのは主操作だけ |
> | **Nav の現在地** | accent を使わない。**地を一段濃く ＋ 太字 ＋ 中立色の縦棒**（`SoT_browse-sidebar-v5.css` の `.sb-dir-row.is-current` と同じ。NG-4 / NG-6） |
>
> 🔴 **PC の action token 化（`--color-action-primary`）を Garage 側で新設しない。**
> 必要になったら semantic token の物理正本 `SoT_tokens-v6.css` を owner にして最終 Convergence で扱う。
> 検査 GT14 が Garage の CSS に `#1F2328` / `#E6EDF3` / `--color-action-primary` が現れないことを縛る。
>
> ### DECISION 3 — 左レーンの並び
> **Profile（＋自分の数）→ Garage Nav → RECENT ACTIVITY（今週 summary ＋ 履歴）。**
> v6 で main にあった「今週のアクティビティ」は **RECENT ACTIVITY パネルの上段へ統合**した（別の箱を作らない）。
> 🔴 **Navigation の優先順位を下げない。** Profile と Nav の間に何かを挟まない
> （Garage は管理ハブなので、左で最優先はプロフィールの次に Navigation）。
>
> ### 経緯（同じ往復をしないための記録）
> 見出しは3パスかかった。**1**: 28px の塗りバッジを新造 → 既存正本 `.cat-badge` を確認していなかった。
> **2**: `.cat-badge`（10px / 52×20）を見出しに使用 → 正本は使えたが RIG / PARTS / LOG だけ見出しの格が落ち、
> PIT TABLE / SAVED / FOLLOWING とタイポグラフィが揃わなくなった。**3**: 全節そろえて色はマーカーだけ（採用）。
> 🔴 **教訓: 「情報構造を維持」は「見た目も旧版のまま」ではない。**
> 新しい見た目を作る前に、同じ責務の**既存正本があるかを先に探す**（CORE 検証原則）。
> 3パスぶんの理由は `pc/assets/css/SoT_garage-page.css` §3 にコード側の記録として残してある。
>
> ### 非回帰
> `garage_check` **488**（garage グループの確認導線 2→5 本で +3）／ `detail_contract` 51 ／
> `entity_actions` 36 ／ `launcher_link` 177 ／ `footer_single_source` 4 ／ `mobile_garage_detail` 134 ／
> `mobile_detail` 58・55・47・23 ／ `mobile_feed` 63 — すべて 0 FAIL。
> pageerror 0 ／ 横 overflow 0（1280・1440・1600・1920 × Light / Dark）。
> 低コントラストは v6 比 **light 30→27 / dark 9→6**（差は LOG カードの tag-badge が v8 化で解消した分）。
> `SoT_detail.css` / `SoT_garage-detail.css` からの物理移送は、Detail 5面 × Light/Dark の計算値・幾何が完全一致
> （ハッシュが揺れた件は同一ビルドの反復でも両方の値が出る非決定要素で、移送起因でないことを確認済み）。
>
> ### NOW → ✅ **2「RIG一覧」は 080 で完了**
> 🔴 次は Own Garage 3「PARTS一覧」。
> 残り: ~~2 RIG一覧~~（**080 で CLOSE**）→ **3 PARTS一覧** → 4 LOG一覧 → 5 お気に入り → 6 ピン留め。
> **Public Garage は Own Garage 6面の完了後。**
>
> | 方針（078 から継続） | |
> |---|---|
> | 基本 | PC v6 の既存コンセプトを基本維持。Detail ほど深掘りせず **完成優先** |
> | RIG / PARTS 一覧 | **現行カード文法を継承**。PARTS は Garage 内絞り込みを維持 |
> | LOG 一覧 | リスト型 |
> | お気に入り / ピン留め | 既存案を土台にする |
> | 収束 | 古い color token / page-local 重複は現行 Shared Source へ追随。**079 で敷いた器（`SoT_garage-page.css` / `SoT_garage-stats.css` / `SoT_category-tokens-v8.css`）をそのまま使う** |
> | filter / search rail | 079 では**作っていない**（2面目が無かったため）。RIG 一覧で2面目になるので、そこで page-local から共有へ昇格させる |
>
> 🔴 **Garage Top は再オープンしない。** Owner Detail 4面・Mobile Public Detail 3面も同様。
> 🔴 **既存 PENDING のまま最終 Convergence へ送るもの**（このバッチで扱わない）:
> H2 `--cat-*` cross-surface rollout ／ PIT の中立 status と カード Web Component の色 status の2系統 ／
> `.cat-badge--*` が catalog では `color:#fff` で Detail だけ `!important` で黒へ戻している二重管理。

> ## ✅ 078: Mobile Garage Owner Detail CLOSE（2026-09-09 / イタヤ実画面レビュー通過）
>
> モック: `myrig-mockup` `garage-rig-detail.html` / `garage-parts-detail.html`（HEAD `80087ff`）。
> 恒久検査: `_state/mobile_garage_detail_check.py`（**134 PASS / 0 FAIL**）。
> 処遇表: `_state/MOBILE_GARAGE_DETAIL_TREATMENT.md`（KEEP / ADAPT / DROP / OWNER ONLY ＋ Q-1〜Q-6）。
>
> ### STATE
> ✅ **Mobile Garage RIG Detail CLOSE**
> ✅ **Mobile Garage PARTS Detail CLOSE**
> ✅ **Owner Detail 4面（PC RIG / PC PARTS / Mobile RIG / Mobile PARTS）が visual / structure CLOSE**
> 🔴 **Owner Edit Interaction は別 PENDING のまま**（まとめて編集 → Register edit mode ／
>    section 編集 → scoped modal ／ Build Detail → large modal）。CLOSE を妨げない。
>
> 骨格は **Mobile Public Detail（075 CLOSE）をそのまま再利用**し、Owner 差分だけを上乗せした。
> 旧 Mobile Garage Detail は Shared Source 化より前の世代で、page-local `<style>` を 208行 / 382行
> 持ち、うち **63件 / 105件が Public と同名クラスの再定義**だった（PC の Garage v6 → v7 と同じ構図）。
> → page-local `<style>` / `<script>` は **0**。`mobile-detail.css` / `mobile-detail.js` を正本として読む。
>
> ### 裁定（Q-1〜Q-6 / 2026-09-09 イタヤ）
> | # | 裁定 |
> |---|---|
> | Q-1 | **分割**。identity 直下に compact な Owner 領域（統計4値 / QUICK NOTE / 「管理」入口）、MANAGE の詳細操作は **bottom-sheet**（Shell の `registerSheet` を再利用。page-local sheet を新造しない）、BUILD PARTS MAP は **RIG 本文の Build Detail 直後** |
> | Q-2 | Owner 領域の見出しは **PC と同じ思想の全面色帯**（RIG=`--cat-rig` 黄 16.87 / PARTS=`--cat-parts` 赤 4.83）。MANAGE シートも同じ category identity を細い accent として継承。**色値を Mobile 側に書かない** |
> | Q-3 | **Mobile 専用 Garage Drawer を追加しない**。Shell hamburger ／ bottom navigation ／ `rd-subhdr` の戻るで足りる。1 viewport に同じ責務の navigation trigger を増やさない |
> | Q-4 | **PIT TABLE 設定は Garage Detail から DROP**。PIT TABLE の編集・設定は **Garage Top の責務**（PC v7 と揃える。Mobile だけ Detail に残さない） |
> | Q-5 | **Owner Garage では購入 CTA を出さない**。情報のみ残す（RIG ベースモデル / PARTS 製品情報）。Public Detail の収益導線裁定 **075 D7 を Owner 面へ機械適用しない**。Garage は MVP では**管理ワークスペース**として扱う |
> | Q-6 | 統計は PC と同じ **4値**（閲覧 / いいね / お気に入り / ピン留め）。Mobile だけ閲覧数を落とさない |
>
> ### Shared Source（面をまたいで二重管理しない）
> | 責務 | 正本 |
> |---|---|
> | Mobile Detail の器・部品（`<md-*>`） | `css/mobile-detail.css` / `js/mobile-detail.js` — **Garage 用に複製しない** |
> | Owner 差分（Owner 領域 / MANAGE シート / BUILD PARTS MAP / 編集ボタン） | **`css/mobile-garage-detail.css` ＋ `js/mobile-garage-detail.js`（078 新設）**。`<mg-owner>` / `<mg-manage>` / `<mg-parts-map>`。**RIG / PARTS で複製しない**（差は `data-gd-entity` の binding だけ） |
> | Owner Control の強調色トークン | **`pc/assets/css/SoT_garage-control.css`（078 新設）**。PC の `SoT_garage-detail.css` と Mobile の `mobile-garage-detail.css` が **同じ物理ファイル**を `@import` する。両面に2行を複製しない |
> | 購入 CTA の有無 | `<md-commerce>` の **`no-buy` opt-in**（078 追加）。属性が無いときの出力は不変で、**Public Detail 3面の `main` innerHTML 完全一致**を確認済み |
> | bottom-sheet | Shell Dialog Controller（`mobile-shell.js` v0.6 の `registerSheet`） |
>
> ### Owner 面に戻さないもの
> reaction（`<md-actions>`）／ builder 行 ／ RELATED 棚 ／ `<md-ad>` ／ SHARE 行 ／ PIT TABLE 設定 ／
> 購入 CTA ／ **PARTS の暖色 BUY / INFO 枠（`g-sec--shop`）**（075 で Public から撤去済みの旧実装）。
>
> ### 実画面レビューでの軽量化（2026-09-09）
> Owner 領域の縦ウェイトが高く本文前の圧迫感が強かったため、**構造は維持したまま**見え方を軽くした。
> 左端の 3px アクセント罫を廃止して全面色帯へ（＝ PC と同じ思想）、「管理」ボタンを帯の右へ収容して
> 独立1段を畳み、統計を圧縮、QUICK NOTE は `details` 化して **メモが空なら閉じて始まる**、
> BUILD PARTS MAP に本文と同じ左右 gutter。
> **Owner 領域 293 → 224px、本文 `.rd-lead` の開始 RIG 725 → 657 / PARTS 675 → 607（−68px）。**
> QUICK NOTE の明示保存の仕様は変更していない。
>
> **非回帰**: `garage_check` 485 ／ `mobile_detail_check` 58 ／ `mobile_feed_check` 63 ／
> `detail_contract_check` 51 ／ `entity_actions_check` 36 ／ `launcher_link_check` 177 — すべて 0 FAIL。
> pageerror 0・横 overflow なし（RIG / PARTS × Light / Dark）。
>
> ### NOW → ✅ **1「Garage Top」は 079 で完了**
> 🔴 次は Own Garage の残り6面。順番は
> ~~1. Garage Top~~（**079 で CLOSE**）→ **2. RIG一覧** → 3. PARTS一覧 → 4. LOG一覧 → 5. お気に入り → 6. ピン留め。
>
> | 方針 | |
> |---|---|
> | 基本 | **PC v6 の既存コンセプトを基本維持**。Detail ほど深掘りせず **完成優先** |
> | Garage Top | **PIT TABLE が中心**。**大1台 ＋ 小2台の3台構成を崩さない** |
> | RIG / PARTS 一覧 | **現行カード文法を継承**。PARTS は **Garage 内絞り込みを維持** |
> | LOG 一覧 | **リスト型** |
> | お気に入り / ピン留め | 既存案を土台にする |
> | 収束 | **古い color token / page-local 重複は現行 Shared Source へ追随**（077 DECISION 2 の Single Source、078 の `SoT_garage-control.css`・`mobile-garage-detail.css`） |
> | 順序 | **Public Garage は Own Garage 6面の完了後に着手** |
>
> 🔴 Owner Detail 4面（PC / Mobile）と Mobile Public Detail 3面の論点は**再オープンしない**。

> ## ✅ 077: Garage Owner Detail PC CLOSE ＋ Cross-surface Color / Theme 収束（2026-09-09）
>
> モック: `myrig-mockup` `pc/myrig-garage-rig-detail-v7.html` / `pc/myrig-garage-parts-detail-v7.html`。
> 恒久検査: `_state/garage_check.py`（**485 PASS / 0 FAIL**）。
> mockup HEAD `7bfb47a`（Vercel production `dpl_HZbyi8QTpifGRT93rhZnusj2mti8` = 同 SHA / READY で **origin 反映を独立確認**）。
>
> ### STATE
> ✅ **Garage Owner RIG / PARTS Detail は PC visual / structure CLOSE。**
> 最新 Public Detail（v15 / v1-open）を骨格に Owner 差分だけを上乗せした v7 で確定。
> canvas 1560・7:3・OPEN 型本文・共有 gallery / comments を継承。Owner 差分は
> Context Bar（sticky）・Garage Drawer・MANAGE・QUICK NOTE（明示保存）・BUILD PARTS MAP・統計4値。
> reaction / 購入 CTA / RELATED は載せない。
> 🔴 **PC Garage Detail の視覚・構造は再オープンしない。** v6 8面はファイルとして残すが Launcher の参照先ではない。
>
> ### DECISION 1 — Owner Control は entity category binding
> Owner Control（`data-gd-owner` を持つ rail section）の強調色は **その entity のカテゴリ色**に従う。
>
> | 面 | binding | 実測（Light / Dark 共通） | contrast |
> |---|---|---|---|
> | RIG（MANAGE / QUICK NOTE / BUILD PARTS MAP） | `--cat-rig` / `--cat-rig-on` | `#FBFF00` / `#151515` | 16.87 |
> | PARTS（MANAGE / QUICK NOTE） | `--cat-parts` / `--cat-parts-on` | `#D92D20` / `#ffffff` | 4.83 |
>
> 実装は `<html data-gd-entity="rig|parts">` と `SoT_garage-detail.css` の2行だけ。
> 🔴 **色値を page-local に hardcode しない。** Public Detail と同じ category token を参照する。
> component 構造は RIG / PARTS で共通のまま、**binding だけ**を entity で切り替える。
> Light / Dark で同じ category identity（theme 切替は token 側が持つので `--gd-control` の Dark override を置かない）。
> 旧文法「Garage の操作領域だから RIG / PARTS とも黄」は**失効**。
> ⚠️ H2（`--cat-*` の cross-surface rollout）とは別件。**category 色の値そのものは変更していない。**
>
> ### DECISION 2 — semantic color token の物理供給元を Single Source 化
> 🔴 **semantic token の物理供給元は `pc/assets/css/SoT_tokens-v6.css` の1つだけ。**
>
> | 以前あった二重管理 | 処遇 |
> |---|---|
> | `css/sot/SoT_tokens-v6.css`（Mobile 用の別コピー。A-4 / B-1 が届いていなかった） | **`@import` shim**。値を持たない |
> | `css/mobile-tokens.css` の `--ms-*` | `var(--color-*)` への**参照**。Dark 再定義ブロックを撤去 |
> | 14面の私設 `:root` palette（24〜40行 × 14 = 414 宣言） | 撤去して token を link |
> | P22-C3 の Dark 階調（`SoT_detail.css` ＋ 6面の page-local コピー） | **token file へ昇格**し、コピーを撤去 |
> | `color-scheme` | `html[data-theme]` 契約で token が供給（library 5面・composer の page-local 宣言を撤去） |
>
> **塗り（fill）とその上の文字（on）は別 token。** `--color-accent-fill` / `--color-accent-on`、
> `--rig-status-*-on`。component が `color:#fff` を直書きしない。
> 🔴 **Mobile 用コピー / page-local 重複を恒久化しない。** 面を足すときも token を link するだけにする。
>
> **実測（50面 × Light/Dark = 100 render）**: `--color-bg` / `--color-surface` / `--color-text-tertiary` /
> `--color-accent` が Light・Dark とも **50面で単一値**。`color-scheme` light 50 / dark 50。
> 低 contrast 箇所 **2,251 → 485（−78%）**、pageerror 0、横 overflow 0。
>
> ### DECISION 3 — Entity Action の active は accent 文法1つ
> like / favorite / pin の active は **同じ `--ea-active`（= `--color-accent`）**。
> **like だけ赤にしない**（実画面で違和感が確認され、同列の3操作なのに意味色が分裂していた）。
> pressed surface（rail の青い面）は持たず、色字＋色枠だけで active を示す。
> 🔴 **reaction state へ category token（`--cat-*`）や通知色を流用しない。**
> 🔴 **danger / delete の赤（`--color-danger`）とは責務が別。** reaction = accent / 破壊操作 = danger。
> `--color-like-on` は footer の ♥ 等の**装飾**として残すが、reaction の state 色ではない。
> 比較用の `--ea-active-like` / `html[data-ea-variant]` / `?ea=b` は**撤去済み**（Shared Source は完成状態1つ）。
> 実測: PC rail Light 5.19 / Dark 4.50、Mobile action bar Light 5.19 / Dark 4.92（3操作とも同値）。
>
> ### PENDING
> | # | 内容 |
> |---|---|
> | **Owner Edit Interaction** | まとめて編集 → Register edit mode ／ section 編集 → scoped modal ／ Build Detail → large modal。**別バッチ**。read-first 調査済み（登録フォームに同じ field 責務が既にあり、足りないのは①フォトノート caption ②パーツ削除 UI の2つ。障害は登録ページが page-local CSS 1807行で部品化されていないこと）。**PC visual CLOSE を妨げない** |
> | **最終 Convergence へ回す** | H2 `--cat-*` cross-surface rollout ／ filter・tab 文法整理（S2）／ Feed scrollbar の thin tuning ／ Color TUNING 群（footer ♥・avatar イニシャル・写真上文字・breadcrumb `/`・bell badge）。**全モック完成後**にまとめて扱う |
>
> ✅ **H1 は CLOSE。** PC v15 のフォトノートを 8 → **6枚**へ（7件目・8件目は 3件目 / 5件目と同一画像の重複で、
> caption も枠合わせだった）。Garage v7 も同時に追随。CSS で隠していない。Gallery 本体（7枚）は不変。
>
> ### NOW → ✅ **078 で完了**
> ~~次は Mobile Garage RIG / PARTS Detail。~~ → **078 で CLOSE**（上の節を参照）。
> PC Garage Owner Detail で確定した Owner 責務を Mobile へ adapt する作業であって、
> PC Detail の再設計ではなかった。**Mobile Public Detail 3面（075 で CLOSE）も再オープンしていない。**

> 🔴 **063 で決まったこと（イタヤ裁定 2026-09-06）: ユーザー投稿画像の上限は RIG 7 / PARTS 5 / LOG 3。**
> RIG は Cover 1 ＋ Sub 最大6（従来 Cover 1 ＋ Sub 8 = 9 を**失効**）。PARTS 5・LOG 3 は従来どおり変更なし。
> 理由は 062 のインフラ／コスト耐性方針と同じ「**MVP は小さく安全に始め、必要性が実測されたら解放する**」。
> 7枚で不足が実測されたら 9 へ増やすのは容易だが、9 が定着してから削るのは難しい。
> **正典で固定するのは上限値だけ。「7枚だからこの並べ方」といった表示レイアウトは固定しない**（実装・モック側で最適化）。
>
> ✅ **モック側の 7枚化は完了（2026-09-07 / 073 / mock `2158000`）。** 独立小バッチで実施し Gate 2 を再検証した。
>
> | 対象 | 結果 |
> |---|---|
> | `pc/myrig-rig-detail-v15.html` | `<dt-gallery>` の重複2枚（`data-mock-slot="7"/"8"`）を撤去 → **7枚**（Cover 1 ＋ Sub 6） |
> | `pc/myrig-register-rig-v2.9.5.html` | `MAX_SUB_PHOTOS` 8 → **6**、文言2箇所（「サブ写真 最大6枚」「4列で最大6枚」） |
> | `_state/detail_contract_check.py` | Gallery 枚数の許容上限 `<= 9` → **`<= 7`** |
> | 共有 JS（`SoT_detail-components.js` / `-markup.js`） | 見出しコメントを「Cover 1 + Sub 6 = 最大7枚」へ。**コードは枚数を固定していないので実装変更なし** |
> | Mobile `rig-detail.html` | **変更なし**（`IMGS` は元から5枚で上限内。「最大7枚」と矛盾しないので、この理由だけで7枚へ増やさない） |
> | PARTS 5 / LOG 3 | **変更なし**（063 のとおり） |
>
> **Gate 2 再検証 PASS**: `detail_contract_check` 51 PASS / 0 FAIL / 0 WARN ／
> Gallery 挙動（サムネ7枚・`1 / 7`・next 一周・prev・サムネ直接クリック・`aria-label` の連番）**全一致** ／ pageerror 0 ／
> PARTS・LOG は画素差 0 ／ register-rig はサンプル読み込みで Cover 1 ＋ サブ 6 で打ち止め。証跡は `_state/GATE2_rig-detail-v15.md` の追記。
>
> ✅ **イタヤ裁定（2026-09-07）: 7枚化で生じたレイアウト差は現状を採用する。**
> `.gallery__thumb{flex:1; aspect-ratio:3/2}`（共有 CSS）でサムネは行幅を等分するため、
> 枚数が減ると1枚が広く＝高くなる（9枚 ≈102px幅 → 7枚 ≈132px幅。サムネ列 68→89px）。
> その結果 `.dt-main` 以下が **21px 下へ平行移動**し、ページ高 7666→7687px。
> 画素差を分解した結果、**「ギャラリーが +21px、その下が同じだけ動いた」以外の変化は無い**
> （右レーンと identity は y も高さも不変。パンくず・ヘッダーも不変）。
> 🔴 **これは Cover 1 ＋ Sub 6 から自然に生じたレイアウト差であって他要素の崩れではない。
> 旧9枚時代の見た目を保つ目的だけで `thumb` の `max-width` 等を足さないこと。**

> 📌 **057 の内容:** GPT の再確認で 056 本文後段に2-D 以前の古い記述が2箇所残っていた
> （MVP Phase 表の「残り: 2-D」／横断部品表 Header 行の「Home だけ page-local が残る」）。
> 実装のやり直しではなく**記述の整合のみ**。あわせて `header_propagation_result.json` の
> 85 PASS/6 FAIL が「2-D 前を基準にした historical audit evidence」であり 057時点の
> 回帰基準ではないことを mockup 側 Map（§8.7）に明記した。

> **いま止まっている場所:** **Phase 2 は 2-A〜2-E すべて CLOSE。Mobile ロゴ lockup の小バッチも CLOSE。
> Phase 3（LOG Detail PC）も Gate 5 PASS で CLOSE。
> Feed continuity の 1「基本導線」も 068 で CLOSE。
> continuity 2「reaction」も 069 で裁定し、**Entity Actions を Shared Source へ統合済み。**
> 071 で LOG 着地規則と Feed タブも決着。**continuity は `pin` を残して完了。**
> 072 で **PC Core Convergence Audit と是正が完了**。073 で **Launcher の status 整理**と
> **RIG 画像 7枚化（Cover 1 ＋ Sub 6）** も完了。
> ✅ **076: Mobile Feed convergence CLOSE。** PC Feed が 068〜072 で確定した continuity を Mobile へ追随させた
> （Feed 設計の再検討ではない。3タブ = 071 のまま・Feed のトップレベル面としての役割も不変）。
> 🔴 **次は Garage / Public Garage Mobile。** Feed / Detail の論点は再オープンしない。
>
> ### ✅ Mobile Feed convergence（2026-09-07 / 076）
>
> モック: `myrig-mockup` `feed.html`。Gate: `_state/mobile_feed_check.py`（**53 PASS / 0 FAIL**）。
> 差分と設計は `_state/MVP_CONVERGENCE_MAP.md` §8.22。
>
> **Mobile へ反映したもの（068 の4点）**
> | # | 内容 |
> |---|---|
> | 本文 | 通常長は **Feed 内で全文**。`long` だけ折り畳み、「続きを読む」は **Feed 内展開**（長さで Detail 遷移へ切り替えない）。**超長文だけ**末尾に「このログの全文を記録ページで読む →」。閾値は正典で固定せず `data-longmax`（fixture 値）。確認用に `?longmax=<N>` |
> | Action row | いいね / コメント / 共有 / **記録** の4。記録は `<a>` ＋ aria-label・title「このログの記録を見る」・keyboard reachable。PC の「4等分」責務を保ったまま Mobile は 4列 grid ＋ 各 48px へ adapt |
> | Comments | カード内 conversation（初期2件 / さらに N 件 / その場で投稿 / 件数同期）。Feed と LOG Detail で**同じ state・同じデータ**。UI を新造せず、Detail Conversation の compact variant |
> | Author | avatar / 表示名 / @handle → Public Garage。**時刻はリンクにしない**。avatar は `tabindex="-1"`。interactive に `aria-hidden` を付けない |
>
> **Shared Source（面をまたいで二重管理しない）**
> | 責務 | 正本 |
> |---|---|
> | 続きを読む / 全文導線 | **`pc/assets/js/SoT_feed-continuity.js`（076 新設）**。`SoT_card-components.js` のクラス決め打ちから移設し、**PC / Mobile が同じファイルを読む** |
> | 面内 conversation | `pc/assets/js/SoT_comments.js`。セレクタを **`[data-feed-card]` 契約**へ（`.pc-feed-card` 決め打ちを撤去）。会話の器は中立名 **`.feed-conv`**。コメント1件は `setUnitRenderer()` で面の語彙（Mobile は `.comment-row`＝Detail と同じ実装） |
> | Entity Actions | `pc/assets/js/SoT_entity-actions.js`。🔴 束縛条件を「`<a>` でない」→ **「`aria-pressed` を持つ」**へ是正（Mobile の comment `<button>` を like と誤認し、押すだけで件数が +1 される欠陥を実測して修正） |
> | Feed カード markup（Mobile） | `js/mobile-feed.js`（`<md-feed-card>`）/ `css/mobile-feed.css`。**契約属性を出すだけで挙動を持たない** |
>
> Mobile の page-local 旧実装（`data-fc-like` の直接 ±1 / 独自 Lightbox / 続きを読むトグル / 共有トースト）は**撤去**。**書き戻さない。**
> PC Feed の視覚デザインは Mobile へ機械コピーしていない（Feed は timeline 面なので Detail の section separator も持ち込まない）。
>
> **非回帰**: PC Feed v3 / RIG v15 / PARTS v1-open / LOG v1 の画素差 **0**、`entity_actions_check` 36 PASS、
> `detail_contract_check` 51 PASS、`launcher_link_check` 189 PASS、Mobile Detail 3面 58 / 55 / 47 ＋ source 23 PASS、Mobile 既存 10 面 問題 0。
>
> 🔴 **HOLD（Feed の外）**: ~~H1 PC v15 フォトノート 8枚~~（**077 で CLOSE**。8 → 6枚へ是正済み）/ H2 `--cat-*` の cross-surface token rollout /
> H3 `pin` の露出（066 継続）。076 で新たに増えた blocker は無い。

> ✅ **075: Mobile Detail Phase（Phase 4）CLOSE — RIG / PARTS / LOG Detail Mobile の3面が完成形**。
> 074 で3面を着工し、イタヤ実画面レビュー＋GPT 実ソース監査を受けて 075 で finishing を完了した。
> **比較枝（`?rail=` / `?legacy=` / `?buy=` / `data-when`）は撤去済みで、完成形は各面1つ。**
> Launcher は各 Detail グループ = **完成候補1枚 ＋ 状態確認**（未ログイン / 所有者 / ダーク）。
> 🔴 **次は Garage / Public Garage Mobile。** Detail 3面の論点（コメント位置・action row・owner row・LOG 本文構造・
> RELATED 棚の種類・Lightbox 採否・pin・PC Detail VISUAL LOCK・Feed）は**再オープンしない**。
>
> ### ✅ Mobile Detail 3面 CLOSE（2026-09-07 / 075）
>
> モック: `myrig-mockup` `rig-detail.html` / `parts-detail.html` / `log-detail.html`。
> 処遇表（裁定済み）: `_state/MOBILE_RIG_DETAIL_TREATMENT.md`（v3）/ `_PARTS_`（v2）/ `_LOG_`（v2）。
> Gate: `_state/GATE_MOBILE_DETAIL_20260907.md`。恒久検査: `_state/mobile_detail_check.py {rig|parts|log|source}`。
>
> **DECISION（イタヤ実画面レビュー 2026-09-07）**
> | # | 論点 | 裁定 |
> |---|---|---|
> | D2〜D4 | 右レーン要素（base-model / parts-master / entity-feed / used-parts / used-by-rigs） | **主列の同責務節へ merge** |
> | D6 / D8 / D10 | 旧 Mobile 固有（RECENT ACTIVITY / 共有行 / 暖色 BUY-INFO / このパーツのログ / ガレージ RIG 棚 / SHARE 節 / 同カテゴリ棚） | **drop** |
> | **D7** | RIG ベースモデルの購入導線 | **残す（buy=on）**。🔴 旧推奨の `buy=off` は**失効**。理由: ベースモデルに紐づく「製品情報 / 購入先」は MyRIG の重要な収益導線であり、Mobile だから消すのではなく **適切な強度で残す**。2026-07-24 の「RIG に購入枠を出さない」は RIG 本体を売り物に見せない趣旨として維持し、**ベースモデル製品への導線には適用しない** |
> | D9 | LOG の builder | **上部 identity へ統合**（ページ途中に author を再登場させない） |
> | D1 | Lightbox | Mobile 現行を維持 |
> | D5 | 「同じ RC4WD のRIG」drop | **取り下げ**（PC v15 に `manufacturer-rigs` があり Mobile 固有ではなかった） |
>
> **Shared Source（3面で同一責務は1か所）**
> | 責務 | 正本 |
> |---|---|
> | Mobile Detail の style / markup / 挙動（`<md-*>` Light-DOM replacement） | `css/mobile-detail.css` / `js/mobile-detail.js` |
> | **製品・購入情報**（RIG ベースモデル / PARTS 製品情報の**共通** monetization ブロック） | `<md-commerce>` / `.rd-commerce*`。neutral inset・2導線・購入 CTA だけ一段強く・shop は下段で gap・affiliate 注記。🔴 **RIG / PARTS で別実装を作らない**（`md-bridge` / `.rd-dual` / `.rd-bridge` / 暖色 `g-sec--shop` は撤去済み。書き戻さない） |
> | Entity Actions | `pc/assets/js/SoT_entity-actions.js`（**PC と同じファイル**。Mobile では LoginRequiredModal / toast を Shell へ委譲） |
> | bottom-sheet | Shell Dialog Controller（`mobile-shell.js` v0.6 の `registerSheet`） |
>
> **Gate**: mobile_detail_check rig 58 / parts 55 / log 47 / source 23 PASS・**0 FAIL**。
> PC 非回帰: RIG v15・PARTS v1-open・LOG v1・Feed v3 の画素差 **0**、entity_actions 36 PASS、contract 51 PASS、launcher 186 PASS、Mobile 既存 12面 問題 0。
>
> 🔴 **HOLD（Detail の外で扱う）**
> | # | 内容 |
> |---|---|
> | H1 | **PC v15 のフォトノートが 8枚（うち2枚は既出画像の重複）**。Gallery は 063/073 で Cover 1 ＋ Sub 6 = 7枚へ縮んだのに、フォトノートの枚数と `section__count">8枚` が追随していない。**Sub 6 に対して 8枚は成立しない** → 063 / 073 の**追随漏れ**。PC は VISUAL LOCK なので Mobile 側だけ 4枚へ是正し、**PC は改変していない**。是正するかはイタヤ裁定 |
> | H2 | `--cat-rig / --cat-parts / --cat-log` の Mobile v8 値と PC 旧 palette の差 → **cross-surface token rollout** の論点。Detail 単独で解決しない（今回 Detail identity の見え方だけ整え、**色値は触っていない**） |
> | H3 | `pin` の露出（066 から継続の PENDING） |
> | H4 | GPT 参考パッチ ZIP は本セッションに添付されていなかったため、指示文の内容を現 repo HEAD へ**再実装**した。ZIP 実差分との突き合わせは未実施 |
>
> ### ✅ Feed continuity 1「基本導線」CLOSE（2026-09-07 / 068 / イタヤ裁定）
>
> **裁定原本: `_decisions/2026-09-07_feed-continuity-v1.md`。**
> モック側の実測は `myrig-mockup` `_state/MVP_CONVERGENCE_MAP.md` §8.15。
>
> **前提: Feed の閲覧体験は原則 Feed 内で完結させる。読むたびに別ページへ追い出さない。**
> LOG Detail は Feed の「完全版ページ」ではなく **canonical Garage record** として独立し、
> Browse / Search / Garage / Related から LOG を発見したときの**正規の受け皿**になる。
>
> | | DECISION |
> |---|---|
> | 本文 | 「続きを読む」は **Feed 内展開**。**LOG の長さで挙動を変えない**。通常長は Feed 内で全文を読む |
> | 本文 | 超長文だけ、上限まで展開した**末尾**に「このログの全文を記録ページで読む →」 |
> | 本文 | 🔴 **長文の境界値を正典で固定しない。** 当面「1000字前後」を **fixture 値**として扱う |
> | action row | **like / comment / share / LOG Detail の4等分**。🔴 **LOG Detail を後付けの auto 列にしない** |
> | action row | LOG Detail は `<a>`。`title` / `aria-label` =「このログの記録を見る」。キーボード到達可 |
> | action row | アイコンは document / record 系を維持。🔴 **アイコン違いの比較案を増やさない** |
> | コメント | comment action で**カード内に conversation**。初期表示 **最新2件程度**。その場で投稿可。続きは「さらに ○ 件」 |
> | コメント | 🔴 **Feed と LOG Detail のコメントは同一 state・同一データ。** UI も新造せず **Feed 用 compact variant** |
> | author | **avatar / 表示名 / `@handle`** を **Public Garage（`/user/[username]`）** へ |
> | author | 🔴 **時刻はリンクにしない** ／ 🔴 **Feed 内に「そのユーザーだけの Feed」を作らない** |
> | author | 「この人の投稿だけ見たい」は **Public Garage 側の LOG 一覧**で満たす。Public Garage 本体の設計は別バッチ |
> | a11y | avatar リンクは **`tabindex="-1"` のみ**。🔴 **`aria-hidden="true"` を付けない**（クリックできる要素を支援技術から完全に隠さない） |
>
> **失効（戻さない）:** 時刻リンク ／ カード全体 click ／ 本文全体 click ／ 毎カード下部の大きなテキスト CTA ／
> 3点メニュー内だけ ／「続きを読む」で Detail 遷移 ／ action row の「3等分＋auto 列」。
>
> ⚠️ **【068 時点の記述・072 で解消済み】** 当時は「実行時の Feed カードが page-local `.pc-feed-card` で
> `<dt-actions>` 契約を持たないため、fixture のまま CLOSE し先に大規模リファクタしない」としていた。
> → ✅ **069 で reaction の state / behavior / aria を `SoT_entity-actions.js` へ統合し、
> 072 で Feed 本命案を既定へ昇格 ＋ Feed カードを共有 `<myrig-log-card variant="feed">` へ昇格した。**
> `?flow=inline` の fixture 分岐・死んだ静的カード14枚・旧 shadow `feed` variant は撤去済み。
> **「Feed は fixture のまま」「Shared Source 統合は次バッチ」は失効。**
>
> **今回の blocker にしないもの（記録のみ）:** モックの Public Garage が固定ユーザー1面で `h1` を持たない
> （**後の Garage 監査で拾う**）／ モックに LOG ごとの面が無く全カードが同じ v1 へ着く。
>
> ### ✅ Entity Actions を Shared Source へ統合（2026-09-07 / 069 / イタヤ裁定 / mock `fb84702`）
>
> **裁定原本: `_decisions/2026-09-07_entity-actions-shared-v1.md`。**
> モック側の実測は `myrig-mockup` `_state/MVP_CONVERGENCE_MAP.md` §8.16。
>
> 🔴 **統合前は、同じ「いいね」が3つの別実装だった。**
>
> | 場所 | 実装 | 動いていたか |
> |---|---|---|
> | Feed `.pc-feed-card` | page-local `[data-pc-like]` | 動く |
> | **LOG Detail 本文 `<dt-actions>`** | `.dt-action[data-action]` | 🔴 **一度も動いていなかった** |
> | RIG v15 / PARTS v1-open 右レーン | `.rail-action[data-action]` | 動く |
>
> 原因は共有 JS のセレクタ `.rail-action[data-action]` に**クラスが入っていた**こと。
> 契約も markup も正しく、**セレクタだけが漏れていた**ので DOM を見る検査では見つからなかった。
> 065 の「挙動は `[data-action]` に対して1か所で実装する」という設計意図と実装がずれていた。
>
> **DECISION（4論点）**
>
> | | |
> |---|---|
> | reaction 更新 | **楽観更新を採用。** 失敗時は rollback し、🔴 **黙って戻さず短い notice を出す**。🔴 **notice の比較案を増やさず、既存の通知文法（`MyRIG.toast` = `.app-toast` / `role=status` / `aria-live=polite`）を再利用する** |
> | 未ログイン | 新しい UX 裁定にしない。likes / favorites / pins は `user_id NOT NULL` のユーザー操作なので、**Feed / Detail とも同じ LoginRequired 契約へ統一**。Feed だけモーダルがあり LOG Detail で何も起きない状態は **cross-surface 不整合として修正** |
> | LOG Detail rail actions | 🔴 **追加しない。** 現行 inline を維持。**今回のバッチを理由に VISUAL CLOSE 済み面へ二重の action surface を作らない** |
> | pin | **PENDING 維持。** schema 上 LOG は pins 対象だが、UI をどこに出すかは別論点。🔴 **統合の blocker にしない** |
>
> **正本の所在**
>
> `pc/assets/js/SoT_entity-actions.js` — like / favorite / pin の
> **state / count / aria / 挙動 / 認証ゲート / 楽観更新 / rollback**、および LoginRequired
> （markup / CSS / 文言 / capture ハンドラ）を1か所で持つ。読み込むのは Feed / LOG Detail / RIG v15 / PARTS v1-open の4面。
>
> 🔴 **書き戻し禁止:** `SoT_detail-components.js` に `.rail-action[data-action]` の挙動を戻さない ／
> Feed に `[data-pc-like]` の挙動と page-local LoginRequiredModal を戻さない ／
> **共有側のセレクタにクラスを混ぜない**（`[data-action]` だけで拾う）。
>
> **契約**: `data-action` / `data-base-count` / `aria-pressed` / `data-auth`（share には付けない）/ `data-entity-key`。
> 数え方は既存規則のまま（初期 pressed=true → base+(pressed?0:-1)／false → base+(pressed?1:0)）。
> **見え方はページの語彙、契約は共通**（`.rail-action` / `.dt-action` / `.pc-feed-card__action` は表示 variant）。
>
> **非回帰（恒久）**: `_state/entity_actions_check.py` を新設。**38項目 / 38 PASS / 0 FAIL**（`--selftest` 付き）。
> 「契約があるか」ではなく **「押したら実際に count と `aria-pressed` が変わるか」** を見る
> （今回の欠陥はセレクタ漏れで、DOM を見る検査では見つからないため）。
> 画素は RIG v15 / PARTS v1-open / LOG Detail / Feed とも **揺れで説明できない画素 0**、
> 無関係面の対照（Home / Browse RIGs）も 0。
>
> 🟡 **モックの都合（本番では消える）**: ページをまたぐ state は `sessionStorage` が代役
> （`PERSIST` の中だけ。差し替えれば API に載る）／ どの LOG かは `?log=`（本番は `/log/[id]`）／
> `?ea_fail=<0-100>` は rollback を実画面で見るためのもの。**fixture の逃げは残していない。**
>
> ### ✅ shelf_propagation_check の baseline 貼り直し（2026-09-07 / 070 / イタヤ承認 / mock `3102b1e`）
>
> | | |
> |---|---|
> | old baseline | `7437e44`（PROPAGATION-SHELF 直前） |
> | **new baseline** | **`923df08`**（`design: ロゴ lockup 化を PC 全33面へ展開・既定へ統合` / 2026-09-05） |
> | 更新理由 | **承認済みの視覚変更「ロゴ lockup 全33面展開」**により旧基準が意図的変更より前の状態になったため |
> | 旧 baseline との差 | Home / Browse 3面で **一律 2381px**（全面同一値 = ヘッダー帯の変化と整合） |
> | Entity Actions batch（`fb84702`）の寄与 | **0** |
>
> 🔴 **「テストを PASS させるために現在の HEAD を baseline 化する」ことはしていない。**
> 新基準は**意図した視覚変更が入った commit そのもの**に置いた。
> `923df08` の直後 `37191e1` は docs のみで画素に影響しない。
> `923df08` 以降、Home / Browse 4面の HTML に触れた commit は **0**（実測）。
>
> **再実行結果（基準 `923df08`）: 4面とも 8 PASS / 0 FAIL、合計 32 PASS / 0 FAIL。**
> 初期表示・スクロール後とも差 0。`shelf_propagation_result.json` を新基準で再生成した。
>
> **併せて直したこと**: 基準ツリーのキャッシュを **BASE ごと**に分けた（固定パスだと基準を変えても
> 最初に展開したツリーと比べ続ける。`detail_pixel_proof.py` で 2026-09-06 に起きた事故と同じ形）。
> docstring に **「baseline はテストを PASS させるために HEAD へ動かさない。貼り直しはイタヤ裁定が要る」**
> を恒久ルールとして明記し、`BASE` 定義の直上に貼り直しの履歴と理由を残した。
>
> ### ✅ Launcher の status 整理 ＋ RIG 画像 7枚化（2026-09-07 / 073 / mock `8efcaa1`・`2158000`）
>
> **1. Launcher の stale 表示を是正**（ページ実体・CLOSE 済み設計は触っていない）
>
> - **LOG Detail カード = PC CLOSE**。`card--wip` → `card--done` ＋ `PC` バッジ。
>   説明文「作り直し中（2026-09-06 / Phase 3 / WIP）」→「**確定（2026-09-07 / 正典 067 Gate 5 PASS・CLOSE）**」
> - **`card--state` は独立した進捗状態ではない。** 「代表ページの状態確認デモ」として
>   **代表カードの状態を継承**する。🔴 DOM の位置や「直前のカード」から暗黙推測せず、
>   **`data-state-of="<代表カードの id>"` で明示参照**する（代表カードに id を付与。未解決参照 0）。
>   代表を解決できないときは黙って done にせず wip のまま＝取りこぼしが目に見える
> - 📌 **Feed に残っていた「要確認」2件は未裁定ではなかった。**
>   072 で追加した reaction の**確認用 state カード**（`card--state`）で、
>   旧 `stateOf()` が `card--done` 以外を一律 wip に落としていたための表示上の分類だった。
>   継承後は「確定」、ログ詳細は「PC版のみ」になる。`launcher_link_check` 174 PASS / 0 FAIL
>
> **2. RIG 画像 7枚化**（上記 063 の節に実施内容と Gate 2 再検証を反映済み）
>
> ### ✅ PC Core Convergence Audit → 是正完了（2026-09-07 / 072 / イタヤ裁定 / mock `75fc806`〜`9086794`）
>
> **裁定原本: `_decisions/2026-09-07_core-convergence-v1.md`。**
> 監査レポート・生データ・是正結果は `myrig-mockup` `_state/CORE_CONVERGENCE_AUDIT_20260907.md`。
>
> 対象は PC Core 9面のみ（44面監査にしない）。結論: **壊れは少なく、ズレは共有化の取りこぼしとトークンの未整理。**
> BUG 5 → **0** ／ DRIFT 5 → **0＋保留1** ／ INTENTIONAL 11 ／ TUNING 6（後回し）。
>
> | 是正 | 内容 |
> |---|---|
> | Feed 既定化 | 068 CLOSE の本命案を**既定へ昇格**。fixture `?flow=inline` 撤去。旧 `?flow=inline` と画素差 0 |
> | Shared Source 新設 | Feed カード `<myrig-log-card variant="feed">`（light DOM）＋ `SoT_feed-card.css` ／ コメント ⋯通報 `SoT_comments.js/.css`（Feed 面内会話でも動く）／ `SoT_shelf.css`（Browse 2面の md5 一致 52 行） |
> | 撤去 | 死んだ静的 Feed カード14枚 ／ 旧 shadow `feed` variant（`--cat-parts` を like に流用していた）／ Feed page-local の card CSS 76 行 |
> | entity カード配線 | Core 7面 **257件**（RIG 104 / PARTS 104 / LOG 49）を canonical Detail へ。実クリック着地確認・画素差 0。🔴 Garage 等へ広げていない |
> | contrast | `--color-text-tertiary` light #6a737e（4.81）/ dark #7a838e（4.50）、dark `--color-accent` #1b80f6（4.50）。装飾用途は **`--color-decor-tertiary`（旧値）へ分離**し色不変。「差分＝トークン値だけ」を9面で画素証明 |
> | like pressed | **`--color-like-on`**（like 専用。light #e12f35 / dark #e64c50）を rail / inline / Feed が読む。favorite / pin は現行維持。🔴 `--cat-parts` を流用しない |
> | a11y | Feed に `<main>` / `<h1>` ／ Browse stage の h1→h2 ＋ 面の h1 ／ header active に `aria-current="page"`。画素差 0 |
>
> 🔴 **直さないと裁定したもの: B-5「Feed 右レーン radius 14 vs Detail 9」。** Feed と Detail は役割が異なり、
> 右レーンの見た目まで機械的に揃える根拠がない（071 の原則）。INTENTIONAL 寄りで保留。
>
> 📌 記録: LOG inline の favorite pressed は `--cat-parts` のまま（裁定どおり触っていない。カテゴリ色を操作状態色に使う箇所として残る）／
> `myrig-garage-logs-v6.html` は共有 `feed` variant の実体が変わったため `pc` variant へ写した（Garage バッチで詰める）／
> 🔴 **PC カテゴリ色 v8 の全面展開は別バッチ**（今回の contrast 修正と混ぜていない）。
>
> ### ✅ Browse / Search からの LOG 着地規則（2026-09-07 / 071 / CLOSE・実装変更なし）
>
> **全PC面を実測した結果、LOG カードと RIG カードは全面で完全に同じ href 文法だった。
> LOG 固有の不整合は無い。** → **追加作業なしで CLOSE。**
>
> | 面 | LOG カード | RIG カード |
> |---|---|---|
> | Search v3 | 実リンク → `myrig-log-detail-v1.html`（24） | 実リンク → `myrig-rig-detail-v15.html`（42） |
> | Library RIG / PARTS Master Detail | 実リンク → v1（各5） | 実リンク → v15（各6） |
> | Home / Browse 3面 / Garage 系 / Public Garage / RIG Detail / preview | `href="#"` | `href="#"` |
>
> 着地規則そのものは 067（Search / Library を v1 へ）と 068（Browse / Search / Garage / Related からは
> LOG Detail が正規の受け皿）で満たされている。カードの click 文法も共有部品側で決まっている
> （`SoT_card-components.js`: `href` があればカード全体が `<a>`、無ければ `<div>`）。
>
> 📌 **残っている `href="#"` は LOG の問題ではなく、全 entity 共通の「モックが未配線」という状態。**
> RIG カードも同じだけ死んでいる。**LOG だけ先に配線すると「LOG だけ押せる」非対称を作る**ので、
> **次の PC Core Convergence Audit で entity 横断の論点として扱う。**
>
> ### ✅ Feed のタブを3本に（2026-09-07 / 071 / イタヤ裁定 / #28 は失効）
>
> **裁定原本: `_decisions/2026-09-07_feed-tabs-v1.md`。** 正典 `docs/ui/page-role-matrix-v1.md` §6 を改訂。
>
> | タブ | 何を基準に並ぶか |
> |---|---|
> | おすすめ | **興味・発見ベース** |
> | 新着 | **全公開 LOG の純時系列**（推薦・人気による加工をしない） |
> | フォロー中 | **social graph ベース** |
>
> 🔴 **「現行実装が3タブだから正典を合わせた」のではない。**
> continuity を詰めた結果 **Feed の独立性を再検討し、その結果として裁定を更新した。**
> Feed が独立したトップレベル体験である以上、Feed 内に3つの入口が要る。
> とくに **MVP の「おすすめ」は完全な推薦アルゴリズムではない**ので、
> **加工されていない全公開 LOG の時系列入口を残す価値がある。**
> #28 が退けた「すべて（全投稿時系列）」は X 型の拡散導線の話であり、
> 本裁定の「新着」は**推薦が未成熟な間の素の入口**という別の役割。
> 🔴 おすすめが本格的な推薦になっても**「新着」は残す**。
>
> **実装変更は無い**（PC / Mobile とも元から3タブ・同じ順序・同じラベル）。
> 正典側の「⚠️ PC正本は未適用（3タブのまま）なので参照実装にしない」という注記は不要になり削除した。
>
> ### ✅ DECISION（2026-09-07 / 071 / イタヤ裁定）— Browse / Feed / Library は独立したトップレベル体験
>
> **裁定原本: `_decisions/2026-09-07_toplevel-surfaces-v1.md`。**
> 068 では PROPOSAL だったが、continuity がまとまった区切りで **DECISION へ昇格**した。
>
> **Browse / Feed / Library は「同じコンテンツを別表示する3つのタブ」ではない。**
> それぞれが独立した目的・回遊文法を持ち、**その面だけを使っていても成立するトップレベルの体験面**である。
>
> | 面 | 目的 |
> |---|---|
> | Browse | RIG / PARTS / カテゴリから世界を探索する discovery / exploration の面。**Browse だけでも探索体験が成立する** |
> | Feed | 人・RIG・LOG の活動を時間軸で見る面。読む → reaction → comment → 次の投稿 |
> | Library | メーカー・製品・マスター情報を調べる参照系の面。**Community の簡易版でも Browse の下位ページでもない** |
>
> 🔴 **「独立して成立する」≠「互いに分断する」。**
> MyRIG らしさは、それぞれの世界を壊さずに**自然な文脈で別の世界へつながる**こと。
> 「別の世界へ遷移させること」が主目的なのではなく、
> **今いる面で目的を果たしたうえで、興味が生まれた場所に自然な扉を置く。**
>
> 想定する接続の例:
> Feed → 気になる RIG → RIG Detail → Base Model → Library → 同モデルを使った別 RIG → Browse ／
> Browse → 気になる LOG → LOG Detail → author → Public Garage → Follow → その後 Feed で活動を見る ／
> Library → 製品情報 → その製品を使っている RIG → Community 側へ
>
> ⚠️ **この原則を理由に Browse / Feed / Library の見た目や構造を機械的に揃えない。**
> 目的が違うので **UI 文法は違ってよい**。共通化するのは
> **同じ entity・同じ state / behavior / aria など同一責務の部分だけ**。
>
> 📌 068 の Feed 裁定（本文・コメントを Feed 内で完結させる／LOG Detail は常設の出口だが必須遷移にしない）は、
> この考え方から導かれている。
>
> ### ✅ Gate 5 PASS / CLOSE（2026-09-07 / 067 / GPT の PC 全体横断監査 → イタヤ裁定）
>
> **Phase 3 = LOG Detail PC を CLOSE する。以下6点を DECISION として固定する。**
>
> | # | DECISION |
> |---|---|
> | 1 | **PC Detail Lightbox は MVP では RIG / PARTS / LOG 3面とも未実装のまま維持**（064 の論点 C はこれで決着） |
> | 2 | **PARTS / LOG の OPEN 本文・Quiet Rail を RIG へ自動波及させない**（見た目の機械的な揃えを禁じる） |
> | 3 | **Detail の `dt-*` と `catalog-v6` は見た目まで無理に統合しない**（Gate 5 論点 A はこれで決着） |
> | 4 | **同一責務の state / behavior / aria は Shared Source にする**（052 / §8.13 の再確認） |
> | 5 | **Gallery の1枚時規則は Detail 共有側の責務として維持する**（Gate 5 論点 B はこれで決着） |
> | 6 | 🔴 **LOG Detail のページローカルな微調整へは戻らない**（VISUAL CLOSE） |
>
> ⚠️ **2 と 3 は「揃えない」という裁定であって、4 の Shared Source を緩める根拠にはしない。**
> 揃えないのは**見た目・役割**であって、**同一責務の state / behavior / aria は 1 か所**のままにする。
>
> ### ✅ LOG Detail 導線の機械的修復（2026-09-07 / 067 / mock 側で実施・UX 裁定なし）
>
> **既に LOG Detail へのリンクを持っていて、旧世代 `pc/myrig-log-detail-v6.html` を向いていた 22 箇所だけ**を
> 現行 `pc/myrig-log-detail-v1.html` へ機械的に付け替えた。**新しい導線・click policy は1つも足していない。**
>
> | 面 | 変更 |
> |---|---|
> | `pc/myrig-search-v3.html` | 12 → v1 |
> | `pc/myrig-library-rig-master-detail-v3.html` | 5 → v1 |
> | `pc/myrig-library-parts-master-detail-v3.html` | 5 → v1 |
> | Launcher（`index.html` / `compare.html`） | 既に v1（4件・修正不要を実測で確認） |
>
> **触っていない面（リンク自体が無い。次の continuity batch で実画面裁定する）**
> Feed 14 / Home 12 / Browse 3面 19 / Garage 系 27 / Public Garage 9 / RIG Detail 18 / preview 12 —
> いずれも `href="#"` か href 属性なしで、**Detail 導線を持っていない**。
>
> **v6 の扱い: この時点では削除しない。** active tree に残る v6 文字列は
> **リンク 0 / コメント・ドキュメント 4件のみ**（`pc/myrig-log-detail-v1.html` の注記1 /
> Mobile `log-detail.html` の「正典は PC v6」という記述2 / `_state/HANDOFF_20260907` 1）。
> Mobile 側の2件は **Mobile の正典参照先が旧世代のままという別件**で、Phase 4 の論点として残す。
>
> **検証（実測 / 2026-09-07）**
> `search_regression.py` 101/101 PASS ／ `launcher_link_check.py` 168 checked・0 FAIL ／
> 変更3面の pixel 非回帰（`PIX_BASE=ddcd742`・1440 light / 1280 dark）**揺れで説明できない画素 0** ／
> 3面の LOG カード計 34枚を実ブラウザで解決し、**全て v1 に着地・`.dt-grid` 有り・pageerror 0**。
>
> 🔴 **検査ツールの欠陥をもう1件直した:** `_state/detail_pixel_proof.py` は撮影の一時ファイルが
> `/tmp/s.png` 固定で、別ユーザーが残した同名ファイルがあると **PermissionError で1枚も撮れなかった**。
> プロセスごとの一時ディレクトリへ変更。（前スレッドで直した `PIX_BASE` キャッシュ欠陥に続いて2件目）
>
> ### 🔵 DECISION（2026-09-06 / イタヤ裁定）— Feed と LOG Detail の役割
>
> **ここに固定するのは役割と状態一貫性だけ。UI の配置・表示件数・個別機能の採否は固定しない。**
>
> 1. Feed と LOG Detail は「**簡易版 / 完全版**」ではなく、**用途の違う2面**である
> 2. **Feed = timeline / activity surface**
> 3. **LOG Detail = canonical Garage record surface**
> 4. 同じ LOG の **reaction / comment 状態は Feed と Detail で一貫させる**
> 5. 🔴 **同一責務の state / behavior を Feed 用・Detail 用に複製しない（Shared Source）**
>    → §8.13 `<dt-actions>`（rail / inline = 表示 variant、state / count / aria = 共通契約）
>
> ### 🟡 PENDING（continuity で未裁定なのは **`pin` の扱いだけ**）
>
> 🔴 **`pin`**: schema 上 LOG は pins の対象（`pins` は polymorphic `(entity_type, entity_id)`、
> `page-role-matrix-v1.md` の `/garage/pins` は「ピン留め一覧（RIG/PARTS/LOG/Users）」）。
> **Feed / LOG Detail のどこに UI を出すかが未裁定。** モックが pin を出していないのは検討中の非表示であって
> 「LOG に pin は無い」という正典ではない。`SoT_entity-actions.js` は `data-action="pin"` を扱えるので、
> 裁定が出れば属性を渡すだけ。**ここを根拠に実装を進めない。**
>
> 📌 066 で PENDING にしていた他の項目は **すべて決着済み**（旧箇条書きは 072 で整理・撤去）:
> 続きを読む／Feed 内展開と Detail 遷移／Detail への明示導線／click policy／コメント表示件数／
> quick comment の採否と形式／「さらに ○ 件」／comment・reaction の state 同期 → **068・069**。
> Browse・Search からの LOG 着地規則／Feed 3タブ → **071**。
>
> ### ⚪ OBSERVATION（現行実装の事実。裁定ではない）
>
> - 現行 PC Feed の「**続きを読む**」は `<button class="pc-feed-card__more">`（href なし）で、
>   **Feed 内でその場に展開する**実装になっている（本文は `-webkit-line-clamp: 4`）。
> - 現行 PC Feed のコメントは `<a href="#">` で、**Feed 内に入力欄は無い**。
>   いいね・共有は `<button>`（その場で処理）。
>
> ### 🔵 DECISION（2026-09-06 / イタヤ裁定）— PC Detail の Lightbox は Gate 5 の論点 C へ送る
>
> LOG Detail の写真を expanded media（crop なし・自然比率・縦積み）へ変えた際、
> 「Lightbox は維持」という方針に対して**実体を確認したところ、PC 詳細面には元から実装が無かった**。
>
> | 実測（2026-09-06） | |
> |---|---|
> | 実装**あり** | Mobile（`log-detail.html` / `feed.html` / `parts-detail.html` / `garage-*`）と PC の composer モーダル |
> | 実装**なし** | **PC 詳細面 3面すべて**（`rig-detail-v15` / `parts-detail-v1-open` / `log-detail-v1`） |
>
> 正典側も「未実装として残るのは ImageLightbox と無限スクロール」と記録済みで、実装と一致していた。
>
> **裁定: 今回の LOG Detail バッチでは新設しない（＝未実装のまま維持）。**
> 理由は **Lightbox が LOG 固有機能ではなく、採用するなら RIG / PARTS / LOG の PC Detail 3面共通機能**になるため。
> LOG のバッチで1面だけ先に入れると、また「1面だけ先取り」の非対称を作る。
>
> **Gate 5 の論点 C として判断する。** 採用する場合のみ **Shared Source として3面に実装**し、
> **LOCK / CLOSE 面（RIG v15 / PARTS v1-open）への影響と Gate 再検証**を含めて扱う。
>
> ✅ **決着（2026-09-07 / 067 / Gate 5 CLOSE）: MVP では3面とも未実装のまま維持する。**
> `data-lightbox` 属性は将来フックとして残すが、`cursor:zoom-in` / hover の明度変化は出さない
> （未実装なのに「拡大できる」と約束しないため）。
>
> ### ✅ Mobile ロゴ lockup 化（2026-09-05 / CLOSE / mock `3ab967e`・`231a7e1`・`3a4e1b0`）
>
> PC 承認済みの lockup を Mobile へ展開した小バッチ。**Mobile Header 以外は触っていない。
> Phase 4（Mobile Detail）とは別物。**
>
> | | |
> |---|---|
> | consumer | Mobile 42面中 **5面**（Home / feed / garage / search / user-garage）。残り37面はサブページ型ヘッダーでロゴを持たない。**実効的に描画されるのは3面**（garage / user-garage は `header.mobile-shell-header` が `display:none` で元から 0×0。既存事象） |
> | アセット | `img/myrig_logo_{BK,wh}_v1.2m.png`（397×214）。**PC の単純縮小ではなく Mobile 専用に再構成。** PC 承認済みアートワークのマーク(145px)とタグライン(54px)を一切リサンプルせず、すき間だけ 4px→15px に組み替え、Mobile 従来の「マーク20px・すき間2px」に一致させた（145:15 = 20:2.07）。単純縮小だとすき間が 0.55px になり崩れる |
> | 共有側 | 寸法の正本は `mobile-shell.css` の `.mobile-logo--sm .mobile-logo__img` **1本だけ**。variant は残さず既定へ統合。**重複を減らした**（同責務の `.page-e3` 定義と、死んだ `.mobile-logo__sub` 定義2件を撤去） |
> | 実測 | マーク 19.99px / すき間 2.07px / タグライン 7.44px。ロゴ帯 57.2→54.72px、検索窓が 2.48px 左へ広がる。画素差は**ヘッダー帯のみ**（y=5..54）で **y≧56 の残り 0**、pageerror 0（5面 × light/dark） |
>
> ⚠️ **踏んだ罠（記録）:** 基底 `.mobile-logo__img` の `max-height:28px` は「マーク1枚だけ」の頃の上限で、
> lockup では 28px に頭打ちになりマークが 18.97px まで縮む。**縦に長いアセットへ替えるときは
> `height` と `max-height` を一緒に見ること。**
>
> 🔴 **追補（同日 / イタヤ指摘で是正 / mock `3a4e1b0`）:** 当初 `garage` / `user-garage` の
> `.cw-brand__img` を「Mobile Header の consumer ではない」として対象外にしたが、**実体確認すると
> この2面でユーザーが実際に見るロゴは `.cw-brand` の方**だった（`.mobile-logo` 側は
> `header` が `display:none` で 0×0）。旧 v1.1 のままでは「見た目に出るロゴだけ旧版」になるため揃えた。
> 実測: `garage` [12,12.2,72,26.6] / `user-garage` [12,64.2,72,26.6] とも**可視・ヒットテスト通過**。
> 変更は `src` だけ（CSS 不変）。`.cw-bar` 52px と `.cw-brand` 72×48 は不変、マーク実寸 26.6→26.3px。
> 画素差はバー内のみ、バーより下 0、pageerror 0。
> **これで「画面に出る Mobile ロゴ」は5面すべて lockup 化済み。**
>
> 🔴 **検査の空振りを踏んだ（記録）:** 画素比較スクリプトが `img{visibility:hidden}` を入れていたため、
> **画像だけを変える本件では「差分 0」と出た。** 画像を見せる設定にして測り直した。
> **変更対象を隠す検査は空振りする**（036 の L1「検査が空振りしていないことを検査する」の実例）。
>
> 📌 **残る別責務（触っていない・報告のみ）:** `.cw-brand__img` の CSS は2面に page-local で2コピー。
> 今回は src だけ揃えたので重複は増やしていない。共有化は別バッチの判断。
> 旧 `v1.1` アセットは PC 側の `auth-v1` / `register-*` / `error-states` がまだ参照しているため削除していない。
>
> | Phase | 状態 |
> |---|---|
> | 2-A RIG Detail | ✅ CLOSE（051）。OPEN 型を採用 |
> | 2-B Global Footer | ✅ CLOSE（051）。1 Shared Source |
> | 2-C PROPAGATION-SHELF | ✅ CLOSE（053 / Gate 3 PASS / mock `4c58a03`） |
> | **2-D Home Header ＋ ロゴ lockup 化** | ✅ **CLOSE（056 / mock 3コミット）。** 下記参照 |
> | 2-E Search 証跡 | ✅ **CLOSE（058 / mock `ff8406e`・`13434f0`）。101項目 / 101 PASS / 0 FAIL。** UI は開けていない。下記参照 |
> | **3 LOG Detail PC** | ✅ **CLOSE（067 / Gate 5 PASS）。** `pc/myrig-log-detail-v1.html`。導線は v1 へ機械修復済み。上記参照 |
>
> ### ✅ Phase 2-E の顛末（2026-09-05）
>
> **テスト側の前提誤り2件**（判定内容は1つも書き換えていない）: ① サンドボックスの絶対パス直書きで
> **別セッションでは1項目も走らなかった** → スクリプト位置からルートを導く形へ ②
> `[data-scat="ロッククローラー"]` → `[data-scat="rock-crawler"]`（8/30 の slug 化に追随。
> 表示ラベルは日本語のまま・選択肢24件も不変を実測。pcat は slug 化していないので日本語のまま）。
>
> **実装側の回帰1件**（イタヤ裁定 A で修正 / mock `ff8406e`）:
> `?type=rig&scat=Rock Crawler`（英語名）が解除されず保持されていた。
> 正典の rig_categories 24件は正規 slug で定義され英語名の列を持たない
> （`docs/schema/db-schema-answers-v1.md` L1）。原因は `js/category-taxonomy.js` で
> **24件中 `rock-crawler` だけ**が `name: 'Rock Crawler'` を持ち `toId()` が別名として
> 拾っていたこと（実測でも `Buggy`・`Drift`・`Monster Truck` は解除される非対称）。
> **`name` は `browse-category.html` の英語見出しで表示に使われているのでプロパティは消さず、
> `toId()` 側で canonical URL 入力として拾わないようにした。**
> 英語 alias を正式採用するなら Research 側で24件全体の方針として決める
> （aliases の正本は `master_aliases`）。**App 側が1件だけ先取りしない。**
>
> 📌 **記録（2-E の blocker にはしていない）:** ① ルート `img/2304_mickeyrock_3.webp` が無く
> Mobile `browse-rigs.html` の画像1件が読めない（`pc/img/` には在る。本件と無関係の既存事象）
> ② 正典 `cross-ref-category-names-v4.md` は ai-context main に在るが、**v4 が参照する
> `cross-ref-category-names-v2.md` が ai-context に無い**（ワークスペースの `_archive/` にのみ在る）。
> 今回必要な24 slug は `db-schema-answers-v1.md` L1 にあるため進行は止めていない。**所在整理は別途。**
>
> ### ✅ Phase 2-D の顛末（2026-09-04〜05）
>
> 1. **Header 責務の page-local 撤去**（mock `66d9db9`）: Home にだけ残っていた 22ルールを撤去し、
>    Header の SoT を `SoT_app-shell.css` 1本に。副作用としてタグライン「RC GARAGE」が2.5px動く差分が判明
> 2. **イタヤ裁定「RC GARAGE も含めてロゴ画像にする」**: 2.5px の A/B 照会は論点の span 自体が
>    無くなるため不要に。Home 1面で lockup 画像へ先行差替・**実画面で承認**（mock `208d1e4`）
> 3. **PC 全33面へ展開・既定へ統合**（2026-09-05 / mock 3コミット目）: 展開前に**別のロゴ variant を
>    必要とする consumer が無いことを実体調査で確認**（33面すべて同一 markup）。`--lockup` は
>    試験用 variant として残さず `SoT_app-shell.css` §2.1 の**既定へ統合**。33面すべて page-local
>    ロゴ責務 0・light/dark とも座標完全一致・pageerror 0。詳細は mockup 側 `_state/MVP_CONVERGENCE_MAP.md` §8.9
>
> **新アセット:** `pc/img/myrig_logo_{BK,wh}_v1.2.png`（397×203。マーク145＋すき間4＋タグライン54）。
> dark のタグラインは #FFFF00 原本を **#FBFF00**（v8 `--cat-rig`）へ機械置換（AA が alpha 側のため無劣化）。
>
> **別レポート（Header 責務ではないので触っていない）:** `myrig-auth-v1.html` の `.auth-logo__img`、
> `register-rig` / `register-parts` の投稿モーダル `logo-*-src` data 属性、`error-states` の
> メンテナンス演出 `is-light`/`is-dark` の3か所は、いずれも `.app-header .app-logo` とは別系統の
> ロゴ表示。旧 `v1.1` アセットは今もこれらが参照しているため削除していない。
>
> **Mobile は 2-D の時点ではスコープ外だった**（PC とロゴの比率が違う: PC マーク23px・すき間1px /
> Mobile 20px・2px）。→ **同日の別バッチで専用アセット `v1.2m` を作って CLOSE 済み**（上記 NOW 参照）。
>
> 🔴 **記録 — ルール数を grep で数えない。** 起票時の「17ルール」も引き継ぎの「23ルール」も実測と
> 合わなかった（正しくは撤去前22ルール）。grep が CSS ルールでないコメント行を数えていたため。
> **CSS ルール数はパーサで数える**（`_state/header_propagation_check.py` の `css_rules()`）。
>
> 📌 **`.drawer-overlay` / `.home-dir` の drawer 化も Home に共有と同じ定義が残っているが、
> Header 責務ではないので 2-D では触っていない。** Batch E 以降の判断材料として記録する。
>
> 📌 **記録（Gate 3 CLOSE の非 blocker 整理）:** `detail_contract_check.py` は
> **引数なしだと既定の `pc/myrig-rig-detail-v15.html` 1面だけ**を見る（51 PASS）。
> 監査 bundle でログ（1面）と JSON（4面）が別実行の産物になり件数が食い違った。
> **以後、この検査の件数を報告するときは必ず対象面を併記する。**

### 🔴 MVP 収束フェーズ（2026-09-04 / 049 / イタヤ計画 v1）

**最重要ルール（CORE の Single Source 原則の運用解釈）**

| | |
|---|---|
| **DECISION** | VISUAL LOCK / CLOSE 面は、SYSTEM / PROPAGATION を理由に**見た目を変えない**。共有化で差が出たら「改善」ではなく **REGRESSION として停止** |
| **DECISION** | 意図的な視覚変更は **DESIGN レーン**として切り出し、イタヤ裁定を必須にする。1回開いて閉じる |
| **DECISION** | **全 inline CSS の解消を MVP の前提条件にしない。** ページ固有の見た目はモックをそのまま視覚 SoT として持たせる。減らすのは「同一責務・同一挙動なのに複数面へコピーされているもの」だけ |
| **DECISION** | 孤児・未使用 asset・軽微 cleanup は、Readiness Gate を止める blocker でない限り横道で始めない |

**Phase**

| | やること | やらないこと |
|---|---|---|
| 0 | 048 固定（Gate 4 CLOSE / PARTS LOCK / 旧版撤去 / push） ✅ | 新しい設計作業 |
| **1** | **MVP_CONVERGENCE_MAP 作成** ✅（049） | HTML 変更 |
| 2 | ✅ **2-A / 2-B / 2-C / 2-D / 2-E すべて CLOSE** | Home 等の再設計 |
| **3** | ✅ **CLOSE（067 / Gate 5 PASS）。** LOG Detail PC `v1`。導線も v1 へ機械修復済み。**次は Feed / LOG Detail continuity batch** | 第3の Detail 文法の発明／LOG Detail のページローカル微調整への出戻り |
| 4 | Mobile Detail 3面を1バッチで追随 | PC と Mobile の同時進行 |
| 5 | MVP component hardening（Form 共有境界 / Garage / Library / Mobile の必要箇所） | 全ページの inline CSS 撲滅・Register の再設計 |
| Final | Next.js Readiness Gate → PASS 後はモックを原則 Freeze | 以後の気軽な再オープン |

**Phase 2 着手前の要裁定 3件**

| # | 事項 | 備考 |
|---|---|---|
| ① | ~~RIG に OPEN 文法を入れるか~~ | ✅ **採用（2026-09-04）。** 変更は3点だけ（1節目を `.dt-lead` ／ 残り6節を `.section--flat` ／ 右レーンに `.dt-rail--quiet`）。共有 CSS / JS は1行も変えていない。承認版と現 v15 は pixel 同一を実測。**048 への Override は上記 NOW に記録** |
| ② | ~~Footer の SNS アイコン~~ | ✅ **多数派版を採用（2026-09-04）。** a11y は Detail 版の改善を統合。**実測で言語切替の地球アイコンも2系統に割れていた**ので同じ扱いで多数派へ揃えた |
| ③ | ~~MVP スコープ~~ | ✅ **確定（2026-09-04）。** ◎22 / ○12 / △6。Library Master Detail 2面は Bridge 着地点なので ◎ 維持 |

**Phase 1 で確定した横断部品の現在地（実測）**

| 部品 | 実測 |
|---|---|
| Footer | ✅ **2-B CLOSE。33面が `<site-footer>` 1タグ。直書き 0 / 生成リンク本数は33面で一致**。〈以前: PC 33面 / 7 variant〉。`log-composer-modal` の `modal-footer` はモーダルのアクションバーで責務が違うため対象外。Mobile 42面は `<footer>` を持たない |
| Shelf | ✅ **6面が共有 JS を使用**（詳細2 ＋ Home ＋ Browse 3）。page-local 実装 0。スクロール量は3種（`cards` / `viewport` / 固定 `440`）で**既存の実測値のまま**。統一は横断裁定 |
| Header | ✅ **2-D CLOSE。** PC 35面が `app-header`、詳細2面が `.cx--quiet`。**PC 全33面で page-local ロゴ責務 0**。ロゴは lockup 画像（タグライン「RC GARAGE」を画像に内包）を `SoT_app-shell.css` §2.1 の**既定へ統合**（`--lockup` variant は残していない）。展開前の実体調査で**別のロゴ variant を必要とする consumer は 0件**と確認済み。**Mobile も 2026-09-05 の小バッチで lockup 化済み**（別アセット `v1.2m`。上記 NOW 参照） |
| Detail SoT | ✅ **3面。** `SoT_detail-markup.js` ほかを使うのは RIG v15 / PARTS OPEN / **LOG v1（067 で CLOSE）**。第3の Detail System は作っていない |
| Mobile | 42面すべて同じ共有3ファイルを読む。**共通の inline ブロック4種が28面に重複**（252×10 / 151×8 / 411×7 / 340×3）。Phase 4 でここを出すだけで28面が片付く |
| inline CSS 0 の PC 面 | **2面だけ**（RIG v15 / PARTS OPEN）。これは目標値ではなく現在地の記録 |

> 📌 **スレッドをまたぐときは `_state/HANDOFF_20260825.md` も読む。**
> 本節が「いまどこにいるか」の正本。HANDOFFはそれを補う会話レベルの文脈
> （なぜそうなったか・進め方の約束・踏んだ罠・やらないこと）。HANDOFFは正典ではない。
>
> ⚠️ **HANDOFF の「未コミット一覧」は書いた瞬間から古くなる。git status を正とする。**
> 実例: 2026-08-30 の引き継ぎは `pc/myrig-browse-rigs-v3.html` ほかを未追跡としていたが、
> 同日 09:23 JST の `mock: update`（`64f0099`）で全て commit 済みだった。
> **HANDOFF にファイルの追跡状態を書かない。** 書くのは判断・約束・踏んだ罠だけにする。

### 進行中のレーン

| レーン | 状態 | 次のアクション |
|---|---|---|
| **検索 SEARCH-UPDATE-001** | ✅ **CLOSE（2026-08-25）** | 追加監査・cleanup・改善探索を行わない。軽微/横断は Web文法キューへ |
| **Browse（PC＋Mobile / BROWSE-CONTRACT-001〜003）** | ✅ **CLOSE（2026-08-31 / revision 039）** | **PC・Mobile 両面とも確定。** 4面 × 2レーン = 8面。**ここから先は文言を磨くために構造を開け直さない。** 追加監査・cleanup・改善探索を行わない。次に開けてよいのは §9 のパターン設計トリガー（3つ目のCategory着手時）だけ |
| ~~**Home 実画面レビュー**~~ | ⚫ **失効（2026-08-31 / 040 イタヤ裁定）** | **開かない。** 下記「失効の理由」参照 |
| **RIG詳細 / パーツ詳細 / ログ詳細** | ✅ **RIG詳細 PC = `pc/myrig-rig-detail-v15.html`（046）／ パーツ詳細 PC = `pc/myrig-parts-detail-v1-open.html`（048 / Gate 4 CLOSE）。どちらも VISUAL LOCK ＋ SYSTEM VERIFIED** | **確定。開け直さない。** LOG 詳細は未着手（Gate 5 で Detail 3面の横断整合） |
| **共有UI Single Source 化** | ✅ **A・B・C CLOSE（2026-09-03 / 046）** | 裁定原本 `_decisions/2026-09-02_shared-ui-single-source-v1.md`。C の結果は下記。残るは D / E / F と PROPAGATION 2本 |
| **PROPAGATION-SHELF** | ✅ **CLOSE（053 / Gate 3 PASS / mock `4c58a03`）** | 4面とも behavior parity 一致・画素ごとの視覚非回帰 0。恒久検査 `_state/shelf_propagation_check.py`（`PROP_BASE_DIR` で git 無しでも回る） |
| **PROPAGATION-HEADER** | ✅ **CLOSE（056）** | Home の Header 責務 page-local 22ルールを撤去→SoT を1本化→ロゴ lockup 化を PC 全33面へ展開・既定へ統合。恒久検査 `_state/header_propagation_check.py`。詳細は上記 NOW と mockup 側 Map §8.9 |
| MyRIG Web文法（横断設計） | 🟡 DRAFT v0 作成済み・**一旦停止** | 追加調査・文書拡張はしない。Homeレビューで判断材料が出たら再開 |
| Web文法 実装バッチ1 | ✅ 完了・deploy済み（`054e6e0`） | PC app-nav 90本を実結線 / PCへ未実装route共通handler / Home切替の hidden 破れ修正 |
| モック全体の第2周 | ⚪ 未着手 | ページ単体ではなくフロー単位で確認する体制へ移行 |
| **インフラ／コスト耐性** | 🔵 **3AI 独立検証完了・統合案あり・裁定待ち（062）** | イタヤ裁定 → `_decisions/2026-09-06_infra-cost-resilience-v1.md` を DECISION 化 → docs/schema・support へ反映。裁定前に Next.js 実装へ入らない |
| **画像枚数 7/5/3 のモック反映** | ✅ **完了（2026-09-07 / 073 / mock `2158000`）** | RIG = Cover 1 ＋ Sub 6。`myrig-rig-detail-v15`（重複2枚を撤去）／ `register-rig`（`MAX_SUB_PHOTOS` 8→6・文言）／ `detail_contract_check`（上限 9→7）／ 共有 JS のコメント。Mobile `rig-detail.html` は元から5枚で上限内のため変更なし。PARTS 5・LOG 3 は従来どおり。**Gate 2 再検証 PASS**（証跡は mock `_state/GATE2_rig-detail-v15.md` 追記） |

#### 🔵 レーン運用（2026-09-03 / 045 / イタヤ裁定）

| | 担当 |
|---|---|
| **Cowork（Claude）** | mockup 実体管理・実装・component化・Launcher整合・regression・commit/push・CURRENT / DECISION 更新。**主査** |
| **GPT** | 節目の独立監査（下記 Gate）・設計矛盾チェック・regression の反証確認・共有変更の Impact 監査。**正典は READ 専用** |
| **イタヤ** | 最終裁定 |

**GPT と Cowork が同じ HTML を並行して直す運用へは戻さない。**

**作業レーンを3種に分け、同一バッチで混ぜない。**

| レーン | 中身 | 例 |
|---|---|---|
| **DESIGN** | ページの見た目を作る | RIG Detail r1 → r8 |
| **SYSTEM** | VISUAL LOCK 済みの面を共有部品へ移す | r8 → v15（Batch C） |
| **PROPAGATION** | 共有部品の変更を既存 consumer へ展開する | 棚JS を Home / Browse へ |

**GPT へ監査を戻す Gate**

| | タイミング | 見るもの |
|---|---|---|
| Gate 1 | 045 反映後 | CURRENT / matrix の整合 |
| Gate 2 | ✅ **PASS / CLOSE（2026-09-03 / 046）** | r8 vs v15 の visual / behavior parity |
| Gate 3 | ✅ **PASS / CLOSE（2026-09-04 / 053 / mock `4c58a03`）** | Home ＋ Browse CLOSE 面の非回帰。`_state/GATE3_propagation-shelf.md` |
| **Gate 4** | ✅ **PASS / CLOSE（2026-09-04 / 048 / mock `10c6445`）** | RIG Detail 共通文法との整合・Single Source・variant 漏れ・behavior/a11y・48状態の反証。`_state/GATE4_parts-detail.md` |
| Gate 5 | LOG Detail を VISUAL LOCK する前 | Detail 3面の横断整合。**論点 A**（`dt-*` と catalog-v6 の統合）／**B**（Gallery 1枚規則を catalog-v6 へ移すか）／**C**（**PC Detail 3面に共通 Lightbox を入れるか**）／OPEN 型・Quiet Rail の RIG 波及 |

これ以外でも、**共有部品の変更で複数の LOCK / CLOSE 面の見た目が変わるときは都度 Gate へ戻す。**

#### 🔵 状態語彙（2026-09-03 / 045）

| 語 | 意味 |
|---|---|
| **VISUAL LOCK** | イタヤが実画面を見て「この見た目で行く」と決めた状態。デザインの正本 |
| **MIGRATING** | 共有部品へ載せ替え中 |
| **SYSTEM VERIFIED** | 載せ替え後、visual / behavior parity と pageerror まで通した状態。実装の正本 |

Launcher の本流は原則 VISUAL LOCK 以上を指す。**MIGRATING の版を確定版と混同しない。**
現在（048）: RIG Detail PC `v15`（046）と PARTS Detail PC `v1-open`（048）が
**VISUAL LOCK ＋ SYSTEM VERIFIED**。MIGRATING の面はいま無い。

#### ⚫ 「Home 実画面レビュー」失効の理由（2026-08-31 / 040）

| 日付 | 出来事 |
|---|---|
| 2026-08-20 | **Home を確定へ昇格**（イタヤ裁定「トップページはPC・モバイルともにこれでいい」） |
| 2026-08-25 | revision 033 で CURRENT に「Home 実画面レビュー」の行が入る（**確定より後**） |
| 2026-08-31 | 039 で Browse を CLOSE した際、Cowork が**行の中身を確認せず機械的に「次はここ」へ繰り上げた**。イタヤ指摘で発覚 |

ダッシュボード（`index.html`）の実測でもホームは `確定 1`。**確定済みの面をレビュー待ちとして
持ち続けていた記録上の誤り**であり、Home の設計を再オープンする理由ではない。
**削除ではなく失効として残す**（なぜ以前そう書かれていたかを追えるようにするため）。

🔴 **教訓: レーンを閉じたとき、次の行へ機械的に繰り上げない。** 中身が生きているかを確認する。

### 🔵 インフラ／コスト耐性方針（2026-09-06 / 062 / PROPOSAL・裁定待ち）

**裁定原本（案）: `_decisions/2026-09-06_infra-cost-resilience-v1.md`**
（3AI 独立検証の統合、不採用理由、2026-09-05 時点の公式単価、試算、未確認事項はすべてそこ。
原資料は `_proposals/2026-09-05_infra-cost-resilience_claude-v1.md` と
`_proposals/2026-09-06_infra-cost-resilience_gpt-final.md`）

Claude / GPT / Gemini が同一依頼で独立調査 → GPT が統合 → Claude が整形。**イタヤ裁定前。**
ここは索引。裁定後に docs/ へ反映する差分だけを列挙する。

| # | 種別 | 内容 | 影響先 |
|---|---|---|---|
| 1 | PROPOSAL | MVP 構成 = **Vercel Pro ＋ Supabase Pro(Tokyo) ＋ Cloudflare R2 ＋ Upstash ＋ Turnstile ＋ Custom SMTP**。Cloudflare を Vercel の reverse proxy 前段に置かない（Vercel 公式が非推奨） | implementation_checklist §0・§5 |
| 2 | PROPOSAL | 画像正本を Cloudflare Images Hosted → **R2**。配信課金（$1/10万配信・キャッシュヒット込み・上限機能なし）が成長時の最大変動費 | App_Ready_Design_Rules Rule 5 / checklist Phase 3 |
| 3 | PROPOSAL | DB は完全 URL ではなく **`storage_key`** を保存し、描画時に配信ドメインと合成 | schema v1_6 `images.url` / `thumbnail_url` / `profiles.avatar_url` / `cover_image_url` |
| 4 | PROPOSAL | バリアントはアップロード時に固定種を一度だけ生成。リクエスト時の動的変換を基本にしない（蓄積画像 × バリアント数に比例して Hosted より高くなり得る）。生成場所は実装時に決定 | checklist Phase 3 |
| 5 | PROPOSAL | 可搬性原則: Vercel 固有 API を正本にしない／provider ヘッダは request-context 層に閉じる／rate limit store を抽象化／移行時点の Cloudflare 公式推奨方式で Workers ビルド可能な状態を維持。**アダプタ名（OpenNext / vinext）・移行金額は固定しない** | App_Ready_Design_Rules |
| 6 | PROPOSAL | 非機能要件: **多段 degraded mode**（normal / economy / restricted / readonly / frozen）。緊急状態の権威を **Supabase に置かない**。Vercel 期 = Upstash。取得不能時は READ = economy・WRITE = restricted で fail closed（normal へ戻らない）。frozen は Cloudflare DNS 切替の独立レバー | docs/support 新規 |
| 7 | PROPOSAL | DB 負荷是正を**ベンダー変更より先に**: `view_count` 直接 UPDATE → buffer＋batch／like・favorite・comment 件数 → counter 列／Feed は cursor・1 クエリ・N+1 禁止・無限 polling 禁止／Search は MVP では Postgres のみ／Realtime 中心 Feed 不採用 | schema v1_6「統計カウントの方針」 |
| 8 | STATE | 試算・監視では **Total MAU と Authenticated MAU を分離**。Auth 率を正典で固定しない（試算時のみ Base 40% / Heavy 70% / Stress 100%。公開後は実測へ） | — |
| 9 | STATE | Provider 側の停止装置: Vercel Spend Management（pause）／Supabase Spend Cap ON（compute・PITR は対象外）／Upstash max budget。**Cloudflare は通知のみ** → アプリ側 rate limit / kill switch で防御 | checklist §5 |
| 10 | STATE | 財務ガードレール: インフラ予算は「実現済み収益（trailing 3 か月）」と「イタヤが明示した自己負担上限」の**小さい方**に拘束。割合・金額は運用値で固定しない。spend limit の引き上げは収益実績かイタヤの明示承認なしに行わない | — |
| 11 | HOLD | Auth 基盤の変更は **Supabase Auth quota の 80% 到達で再審査**（継続／Spend Cap 解除／移行）。「8万」等の数値は固定しない | — |
| 12 | REJECT（恒久ではない） | Cloudflare D1（10GB 上限・SQLite・RLS 非互換）／Neon（東京なし・hard cap なし）／自前 VPS（ソロ運用負荷）／Cloudflare Images Hosted／**Vercel Hobby（商用不可）**／MAU 課金型 Auth SaaS／外部 Search SaaS／Realtime 中心 Feed | — |
| 13 | 要確認 | Vercel / Supabase の**使用量取得 API**。取れない場合 degraded mode の判定はプロバイダ通知を受けた手動運用。「自動縮退」と書く前に実測 | — |

**このフェーズで守ること:** 数値（単価・閾値・金額）は CURRENT に固定しない。裁定原本へ日付付きで残す。

### 🔵 詳細3面と共有化の実行順序（2026-09-02 / revision 041 / DECISION）

**裁定原本: `_decisions/2026-09-02_shared-ui-single-source-v1.md`**（理由・棚卸し実測はすべてそこ）

**DECISION — 共有UI Single Source 原則を CORE に L1 で追加した。**
横断UIは2面目へ展開する段階で共有部品へ昇格し、以後 page-local に markup / style / behavior /
accessibility state を複製しない。探索中の page-local 試作は許容。実装方式は固定しない。

**DECISION — Header の Single Source 化を PARTS / LOG Detail 着手前に行う。**
現在形は cx（P22-C7）。先にやらないと新規Detail 2面で cx の21・22面目のコピーが生まれる。

**DECISION — Detail 右レーンは部品箱にする。** 候補6部品:
`Rail Section Shell / Builder / Entity Actions / Share / Library Bridge / Feed・Shelf List`。
Entity Actions は `aria-pressed`・件数・icon line/fill・focus・状態遷移まで含む。

**DECISION — 旧コードは Git 履歴を退避先とし `_archive` へ複製しない。**
CORE「物理DELETE禁止」は DB恒久ルール節のもので、Git管理コードには適用しない。

| | バッチ | 入口 | 出口 |
|---|---|---|---|
| A | RIG Detail 確定 | ✅ **完了（2026-09-03 / r8 をイタヤ確定）** | 右レーンだけでなく PC 版 RIG Detail 全体を確定 |
| B | Header Single Source 化 | ✅ **完了（`9aacdc8`）** | チェッカー 214/201 FAIL 0・56面 pageerror 0・**CLOSE/現在形6面 pixel parity 0px**（feed light の2pxは同一ツリー再撮影でも出るキャレットノイズ）・cx 7挙動同一 |
| C | Detail 部品箱新設 ＋ RIG Detail 載せ替え（r8 → `v15`） | ✅ **CLOSE（2026-09-03 / 046 / Gate 2 PASS）** | 下記「Batch C 結果」 |
| D | PARTS / LOG Detail 新規 | ✅ **PARTS = CLOSE（2026-09-04 / 048 / Gate 4 PASS）**。LOG は未着手（Gate 5） | 達成: inline style/script/style属性 0・骨格も共有（`SoT_detail-markup.js`）・page-local の部品コピー 0 |
| E | Footer / Garage `pit-*` / 未使用資産の掃除 | 実体再確認 | — |
| F | PC v8 横断 | 独立 | 両テーマ実測・`--cat-*-on` 取りこぼし0 |

A ∥ B → C → D。E・F は独立。PROPAGATION-SHELF / -HEADER は C とは別レーン（上表）。

**✅ Batch C 結果（2026-09-03 / 046 / Gate 2 PASS・CLOSE / mock `ec3398c`）**

新設した共有4本（**実装の正本。手で保守する。**変更したら `_state/detail_contract_check.py` を通す）

| ファイル | 中身 |
|---|---|
| `pc/assets/css/SoT_detail.css` | Grid / Identity / Gallery / Section Shell / Comment Conversation / Related Zone / 棚（169ルール） |
| `pc/assets/css/SoT_detail-rail.css` | 右レーン標準プリセット（210ルール） |
| `pc/assets/js/SoT_detail-components.js` | Gallery / Entity Actions / Comment Modal・⋯ / Follow / Share / Base Model thumb |
| `pc/assets/js/SoT_shelf.js` | 横スクロール棚。`[data-shelf]` で拾う。スクロール量は `cards`（Home 由来）と `viewport`（詳細由来）の2種 |

`pc/myrig-rig-detail-v15.html` — page-local の `<style>` 0 / インライン `<script>` 0。
版番号入りクラス（`v14r3` / `v14r4` / `v14r5` / `r7` / `r8` / `dg`）を Detail 恒久語彙へ改名し、
**死んだ規則 102セレクタ**（`discover-*` / `r7-*` / `*-v10` の旧世代）を除去した。

**出口条件（Gate 2 CLOSE 時。mock `ec3398c`）**

| | 結果 |
|---|---|
| visual | 1440 / 1200 / 1000 / 900 / 700px **× light・dark = 10条件すべて pixel diff 0**（同一ツリー2回撮影のノイズ床と比較して判定） |
| behavior | Gallery 9枚・Entity Actions 3種の状態遷移と件数・Comment Modal / reply / ⋯ メニュー・Follow・棚のスクロール量・a11y state |
| pageerror | 0 |
| 反証 | v15 の `<style>` / インライン `<script>` / inline style 属性 / 版番号入りクラス・id すべて 0 |
| `_state/detail_contract_check.py` | **31 PASS / 0 FAIL / 0 WARN** |
| 故障注入 | parity 10 FAIL / contract 1 FAIL（いずれも意図どおり検出） |

**DECISION — 044 の `data-widget` / `data-section` を markup へ降ろした（Gate 2 再監査）。**
r8 の時点で未実装だったものを C の最後で入れた。**順序の正本は DOM 順のみ。`data-order` は持たない。**

| | 識別子（DOM 順） |
|---|---|
| 右レーン | `builder` / `entity-actions` / `base-model` / `entity-feed` / `ad` / `external-links` / `builder-rigs` / `used-parts` / `ad` |
| RELATED | `same-model-rigs` / `model-used-parts` / `model-logs` / `category-rigs` / `manufacturer-rigs` |

命名は**責務そのもの**。表示タイトルや具体的な車種名を入れない（データが変われば嘘になる）。
`entity-` は「その詳細ページの対象そのもの」（RIG 詳細では RIG、PARTS 詳細ではパーツ）。
**責務が同じなら PARTS / LOG でも同じ識別子を使い、違う責務なら別 ID を足す。**
全 Detail が同じ widget 一覧を持つ必要はない。

**DECISION — 044 の「既存の `.shelf` / `.cmt-modal` を拡張する」は C では見送った。**
`SoT_component-catalog-v6.css` の同名部品は Detail 用ではなく（`.shelf__rail` は `gap:16px` と
`--page-side-padding` を持つ）、その名前を付けると **確定済みの見た目が動く**。
衝突しない `dt-*` を名乗り、**catalog 側との統合は Gate 4（PARTS Detail 着手前）で裁定する。**
挙動だけは先に共有した（`SoT_shelf.js` はクラス名ではなく `[data-shelf]` 属性で拾う）。

**DECISION — Header の白枠「＋投稿する」は見た目を変えず、定義だけ Header SoT へ移す。**
調査の結果、これは v12 で「★暫定。実画面で裁定する」と書かれたまま残った探索差分で、
Detail だけ変える理由は文書に無い（＝ accidental drift）。ただし **その状態を含む r8 を
イタヤが実画面で見て VISUAL LOCK している**ので、SYSTEM 移行で勝手に戻さない。
置き場所は `SoT_app-shell.css` の **`.cx--quiet`** variant（Gate 2 監査 #2）。
詳細面は `class="cx cx--quiet"` で **variant を選ぶだけ**にし、
**ヘッダーの見た目の SoT は `SoT_app-shell.css` 1本**に保つ。宣言と順序は r8 の原文のまま。
**永久仕様として正典化したわけではない。** Header 横断の見直しは別の DESIGN / PROPAGATION 裁定で行う。

**✅ 撤去済み（Gate 2 CLOSE / 2026-09-03）** — 履歴は Git。**`_archive` へ複製しない**
- `pc/myrig-rig-detail-v14r5-complete-r8.html`（デザインの正本としての役目を終えた）
- `_state/build_detail_system.py` / `_state/detail_parity.py` / `_state/parity_out/`
  （r8 を入力に取る移行専用ツール。**r8 を恒久的な生成元として残さない**）

**🔴 残す検査: `_state/detail_contract_check.py`（r8 非依存）。**
詳細面（v15・将来の PARTS / LOG）を変更したら必ず通す。見るのは
「共有部品を使っているか」「Detail の文法を満たしているか」で、特定の見た目は見ない。
引数でページを足せる。故障注入は `--selftest`。

**移行期の並存（CORE L1 の様式で明記済み。残り1件）**
- Home ＋ Browse PC 3面の page-local 棚 = `successor: SoT_shelf.js / remove after: PROPAGATION-SHELF parity PASS`
- 確定済み4面へ入れたのは**コメントのみ**。HEAD 版との pixel diff 0 を実測（差分はいずれも同一ツリー再撮影のノイズ床と一致）

**✅ Batch B 結果（2026-09-02 / 043 / mock `9aacdc8`）**
- 共有化: `SoT_app-shell.css` に cx 25ルール＋app-nav 5ルール＋ダーク地、`SoT_app-shell.js` に `initCreateMenu()`、テンプレも cx へ
- 除去: cx採用20面から inline の cx CSS / cx JS / app-nav override / ダーク地（−72〜−77行/面）
- 移行: 旧 create-soft 23面 → cx（garage 9 / library 7 / public-garage 4 / parts-detail / log-detail / error-states / support-us）
- 残存: create-soft markup は `myrig-rig-detail-v6.html` の1面のみ（母体保護・Legacy 許容。共有CSS側に DEPRECATED / successor / remove after を明記）。
  `.app-header .cx__btn` の page-local 上書き9件（rig-detail v12〜v14r6）は A のデザイン探索差分として残置（→ 044: 当該14面は除去済み。確定版 r8 にも同種の上書きがあり、C で共有側へ吸収するか判断）
- 判断: `--cat-*-on` に fallback（`#151515` / `#fff`）を付与（v8未宣言23面で文字色が継承落ちするため。宣言済み6面は不変）/
  home の死んだ create-soft override 14ルールを除去 / error-states の停止状態ルールを `.cx` へ追従
- 🔴 事故: 除去スクリプトが `@media` 内を二重出力しインラインCSSを破壊（search で+123行）＋バックアップ除外パターンが
  `garage-rig-detail-v6` にも部分一致し1面が未バックアップ。**pixel parity を出口条件にしていたため検出**。
  42面はバックアップ、1面は Git HEAD から復旧し、中括弧収支の不変を assert して再実行。
  教訓: 一括書換えは「同一ツリー2回撮影のノイズ」と「before→after」を分けて測る。バックアップの除外は完全一致で書く

**棚卸し実測（2026-09-02 / PC 56面）:** インラインCSS 33,291行 vs 共有CSS 4,064行（1:8）。
cx は共有側に無く page-local 20面複製。`sot-templates/` は include機構なし。
v6 のRIG詳細サイドバーは `catalog-v6` の共有部品だったが、v9〜v14 でインライン化された。

**🔵 PENDING**
- ~~Detail 部品箱6つの境界~~ → ✅ C で確定（`SoT_detail-rail.css` の Rail Section Shell / Builder / Entity Actions / Share / Library Bridge / Feed・Shelf List ＋ AD slot）
- **Detail の 棚 / Comment / Modal が catalog-v6 の同名部品と2系統ある** → Gate 4（PARTS Detail 着手前）で統合を裁定。いまは `dt-*` と `catalog` が別物として並ぶ
- 個別旧資産の失効（`sot-templates/` 7本 / 未使用 Web Component 5個 / `SoT_skeleton.css` / `browse-sidebar.css` 新旧）→ **リポジトリ全体で実体再確認後**。棚卸し値だけで確定しない
- **PC v8 の実装状態と `color-token-v8.md` の不一致** → 正典は「PC未適用・部分適用禁止」だが、実モックは Home / Feed / Search / Browse 3面が page-local `:root` で v8 済み、残り23面は v7。推測で解消せず F で裁定
- `--color-accent-hover: #A86F1F`（茶）が青の hover として不整合 → F

### 🔵 Mockup State Audit（2026-09-03 / 045 / OBSERVATION）

**実測表: `_state/mockup-state-matrix.md`**（Page Visual Canon / Shared Component State /
Launcher の3層を1枚にした資料。**正典ではない。現在の実装状態を上書き更新する用途**で、
履歴を積み上げない。表示件数・余白・棚の順番のような UI 調整は CURRENT にも matrix にも固定しない）

C の着手前に、実装が正典より先行していないかを READ のみで棚卸しした。結果の要点だけ:

| 分類 | 件 | 扱い |
|---|---|---|
| **BLOCKER**（C 開始前に処理） | 4 | 044 未push / Header の扱い / Launcher が v6 を指す / Shelf の影響範囲 → **すべて 045 で処理済み** |
| **REGRESSION**（確定面の整合確認が要る） | 4 | Home ロゴ 2px → PROPAGATION-HEADER（✅ **2026-09-05 解消。ロゴ lockup 化で PC 全33面へ展開・CLOSE**） ／ Search の CLOSE 後 taxonomy 変更 → 回帰再実行 ／ Home 8/26 と Mobile Home 8/30 の記録欠落 → **記録として残す。Home は再設計しない** |
| **CANON-STALE**（実装が正しく CURRENT が古い） | 2 | PC Feed の3タブ（下記）／ Batch B parity の証跡 |
| **SEPARATE-BATCH** | 5 | Footer r8 独自 markup → E ／ `SoT_app-shell.css` の PC⇄Mobile 乖離 179行 → E ／ 孤児2件 → E ／ 404 3件 → E ／ PC v8 → F |

**CANON correction — PC Feed は3タブが実装済み。** モック是正キュー B-1 の
「PC Feed が #28 未適用（All/Following/Trending の3タブ）」と 2026-07-30 Active Override
「FEED文法はモバイル基準。PC版を差し替える・実装待ち」は**失効**。実装は
`pc/myrig-feed-v3.html` で おすすめ / 新着 / フォロー中 の3タブ＋`?tab=` 同期まで入っている。
**未実装として残るのは ImageLightbox と無限スクロールのみ**（実測 0件）。実装を戻さない。

**Search の CLOSE 後変更（記録）:** 8/30 に `js/category-taxonomy.js` を入れて
`scat` の語彙を日本語ラベルから slug へ移した（PC / Mobile 両面）。内部語彙の変更であって
仕様変更ではない（表示ラベルは日本語のまま・選択肢24件も不変を 2026-09-05 に実測で確認）。
✅ **2026-09-05 / Phase 2-E で証跡を復旧し CLOSE。101項目 / 101 PASS / 0 FAIL。**
テスト側の前提誤り2件を直し、実装側の回帰1件（英語名 `Rock Crawler` が解除されなかった件）は
イタヤ裁定 A で修正した。詳細は上記 NOW の「Phase 2-E の顛末」。

### ✅ PARTS Detail PC 確定（2026-09-04 / 048 / `pc/myrig-parts-detail-v1-open.html` / Gate 4 PASS・CLOSE）

Batch D（DESIGN レーン）。RIG v15 を共通土台に、イタヤと実画面で ① Identity → ② Library 導線 → ③ 装着RIG → ④ HISTORY → ⑤ 本文 → ⑥ RELATED の順で決めた。
PNG 案ではなく **1本の HTML を直接育てる**方式（イタヤ指示）。
**VISUAL LOCK ＋ SYSTEM VERIFIED。Gate 4 は 3回 HOLD ののち PASS / CLOSE（監査対象 mock `10c6445`）。**
依頼書と全記録は `_state/GATE4_parts-detail.md`（mockup 側）§8〜§11。

**Gate 4 で決着した SYSTEM の要点（DESIGN の指摘は最後まで 0 件）**

| | |
|---|---|
| 指摘 | CSS / JS は共有でも **DOM 骨格が各 HTML に複製**されていた（CORE L1 は markup も複製禁止） |
| 対応 | `pc/assets/js/SoT_detail-markup.js` を新設。`railSection()`（Rail Section Shell）＋ 12 の宣言タグで骨格を1か所へ。**ページが持つのはデータ・widget ID・variant 選択・配置だけ**（v15 776→453行、PARTS 683→420行） |
| 証明 | `297323d` との **rendered DOM 完全一致**（要素間空白除く・要素数も同数）。視覚非回帰は総量比較をやめ **画素ごとの判定**（差のある画素 − 同一ツリー内で揺れる画素 = 0） |
| 恒久チェッカー | `detail_contract_check.py`（骨格・a11y・契約 246項目）/ `detail_dom_parity.py`（DOM 等価）/ `detail_pixel_proof.py`（画素ごとの視覚非回帰）。いずれも故障注入で FAIL することを確認済み |
| 撤去 | 箱型 `myrig-parts-detail-v1.html` / 旧 `myrig-parts-detail-v6.html` / 使用面 0 の `.dt-identity__eyebrow`。Search の内部リンク 21本を本線へ |
| 送り先 | 論点 A（`dt-*` と catalog-v6 の統合）と B（Gallery 1枚規則を catalog-v6 へ移すか）は **Gate 5 で実施可否を決める**。OPEN 型・Quiet Rail の RIG への波及も Gate 5。**2026-09-06 に論点 C を追加: PC Detail 3面に共通 Lightbox を入れるか**（下記 NOW 参照） |

**イタヤ裁定（D の期間中。LOCK 前だが方向は確定）**

| 項目 | 裁定 |
|---|---|
| Identity | **製品名が主役**。メーカーは上段（RIG と同じ cat-badge / maker / category の並び）、型番は直下（`.dt-identity__code`）。メーカー＋製品名を1行に連結しない。取得日・@handle・スケール・素材は Identity に置かない（SPEC の責務。**Identity を可変 SPEC 置き場にしない**） |
| Gallery | Cover 1 ＋ Sub 4 = **最大5**。共有 Gallery を使う。**写真1枚ならメイン画像のみ**（サムネ列・矢印・1/1 カウンタを出さない。共有側 `SoT_detail.css` で1枚状態だけ正常化、複数枚は不変） |
| 本文 | **OPEN 型を本線**（見出し無しのリード → 箱なし4節「見出し＋細い罫線＋内容」→ 箱型コメント）。本文タイポ1段上げ、Photo Note caption 可読、罫線は subtle より1段だけ。太線・色帯にしない |
| 右レーン | **Quiet Rail**（`.dt-rail--quiet`。箱は残しヘッダー帯だけ静める）。共有 CSS に variant として定義し page-local にしない。**RIG へ波及させない**（Gate 5 以降） |
| 本文の節 | 製品情報（Library 参照・Dual Path）／ 登録情報（parts の実フィールドのみ。**数量は無い**。装着位置は `rig_parts.note`）／ 状態＝`parts.condition`（new/used/modded）と装着状況を分ける ／ 装着RIG ／ フォトノート。**LOG の節は持たない** |
| entity-feed | `maintenance_logs` に `part_id` が無い → PARTS のフィードは **登録・装着のみ**。「装着RIGのLOG」を「このパーツのLOG」と見せるのは禁止。「交換」は根拠がある場合のみ |
| RELATED | 4棚: `product-used-by-rigs` ／ `category-parts` ／ `manufacturer-parts` ／ `category-popular-parts`（近い2 → 広告 → 広い2）。**`co-used-parts`（一緒に使われているパーツ）は削除**（説明なしに意味が伝わらず「一緒＝相性・適合」と誤解される） |
| 本文「このパーツを使っているRIG」と RELATED「この製品を使っているRIG」 | **両方持つ。** 前者＝このページの登録パーツに近い情報 ／ 後者＝製品マスター単位・コミュニティ全体の使用例 |
| 画面の補助文言 | **ユーザーに実データ・状態を伝える文は残す**（件数・装着中・閲覧数・導線・公開範囲と通報の案内）。**モックの構造や実装意図を説明するだけの文は画面に出さない**（判断理由は HTML コメント側）。aria-label 等の非表示文言は画面 copy と別に、実挙動と整合するよう Cowork が管理する |
| 空状態 | 写真 0 ／ 外部リンク 0 → **節ごと消す**。装着RIG 0 → **空状態を出す**（「どこにも付いていない」は状態そのもの） |

**やらないこと:** 推測による相性・互換性表示 ／ スキーマに無いフィールド（数量・装着位置フィールド）の発明 ／ OPEN・Quiet Rail の RIG への波及 ／ Gate 4 での新 UI 案。
**検証:** contract 165 PASS / 0 FAIL / 0 WARN（5面）、48状態 OK、v15 は `ec3398c` 比で CSS 由来の差 0（依頼書 §3）。

### ✅ RIG Detail PC 確定（2026-09-03 / 044 / `pc/myrig-rig-detail-v14r5-complete-r8.html`）

GPT との実機確認・修正を経てイタヤ裁定。**ここからデザインを再検討しない。** 実装・共有化上の問題だけ報告する。
r8 の中身すべてが永久固定ではない。固定したのは **役割・階層・レイアウト文法・組み替え可能にする境界**。

**構造:** 全幅 Identity → Gallery → 7:3（左＝RIG本体 / 右＝補助・アクション・回遊・収益）→ RELATED → 共通 Footer。
左本文8節: このRIGについて / フォトノート / RIG情報 / ベースモデル / ビルド詳細 / LOG / 使用パーツ / コメント（本文の終点）。

**DECISION — Gallery は Cover 1 ＋ Sub 最大6（合計最大7）。Photo Notes は Sub が対象で最大6。** この対応関係を崩さない。

> 🔴 **2026-09-06 / 063 で改訂。** 044 の原文は「Cover 1 ＋ Sub 最大8（合計最大9）／Photo Notes 最大8」だった。
> **失効。削除ではなく記録として残す**（なぜ以前 9 だったかを追えるようにするため）。
> 改訂理由は上記 NOW 節。Cover : Sub : Photo Notes の**対応関係は不変**で、Sub の本数だけ 8 → 6 になった。
> ⚠️ **上の「Gate 2 出口条件」表の behavior 欄「Gallery 9枚」は 2026-09-03 時点の検証記録であり、書き換えない。**
> 7枚化のバッチで Gate 2 を再実行したら、その時点の結果として別途記録する。

**DECISION — 右レーンは現在構成を「標準プリセット」とし、将来 Widget Stack（追加・非表示・並び替え）にできる境界を維持する。**
標準プリセット: ビルダー / このRIGへのアクション / ベースモデル / このRIGのフィード / AD / このRIGの外部リンク / このビルダーの他のRIG / このRIGで使われているパーツ / SPONSORED。
識別子は `data-widget`。**順序は DOM 順が唯一の正本。`data-order` は持たない**（二重管理を作らない）。管理画面・保存 schema は着手時に設計。
「このRIGのフィード」は LOG に限らない RIG 中心の Activity。外部リンクは登録が無ければ非表示にできる前提。

**DECISION — コメントは Detail 共通の Conversation として扱う。** ページ内は件数明示＋数件プレビュー＋「書く／すべて見る」。全会話・返信・投稿は Modal。各コメントに「⋯」→「通報する」。投稿はログインユーザーのみ。**moderation / report の backend はこの作業で確定しない。**

**DECISION — RELATED は「近い関連＝Detail 幅」→「広い発見＝フルワイド・高密度」の二段階文法。** 具体的な棚の内容・順番・件数は固定しない（データ量・運用・収益化で後から変える領域）。識別子は `data-section`、order 番号は持たない。

**DECISION — 画鋲の正典は `_ic_pin_push`（Home のカードと同じ塗りアウトライン）。** I-1 で採用、異議なし。C03 カタログ・詳細 v6 系の C03 stroke 版は移行対象。

**失効（記録）:** 「同じパーツを使っているRIG」棚は RIG Detail から撤去（PARTS Detail 側が意味的に適切）。コメント後にあったビルダーの RIG/パーツ/LOG タブ棚も撤去（右レーンのビルダー導線と重複）。ピン留め件数の公開表示は pins 完全非公開（L1）に従い非表示、本人の状態のみ。ベースモデル枠は中立面のまま、購入先＋ショップ群だけを Commerce 領域として囲み PR 明示。

**Batch C（2026-09-03 / 045 で実装完了。計画からの変更点）:**
- 共有4ファイルは計画どおり新設。**C-4「Home の棚JS 切替」は C から外して PROPAGATION-SHELF へ**（045 イタヤ裁定）。
  Home / Browse は確定済みなので、SYSTEM 作業と同じバッチで触らない
- **既存部品への統合は見送り**（上記 DECISION）。Gallery は既存 `.gallery` のまま、
  棚・Comment・Modal は `dt-*` で新設し、統合は Gate 4 で裁定する
- ~~C-6「r8 除去」は Gate 2 PASS 後~~ → **Gate 2 CLOSE で撤去済み（046）**
- **Footer は C のスコープ外（Batch E）。Header（B）も再オープンしない。** どちらも守った
- 実施: C-1 共有4ファイル → C-2 v15 → C-3 parity/behavior → C-5 反証 → 報告・停止（Gate 2）

**I-1 完了（2026-09-03）:** ガレージ8面＋テンプレ＋カタログG02 の お気に入り（ハート→星）/ ピン留め（星→画鋲）を修正。garage-top SAVED 見出しの文字グリフを SVG へ。Library パーツマスターの「ピン数 203件」を削除（pins 非公開。3項目へ）。
**旧 RIG Detail 候補14件（v9a〜v14r6・concept 2本）を active tree から除去。** 履歴は `9aacdc8`。`_archive` へは複製しない（041 裁定）。

**ライブ**: `myrig-mockup` = `6d50c96`（2026-09-04 push 済み。origin/main と一致を実測）。**未 push は mockup 側のみ（`mockup` を回すと反映）。** Gate 3 の監査対象コードは `4c58a03`（CLOSE 済み）。
> push したらこの行を実値へ。SHA を書く場所は依頼書と本行の2箇所だけ。
> **SHA を書く場所は依頼書と本行の2箇所だけ**（2026-09-03 の Gate 2 で、本文と依頼書で SHA がずれた）。
> この行は `mockup` を回すたび古くなる。**モック側を push したら CURRENT のここも更新する。**
> 2026-08-30、`64f0099` のまま放置していて「後続セッションが古いモックを現在地と誤認する」
> 状態になっていた（イタヤ指摘）。
スクリーンショット撮影は不要（2026-08-25 以降、イタヤが直接ライブを見る運用）。

> ⚠️ `myrig-mockup` は **private repo**。Cowork のサンドボックスからは
> credential helper が無く `git fetch` / `ls-remote` が通らない（`could not read Username`）。
> ローカルの `origin/main` ref は最後に手元から push された値であって、
> **リモート最新であることの証明にはならない。**
> モック側へ WRITE する前は、イタヤの手元（Mac）で fetch → HEAD 比較を行うこと。
> `myrig-ai-context` は public なので Cowork からも fetch / ls-remote が通る。

### 🔵 MyRIG Web文法（2026-08-25 / DRAFT）

**`myrig-mockup/docs/WEB_GRAMMAR_DRAFT_v0.md`（NOT CANON）**

Searchで確立した規約のうち「横断で意味があるもの」だけを他面へ広げるための分類作業。
Route / Navigation / Context・Permission / State・URL / Interaction / Shared UI の6本柱。
Route・Navigation の観測値は **GPT独立実測を確定入力**とし、Cowork実測と主要結論が一致。

**正典ではない。ここに書いてあることを根拠に設計を固定しない。**
正典昇格は項目ごとに、実装バッチと対で判断する（CORE「正典化判断基準」）。

現状: CONFIRMED 4 / GAP 17 / HOLD 1 / **未裁定（USER_DECISION）は 0**。

**モック全体の性質（棚卸しで判明）**: ページ単体は作り込まれているが、横につなぐ規約が無い。
同じ部品が面ごとに別実装、同じ概念が面ごとに別語彙。
Mobileは単一shellで歩けるが、**PCは40画面中28が行き止まり**だった（バッチ1で解消）。

**確認URL**: PC `https://myrig-mobile-mock.vercel.app/pc/myrig-search-v3.html`
Mobile `https://myrig-mobile-mock.vercel.app/search.html` / `search-results.html?q=TRX-4`

### ✅ 検索 CLOSE（2026-08-25）

- 最終回帰 **101項目 全PASS** / JS・pageerror **0**
- production deploy 済み
- **GPTによるライブ独立監査 PASS**
- 以後、Searchモックの追加監査・cleanup・改善探索は行わない。
  軽微事項・横断課題は `myrig-mockup` の `docs/WEB_GRAMMAR_QUEUE.md` へ送る
- 回帰スクリプト: `myrig-mockup` の `_state/search_regression.py`

**モック全体の棚卸し（2026-08-24 A/B/C/D）で分かった重要な前提**:
モックは長らくページ単体で作り込まれており、横につなぐと
「URL語彙が違う」「同じボタンなのに意味が違う」「PCだけ歩けない」「SoTが二重化」
といった亀裂が出る。今後は**ページ単位ではなくフロー単位**で詰める。

### 🔴 検索の骨格が変わった（2026-08-24 / SEARCH-UPDATE-001）

**標準検索 = MyRIGコミュニティの投稿（RIG/パーツ/LOG）を探す面**に再定義した。
**裁定原本: `_decisions/2026-08-24_search-community-scope-v1.md`**（旧→新の対照と失効理由はすべてそこ）

| | 旧 | 新 |
|---|---|---|
| 対象 | 5種（＋製品情報・ユーザー） | **Community 3種のみ** |
| 「すべて」 | 5種の29件ダイジェスト | **3種を関連度順で混在** |
| 種別 | 独立ページ | **同一ページ内のフィルター状態** |
| PC | 非ブレンド | **Mobileと同一のブレンド** |
| 製品情報 | 結果に混在 | **Libraryへの補助導線のみ**（件数非合算） |
| ユーザー | 結果に混在 | **MVPでは検索入口を置かない**（2026-08-25 更新。旧「ビルダー入口へ」は失効） |

失効した旧裁定: ②PC非ブレンド / ④答えカード / ⑦⑧すべてはソート不可 / 29件ダイジェスト /
種別タブ無限スクロール / 種別6本 / 関連ユーザー枠 / FEATURED差し込み /
**ビルダー入口（2026-08-25 失効）** / **自分のRIGから探す（2026-08-25 失効）**。
**削除ではなく失効として記録**（なぜ以前そうだったかを追えるようにするため）。

### 🔴 検索の確定仕様（2026-08-25 / DECISION）

裁定原本と「なぜそうなったか」は
`_decisions/2026-08-25_search-closure-v1.md` / `_decisions/2026-08-24_search-community-scope-v1.md`。
詳細な画面仕様は `docs/search/search-page-plan-v2.md`。ここは索引。

**対象と入口**
- 標準Searchの対象は Community 3種（**RIG / パーツ / LOG**）
- 検索トップ「MyRIGで探す」は **3入口のみ**（RIG / パーツ / LOG）
- 「もっと詳しく探す」は **2入口**（条件で探す / 製品名・型番で探す）
- **ビルダー／ユーザー専用の検索入口はMVPから撤去**。
  ユーザー検索という概念の永久廃止ではなく、方式は将来検討（PENDING）
- **「自分のRIGから探す」は撤去**。`rig_scope` はSearch仕様に含めない
- `scale`（`size_class`）はHOLD中のため **UI非表示・URLへ送らない**

**URL / 状態**
- URLの外部語彙 `type` は **`rig` / `parts` / `log`**。
  PC内部の `logs` はmappingの内側だけに閉じる
- `q` なし / `q=` 空 は **自由語条件なし**
- `q` は **実結果・件数・Facet件数・Mobileシート内previewの基底predicate**
- `q` の検索対象に **ユーザー名 / @handle を含めない**
- 検索トップの Enter と固定CTAは **同一の Search State serializer** を通す

**Facet**
- **canonical-valid vocabulary と fixture存在値を分離する**
- 正典上有効だが fixture 0件の値は **保持して0件表示**
- 本当に無効な値のみ **解除＋通知**
- 同一Facet内は **OR**、異Facet間は **AND**
- 種別固有Facetを選んだら **対応する type へ state を正規化**
- scope変更で無効になる条件は解除し、**silent clear 禁止**（必ず通知）
- `maker` は RIG/PARTS では正本属性。LOG は **linked RIG から確実に導出できる場合のみ派生**

**Library / その他**
- Library は Community検索結果とは **別母集団**。Community件数へ合算しない
- 「関連する」と表示するのは **明示的な関連根拠がある場合のみ**
- **Search面は広告ゼロ**

### ✅ Browse CLOSE — PC ＋ Mobile 両面確定（2026-08-31 / revision 039）

**裁定原本: `_decisions/2026-08-31_browse-domain-scope-inheritance-v1.md`**

**OVERRIDE / L1改訂 — ローカルナビの本数は「Domain内のscope継承」で決める。**
037 の RIG ROOT `トップ / RIG / LOG` を**失効**。

| Domain | 面 | ローカルナビ |
|---|---|---|
| — | RCカー HOME（WORLD ROOT） | **なし**（Domainの外側） |
| **RIG** | すべてのRCカー（RIG ROOT） | **トップ / RIG / パーツ / LOG** |
| **RIG** | Rock Crawler（RIG Category） | **トップ / RIG / パーツ / LOG** |
| **PARTS** | すべてのパーツ（PARTS ROOT） | **トップ / パーツ / LOG** |
| **PARTS** | モーター・ESC（PARTS Category） | **トップ / パーツ / LOG** |

**DECISION — 判断基準は2階建て【L1】**

| 方向 | 基準 |
|---|---|
| **Domain内（Root → Category）** | **継承する。** 需要判定を持ち込まない |
| **Domain間（RIG系 ↔ PARTS系）** | **閲覧需要の非対称性で決める。** 揃えない |

Root → Category で変わるのは scope だけ。**子が親より閲覧できるものが多い構造を作らない。**

**🔴 書き方の注意【L1】: 「深さで軸を変えない」とは書かない。**
HOME にナビが無いことと矛盾する。正しくは「**同一Domain内では継承する**」。

**DECISION — RIG ROOT の `パーツ` は PARTS ROOT の `パーツ` と別物。**

| | 定義 | MOCK |
|---|---|---|
| `すべてのRCカー → パーツ` | 全RIGに現在装着中のパーツ（`removed_at IS NULL`） | 12,480 |
| `すべてのパーツ → パーツ` | 登録されている全パーツそのもの | 18,492 |

**STATE — ROOT構造は維持。HOME統合・ROOT削除は行わない。**
検討過程で出た「ROOT不要説」は採らない。違和感の正体は ROOT の存在ではなく
**RIG ROOT だけ閲覧能力が欠けていたこと**だった。

**改訂根拠:** 2026-08-31 の PC/Mobile 4面同時比較。
037 は基準そのものではなく**適用範囲**を誤り、Domain間の基準を Domain内の縦方向にも適用していた。
**L1 は失効理由の明示を伴わなければ改訂しない。**（失効範囲の全一覧は裁定原本 §2）

**🔴 副作用として顕在化した 036違反 2件（修正済み）**

| 場所 | 欠けていた型 | 状態 |
|---|---|---|
| `css/mobile-shell.css` | `part` | **顕在化。** RIG ROOTのパーツviewが白画面だった |
| `pc/myrig-browse-category-v3.html` | `parts` | 潜在。該当棚を足した瞬間に消える |

判定側だけ修正。`data-entity-type` は書き換えない（036のまま）。
**「今は影響が無い」不整合は、影響が出る変更が来るまで待っているだけである。**

**検証: `browse_sidebar_v5_check.py` 214項目 FAIL 0 /
`browse_contract_check.py` 201項目 FAIL 0 WARN 4 / 8面 pageerror 0。**

チェッカーに**軸を実際に押す**恒久回帰を追加（037までラベルしか見ていなかった）。
故障注入5種すべてで FAIL を確認済み。

**🔵 PENDING — セクション並びパターン（A/B/C）は今作らない。**
実装済みCategory面が2つしかなく、2例から3パターンは導出できない。
**再開トリガー: 3つ目のRIG Category（ドリフト等）を作るとき。**
設計注意: A/B/C を「レイアウト」として設計しない。差は並び方ではなく
**どの関係棚が成立するか**。軸は `Domain × セクションレジストリ（並び順つき）`。

**🔵 PENDING（継続）:** 表示グループ判定が5か所に分散している件の集約は別トラック。

**🔴 事故 — Cowork が commit author を変えて Vercel 本番デプロイを止めた（2026-08-31）**

規則は `MyRIG_CORE.md` 実行レーン分離 §「commit author」へ恒久記載。要点のみ:

- `myrig-mockup` の author は **`MyRIGRC <admin@myrigrc.com>`** を維持。AI名義へ変えない
- Vercel Hobby は見知らぬ author の production deploy を **BLOCKED** にする
- 039 のモックコミット `31b610b` が該当。以降3件が `UNKNOWN` で停止
- author を戻した空コミット `fe91492` の直後、同一内容が `Ready in 8s` で通り原因確定
- `31b610b` は push 済みのため author を直さない（履歴書き換え・force push はしない）
- Cowork は `git add -A` も使っていた。**CORE 157行目の明記ルール違反**

**切り分けで露呈した問題のほうが重い。** Cowork は自分が author を書き換えた事実を
報告せず、障害の容疑者からも外していた。対照実験の前に3回原因を断定している。
**自分の直近の操作を最初の容疑者にすること。**

---

### PC Browse V5 再CLOSE（2026-08-30 / revision 037 — 上記 039 で一部改訂・記録）

**裁定原本: `_decisions/2026-08-30_browse-axis-relation-view-v1.md`**
036 の CLOSE を「ローカルナビ」と「Root current」に限って再オープンし、**この状態で再CLOSE。**

**OVERRIDE — 「全Browse面で共通4軸」を失効。**（039 でも維持。共通4軸は復活させない）

> 🔴 **本節のナビ表は 039 で改訂された。最新は下の「Browse CLOSE（039）」を見ること。**
> 037 は RIG ROOT を `トップ / RIG / LOG` とし、面ごとに本数を決めていた。
> 039 で Domain内継承へ改訂。**この行は「なぜ以前そうだったか」を残すための記録。**

**DECISION — Domain間の相互参照は「大量閲覧の主要目的があるとき」だけナビ化。**（039 でも維持）

| 情報 | 置き場所 |
|---|---|
| PARTS ROOT を使っているRIG | 棚 |
| モーター・ESC を使っているRIG | 棚 |
| RIG系のパーツ（大量横断して見る意味が強い） | **ナビ** |
| LOG（各スコープの「活動・経験を見るモード」） | **全面のナビに常設** |

> **039 で失効:** 「RIG ROOT で使われているパーツ → 棚」。ナビへ昇格した。

**DECISION — 関係条件は `rig_parts.removed_at IS NULL`（現在装着中のみ）。**
関係件数は1か所（`js/browse-scope-relations.js`）へ集約し、MOCK であることを明記する【L1】。

**OVERRIDE — Root current は子カテゴリ current と同一の表示文法。**
036 の Root専用デザイン（全面帯＋濃い下罫線）は失効。非current の Root entry 文法は維持。

**🔵 正典化しない: 具体的なラベル文言・余白・表示件数などのUI調整。**
正典化するのは「どの面にどの閲覧モードがあるか」「相互参照はナビか棚か」
「どういう関係を根拠に表示してよいか」まで。日本語ラベルは実装側で調整可能にしておく。
**文言を磨くために構造を開け直さない。**

**🔴 PENDING — PARTS系 LOG の直接関連モデル／実データ抽出方式。**
UIとして LOG を置くことは確定。UX概念は「そのパーツに関するLOGを見られる」。
ただし `maintenance_logs` は `rig_id`（NULLABLE）を持つが **`part_id` を持たない**ため、
現行DBでは正確に抽出できない。**`装着RIG → そのRIGの全LOG` を代替して「関連LOG」と偽装しない。**
モックは棚を `装着RIGのLOG` と正確に名乗り、画面上にも暫定表示と明記してある。
候補: `maintenance_logs ↔ maintenance_log_parts ↔ parts` の多対多。**UIとは分離して扱う。**

**検証: `browse_sidebar_v5_check.py` 211項目 FAIL 0 /
`browse_contract_check.py` 76項目 FAIL 0 WARN 9 / PC 4面 pageerror 0。**

**036 の表示グループ裁定（`rig+rig_master` / `part+parts+part_master` / `log`）は無改訂で有効。**

**STATE — モック進行状況ダッシュボード（`index.html`）。**
2026-08-30 時点でブラウズ4面は 🔵 PC版のみ（青）だった。
**2026-08-31 / 039 で Mobile 4面が揃ったため、4面とも PC/Mobile 両面確定（緑）へ昇格。**
見比べビュー `compare.html` も追従。

---

### PC Browse V5 CLOSE（2026-08-30 / revision 036 — 上記 037 で更新済み・記録）

**正典2本**
- `docs/ui/browse-sidebar-v5.md` — Sidebar / Breadcrumb / WORLD階層 / **WORLD selector** / **Root current**
- `docs/ui/browse-display-contract-v1.md` — カード / 棚 / ローカルナビ / **§12 4軸の表示グループ**

**裁定原本**: `_decisions/2026-08-30_browse-axis-display-groups-v1.md`

対象5面（PC）: HOME `myrig-home-v3.html` / RIG ROOT `myrig-browse-rigs-v3.html` /
Rock Crawler `myrig-browse-category-v3.html` / PARTS ROOT `myrig-browse-parts-v3.html` /
Motor・ESC `…?category=motor-esc`

**🔴 PC 5面のデザイン・Sidebar文法・WORLD selector・Root current・4軸は再オープンしない。**
新しい根拠がない限り再調整しない。Claude / GPT / Gemini とも改善提案を出さないこと。

**確定した内容（8/30 分）**

| # | 確定 |
|---|---|
| 1 | HOME の CURRENT WORLD は「大きなcurrentカード」ではなく **WORLD selector**（`RCカー / RC CARS` ＋ chevron、「現在表示中」文言なし、connector・縦棒なし、dropdown は overlay、将来WORLD は disabled） |
| 2 | **Root current** — HOME=なし / RIG ROOT=すべてのRCカー / Rock Crawler=ロッククローラー / PARTS ROOT=すべてのパーツ / Motor・ESC=モーター・ESC。Root entry の寸法は変えず、薄い無彩色の面＋下罫線の色のみ |
| 3 | **4軸の表示グループ** — RIG=`rig`+`rig_master` / PARTS=`part`+`parts`+`part_master` / LOG=`log`。`data-entity-type` は書き換えない |
| 4 | RIG ROOT は Rock Crawler の別バージョンにしない。全カテゴリ混在／RIG棚にPARTSカードを混ぜない |
| 5 | current の強さ: 4軸 ≧ WORLD selector ＞ Directory 子current ＞ Root current。**地色との相対差で判定** |

**検証**: `_audit/browse_sidebar_v5_check.py` **174項目 FAIL 0**（140→174 へ強化）/
`_audit/browse_contract_check.py` **76項目 FAIL 0 / WARN 9**（既知: 自己リンク7・インライン`<style>`2）/
PC 5面 light・dark とも pageerror 0。

**🔴 チェッカーの原則【L1】** — 2026-08-30、master系の棚が4軸で全部消えていたのに
チェッカーは 140項目 FAIL 0 で通っていた。Root current も、`is-current` は付いているのに
CSS が打ち消していて見えない状態で通っていた。同じ穴を開けないため:

- **「クラスが付いているか」ではなく「見た目に差が出ているか」を検査する**
- **「押せるか」ではなく「押した結果どうなるか」を検査する**
- **検査が空振りしていないことを検査する**（対象棚が0本なら判定は素通りする）
- **チェッカーを足したら、故障を注入して FAIL することを確認してから採用する**

**レーン分離は解除。** PC Browse 系ファイルの GPT 触り禁止は CLOSE をもって終了する。
ただし再オープンしない範囲（上表）への変更提案は受け付けない。

---

### 🔴 Browse整合 BROWSE-CONTRACT-001（2026-08-29 / 記録）

**正典: `docs/ui/browse-display-contract-v1.md`（L2。§3.2 禁止事項と §4.3 定義場所は L1）**

Browse系の見た目が面ごとにズレる問題を全面実測した結果、原因は指示や担当者ではなく
**同一部品が5系統に分裂し、揃える先が特定できない**という構造的欠陥だった。

| カード部品 | 定義場所 | 判定 |
|---|---|---|
| `myrig-*-card variant="browse"` | `SoT_card-components.js`（正典） | **正しい**。PC Category Top / PC Home が使用 |
| `.gcard` / `.m-card-*` | `css/mobile-shell.css`（共有） | 正典 §5 準拠 |
| `.bp-card` | **HTML 2ファイルにインライン重複** | 共有CSSへ移送が必要 |
| `.mec-card` | `js/parts-category-demo.js` 内 | **正典逸脱**。手書き |
| `.edit-card` | **PC 4ファイルに重複・既に乖離** | 別トラック（§8-3） |

**核心:** `pc/myrig-browse-parts-v3.html` は L19 に「カード内部は SoT_card-components.js に
委譲。上書きしない。」と明記され、静的マークアップも `<myrig-part-card variant="browse">` で
**正しく書かれていた**。それを後付けの `js/parts-category-demo.js` が `innerHTML` 全置換で
破棄し `.mec-card` へ差し替えていた。これを L1 禁止化した（契約 §3.2-4）。

`SoT_card-components.js` L96 に `browse card shared CSS (INDEX / Category Top / Search)` と
あるとおり、**Category Top用のカードvariantは正典が最初から用意していた**。

- **基準画面 = Rock Crawler Category Top（PC / モバイル）。変更しない**
- 契約は数値を固定せず、**チェッカーが実行時に基準画面を実測して比較**する
  （数値を文書に書き写すと基準画面変更時に文書が嘘になるため）
- **修正の前にチェッカーを緑にしてから着手**する。作業者自身の作業も検証対象になる
- モバイルのカード本体は3面で**完全一致していた**。不一致はバッジ・ナビ・ヒーローのみ
- 棚の左ガター（`padding-left` / `scroll-padding-left`）は**修正済み**を実測確認
- 見出しとサブタイトルの重なりは**実測0件**。`compare.html` のPNG書き出し起因の見え方であり、
  **書き出し画像でレイアウトを判断しない**

**レーン分離:** このレーンの間、GPT WorkはBrowse系5ファイルを触らない。
対象 = `js/parts-category-demo.js` / `browse-parts.html` / `browse-category.html` /
`pc/myrig-browse-parts-v3.html` / `pc/myrig-browse-category-v3.html`。
並行編集すると、どちらの変更か判別できなくなる。

### 直近で片付いたこと（2026-09-16 / 106）

- **「`mockup` は正典をpushしない」（2026-08-24 記述）が現在は誤りであることを実測で確認し訂正。**
  `~/.zshrc` の `push` 関数が `mockup` の1行目から正典を push している。
  ただし commit 済みのものだけが対象で、`push` が失敗しても `mockup` は止まらない
- 2026-09-15 の常時1件運用を実運用で検証。`mockup` 1回で
  正典push → モックcommit/push → deploy → 旧deployment削除 まで通ることを確認
  （`c52e506` / `dpl_8Wj8EHZfxq7HwyVeMP8e67Q66mVc` / 残1件）

### 直近で片付いたこと（2026-09-15 / 105）

- **Vercel Deployment Storage 無料枠 10GB 100% 到達を解消。** 21件の古い Production Deployment を削除し、
  `mockup` に `vercel remove --safe --yes` を追加して**常時1件運用**へ移行（下記「デプロイ運用」参照）
- `.vercelignore` に `Claude outputs` と `_state/shots_*` を追加。アップロード量 約80MB → 約33MB
- `mockup` の実体を `functions mockup` で確認。CURRENT の「`~/.zshrc` は未確認のため推測」を実測記述へ置換
- `vercel` が PATH に無く `npx vercel` 経由であることを確認（CLI 59.18.0）

### 直近で片付いたこと（2026-08-25）

- **Web文法 DRAFT v0 を作成**（`myrig-mockup/docs/WEB_GRAMMAR_DRAFT_v0.md`・NOT CANON）
- **Web文法 実装バッチ1 完了・deploy済み**（`054e6e0`）
  - PC app-nav を実PCサービス画面へ結線（30ファイル / 90本）。
    **PCは40画面中28が行き止まりだった**のが解消
  - `pc/assets/js/SoT_app-shell.js` に未実装routeの共通handlerを追加。
    Mobileと同条件（href が無いか `#` のときだけ preventDefault + toast）。
    これが無く、PC 33ファイルの法務リンクはMobileと同じmarkupなのに何も起きなかった
  - Home「今週の人気」の RIG/パーツ切替を修正（`hidden` がCSSの `display:flex` に負けていた）
  - 実測15項目 全PASS / pageerror 0 / 見た目の変更なし
- **検索を CLOSE**（上記）。相互監査（Cowork実装＋実操作測定 / GPT独立ライブ監査）で
  P0級を計12件是正。最終回帰101項目 全PASS
- モック全体の棚卸し（A/B/C/D）を実施し、
  **`_state/MyRIG_MOCK_SNAPSHOT_20260824.md` を観測資料として格納した**
  （Status: OBSERVATION / NOT CANON。確定仕様ではない。
  Searchに関する観測値は 2026-08-25 の CLOSE 以前のものなので一部陳腐化している。
  Web文法・全体配線監査の入力資料として使い、現在状態を断定するときは実装を再確認すること。
  **正本参照位置はここ1箇所。`myrig-mockup/docs/` へ複製しない**）
- 旧・検索サイドバー（361行）を active HTML から撤去し `_archive` へ退避。
  `<div hidden>` の閉じ位置ミスで**実画面に出たままだった**（撤去済み軸やメーカー重複が見えていた）
- `docs/WEB_GRAMMAR_QUEUE.md` を新設（Search外の横断課題の置き場）

### 直近で片付いたこと（2026-08-24）

- **⚠️ revision番号が枝分かれしていたのをマージで解消。**
  Cowork側とGPT側が独立に **027** を採番していた（Cowork=NOW節新設 `7276e2f` /
  GPT=共通Search Service原則 `9ea1669`）。両者は共通祖先026から分岐しており、
  マージして統合。**内容は両方とも失われていない**（Search Service節は
  search-page-plan-v2 §裁定済み・未実装 の直後と、本ファイル「現在地」節に残存）。
  以後の採番衝突を防ぐため、上記「正典のWRITEはCoworkに一本化」を裁定。
  マージ後の通し番号は **030**（027の重複を残したまま先へ進める。過去の番号は書き換えない）
- **CORE に「正典化判断基準」を新設。** すべての設計判断を正典化しない。
  判定は重要度ではなく「黙ってズレたときのコスト」で行う。
  UI詳細（件数・位置・カードサイズ・文言）は正典に固定せず、実装＋DECISIONSへ。
  **この検索バッチ自体がその実例**（旧正典は間違いではなく、実物を見て新情報が入った結果の更新）
- 検索設計の全面改訂（上記）。PC/Mobile/検索トップ/ランチャー/compare まで一貫
- ヘッダー検索にAmazon型サジェストを実装（再検索してもフィルターは保持）
- ランチャーの検索セクションを3項目へ（種別はページではなく状態）
- `compare.html` のPC専用ページが両ペインでホームにフォールバックしていた不具合を修正

### 直近で片付いたこと（2026-08-23）

- PC検索バッチを実装・デプロイ（mockup `06e52dd` / `5c84467` / `ca9dfe4`）
- **デプロイが8/22以降ずっと落ちていた事故を解消**（下記「デプロイ運用」参照）
- 「PCはローカル確認のみ」というCURRENTの誤記を訂正

### 直近で片付いたこと（2026-08-22）

- GPT側GitHub連携完了 → Claude/GPT が同一repoを共通正本として参照
- GPT外部監査 A/B/C/D を全件解消（Auth middleware / soft-delete / RLS / 誤記）
- HOLDを「裁定待ち」と「将来議論項目」に分離、AIが催促しない運用へ
- 検索裁定4件（②④⑤⑥）が`_proposals`に埋もれて正典から欠落していた件を是正

### 環境ごとの注意

- **デスクトップCowork**: ローカル直読み。ファイル編集・commit可。pushはイタヤ手動
- **ブラウザ通常チャット**: Knowledge経由。**push後は Sync now が必要**（自動同期ではない）
- **Knowledge経由の環境で `revision.txt` が索引に乗らない場合**は、CURRENT本文の revision を正とする。
  ただしこれは「片方が取得できない」ケースの話。**両方取得できて食い違う場合は下記のとおり停止する**（別の事象）

### 🔴 revisionは2箇所を突き合わせる（2026-08-25 / CDNキャッシュ対策）

`revision.txt` **だけ**を見て信じない。
`revision.txt` と本ファイル冒頭の `revision:` を**両方取得して一致を確認**してから作業を始める。

食い違ったらCDN/プロキシの古いキャッシュを疑い、再取得する。
再取得しても食い違うなら**仕様判断を停止**し、その旨を報告する。推測でどちらかを正としない。

#### 🔴 突き合わせだけでは足りない — raw取得には毎回一意クエリを付ける（2026-08-31 / 040）

**2ファイルが同時に同じ最初期値でキャッシュされる事象が実際に起きた。**
素のURLで両方を取得したところ、`revision.txt` も `MyRIG_CURRENT.md` 冒頭も
`MYRIG-20260821-001` を返し、**一致してしまった**（正しくは `039`）。
同一URLへ `?cb=<一意値>` を付けて再取得したら、両方とも `039` が返って確定した。

「2ファイルが同時に同じ古さでキャッシュされる確率は低い」という前提は成立しない。
**突き合わせは維持するが、それ単独を検出手段として信頼しない。**
`raw.githubusercontent.com` を使うときは毎回異なるクエリを付ける。
恒久ルールは `MyRIG_CORE.md`「raw取得には毎回一意のクエリを付ける」節。

**実際に起きたこと（2026-08-25）**: GitHub main から `revision.txt` を取得したら
`MYRIG-20260825-001` が返った（正しくは `033`）。**1つ古いのではなく最初期の値**だったので
明らかにおかしいと気づけたが、もし `032` のような"それらしい値"が返っていたら
1つ前の正典で作業してしまうところだった。
2ファイルが同時に同じ古さでキャッシュされる確率は低いので、突き合わせるだけで大半は検出できる。

正本更新時は**必ず両方を同時に書き換える**こと（片方だけ更新すると、この検出が機能しなくなる）。

### 🔴 正典のWRITEはCoworkに一本化（2026-08-24 裁定）

**`myrig-ai-context` への書き込みはCowork（Claude）だけが行う。GPTはREAD専用。**
GPTが読むのは自由だが、修正してpushしない。

### 🔴 `mockup` は正典も push する（2026-09-16 実測。2026-08-24 の記述を訂正）

**旧記述「`mockup` が push するのは `myrig-mockup`（モック）だけ。正典は手動pushが要る」は現在は誤り。**
どこかの時点で `~/.zshrc` に `push` 関数が追加され、`mockup` の1行目がこれを呼んでいる。

```zsh
push () {
	(
		cd ~/Desktop/MyRIG/myrig-ai-context || exit 1
		git fetch -q || exit 1
		local n=$(git rev-list --count origin/main..HEAD)
		if [ "$n" -eq 0 ]; then echo "正典: push するものはありません"; exit 0; fi
		echo "--- 正典を push します ($n件) ---"
		git log --oneline origin/main..HEAD
		git push && echo "--- 正典 push 完了 ---"
	)
}
```

つまり `mockup` は「正典を push → モックを commit＋push → deploy → 古い deployment 削除」を
一度に行う。**正典のためだけに別コマンドを打つ必要はない。**

ただし2点、依然として自分で担保すること。

- **push するのは commit 済みのものだけ。** Cowork が正典を編集しても commit していなければ
  `push` は「push するものはありません」と言う。正典を直したら commit まで済ませること。
- **`push` が失敗しても `mockup` は止まらない。** `push` の後ろに `|| return 1` が無く、
  サブシェルの `exit 1` も呼び出し元へ伝播しない。正典の push が失敗したまま
  モックだけがデプロイされる経路が残っている。`mockup` 実行時は冒頭の
  `正典: push するものはありません` / `--- 正典 push 完了 ---` のどちらかが出たことを目で確認する。

---

## デプロイ運用（2026-08-23 実測で確定・過去の記述を訂正）

### 手順

1. Cowork が対象ファイルのみ編集（`git add -A` は使わない）
2. `_backup/<バッチ名>_<YYYYMMDD>_JST/` へ改修前をバックアップ
3. commit（**author は指定しない。下記を厳守**）
4. イタヤがターミナルで `mockup` → push ＋ Vercel deploy
5. 実機／ブラウザで確認 → 裁定 → 次バッチ

`mockup` は `~/.zshrc` のシェル**関数**（エイリアスではない）。
2026-09-15 に `functions mockup` で実体を確認済み。**推測ではなく実測**。

```zsh
mockup () {
	push
	local repo="$HOME/Desktop/MyRIG/App/MOKUP/myrig_pc_Ver3"
	local msg="${1:-mock: update $(TZ=Asia/Tokyo date '+%Y-%m-%d %H:%M JST')}"
	cd "$repo" || return 1
	git add -A
	git diff --cached --quiet || { git commit -m "$msg" || return 1; }
	git push || return 1
	npx vercel --prod --cwd "$repo" --token="$VERCEL_TOKEN" || return 1
	npx vercel remove myrig-mobile-mock --safe --yes --token="$VERCEL_TOKEN"
}
```

注意点が3つある。

- **`git add -A` で全件ステージする。** 手順1の「Cowork は対象ファイルのみ編集」は
  Cowork の編集範囲を縛るルールであって `mockup` は対象外だが、
  結果として**作業中の一時ファイルも巻き込んでcommitされる**。
  一時ファイルを作ったら `mockup` の前に消すこと。
- **`vercel` は PATH に入っていない。** `npx vercel` で毎回取得している（2026-09-15 時点 CLI 59.18.0）。
  ターミナルで `vercel ...` と直打ちしても `command not found` になる。
- 最終行の `vercel remove` が容量対策（下記）。

### 🔴 commit author を上書きしないこと（L1）

`myrig-mockup` には `user.name = MyRIGRC` / `user.email = admin@myrigrc.com` が
リポジトリ設定として入っており、**これが Vercel チームのメンバー**である。
`git -c user.email=...` で上書きすると Vercel が
「not a member of the team」でデプロイを拒否する。

**実際に起きた事故（2026-08-23 検出）**: 8/22 の Cowork コミット2本
（`b1f2640` / `5c8fa55`、author `Cowork (Claude) <info@rccrawlers.net>`）と
8/23 の2本（`06e52dd` / `5c84467`、author `Itaya Hirotomo <info@rccrawlers.net>`）が
チーム外 author だったため、**8/21 の `0042b56` を最後にデプロイが全て失敗していた**。
本番は約2日間 8/21 時点の内容を配信し続けていた。CLI 側の認証
（`admin-71487649` / team `myrig-rcs-projects`）は正常で、原因は commit author 側。
復旧は正しい author の空コミット `ca9dfe4` を積んで押し出す方法を取った
（force push は CORE 禁止のため履歴は書き換えていない）。

**症状の見分け方**: `git push` は成功するのにデプロイだけ届かない。
デプロイURLが `Deployment is building` のまま数分以上変わらない。
Vercel から「attempted to deploy a commit … but they're not a member of the team」通知。

### 配信範囲（従来の記述は誤り）

- `mockup` はリポジトリ直下から deploy するため、**PC版も配信される**。
  `/pc/*` は実際に到達可能（`/pc/myrig-search-v3.html` が HTTP 200）
- 旧記述「PC mockup はローカル確認のみ / `mockup` はモバイル側のみ deploy」は**誤り**
- `mobile/0709 mobile/mockup-deploy/` は配信元では**ない**。
  中身は `.env.local` `.gitignore` `.vercel` のみで、`.vercel` は直下と同一プロジェクトを指す。
  `README_3plans.md:176` の「このフォルダごと mockup-deploy 内に置いて `mockup`」は
  3プラン比較モック時代の運用で、現在の実態と異なる
- `.vercelignore` は `_archive` `*.zip` `.DS_Store` `_backup` `docs`
  ＋ `Claude outputs` `_state/shots_mobile` `_state/shots_20260909` `_state/shots_20260909_color`
  を除外（`pc/` は除外していない = PC版は配信される）

### 🔴 Deployment Storage 対策 = 常時1件運用（2026-09-15 / 105 実施済み）

**事象。** 2026-09-15、Vercel から Deployment Storage 無料枠 10GB 100% 到達の警告。
実測 10.53GB / 10GB。`myrig-mobile-mock` に **21件**の Production Deployment が
すべて残っていた（Hobby プランに自動削除の設定は存在しない。
Deployment Retention は Pro 以上の機能で、プロジェクト設定に項目自体が無い）。

**原因。** Vercel の Deployment は仕様上すべて immutable で、上書き・置換はできない。
`vercel --prod` を実行するたびに新しい Deployment が積まれ、消さない限り残り続ける。
同一 SHA から複数件あったのは、同じ commit 上で `mockup` を複数回叩いたため。
**Git 連携による自動デプロイではない**（project の `link` は `null`、
GitHub 側の登録 Webhook も 0 件）。全件 `source: cli`。

**採った対策。** `mockup` の末尾に以下を追加した。

```
npx vercel remove myrig-mobile-mock --safe --yes --token="$VERCEL_TOKEN"
```

`--safe` は本番ドメインが向いている Deployment をスキップするため、
**配信中の1件だけが残り、他はすべて消える**。デプロイのたびに自動で走るので
Deployment は常に1件。容量は積み上がらない。
`--prod` の直後に `|| return 1` を置いてあるので、デプロイが失敗したら削除は走らない。

**併せて `.vercelignore` を追加。** アップロード量 約80MB → 約33MB。
`Claude outputs`（配信HTMLからの参照ゼロを確認）と
`_state/shots_*`（検証用スクショ 37MB。HTML/CSS/JS からの `_state/` 参照は
`.md` と `.py` へのコメント記述のみで、`shots_*` への参照は無いことを確認）を除外。
なお `.git`（293MB）は Vercel CLI が常に自動除外するのでアップロードされていない。

**トレードオフ（イタヤ裁定 2026-09-15）。** Vercel 上でのロールバックは不可能になる。
モックアップ制作中に「1週間前の状態に戻す」運用は行わない・戻すならローカルの
git / `_archive` から戻す、という前提で採用した。

**この運用は Hobby 期間限定。** Next.js 実装に入って Pro へ上げた後は、
Deployment Retention（Pro 以上）で自動化できるため、その時点で再検討する。

### 恒久対策の候補（未着手）

Vercel プロジェクトを `MyRIGRC/myrig-mockup` に Git連携させれば `git push` だけで
自動デプロイになり、CLI認証と author 判定に依存する経路を減らせる。要検討。
ただし Git 連携にすると Preview Deployment が自動生成されて件数が増えるため、
上記の常時1件運用とは併用できない。Pro 移行時にまとめて判断する。

---

## 現在地（プロジェクト全体）

フェーズ: モックアップ確認中（Next.js実装前）
PC mockup: 39ページ、SoT CSS/JS構成、概ね完成
Mobile mockup: 約55ページ、確認ダッシュボードで管理中
確認状況: 確定0 / PC版のみ6 / 要確認49 / 未着手0（2026-08-20時点）
確認ダッシュボード: 所在不明。2026-08-23 に `myrig_pc_Ver3` 配下を探索したが該当ファイルなし
（`Research/` 配下のダッシュボード群は別物）。**デプロイURL・ソース所在ともに未特定。**
Mockup shell: v0.5 / Home r14 / P22-C35

ナレッジ運用: 2026-08-21 CORE+CURRENT方式へ移行。
本repo（myrig-ai-context）が全AI共通の正本。
旧Claude Knowledge 41本は本repoへ分類移送済み（下記Task Routing参照）。

**2026-08-22 GPT側GitHub連携完了。** ChatGPT GitHub App を
MyRIGRC/myrig-ai-context に限定接続。GPT Project Instructions を
GitHub正本参照方式へ全面更新し、旧GPT Project Knowledgeは削除済み。
Claude（GitHub Integration）とGPT（GitHub App）が同一repoを共通SoTとして参照する体制が成立。
GPT側起動テスト済み: revision一致確認・CURRENT本文からのHOLD3件報告まで正常動作。

**timestamp要確認（2026-08-22）**: 本ファイルの `updated` 表記について、
GPT側確認時刻との間に約2時間のズレが報告された。原因未特定
（Cowork/イタヤ実機いずれの時計かは未確認）。次回更新時にJST取得元を検証すること。
**2026-08-23更新では `ZoneInfo("Asia/Tokyo")` でJSTを取得して記録済み。前回ずれの原因自体は未特定。**

**2026-08-22 GitHub複数WRITE経路の競合防止ルールをCOREへ追加。**
Cowork/GPT双方がGitHub mainへ直接書き込める体制になったため、
WRITE前の同期確認（fetch/diff/pull、SHA再取得）とforce push禁止をCORE.mdに明記した。
詳細はMyRIG_CORE.md「GitHub複数WRITE経路の競合防止」節。

**2026-08-22 GPT取得元をAPIへ明示化。**
`raw.githubusercontent.com`にCDNキャッシュ（最大5分）があり、pushしてもrevisionが古く見える現象を
実測。CORE.md「GPT」節に`api.github.com/repos/.../contents/...?ref=main`経由で取得することを明記。

**2026-08-22 GPT GitHub WRITE実地テスト完了。**
GPTがGitHub API経由で `_audit/gpt-write-test-20260822.md` を直接commit（`8350e10`）。
Cowork側はCOREルールどおり fetch → 差分確認（behind 1）→ pull（fast-forward）を実行し、
local HEADとorigin/mainの一致、revision/CURRENTが変更されていないことを確認した。
READ/WRITEともにClaude・GPT双方で確認済み、複数WRITE経路の競合防止ルールは実運用で機能した。

**2026-08-23 検索基盤の実装原則を裁定。** Next.js実装時はページごとに独立した検索エンジンを作らず、
共通のSearch Serviceを1つ持つ。Community / Catalog / User の論理indexと、Global / Library / User /
Registration / Feed内検索等のranking/filter profileを切り替えて利用する。query正規化・alias/synonym・
typo tolerance・公開可否/権限フィルター等の共通処理はSearch Serviceへ集約する。
Feedの通常表示（おすすめ/フォロー中等）は検索ではなく推薦・ランキング責務として分離する。
具体エンジン製品・index物理構成・同期方式・関連度数式は未確定。詳細は `docs/search/search-page-plan-v2.md`。

## Active Overrides

2026-08-19
カテゴリ色はToken Note v7ではなくv8裁定を採用。
v8: RIG=黄 / PARTS=赤 / LOG=スチールブルー。モバイル適用済み。
Token Note v7文書（緑/紫/橙）は失効し _archive へ退役。
正典は docs/design/color-token-v8.md。

2026-08-21
検索の現行確定仕様は docs/search/search-page-plan-v2.md。
_proposals/ の検索4文書（search-system-design-v3.md ※本文タイトルはv3.1 / search-blueprint-v2 /
search-results-ux-v1 / search-contract-v1）はPROPOSAL。正典扱いしない。
ただし search-contract-v1 は主要7件イタヤ裁定済み・残3件照会中。

⚠️ **2026-08-22 是正**: 2026-08-21のイタヤ裁定8件のうち **②④⑤⑥が正典から欠落**していた
（`_proposals/search-results-ux-v1.md`にしか記載がなく、そこはPROPOSAL扱いのため
「裁定した事実」ごと参照不能になっていた）。search-page-plan-v2 に**「裁定済み・未実装」節**を
新設して移設済み。②PC非ブレンド化（PC版バッチ）/ ④答えカード（Phase 3）/
⑤絞り込み候補チップ（Phase 3）/ ⑥保存検索（Phase 4）。
**教訓: 裁定した内容を`_proposals/`に置いたままにしない。裁定と同時に正典側へ移す。**

2026-08-24
**SEARCH-UPDATE-001 — 標準検索をCommunity検索へ再定義。**
正典原本は `_decisions/2026-08-24_search-community-scope-v1.md`。
標準検索の対象は RIG / パーツ / LOG のみ。製品情報は Library、人は「ビルダー」探索の責務。
上の裁定②④は**失効**、⑤は改訂（ヘッダー検索のサジェストとして実装）、⑥のみ有効。
`docs/search/search-page-plan-v2.md` は 🔴 印付きで旧記述を残したまま改訂済み（削除しない）。

2026-07-30
FEED文法はモバイル基準（おすすめ/フォロー中タブ）。
PC版を差し替える（_decisions/p22-c21 参照）。実装待ち。

⚠️ **2026-09-07 失効**: タブ本数の部分は下記 071 で改訂された。
PC / Mobile を揃えるという方針自体は有効。

2026-09-07
🔴 **#28裁定（2026-07-23）「Feed はおすすめ / フォロー中の2タブ。全投稿時系列は置かない」を失効。**
正典原本は `_decisions/2026-09-07_feed-tabs-v1.md`。
Feed のタブは **「おすすめ（興味・発見）/ 新着（全公開LOGの純時系列）/ フォロー中（social graph）」の3本**。
理由は「実装が3タブだから」ではなく、**Feed の独立性を再検討した結果**
（`_decisions/2026-09-07_toplevel-surfaces-v1.md`）。
MVP のおすすめが完全な推薦ではないため、**加工されていない時系列の入口を残す**。
`docs/ui/page-role-matrix-v1.md` は §6 / サイトマップ / Page Matrix / ロードマップ表を改訂済み（v1.6）。

2026-09-07
**Browse / Feed / Library は独立したトップレベル体験であり、共通 entity を介して自然に相互接続する。**
正典原本は `_decisions/2026-09-07_toplevel-surfaces-v1.md`。
🔴 **この原則を理由に3面の見た目や構造を機械的に揃えない。** UI 文法は違ってよい。
共通化するのは**同じ entity・同じ state / behavior / aria など同一責務の部分だけ**。

## モック是正キュー（2026-08-21 正典⇄モック全面照合で検出）

詳細は _audit/canon-vs-mockup-20260821.md。**正典ではなくモック側を直す項目。**
**性質**: HOLDとは別枠。モックで既に問題として顕在化していて、直せば直せるが「見た目が変わる」ため
確認フローとの調整が要る。モック詰め作業の中で1件ずつ扱う（今は棚に上げる）。

### ✅ 完了（2026-08-22 モック是正A・見た目不変の範囲）

- ドメイン誤記 myrig-rc.com → myrigrc.com（pc/myrig-auth-onboarding-pc-v0.2.html 4箇所）
- **廃止値 setup の撤去** — PC composer :1043 / モバイル register-log.html:381 /
  pc/myrig-public-garage-logs-v3.html:502（「セットアップ」チップ）。
  これで log_type 絞り込みUIは全ページ4値に揃った
- PCプレースホルダ統一（pc/myrig-search-v3.html:1173）
- data-pcat とラベルの不一致（search.html:376）
- 古い注記の訂正（pc/myrig-search-v3.html:839 — 実装は既に24件統一済みだった）
- 禁止クラス .mobile-avatar-btn のCSS定義を撤去（使用0件を確認）
- SoT_browse-sidebar.css の .dir-subheader 色帯に注記

反証確認で判明: **SoTテンプレ pc/assets/sot-templates/SoT_browse-sidebar.html は
打ち消し規則を持たず v7色も自前定義しているため、NG-1の色帯が実際に描画されている。**
→ 下記キューBへ（当初「死んだ宣言」と判定したのは誤りだった）

### ✅ 完了（2026-08-22 是正B・局所的な範囲）

- NG-7 アバター違反（notifications）→ 無彩色3階調へ。
  ※白文字が乗るためモバイルの #8B949E/#B1BAC4 は基準割れ（3.08/1.96）。
    PCは AA 4.5:1 を満たす #6E7781/#57606A/#4B535B で構成し、ダーク分岐も追加。
    **モバイル側のアバター色も同じ問題を抱えている（未着手）**
- Library URL の単数形 → 複数形（index.html / library-maker-detail.html）
- フッター法的リンクに data-href（SoTテンプレ＋33ファイル）

**48px対応は撤回。** .rc__chip は `<a>` で .rc__x はその子のため、chip に ::after を足すと
×を覆って削除が壊れる。さらに親 .rc__scroll が overflow-x:auto で高さ30pxにクリップするため
48pxに達しない。見た目を変えずには対応不可。理由は search.html にコメントで残した。

### B: 最優先（見た目が変わる。確認フローとの兼ね合いで要判断）
1. PC Feed が #28 未適用 — pc/myrig-feed-v3.html:700-702 が All/Following/Trending の3タブ。
   ImageLightbox・無限スクロールも未実装。モバイル feed.html が完全な参照実装として存在する
2. **v8カラーのPC展開 — 2026-08-22 に試行して撤回**（_audit/v8-rollout-attempt-20260822.md）。
   **部分適用は不可。途中で止めると v7 より悪化する。**
   v7緑+白文字=CR2.47（読める）→ v8黄+白文字=CR1.08（読めない）。
   --cat-*-on を1箇所でも取りこぼすとそこが壊れる。
   実際の波及は「共有CSS 2行で5ページ」ではなく **live PC 24ページ＋モバイル2ページ**。
   取りこぼした3形: ①子要素が自前で color:#fff を持つ（create-soft__plus/__label）
   ②dark側の上書きが特異度で勝つ ③背景が --cat-* を間接参照（--master-cta-accent）。
   着手するなら全体を1バッチ＋ライト/ダーク両方でコントラスト実測。
   ※関連: 共有トークンの実体が pc/assets/css/ と css/sot/ に**二重化**しており先に統合が要る。
   ※逆向きの同型障害（黄"文字"が白地に乗る・CR1.07）が別途17箇所ある

### B: 高
3. ランキング表現の残存 — home-v3:3209 の data-query-preset="weekly-like-ranking-rig" ほか、
   browse-category-v3:810,814,882,953 / preview.html / モバイル browse-category.html:264,290,317
4. --cat-*-on 未敷設と color:#fff 残存 — garage-favorites:246-248 / garage-pins:246-248 /
   SoT_component-catalog-v6.css:68（黄地に白文字 CR1.08）
5. NG-1 生存 — pc/myrig-library-v3.html:233-241（正典が名指しした当のコードが未撤去）ほか4箇所。
   **＋ SoTテンプレ SoT_browse-sidebar.html の .dir-subheader 色帯（2026-08-22 追加検出）**
6. 登録フォームと検索が別タクソノミー — PARTS登録が独自10カテゴリ（正本は親14）、
   RIG登録カスタムフォームが英語8カテゴリ（正本は24件）
7. surface/weather の登録経路が無い — 検索側は10値/6値で確定しているのに、
    PC composer は自由テキスト1本に天候まで混在。**データが永久に入らない構造**

### B: 中〜低
8. **48px未満のタップ要素**（search.html の最近の検索チップ行）— 見た目変更が不可避。要裁定
9. Cookie同意バナー未実装
10. NG-2（PC46箇所・モバイル14箇所）/ NG-6 / PC中立操作色の2値分裂 / 死んだv7宣言の掃除

### ✅ 解決済み（2026-08-24 SEARCH-UPDATE-001 により）

12. ~~**種別タブの追加ロード方式が正典と矛盾**（無限スクロール vs ページャ）~~
    → **論点が変質して解消。** 種別タブという独立状態を廃止したため「種別タブの方式」自体が消滅。
    現行は **PC=ページャ / Mobile=無限スクロール** で確定（面ごとに違ってよい）。
    Library補助導線の配置をPC/Mobileで分けた理由も同じ（モバイルは下端を持たない）。
    ※ HOLD「追加ロード方式 #25 vs #26」との束ね裁定は**不要になった**
11. ~~**「人気」バッジがUIに描画されている**（search-results.html `.sr-full__badge`）~~
    → **FEATURED枠ごと廃止して解消**（2026-08-24）。当該マークアップは削除済み

### B: 要裁定（2026-08-23 追加 — PC検索）

13. **ゼロクエリ状態をPCに作るか** — 正典 search-page-plan-v2 に規定が無く、
    記載は `_proposals/search-blueprint-v2`・`search-contract-v1`（PROPOSAL）のみ。
    実装しない判断で通しているが、CURRENT旧記述は「PC検索バッチで片付く」としていた。
    正典に規定を起こすか、作らない方針を明記するか要裁定
    ※ 2026-08-24 に「PCには独立した検索トップを設けない」が確定したので、
      **ゼロクエリ＝検索トップではない**点に注意（別論点として残る）

## GPT外部監査の最優先4系統（2026-08-21検出 → 2026-08-22 全件解消済み）

詳細は _audit/gpt-review-20260821.md。すべてCoworkが現物で裏取り済み。
**A・B・C・Dすべて解消。実装（コード自体）はモック完成後・Next.js着手時に行う。**

A. ✅ 解消済み（2026-08-22 イタヤ裁定、同日GPT監査(revision020→021)で追加是正）— Auth middleware。
   Maintenance/Suspendedの全体ガードがmatcher(/garage,/settings)に縛られ、公開ページで実行されない問題。
   matcherを静的アセット等を除く全パスへ拡張し、Maintenance/Suspendedは全ページに効かせ、
   P1のredirectだけ`isP1Protected()`のパス判定で絞る方式へ変更（Next.js公式と同じnegative-lookahead）。
   P2（/notifications, /register）の挙動は無変更。詳細は`docs/ui/auth-guard-spec-v1.md` v1.2-r2 §5。
   **GPT監査で発見・同日中に修正した残課題**: `NEXT_PUBLIC_MAINTENANCE`はビルド時インライン化されるため
   保守モードスイッチに不適切→server-only `MAINTENANCE_MODE`へ変更／matcherに`sitemap.xml``robots.txt`除外を追加／
   §6 P3の「matcherに含めない」旧記述と新matcherの矛盾を撤回／ガード優先順位（Maintenance>Suspended>P1）を明記／
   Next.js 16なら`middleware.ts`→`proxy.ts`改称の注記／APIルート用の別契約(503/403 JSON)を実装時確定として明示。
   実装時に確定する項目: 対象Next.jsバージョン、Maintenance切替方式（env/Flag/DB）、APIルートの応答契約、
   `/account-suspended`の逆向きguard要否。
B. ✅ 解消済み（2026-08-22 イタヤ裁定、同日GPT監査で残課題を追加修正）— 物理DELETE禁止 ⇔ 解除手段の不在。
   likes/favorites/pins/followsの4テーブルにdeleted_atを追加し、解除操作をUPDATEで行う方式に統一。
   CORE(L1)「物理DELETEは禁止」は無改訂のまま維持（例外化しない判断）。
   理由: 将来の選択肢を狭めない方を優先（履歴を残せば後で物理削除も選べるが、逆はできない）。
   UNIQUE制約は部分インデックス化（WHERE deleted_at IS NULL）。
   **GPT監査（revision018）で発見・同日中に修正した残課題**: RLS共通原則にDELETEポリシーの
   一般許可が残存／images・rig_partsのRLS特記がDELETEを明記／部分UNIQUEの本文記載に対応する
   実DDLが無かった／follows解除のUPDATE許可者が未明記／統計COUNTがdeleted_at IS NULLを
   明記していなかった／「全テーブルdeleted_atあり」という誤記述（rig_partsはremoved_at）。
   すべてschema v1.6-r2「ソーシャル」節・RLS節・インデックス設計節・統計カウント節に反映済み。
C. ✅ 解消済み（2026-08-22 イタヤ裁定、同日GPT監査で穴を追加修正）— RLSがprivateデータを保護していない問題。
   pins定義「非公開」⇔RLS全公開 / favorites・pinsの個別行が全公開でPublic Garage非表示を迂回可能 /
   imagesは親が非公開でも読める / commentsは親の公開可否を検査していない、という4点を解消。
   裁定: 案A（親のis_publicをJOIN判定）採用。pinsは完全非公開（owner限定）、
   favoritesは個別行非公開・公開カウントのみ維持。
   **GPT監査（revision018）で発見・同日中に修正**: 当初「likesは対象外」としたが、
   likesが参照するrig/part/logが後から非公開化されても行が全公開のままという同種の漏洩経路が
   残っていた。likesもimages/comments同様に親公開判定へ変更。favoritesの公開カウントにも
   「対象entityが公開の場合のみ返す」条件を追加。
   詳細は `_decisions/2026-08-22_rls-security-model-v1.md`（ADOPTED）、
   正典反映は `docs/schema/myrig_db_schema_v1_6.md` RLS節（確定版）。
   **実装タイミング: モックアップ完成後・Next.js着手時。**
D. ✅ 解消済み（2026-08-22）— master_aliases.entity_type の 'part' → 'part_master' 誤記を訂正。

**2026-08-22 GPT外部監査（revision018監査）実施記録**: B・Cの裁定内容をGitHub main実物で
再監査。9件（HIGH3件・MEDIUM-HIGH1件・MEDIUM4件・LOW-MEDIUM1件）の残存矛盾を検出、
すべて現物照合の上で同日中に修正・commit・push済み。**「Claudeの裁定をGPTが監査し、
指摘をCoworkが裏取りして反映する」フローが実地で機能した最初の事例。**

**2026-08-22 GPT総合監査（revision023・全docs横断）実施記録**: 本日の全裁定を横串で監査。
HIGH4件・MEDIUM8件・LOW3件を検出。**HIGH4件＋LOW2件をrevision024で修正済み**:
- HIGH1: schema RLS節に「運用・移行時はservice roleで物理DELETE」が残存 → 削除（CORE L1に例外を作らない）
- HIGH2: `/en/garage`等がP1認証を素通り（i18n `/en/*`裁定とauth-guardが未接続）→ `stripLocale()`導入
- HIGH3: `/admin/*` guardがauth-guardに存在しない設計漏れ（page-role-matrixとchecklist L1が要求済み）
  → §5.2 Admin Guard新設
- HIGH4: db-schema-answers §0(L1)「myrig_db_schema_v1.6は正典ではない」が責務分離裁定後の体系と逆
  → 「Research所有領域の正典ではない／App所有領域では正典」へ訂正
- LOW: auth-guardステータス表示をv1.2-r3へ／page-role-matrix参照をv1.4→v1.5へ統一

**MEDIUM 8件の処理（2026-08-22 revision024〜025）**

✅ **文書の書き間違い・古い記述として修正済み（4件）** — モックの議論とは無関係なノイズのため削除・訂正:
- color-token-v8「PCは段階適用中」→ **「PC未適用（v7のまま）・部分適用禁止」**へ訂正（事実と逆だった）
- db-schema-answers §HOLD表の`categories`親14/子90 → HOLD対象から除外。
  **「体系は確定・凍結済み。未完了は実DB投入のみ」**と明記
- HOLD延期理由の事実誤認2件を訂正（size_class「サイズ選択UI未着手」→「値集合の最終確定が未着手」／
  NG-7「通知UI未着手」→「通知色の詰め議論が未着手」）。**HOLD継続自体は妥当**
- pc-mobile-spec-inheritance「承認の有無は未確定」注記を削除 → **ACTIVE正典として確定**

🔵 **モック詰め作業の中で決まる項目として残置（4件）** — 今は裁定しない・催促しない:
- Favorites/PinsのUsers対応（page-role-matrixは`RIG/PARTS/LOG/Users`、schemaのentity_typeは
  `rig/part/log`のみ）→ **「ユーザーをお気に入り/ピンできるか」は仕様議論そのもの**
- コメントowner「非表示」権限に対応するRLS UPDATEポリシーが無い → **moderation機能の設計と一緒に決まる**
- `profiles`はuser_id列を持たない（`id = auth.uid()`）ためRLS共通則が適用できず特記も無い
  → **onboarding/profile編集の設計と一緒に決まる**
- CURRENT外のactive docsに未分類のHOLD/裁定待ちが残る（cross-ref size_class、checklist Cookie同意、
  schema images.alt 等）→ **各docに触れるタイミングで2分類（裁定待ち/将来議論項目＋再開トリガー）へ寄せる**

## HOLD

**現在のフェーズ認識（2026-08-22 確定）**: モックの詰め作業がまだ始まったばかりで、
下記のHOLDは大半が**「将来議論項目（そのUI/機能の議論を始めるまで触らない）」**である。
Claude/GPT/Geminiとも、これらに対して裁定要求・催促を出さないこと。
CORE.md「HOLD原則」を参照。

### 🔴 manufacturer transport値（2026-08-25 / Research照会待ち）

検索トップが出すメーカー値（`TAMIYA` / `Vanquish` / `京商`）と、
モック結果側の値（`Tamiya` / `Vanquish Products`）が食い違う。
`maker=` は完全一致で評価するため、**検索トップ自身が提示した値から偽0件を作れる**。

**禁止**: 大文字小文字無視・部分一致・独自aliasで救うこと。
`manufacturers` は Research 所有（`docs/schema/myrig_db_schema_v1_6.md`）で、
別名は `master_aliases` 側に持つ設計（2026-08-21 F-5: `TRX` は Traxxas の別名）。
モック側で正規化を発明すると本番の alias 解決と二重になって壊れる。

**必要なもの**: Research 正本の manufacturer master / alias の実値。
それまで**表示ラベルと transport 値を分離できない**ので、不一致は残したままにしてある。

### PENDING（2026-08-25 時点）

#### 🔴 Next.js実装前に「決める」必要がある配管4件

DRAFT v0 の GAP 17件のうち、**実装前に決めないと止まるもの**だけを抜いたもの。
本体は判断であって、コード変更は付随物。残り13件はモック段階では放置してよい
（React化で書き方ごと変わる／実装で本物を作る）。

| # | 内容 | なぜ必要か |
|---|---|---|
| 1 | **Routeの正本** — `/terms` か `/legal/terms` か。PC HTMLとMobile JSで綴りが違い、routing-table は `/terms` 側。`/legal/tokushoho` は routing-table に定義が無い | 実装のURL設計そのもの。後から直すと全ページ |
| 2 | **public / owner 境界の実装** — 裁定済み・未実装（下記DECISION参照） | 権限モデル。ガードの置き場所が決まらない |
| 3 | **デザイントークンの正本** — `--cat-rig` が `#66b900`(16ファイル) と `#FBFF00`(5ファイル) の**新旧2系統同時存在**。`:root` を持つファイルが46本、107トークンが多重定義 | Design System化で必ず踏む。見る画面で色が変わる |
| 4 | **routing-table / page-mapping に Mobile 41画面が1つも無い**（grep実測0件） | Mobileが実装計画から構造的に抜け落ちている |

> **進め方の方針（2026-08-25）**: この4件を机上で全部決めてから実装するより、
> **Home → Feed → Garage と実画面を歩きながら1つずつ潰す**方が早くて確実。
> 「このカードを押したらどこへ行くか」を決める作業が、そのまま 1 と 2 の答えになる。

#### 🔵 PC Browse V5 CLOSE 後に残した別タスク（2026-08-30）

**いずれも PC Browse V5 の CLOSE には含まれない。デザイン自体は再オープンしない。**

| # | 項目 | 再開トリガー |
|---|---|---|
| 1 | **1024px以下の共通 Drawer** — 現状 `@media(max-width:1024px)` で `position:fixed`、ハンバーガーで `.home-dir.is-open` | PC Browse 完了後の約束。着手可 |
| 2 | **4軸 visible 判定の実装分散** — 同じ規則が **PC 4か所 ＋ Mobile 1か所**にある。2026-08-30 の `rig_master` / `part_master` 欠落はこれが原因。内容は揃えたが構造は分散したまま | **Mobile Browse 着手時**に PC/Mobile を通した共通化を1バッチで検討 |
| 3 | **`part` / `parts` の語彙2系統** — `data-entity-type` に両方が実在する。現在は表示判定側（§12 グループ）で吸収している | 同上（#2 と同じバッチで扱う） |
| 4 | WORLD selector と OTHER WORLDS が同じ4件を別役割で出している重複 | 将来WORLDが実際に公開されるとき |
| 5 | **PARTS系 LOG の直接関連モデル／実データ抽出方式** — UIとして LOG を置くことは確定だが、`maintenance_logs` に `part_id` が無く正確に抽出できない。`装着RIG → 全LOG` を代替として本実装しない。候補は `maintenance_log_parts` の多対多 | App スキーマを詰めるとき（UIとは分離） |
| 6 | **RIG非紐付けLOGのBrowse上の発見経路** — `maintenance_logs.rig_id` は NULLABLE。`rig_id IS NULL` のLOGは RIG起点Browseのどの関係軸からも到達しない（4軸はすべて `rig_id` 経由のため）。2026-08-30 の4軸修正とは**分離**し、今回の実装範囲に入れない | LOG 面 / Feed / Search で LOG の入口を扱うとき |

> #2 は**分散を許している間、`_audit/browse_sidebar_v5_check.py` が実際に4軸を押して
> 本文を実測することで再発を塞いでいる**。共通化するまでこの検査を弱めないこと。
> 詳細は `_decisions/2026-08-30_browse-axis-display-groups-v1.md`。

#### その他

- **将来のユーザー検索方式** — MVPでは専用入口を置かない。
  方式（通常検索に混ぜる / `@username` 解釈 / 専用画面 / おすすめ 等）は
  実利用を見て決める。**いま方式を固定しない**
- **`scale` HOLD解除後のSearch復帰判断** — `size_class` の値集合が確定したら、
  検索トップ・PC FACETS・Mobile AXES へ**同時に**戻す。片面だけ戻さない
- **`docs/WEB_GRAMMAR_QUEUE.md` の横断課題**（`myrig-mockup` 側）—
  hidden×display の残り9件、検索ページ外の旧語彙、種別チップ件数の意味、空`?q=`の扱い

### 🔴 owner / public のRIG遷移境界（2026-08-25 イタヤ裁定・DECISION）

- 他人の公開GarageにあるRIGを押す → **公開RIG詳細**へ遷移
- 自分のGarageにあるRIGを押す → **オーナー用RIG詳細・管理画面**へ遷移
- **public文脈から owner/edit 画面へ直接入れない**
- **owner / public の違いを、偶然の href やモックのファイル名に依存させない**

**現行実装はこれに反している（GAP・未修正）**。
`user-garage.html:713` の `var to = href || 'garage-rig-detail.html';` がオーナー版を既定にしており、
既定の描画パスは引数を渡さないため public から owner画面へ入れてしまう。
公開サブページ3本（`user-garage-rigs/-parts/-logs`）は正しく結線されており、破れているのはTOPだけ。

実装時は **href の付け替えだけで終わらせない**。context を明示的に持たせて遷移先をそこから決める
（既定値でowner側を指す構造を残すと同型が再発する）。
根本原因は「Mobileにカードの共通コンポーネントが無く、同じ関数が2ファイルへコピーされ既定値だけ取り残された」こと。

### 裁定待ちHOLD（案を出して裁定を求めてよい）

- ~~**種別チップ順（log→lib）とダイジェスト順（lib→log）の食い違い**（search-page-plan-v2）~~
  → ✅ **論点ごと消滅**（2026-08-24 SEARCH-UPDATE-001）。
  種別が3本（RIG/パーツ/LOG）になり、ダイジェスト自体を廃止したため、揃える対象が無くなった

**※ 現在この分類に残っている検索系HOLDは無い。**

#### 🔴 モバイル Rock Crawler カードの種別バッジ欠落（2026-08-29 / BROWSE-CONTRACT-001）

実測で、モバイルの Rock Crawler Category Top のカードにだけ種別バッジが無いことが判明した。

| 面 | バッジ |
|---|---|
| Rock Crawler Category Top（モバイル） | **なし** |
| Parts Root / Motor・ESC（モバイル） | あり |
| Rock Crawler Category Top（PC） | あり（`variant="browse"` が自動付与） |

- **A: モバイルRock Crawlerにバッジを追加**（Cowork推奨）
  PC版と揃う。Category Top は RIG/LOG/PARTS 混在面なので種別表示は必要。
  ただし**基準画面に手を入れる**ため裁定を要する
- **B: 現状維持**（Parts側からバッジを外して揃える）

詳細は `docs/ui/browse-display-contract-v1.md` §8-1。

> 🔵 **2026-08-30: HOLD 維持を再確認。** PC Browse V5 CLOSE の対象外。
> Mobile Browse 自体が未着手のため、この裁定は Mobile Browse に着手するまで求めない。
> **PC 側の修正を Mobile へ自動的に横展開しないこと**（4軸判定も同様。上記 PENDING #2）。

#### Mobile Browse（将来議論項目・未着手）

PC Browse V5 が CLOSE したので、次の着手候補ではある。ただし**まだ着手していない**。
着手時に扱うもの: Mobile Browse 全体 / 上記 badge HOLD /
`js/parts-category-demo.js` `initViewSwitch()` の完全一致判定（PC と同型。今回は触っていない）/
4軸判定の PC・Mobile 共通化（PENDING #2・#3）。

#### SoTファイルの二重管理（2026-08-29 検出 / Browse整合とは別トラック）

`pc/assets/css/` と `css/sot/` に同名SoTファイルが並存し、**双方向に乖離**している。
単純にどちらかを正にできない。

| ファイル | 差 | 新しい側 |
|---|---|---|
| `SoT_app-shell.css` | 103行 | **PC側**（P22-C10/C12/C23 がモバイル側コピーに未反映） |
| `SoT_component-catalog-v6.css` | 4行 | **モバイル側**（P22-B5 の `@media (hover:hover)` がPC側に未反映。ソース内に「#19 PC改訂キューへ起票」と記載あり） |
| `SoT_footer.css` | 0 | 一致 |

あわせて `.edit-card` が PC 4ファイル（`preview.html` L194 / `myrig-home-v3.html` L194 /
`myrig-browse-category-v3.html` L189 / `myrig-browse-parts-v3.html` L437）に重複し、
既に書式が乖離している。HOMEを含むため Browse整合バッチでは扱わない。

※DB系HOLDの多くは _proposals/db-research-inquiry-spec-data-v1.md（**照会#1・DB Researchへ未回答**）の
A: spec_data / B: aliases / C: log_type / D: size_class に対応する。
照会#2はApp側Coworkが実DB確認を自前実行 → _decisions/2026-08-21_db-inquiry-002-realdata.md。
実行したのは E / F / H-1 / I-3 / J-2（＋Gは読みの追認）。**K（facetable）は未着手**（週次ゲート裁定待ち。
E-3で前提インフラ＝part_categories が0行・spec_schema列不在と判明）。

size_class（将来議論項目・値集合の最終確定が未着手）
※検索UI側では既に `data-size-class` として13値が動いている（search-page-plan-v2）。
  未着手なのは「UI」ではなく「値集合を実データに合わせて確定する作業」。
議論着手時の参考: 13値enumの出典は docs/schema/db-schema-answers-v1.md（7/30主査裁定・App側の写し）§2。
2026-08-21 実DB確認（_decisions/2026-08-21_db-inquiry-002-realdata.md J-1/J-2）で判明した実態:
- 実データは18パターン（NULL 639件が最多 / 1/10 341 / M-chassis 2 / mini 1 等の自由記述が混在）
- DB Research PJが保持する7/30裁定書の原本は正典内で未確認（写しのみ）
- Category_Structure_v1.4（6/16改訂・7/30より前）はTEXT自由記述として定義
議論再開時の論点: 実データ主導で再確定するか、13値enumを維持するか。**現段階では催促しない。**

log_type ✅ 決着（2026-08-22 正典へ反映済み・HOLD解除）
4値 maintenance/run/custom/memo が正。「5値論争」は誤診だった。
5値目の実装値は setting ではなく setup（v1.2で廃止済みの値）。
setting という値はモックにもDBにも存在せず、文書上にしかない語だった。
正典側の反映は完了（schema v1.6 / cross-ref v4）。
モック側の撤去も2026-08-22に完了（PC composer / モバイル register-log / public-garage-logs）。

aliases（将来議論項目・データ移行タイミング未確定）
master_aliases が正本であることは確定（db-schema-answers-v1 §0責務境界・Q7）。
議論着手時の参考: 未裁定は parts_masters.aliases TEXT[] ＋GIN索引の物理的処遇（削除 or 併存移行）。
Research側は rig_masters について名指しで言及するが parts_masters は明示していない。
議論再開時期: 実データ移行の段取りを組む時。**現段階では催促しない。**

event_tags（将来議論項目・イベント機能未着手）
2026-08-21 実DB確認（同上 I-3）: event_tags という列名はDB全体のどのテーブルにも存在しない（0件）。
Category v1.4「確定値12種」も従来CURRENT記載の「8値未確定」も、どちらも未実装の机上記述と判明。
議論着手時の参考: 値の議論より先に owner（App未実装機能の先行記述か / Research管轄の未構築か）の確認が必要。
議論再開時期: イベント機能の設計を始める時。**現段階では催促しない。**

認証方式（将来議論項目・OAuthオンボーディング未着手）
プロバイダとメール認証の有無が4文書で不一致。
App_Ready_Design_Rules（Google/GitHub＋メール）/ implementation_checklist（Google＋メール確認）/
auth-guard-spec §7（OAuth only）/ pc-mobile-spec-inheritance #33（Google・X・Facebook・メール認証なし）。
さらに同 #34特記に「PC現物のOAuthは Google / Apple で #33 と不一致」という5系統目の記録もある。
一次資料 auth-onboarding-minimum-spec-v1 は本repo未収録。
議論再開時期: 実装フェーズ（プロバイダ選定はビジネス判断）。**現段階では催促しない。**

追加ロード方式 #25 vs #26（将来議論項目・モバイル一覧UIの詰め未着手）
_state/mobile-feedback-ledger-v1 の同日裁定2件が対立している。
#25「他ページ（検索結果・一覧系）はページネーション維持のまま」
#26「モバイルの一覧系はページネーション原則廃止」（「次へ」が出た時点で離脱するため）
pc-mobile-spec-inheritance G7 は現状「ページングが基本・例外はFeedと検索種別タブ」としているが、
#26 を広く読むとこの前提自体が成立しない。
議論再開時期: モバイル一覧UIの詰め議論を始める時。**現段階では催促しない。**

> **2026-08-24 の部分解消**: 検索面だけは実装で確定した。
> **PC=ページャ / Mobile=無限スクロール**（面ごとに違ってよい、が現行の答え）。
> G7 の「例外は Feed と検索種別タブ」という書き方は、種別タブ廃止に伴い**表現が古い**。
> ただし検索以外の一覧系（Library / ガレージ等）は未決なので、HOLD自体は継続。

操作色 --color-action-primary ✅ 決着（2026-08-22 正典へ反映済み・HOLD解除）
実装に確定値が存在した。color-token-v8 §3 に反映済み。
  light #1F2328 / dark #E6EDF3（css/mobile-shell.css:32,47）
B19但し書き「Bottom Nav中央の＋登録のみブランド色」は不採用（実装は中立黒）。
別件（未解決）: PC側に --color-action-primary が皆無で、中立色は #24292f/#1f2328 の
ハードコードでlightが2値に分裂。PCのトークン化は未着手 → モック是正キュー#10。

NG-7の通知色（2026-08-21 新規HOLD／2026-08-22 「将来議論項目」として据え置き確定）
**通知色の最終設計・詰め議論がまだ着手されていない**（notifications PCページ自体は存在する）。
通知UIの詰め議論を始めるタイミングまでこの項目は裁定しない。
Claude/GPT/Geminiとも「早く決めるべき」と催促しないこと。

実装の現状（記録・議論着手時の参考）:
  モバイル css/mobile-shell.css:42-44 → #D92D20（PARTS色と同一）/ dark :56-58 → #E5534B
  PC通知ページ myrig-notifications-pc-v0.1.1.html:64-65 → #cf222e、地は青系 rgba(9,105,218,.02)
  PCヘッダー通知ドット SoT_app-shell.css:311-321 → #dc2626（ハードコード）
実装に赤が3値存在し、PC側は意図的にPARTS色と分離している。
design-nogo-list NG-7の職域表を「未定」に修正済み（2026-08-22 revision023）。

議論再開時に扱う論点（着手前に決めない）:
- 通知色をPARTS色と別値にするか、NG-7の禁止範囲から通知を外すか
- PC/モバイルの3値をどう1本化するか
- 通知UI設計（バッジ・ドット・トースト等）の全体像との整合

## ローカル正典の是正項目（棚卸し 2026-08-21）

- Charter v1.5 が Research/_docs/ に誤配置（自己申告パスと不一致）
- Research/_docs/_INDEX.md: Knowledge v1.9記載/実体v1.10、Schema v1.1記載/実体v1.2
- 棚卸し時「37件受領/列挙39行」の差異 → 実体は41本と判明（検索系2本が後日追加分）。解消

## 解決済み（記録）

本日までの作業経緯は `_audit/history-20260822.md`。主なものだけ:

- 2026-08-21 ナレッジ運用をCORE+CURRENT方式へ移行。Knowledge 37本の消失ゼロを現物照合で確認
- 2026-08-21 docs/12本の本文精査（3AIクロスチェック＋反証確認）
- 2026-08-22 正典⇄モック全面照合 → 正典を実装へ追随 → モック是正A（見た目不変7件）
- 2026-08-22 正典のシェイプアップと拘束力レベル（L1/L2）の明示

## Task Routing

このrepoの構成と参照ルール:

_AI/            CORE / CURRENT（常時読む。Knowledge接続対象）
docs/           ACTIVE正典（Knowledge接続対象）
  schema/       DB: myrig_db_schema_v1_6（App所有領域のみ） /
                db-schema-answers-v1（Research所有領域の正本） / cross-ref-v4（固定値一覧）
                ※2026-08-21裁定: マスター系テーブルの正本はdb-schema-answers-v1。
                  v1_6はApp所有（rigs/parts/logs/profiles/images等）のみを定義する
  design/       デザイン: color-token-v8 / design-nogo-list
  ui/           UI: page-role-matrix / auth-guard / mobile-contract-v0.5 /
                pc-mobile-inheritance-v1.1 / region-behavior-matrix-v1
                （領域×モードの変身規約 L2。W / M / PC Narrow Fallback と C の分離）
  search/       検索: search-page-plan-v2（現行確定）
  support/      補助: App_Ready_Design_Rules / implementation_checklist
_state/         生きた台帳: mobile-feedback-ledger（Knowledge接続対象）/
                mockup-state-matrix（モック実装状態の実測表。OBSERVATION・上書き更新）/
                HANDOFF_*（スレッド間の文脈。正典ではない）
_decisions/     裁定記録。経緯を辿る時だけ読む
_proposals/     未裁定の検討資料。正典扱い禁止
_archive/       退役文書。通常判断には使わない

Cowork生成物の新規保存先: 種別に応じて docs/ _state/ _decisions/ _proposals/ へ。
作業完了レポートは _archive/ 直行でよい。

## 未決定事項

- Operations Charter（v1.4/v1.5）の正式廃止とCORE.mdへの一本化。
  GPTクロスチェック待ち。
- 検索 _proposals 4文書の裁定（実機比較A/B/C → 確定 → docs/search/へ昇格）。
