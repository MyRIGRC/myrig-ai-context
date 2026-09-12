# Region Behavior Matrix v1.1 — 領域 × モードの変身規約

**拘束力: L2（現在の確定仕様）。** CORE「正典化判断基準」の
「2人（2つのAI）が独立に判断して食い違ったとき何か壊れるか」に該当するため正典化した
（領域の変身先が揃わないと、同じ幅で面ごとに別の姿になり、共有部品が壊れる）。
L2 なので、**より良い案があれば差分を明示して提案してよい**。ただし
「既存仕様と異なる」ことだけを理由に案を捨てない／勝手に変えない。

- 制定: 2026-09-12 JST / Web Fundamentals Phase 2B（Garage M batch CLOSE と同時）
- 参照元: `docs/ui/page-role-matrix-v1.md` 末尾の Breakpoint 注記
- 経緯の原本: CURRENT 093

**適用状況**

| | |
|---|---|
| Garage / Public Garage の Context Rail | ✅ **実装済み・CLOSE**（`myrig-mockup` `b78a2f4`） |
| Feed M / Detail M / Garage filter M | 🟡 **裁定済み・未実装**（別 batch。§3 に契約だけ記載） |
| その他の領域 | 現状の実装をそのまま記載（新規の変身は宣言していない） |

**根拠の分離**: MyRIG の現状＝正典 ＋ モック実体 `b78a2f4`／実測＝Mac 実機 Chrome（実フォント）と cloud Chromium／外部標準＝W3C WCAG 2.2

---

## 0. 何を決めるか（そして何を決めないか）

決める:
1. viewport は**連続量**として扱い、Mobile と PC の間に**未所有帯を作らない**
2. 横断レイアウトの責務は**共有 Shell / 共有部品**が持つ。page-local CSS で Shell 責務（レール・ヘッダー・器の列数）を上書きしない
3. **5 領域**について、**PC 面の W / M / PC Narrow Fallback** と、**専用 Mobile 面の C 契約**を定義し、各セルは「変身」を書く

原則（v1.1 で文言確定）:
- **A. 無断で消さない。** M で領域を**無断で**非表示にしない。**明示的に merge / defer / drop が裁定済みの場合は例外**とし、裁定の所在を表に併記する
- **B. UA / 端末名では分岐しない。** 利用可能幅と **Region の成立条件**（レールを並べられるか／本文の最小幅を確保できるか）で W / M / PC Narrow Fallback を切り替える（C は幅ではなく専用 Mobile 面が持つ契約）。**DOM 構成・React 部品構成・実装方式は固定しない**

決めない（Mac 実測後・実装時）:
- breakpoint の具体 px（本書は W|M ≈ 1025〜1100、M|PC Narrow Fallback ≈ 720 を**現状の実測値**として記録するだけ）
- 実装方式（`display:contents` / grid areas / 部品分割）

---

## 1. 領域（Region）

| 領域 | 中身 | 正本 |
|---|---|---|
| **Global Shell** | ロゴ・Browse/Feed/Library・検索・投稿・通知・アバター／Mobile は BottomNav＋SubHeader | `SoT_app-shell.css` / `mobile-shell.css` |
| **Context Rail（左）** | Browse ディレクトリ・Garage/Public Garage セクション・Search フィルター | Garage の W / M / PC Narrow Fallback ＝ **物理正本 1 本**: `SoT_garage-page.css` §5（Own/Public 共通）／Browse ＝ `SoT_browse-sidebar*.css` |
| **Main** | 一覧グリッド・Detail 本文・Feed | `SoT_garage-list.css` / `SoT_detail.css` / `SoT_feed-card.css` |
| **Aside Rail（右）** | Detail の builder / actions / 関連 / 広告、Feed の右レーン | `SoT_detail-rail.css` |
| **Toolbar** | filter / sort / tabs / chips | `SoT_garage-list.css` / `mobile-garage-list.css` |

## 2. モード（Mode）

