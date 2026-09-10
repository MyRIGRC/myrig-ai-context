# MyRIG CURRENT

revision: MYRIG-20260910-081
updated: 2026-09-10 11:25 JST（生成: Cowork ZoneInfo("Asia/Tokyo")）

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

---

## 🔴 いまやっていること（NOW）

> **スレッドをまたぐとき最初に読む節。** イタヤは環境（デスクトップCowork / ブラウザCowork /
> ブラウザ通常チャット）を切り替えながら作業するため、**前スレッドの記憶に依存せず
> ここだけ読めば再開できる**状態を保つこと。作業の区切りで必ず更新する。

**最終更新: 2026-09-10 / revision 081（**Own Garage PC 6面すべて v7 実装済み**。色の職域収束と横断機能監査の修正も完了。**まだ Garage 全体 CLOSE ではない** — 残るデザイン判断項目の実画面確認後に CLOSE 判定する）**

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

### 🔴 `mockup` は正典をpushしない（2026-08-24 実測）

`mockup` が push するのは **`myrig-mockup`（モック）だけ**。
`myrig-ai-context`（正典）は**別リポジトリ**なので手動pushが要る。
2026-08-24、モック側が同期済みの状態で `mockup` を実行して
`Everything up-to-date` と出たが、正典側は未push4本のまま残っていた。
**正典を更新したら、モックとは別に push すること。**

---

## デプロイ運用（2026-08-23 実測で確定・過去の記述を訂正）

### 手順

1. Cowork が対象ファイルのみ編集（`git add -A` は使わない）
2. `_backup/<バッチ名>_<YYYYMMDD>_JST/` へ改修前をバックアップ
3. commit（**author は指定しない。下記を厳守**）
4. イタヤがターミナルで `mockup` → push ＋ Vercel deploy
5. 実機／ブラウザで確認 → 裁定 → 次バッチ

`mockup` はリポジトリ直下で `git push` → `npx vercel` を実行する。
未commitの変更があれば `mock: update <YYYY-MM-DD HH:MM JST>` という
コミットが自動生成される（`~/.zshrc` は未確認のため、この自動commit部分は推測）。

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
- `.vercelignore` は `_archive` `*.zip` `.DS_Store` `_backup` `docs` のみ除外（`pc/` は除外していない）

### 恒久対策の候補（未着手）

Vercel プロジェクトを `MyRIGRC/myrig-mockup` に Git連携させれば `git push` だけで
自動デプロイになり、CLI認証と author 判定に依存する経路を減らせる。要検討。

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
                pc-mobile-inheritance-v1.1
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