| モード | 成立条件 | 現在の Shell | 現状の実測境界 |
|---|---|---|---|
| **W** | Context Rail と Main（＋Detail は Aside も）を**横に並べても本文が成立する** | PC Shell（ヘッダー nav 展開） | ≥ 1025〜1100 |
| **M** | 横に並べられないが、**1 列に積むと面の目的が画面外へ落ちる** | PC Shell（ヘッダーは hamburger） | 721〜1024 |
| **C** | 1 列が前提 | Mobile Shell（BottomNav） | **専用 Mobile 面のみ**（幅ではなく面で決まる） |
| **PC Narrow Fallback** | PC 面が極端に狭くなったときの安全帯 | PC Shell のまま | PC HTML の ≤ 720 |

🔴 **C は幅ではなく「面」で決まる契約。** `mobile-shell.css` の専用 Mobile 面 42 枚が持つ。
**PC HTML を 720px 以下へ縮めた状態は C ではない**ので、別名 **PC Narrow Fallback** と呼ぶ。
そこは BottomNav にもならず、C の契約を実装する帯でもない。1 列に積むだけの保険。
⛔ 「PC の HTML も 720px で BottomNav になるはず」と読まないこと。
これは「viewport は連続量」という原則と矛盾しない。連続なのは**同じ面の中の W → M →
PC Narrow Fallback** であって、PC 面がそのまま C（専用 Mobile 契約）へ変身するのではない。

## 3. 変身規約（Region × Mode）

凡例 — **merge**: 他領域へ統合／**defer**: 位置を後ろへ送る（消さない）／**drop**: 出さない（裁定必須）

| 領域 | W | **M** | C（専用 Mobile 面）／PC Narrow Fallback |
|---|---|---|---|
| Global Shell | PC ヘッダー（nav 展開） | PC ヘッダー（hamburger）**実装済み** | C: BottomNav＋SubHeader **実装済み**／PC Narrow Fallback: PC ヘッダーのまま |
| Context Rail: **Garage / Public Garage** | 展開レール 260px | **Profile → 横長カード（全幅）／ Nav → アイコンレール 64px（sticky・件数はバッジ）／ Activity・LATEST LOGS → 本文の下へ defer** ✅ **実装済み・CLOSE**（Own 6 面 ＋ Public 4 面・共有正本1本）。DOM 論理順は **Profile → Nav → Main → Activity/LATEST LOGS**（P1 是正）。W は CSS Grid で左列へ戻し **非 sticky**、M は Nav rail の sticky を維持 | 横スクロールのローカルナビ **実装済み** |
| Context Rail: **Browse** | 展開レール 220px | **ドロワー**（階層があるためアイコン化しない）**実装済み** | カテゴリシート **実装済み** |
| Context Rail: **Search filter** | 左固定 | 「フィルター」ボタン → パネル **実装済み** | ボトムシート |
| Main: 一覧 | `auto-fill minmax(カード最小幅)` | 同左（幅で列数を固定しない） | 1〜2 列 |
| Main: Detail | 本文 7fr | 1 列 | 1 列 |
| **Aside Rail: Detail** | 右 3fr（≥1280）／ 350px（961〜1279） | 🆕 **Gallery → Builder＋Actions の compact deck → Main。** 残りは Main へ merge：base-model→ベースモデル／entity-feed→LOG／used-parts→使用パーツ。external-links・builder-rigs・ads は Main 後方または RELATED へ adapt。⛔「Gallery → Aside 全体 → Main」は不採用 | 同左 |
| **Aside Rail: Feed** | 左右 170px | 🆕 **一括でタブ／Drawer へ移さない。** 左: LOG投稿→既存 Header 投稿導線へ merge／LOG種別→既存 `.feed-type-chips` を M で表示。右: 注目RIG・おすすめユーザー→**「発見」Drawer**／広告→Feed 内広告へ merge／法務導線→Global Footer・menu へ merge。⛔ おすすめ/新着/フォロー中の3タブへ周辺情報を混ぜない | 1 列 |
| **Toolbar: Garage filter** | 左レーン内 | 🆕 **現状維持しない。** Toolbar の Filter 操作から、**一覧本文の直前**に折りたたみ panel を開く。**初期 closed**。active 条件は Toolbar 側から分かるようにする。⛔ 一覧の下へ置かない | 横スクロール＋ボトムシート |
| Toolbar: その他 | 1 行 | 1 行（横スクロール可） | 横スクロール |

禁止:
- **無断の drop**（裁定なしに M で領域を消す）
- page-local で `.page` / レール幅 / ヘッダーの M 挙動を書く
- UA・端末名での分岐

## 4. 実測

### 4.1 Mac 実機 Chrome（実フォント・DPR2・高さ 900）— 改修前

| 面 | 721〜1024 の実測 | 1100 以上 |
|---|---|---|
| Garage RIG 一覧 | レール全幅に積む → **一覧の先頭 y=1347〜1650**、filter はさらに下（y≈2400〜3100） | レール左 260・一覧 y=378 |
| Public Garage Top | 同型（y=1262〜1281） | レール左 260 |
| RIG Detail | 1 列。Aside は Gallery 直下（y≈774〜1039）→ 本文 | 2 列（本文 670〜770 / rail 365） |
| Browse RIG | ドロワー（成立） | レール左 220 |
| Feed | 1024 で 3 列 → **834 で左右レーンが消える** | 3 列 |

### 4.2 cloud Chromium — 本 batch の前後（Own 4 面 ＋ Public 4 面 × 9 幅 = 72 条件）

| 幅 | 一覧/本文の先頭 y（Own） | 一覧/本文の先頭 y（Public） | レール幅 |
|---|---|---|---|
| 1440 / 1280 / 1100 | 304 → **304**（不変） | 304 → **304**（不変） | 260 → 260 |
| 1024 | 1273 → **497** | 1170 → **467** | 490/992 → **64** |
| 900 | 1273 → **497** | 1170 → **467** | 428/868 → **64** |
| 834 | 1273 → **497** | 1189 → **487** | 395/802 → **64** |
| 721 | 1273 → **497** | 1189 → **487** | 339/689 → **64** |
| **PC Narrow Fallback** 720 / 640 | 1576 → **986**（Activity が Main の後ろへ） | 1189 → **850**（同上） | 688/608 → 不変 |

M 帯 32 条件すべて: 横 overflow 0 / pageerror 0 / Profile カード内の要素重なり 0 / nav 項目 7（Own）・4（Public）すべて可視・件数バッジ 5（Own）・3（Public）可視・ラベル 1 行（12px）。

## 5. 実装（CLOSE 済み / `myrig-mockup` `b78a2f4`）

**物理正本は 1 本**。Own / Public は data・variant 差分だけ。

| ファイル | 変更 |
|---|---|
| `pc/assets/css/SoT_garage-page.css` §5 | **Own / Public 共通の W / M / PC Narrow Fallback をここへ集約**（**C は専用 Mobile 面の契約であり、この PC CSS の責務ではない**）。W（≥1025）= 左列へ defer を戻し `position:static`。M（721〜1024）= `64px + 1fr` ＋ Profile 横長 ＋ Nav レール（`sticky`）＋ Activity/LATEST LOGS defer。**PC Narrow Fallback（≤720）** = 1 列（Own のみ・Public は従来挙動を維持）。両サイドバー CSS より後に読まれない面があるためセレクタは `.page` 込みで (0,3,0) |
| `pc/assets/css/SoT_garage-sidebar.css` | **Responsive 節を撤去**し「正本は `SoT_garage-page.css` §5」と明記。⛔ @media を書き戻さない |
| `pc/assets/css/SoT_public-garage-sidebar.css` | 同旨のコメントのみ（**responsive の複製なし**。実挙動の変更なし） |

variant 差分として共有側に書いたのは 2 か所だけ: 数（Own `.g-stats` / Public `.gs-profile-stats`）と操作（Own `.gs-profile-actions` / Public `.gs-visitor-actions`）。

改修前バックアップ: `_backup/webfund-2B_garage-rail_20260911_JST/`（3 ファイル）

## 6. 検証結果

| 観点 | 結果 |
|---|---|
| W（1440/1280/1100）非回帰 | geometry **完全一致**。pixel は 24 条件中 **23 条件で完全一致**、残り 1 条件は 13px（最大チャンネル差 5/765）— **同一 CSS の再描画でも同規模の差が出る**ことを確認済み（写真ラスタライズのゆらぎ。CSS 起因ではない） |
| **PC Narrow Fallback（720/640）** | P1 の DOM 是正により **Activity / LATEST LOGS が Main の後ろへ移動**（mainY 1576→986 / 1189→850）。視覚順・DOM 順・Tab 順を一致させた結果であり、**2026-09-12 イタヤ裁定で採用**。⛔ drop ではなく defer 位置の宣言 |
| M（1024/900/834/721）× 8 面 | overflow 0 / pageerror 0 / Profile 内重なり 0 / nav 項目・件数すべて可視 |
| 既存 Garage 検査 | ✅ **最終値は §6.3**（Mac 実機・全 14 本 **4,089 PASS / 0 FAIL**）。⚠️ 途中経過として記録に残る「2136 PASS / 0 FAIL」（古い `index.html` で測った誤り・撤回済み）と「2143 PASS / 1 FAIL」（**G20 是正前**の値）は、いずれも **historical / pre-G20** であって現在値ではない |

### 外部独立監査（2026-09-12 / GPT Work「Astra」・Mac 実機 Chrome・10 面 × 8 幅 = 160 条件）

レイアウト方向は支持。横はみ出し・実行エラー 0、W/C の主要領域の座標・寸法一致を独立に再現。
Profile 高さ Own 約 185px / Public 約 155〜175px、64px レールはラベル欠け 0 で成立。
そのうえで 3 点の是正を提起し、Claude 側で**独立に再現・確認済み**:

| # | 指摘 | Claude の再現結果 | 状態 |
|---|---|---|---|
| **P1** | Activity を視覚的に本文の下へ送っても **Tab 順は DOM 順のまま**（Nav → Activity → Main） | 再現。M 900px で Tab は `Profile×2 → Nav×7 → Activity×6 → Main×6`（Own）／`Profile×3 → Nav×4 → LATEST LOGS×4 → Main×12`（Public）。**視覚順と操作順が逆転**（WCAG 2.4.3 / 1.3.2） | ✅ **是正済み**（§6.1・GT13 も追従） |
| **P2** | Own の現在地バーが M 幅だけ青へ戻っている | 再現。`SoT_garage-page.css` §4 が W で「青は操作・リンクの色なので現在地に流用しない」と**明示裁定**して中立色にしていたのに、M の追加 CSS が `--color-accent` を使っていた。W `rgb(101,109,118)` ↔ M `rgb(9,105,218)` | ✅ **是正済み**（下記） |
| **P3** | 「2136 PASS / 0 FAIL」が再現しない（2143 PASS / 1 FAIL） | 再現。原因は上表のとおり（古い `index.html` で測定）。Astra の値が正しい | ✅ **訂正済み** |

**P2 の是正（2026-09-12）**: M の現在地 inset shadow の色を、Own は `--color-text-secondary`（W と同じ中立色）へ、Public は `--color-accent`（W と同じ既存の青）へ分離。
実測で W/M とも Own `rgb(101,109,118)` / Public `rgb(9,105,218)` に一致。
⛔ Public の青を Own へ揃える統一は**本 batch で行わない**（既存仕様であり、Web Fundamentals の修正範囲を広げないため。別レーンの論点）。

### 6.1 P1 是正（2026-09-12）— DOM 論理順と W sticky の決着

**DOM を `Profile → Nav → Main → Activity / LATEST LOGS` へ移した**（Own 6 面 ＋ Public 4 面）。
Public は CSS が `.myrig-public-garage-sidebar .sg-widget` スコープなので、移動先を同クラスの
`--defer` ラッパーで包んで既存スタイルを維持。Own も対称に包んで Own / Public を同じ構造にした。

| 検証（cloud Chromium・10 面 × 9 幅 = 90 条件） | 結果 |
|---|---|
| Tab 順 | **90 条件すべて `Profile → Nav → Main → defer`** ✅（是正前は `Nav → defer → Main`） |
| W geometry（1440/1280/1100 × 10 面 = 30 条件） | navW / navY / mainY / panelY / panelX / profH / over / scrollH **完全一致** ✅ |
| M 帯（1024/900/834/721 × 10 面 = 40 条件） | overflow 0 / pageerror 0 / rail 64px / mainY Own 497・Public 467〜487 **維持** ✅ |
| `position`（実測値） | W = `static` ／ M = `sticky`（Nav rail）／ PC Narrow Fallback = `static` |

**W の sticky — 案A を試して落ち、案B で確定した経緯（実測）**

Activity をレールの外へ出すと左列は約 1,322px → 約 699px に縮み、それまで背が高くて
貼り付かなかった `sticky` が**初めて効き始める**。イタヤ裁定はいったん案A（sticky 許容）
だったが、**限定 Gate（1100/1280/1440 × 代表4面 × scrollY 0/中間/1200/末尾 = 48 条件）で落ちた**:

| sticky を残した場合 | scrollY=0 の 12 条件は ✅。残り 36 条件で 🔴 **直下の defer 領域と重なる**／
ページ末尾では **Header の下へ潜る**（rail top が −188 まで上がる）／レール先端のリンクが遮蔽される |
|---|---|
| **sticky を切った場合（採用）** | **48 条件すべてで Header 衝突なし・Main 被りなし・defer 被りなし・footer 破綻なし・Nav 操作可** ✅ |

切ったときに scrollY=0 で pixel 差（own-top 227,473px ほか 2.3〜3.4k）が出るが、**差分画像で
中身を確認したところ 1px 未満のサブピクセル丸めのズレ**だった（sticky 要素が合成レイヤーへ
乗って整数 px にスナップしていたのが外れるため）。視覚上の破綻ではない。
`relative` / `static+isolation` / `relative+z-index:0` も同じ結果なので、最小の `static` を採用。

⛔ **「CSS では不可能」とは記録しない。** 証明できたのは
**「現行 Grid 構造と W 静止画非回帰を同時に満たす条件では、試した非 sticky 化手段はいずれも
このサブピクセル差を伴った」**まで。実装方式（wrapper / subgrid / React 部品分割）が変われば
前提が変わる。

### 6.2 G20 の是正（2026-09-12）— 実体からの完全内訳

`usergarage` グループの確認導線を**実体から 1 本ずつ列挙**し、091 / 092 の記録と突合した。

| # | 対象 HTML | リンク種別 | 追加時期 |
|---|---|---|---|
| 1–2 | `myrig-public-garage-v3`（Top） | ライト / ダーク | 091 |
| 3 | 〃 | 未ログインで閲覧（`user-garage.html?guest=1`） | 086 |
| 4 | 〃 | 公開0件（`user-garage.html?empty=1`） | 086 |
| 5 | 〃 | 検索のユーザー結果から（`search-results.html?type=user`） | 086 |
| 6–7 | `myrig-public-garage-rigs-v3` | ライト / ダーク | 091 |
| 8–9 | `myrig-public-garage-parts-v3` | ライト / ダーク | 091 |
| 10–11 | `myrig-public-garage-logs-v3` | ライト / ダーク | 091 |

**Top 5 ＋ 一覧 3 面 × 2 = 11 本。** 086 の 3 本に 091 が 8 本（Top 2 ＋ 一覧 6）を足した形で、
CURRENT 091 の記録と完全一致する。よって G20 の期待値 3 は **086 時点の値の取り残し**と確定し、
`_state/garage_check.py` を **3 → 11** へ是正（内訳コメント付き）。
静的再現で `garage-owner-manage` 2/6・`garage` 6/21・`usergarage` 4/11 の 3 グループとも一致、
確認導線の実体欠落 0。

⚠️ 前回報告の「Top 3 ＋ 一覧 3 面 × 2 = 8」は**算術が誤り**（3 + 6 = 9）。実体は Top が 5 本で合計 11 本。

### 6.3 最終結果（2026-09-12 / Mac 実機・Garage M batch CLOSE）

| | |
|---|---|
| 全 14 regression | **4,089 PASS / 0 FAIL** |
| `garage_check` | **513 PASS / 0 FAIL**（G20 是正後） |
| `garage_top_check` | **291 PASS / 0 FAIL**（GT13 を P1 契約へ追従後） |
| `web_meaning_check`（WM7・新設） | **550 PASS / 0 FAIL** |

**GT13 の旧契約は失効した。** 旧: 「左レーン内が Profile → Nav → RECENT ACTIVITY」。
失効の理由 — **CSS Grid の視覚並び替えだけでは keyboard / DOM 順は変わらない。**
RECENT ACTIVITY を aside の中に置いたまま `grid-row` で本文の下へ送っても、Tab は
**Nav → Activity → Main** と回り、視覚順と操作順が逆転する（WCAG 2.4.3 / 1.3.2）。
現契約は「Context Rail の中は Profile → Nav。Activity は消えず main の後方の defer。
W では CSS Grid で左列の Nav の下へ視覚復帰」。
⛔ 緩めた置き換えではない: P1 以前の DOM へ戻すと GT13 は 8 FAIL する（新設 3 件がすべて落ちる）。

⚠️ 未確認:
- 実機実フォント（iPad）での M 帯。Mac 実機は確認済み。cloud は Google Fonts 遮断で字幅が異なる
- 残り 9 検査（本 batch は CSS のみ・Garage 4 本で十分と判断したが、全 13 本は Mac で流し直すこと）
- **既存検査は M 帯（721〜1024）の変身そのものを assert していない。** 現状の合格は「W と C を壊していない」ことの証明であり、M の正しさは未検査 → §7

## 7. WM7（Viewport Continuum / Garage M）— 恒久検査

`_state/web_meaning_check.py`。既存 13 本は W と PC Narrow Fallback しか assert しておらず、
将来だれかが `SoT_garage-page.css` §5 の @media を触って M 帯を戻しても検知できなかった。
本書 §3 の Garage 行の契約をそのまま検査にしたもの。

| # | assert |
|---|---|
| 1 | M 帯で Garage nav rail が **≒64px** かつ **`sticky`** |
| 2 | **Main が Context Rail の全積みの後ろへ落ちない** |
| 3 | Own / Public とも **Profile / Nav / Main が存在する** |
| 4 | **Activity / LATEST LOGS が単純消失していない**（defer であって drop ではない） |
| 5 | **721 / 834 / 900 / 1024 で横 overflow なし** |
| 6 | **720 / 1025 の境界で期待どおりのモードへ遷移する** |
| 7 | **M 帯でキーボードの主要順序が Context Nav → Main → deferred region となり、視覚順と論理順が逆転しない** |
| 8 | **W 帯で Context Rail / Nav が `static` を維持する。**scroll 時に固定 Header の下を通常どおり通過することは**許容**するが、**`sticky` / `fixed` 化して Header 領域へ留まり続けない**。また **Main / deferred region / footer とのレイアウト重複を起こさない**（⛔ Header との単純な矩形交差は FAIL にしない） |

**550 PASS / 0 FAIL**（10 面 × 9 幅）。`--selftest` の故障注入 5 種で 290 FAIL を検出。

## 8. 残り（別 batch）

§3 で契約は裁定済み、実装は未着手。

| 領域 | 状態 |
|---|---|
| **Aside Rail: Feed の M** | 裁定済み・未実装。左は Header 投稿導線と `.feed-type-chips` へ merge、右は「発見」Drawer ／ Feed 内広告 ／ Global Footer へ merge |
| **Aside Rail: Detail の M** | 裁定済み・未実装。Gallery → Builder＋Actions の compact deck → Main。残りは Main の該当節へ merge |
| **Toolbar: Garage filter の M** | 裁定済み・未実装。Toolbar の Filter から一覧本文の直前に折りたたみ panel（初期 closed） |

⛔ 本書の Garage 行（CLOSE 済み）を、これらの実装のついでに開け直さない。
