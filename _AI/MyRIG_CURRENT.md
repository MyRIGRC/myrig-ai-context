# MyRIG CURRENT

revision: MYRIG-20260822-011
updated: 2026-08-22 12:10 JST

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

## 現在地

フェーズ: モックアップ確認中（Next.js実装前）
PC mockup: 39ページ、SoT CSS/JS構成、概ね完成
Mobile mockup: 約55ページ、確認ダッシュボードで管理中
確認状況: 確定0 / PC版のみ6 / 要確認49 / 未着手0（2026-08-20時点）
確認ダッシュボード: デプロイ済（新規スレッド開始時に必ずURL・所在を確認すること）
Mockup shell: v0.5 / Home r14 / P22-C35

ナレッジ運用: 2026-08-21 CORE+CURRENT方式へ移行。
本repo（myrig-ai-context）が全AI共通の正本。
旧Claude Knowledge 41本は本repoへ分類移送済み（下記Task Routing参照）。

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

2026-07-30
FEED文法はモバイル基準（おすすめ/フォロー中タブ）。
PC版を差し替える（_decisions/p22-c21 参照）。実装待ち。

## モック是正キュー（2026-08-21 正典⇄モック全面照合で検出）

詳細は _audit/canon-vs-mockup-20260821.md。**正典ではなくモック側を直す項目。**

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

### B: 最優先（見た目が変わる。着手前に確認が要る）
1. PC Feed が #28 未適用 — pc/myrig-feed-v3.html:700-702 が All/Following/Trending の3タブ。
   ImageLightbox・無限スクロールも未実装。モバイル feed.html が完全な参照実装として存在する
2. v8カラーがPC 40ファイル中6ファイルのみ適用 — v7ローカル定義14 / 共有CSS継承3 / 未定義17。
   共有 pc/assets/css/SoT_tokens-v6.css:28,54 を直せば5ページ一括解決
3. NG-7 アバター違反 — pc/myrig-notifications-pc-v0.1.1.html:476-482 に
   紫#7c3aed・緑#15803d・橙を含む7色グラデーション。「緑紫橙 全廃」に正面衝突

### B: 高
4. ランキング表現の残存 — home-v3:3209 の data-query-preset="weekly-like-ranking-rig" ほか、
   browse-category-v3:810,814,882,953 / preview.html / モバイル browse-category.html:264,290,317
5. --cat-*-on 未敷設と color:#fff 残存 — garage-favorites:246-248 / garage-pins:246-248 /
   SoT_component-catalog-v6.css:68（黄地に白文字 CR1.08）
6. NG-1 生存 — pc/myrig-library-v3.html:233-241（正典が名指しした当のコードが未撤去）ほか4箇所。
   **＋ SoTテンプレ SoT_browse-sidebar.html の .dir-subheader 色帯（2026-08-22 追加検出）**
7. 登録フォームと検索が別タクソノミー — PARTS登録が独自10カテゴリ（正本は親14）、
   RIG登録カスタムフォームが英語8カテゴリ（正本は24件）
8. surface/weather の登録経路が無い — 検索側は10値/6値で確定しているのに、
    PC composer は自由テキスト1本に天候まで混在。**データが永久に入らない構造**

### B: 中〜低
9. Library URL の単数形残存（index.html:954,960,966 ほか）
10. 48px未満のタップ要素4件（search.html の .rc__chip/.rc__x/.rc__clear/.ax-tools__b）
11. Cookie同意バナー未実装 / PCフッター特商法リンクがhref="#"のまま
12. NG-2（PC46箇所・モバイル14箇所）/ NG-6 / PC中立操作色の2値分裂 / 死んだv7宣言の掃除

### B: 要裁定
13. **「人気」バッジがUIに描画されている** — search-results.html:621
    `<span class="sr-full__badge">人気</span>`。ranking全廃方針および裁定⑦「人気順は使わない」と
    整合していない。「注目」等へ変えるか、順位ではないラベルとして許容するか要裁定
    （2026-08-22 検出。文書監査では「該当語0件」と誤って宣言していた箇所）

## GPT外部監査の最優先4系統（2026-08-21・監査済み／未反映）

詳細は _audit/gpt-review-20260821.md。すべてCoworkが現物で裏取り済み。
**Next.js実装前に解消すること。モックアップ制作はブロックしない。**

A. Auth middleware — Maintenance/Suspendedの全体ガードがmatcher(/garage,/settings)に
   縛られ、公開ページで実行されない。matcher拡張＋内部分岐か、全体ガードとP1ガードの分離。要裁定。
B. 物理DELETE禁止 ⇔ 解除手段の不在 — likes/favorites/pins/followsにdeleted_at/is_activeが無く、
   いいね/フォロー/ピン解除は物理DELETEでしか実現できない。
   soft-delete列の追加か、CORE側で「関係テーブルの解除は例外」と正式裁定するかの二択。要裁定（L1改訂）。
C. RLSがprivateデータを保護していない —
   pins定義「非公開」⇔RLS全公開 / favorites・pinsの個別行が全公開でPublic Garage非表示を迂回可能 /
   imagesは親が非公開でも読める / commentsは親の公開可否を検査していない。
   UI非表示はアクセス制御にならない。要裁定（セキュリティモデル）。
D. ✅ 解消済み（2026-08-22）— master_aliases.entity_type の 'part' → 'part_master' 誤記を訂正。

## HOLD

※DB系HOLDの多くは _proposals/db-research-inquiry-spec-data-v1.md（**照会#1・DB Researchへ未回答**）の
A: spec_data / B: aliases / C: log_type / D: size_class に対応する。
照会#2はApp側Coworkが実DB確認を自前実行 → _decisions/2026-08-21_db-inquiry-002-realdata.md。
実行したのは E / F / H-1 / I-3 / J-2（＋Gは読みの追認）。**K（facetable）は未着手**（週次ゲート裁定待ち。
E-3で前提インフラ＝part_categories が0行・spec_schema列不在と判明）。

size_class
HOLD継続。13値の出典は docs/schema/db-schema-answers-v1.md（7/30主査裁定・App側の写し）§2に実在する。
HOLDの理由は出典の不在ではなく、2026-08-21実DB確認で判明した実装実態との乖離
（_decisions/2026-08-21_db-inquiry-002-realdata.md J-1/J-2）:
- 実データは18パターン（NULL 639件が最多 / 1/10 341 / M-chassis 2 / mini 1 等の自由記述が混在）
  で、13値enumとして運用されていない
- DB Research PJが保持する7/30裁定書の原本は正典内で未確認（写しのみ）
- MyRIG_Category_Structure_v1.4（6/16改訂・7/30より前）はTEXT自由記述として定義
App側提案: 実データ主導（18パターンを土台に再確定）へ切替。イタヤ裁定待ち。

log_type ✅ 決着（2026-08-22 正典へ反映済み・HOLD解除）
4値 maintenance/run/custom/memo が正。「5値論争」は誤診だった。
5値目の実装値は setting ではなく setup（v1.2で廃止済みの値）。
setting という値はモックにもDBにも存在せず、文書上にしかない語だった。
正典側の反映は完了（schema v1.6 / cross-ref v4）。
モック側の撤去も2026-08-22に完了（PC composer / モバイル register-log / public-garage-logs）。

aliases
master_aliases が正本であることは確定（db-schema-answers-v1 §0責務境界・Q7）。
未裁定なのは parts_masters.aliases TEXT[] ＋GIN索引の物理的処遇（削除 or 併存移行）。
Research側は rig_masters について名指しで言及するが parts_masters は明示していない。

event_tags
HOLD。2026-08-21 実DB確認（同上 I-3）: event_tags という列名はDB全体のどのテーブルにも存在しない（0件）。
Category v1.4「確定値12種」も従来CURRENT記載の「8値未確定」も、どちらも未実装の机上記述と判明。
値の議論より先に owner（App未実装機能の先行記述か / Research管轄の未構築か）の確認が必要。

認証方式（2026-08-21 新規HOLD）
プロバイダとメール認証の有無が4文書で不一致。
App_Ready_Design_Rules（Google/GitHub＋メール）/ implementation_checklist（Google＋メール確認）/
auth-guard-spec §7（OAuth only）/ pc-mobile-spec-inheritance #33（Google・X・Facebook・メール認証なし）。
さらに同 #34特記に「PC現物のOAuthは Google / Apple で #33 と不一致」という5系統目の記録もある。
一次資料 auth-onboarding-minimum-spec-v1 は本repo未収録。実装フェーズで裁定する。

追加ロード方式 #25 vs #26（2026-08-21 新規HOLD）
_state/mobile-feedback-ledger-v1 の同日裁定2件が対立している。
#25「他ページ（検索結果・一覧系）はページネーション維持のまま」
#26「モバイルの一覧系はページネーション原則廃止」（「次へ」が出た時点で離脱するため）
pc-mobile-spec-inheritance G7 は現状「ページングが基本・例外はFeedと検索種別タブ」としているが、
#26 を広く読むとこの前提自体が成立しない。台帳が生きた正典であるため要調停。

操作色 --color-action-primary ✅ 決着（2026-08-22 正典へ反映済み・HOLD解除）
実装に確定値が存在した。color-token-v8 §3 に反映済み。
  light #1F2328 / dark #E6EDF3（css/mobile-shell.css:32,47）
B19但し書き「Bottom Nav中央の＋登録のみブランド色」は不採用（実装は中立黒）。
別件（未解決）: PC側に --color-action-primary が皆無で、中立色は #24292f/#1f2328 の
ハードコードでlightが2値に分裂。PCのトークン化は未着手 → モック是正キュー#12。

NG-7の通知色（2026-08-21 新規HOLD）★モック照合で裁定材料が判明
実装では PC が既に PARTS色と別値を採用していた。正典はモバイル片面だけを記述している。
  モバイル css/mobile-shell.css:42-44 → #D92D20（PARTS色と同一）/ dark :56-58 → #E5534B
  PC通知ページ myrig-notifications-pc-v0.1.1.html:64-65 → #cf222e、地は青系 rgba(9,105,218,.02)
  PCヘッダー通知ドット SoT_app-shell.css:311-321 → #dc2626（ハードコード）
実装に赤が3値存在し、PC側は意図的に分離している。「PARTS色と別にする」方向に実装は倒れている。

design-nogo-list NG-7の職域表が、通知・未読・NEWに #D92D20＝PARTSカテゴリ色を割り当てており、
同表1行目「カテゴリ色は種別識別のみ」と自己矛盾。
（NG-4の「赤は警告・削除に見える」は現在地の行の塗りつぶしの話であり通知色とは別論点だが、
「赤＝PARTS色が別の意味を帯びる」という論点は共通する）
CURRENT旧記載の「NG-7一般化は誤り」の実体はここである可能性が高い（未確定）。
要裁定: 通知色をPARTS色と別値にするか、NG-7の禁止範囲から通知を外すか。

## Pending Canonical Updates

### 2026-08-22 モック照合の結果を正典へ反映（完了）

- ✅ **search-page-plan-v2 を全面改稿** — 実装（P22-C29「D案」）へ追随。探し方4枚＋開閉軸5本、
  ソートの裁定⑦⑧（3ピル廃止・「人気順」不使用）、FEATURED のユーザータブ除外、検索面の広告ゼロ、
  検索スコープ定義を実装から昇格、プレースホルダ基準文言を確定
- ✅ page-role-matrix: **Parts Master Detail / Maker Detail の「未作成」誤記を訂正**（実装済み）。
  Feed行にPC未適用の但し書き。未捕捉ページ表（compare/help/legal ほか）と `/rigs` の扱いを追記、v1.5へ
- ✅ color-token-v8: **操作色の実値を実装から確定**（`#1F2328`/`#E6EDF3`・要確認注記を撤去）。
  **v8の適用範囲を明記**（PC 40中6ファイル・未定義17ファイルの二重化を含む）。PC未トークン化も記録
- ✅ design-nogo-list: NG-1「PC側は未処理」・NG-6「是正はhomeのみ」を明記。
  NG-7通知色にPC実装（PARTS色と分離済み）を裁定材料として追記
- ✅ **log_type を4値で決着**（schema v1.6 / cross-ref v4 の両方）。5値論争は廃止値 `setup` の残骸
- ✅ db-schema-answers: `master_aliases.entity_type` の `'part'` → `'part_master'` 誤記訂正（GPT指摘D）。
  本文の size_class 13値にHOLD注記
- ✅ auth-guard: **#14裁定（context8種・文言5グループ）を本文統合**し v1.1 へ。冒頭注記を解消。
  前提5文書がrepo未収録である旨も明記
- ✅ App_Ready: Rule1⇔Rule7の矛盾を解消。「認証はmiddleware一元管理」をP1＋全体ガード限定へ訂正
- ✅ implementation_checklist: Cookie同意の自己矛盾（明示同意型 vs 黙示同意型）を明示化。
  rate limit・画像制限を「暫定の初期値」へ降格。モックが全面noindexである旨を注記

### 2026-08-21 docs精査で解消済み（3AIクロスチェック → 本文修正完了）

- ✅ Page Role Matrix: 冒頭注記3件（Feedタブ / ranking全廃 / 日英MVP）を本文へ統合
- ✅ Auth Guard: middleware matcher の自己矛盾を是正（P2は matcher 対象外で固定）
  ※**冒頭注記（#14文言・§3.1/§3.3旧定義）の本文統合は未実施**。版上げも保留（下記「未解消」参照）
- ✅ Mobile Contract v0.5: search-page-plan v1参照 → v2へ更新
- ✅ Mobile Contract v0.5: **§2にv7カラー（緑/紫/橙）が残存していた重大事故源を是正**（v8へ）
- ✅ pc-mobile-spec-inheritance G7: 無限スクロールの例外にFeed(#25)＋検索種別タブ(#26)を明記
  ※**#25と#26の関係自体は未調停**（下記HOLD参照）
- ✅ App Schema v1.6: **Research所有領域（manufacturers/categories/rig_masters/parts_masters）の
  列定義を参照へ降格**（→ v1.6-r2）。rig_type旧6値・platform自由TEXT・categories単一表・
  specs/spec_data不一致・RLSのdeleted_at一般化を是正
  ※**文書上の整合まで。size_class/power_source/platform_slug のApp側実列追加DDLは未設計。
    App↔Research写像表（cross_ref）も未作成**（下記「未解消」参照）
- ✅ color-token-v8: NG-2/NG-3誤引用、LOGダーク「未裁定」（実際は既裁定）を是正
- ✅ App_Ready_Design_Rules / implementation_checklist: 色・URL・images・notifications・ドメイン
  （myrig-rc.com→myrigrc.com）・sitemap・onboarding経由を是正し、各正典への参照へ降格

### 未解消

- **正典⇄モック照合（2026-08-21）で判明した「正典が古い」項目が未反映**。詳細は
  _audit/canon-vs-mockup-20260821.md。主なもの:
  - **search-page-plan-v2 が実装から1世代遅れ** — 実装は 2026-08-21 P22-C29「D案」
    （探し方4枚＋軸の開閉）へ移行済み。正典の「種別で探す→人・ガレージ独立行→…」構成は
    もう存在しない。ソートの3ピルも裁定⑦⑧で廃止済み（「人気順」は意図的に不使用）
  - **page-role-matrix §3 の「未作成」が誤り** — Parts Master Detail / Maker Detail は実装済み
  - **page-role-matrix が compare.html / help.html / legal.html を捕捉していない**
  - **color-token-v8 が v8の適用範囲を書いていない** — PC 40中6ファイルのみ適用の段階適用中
  - **正典がPC側にトークン層が無い事実を記述していない**
  - **NG-1「モバイル側は処理完了」/ NG-6「P22-C12で是正」の適用範囲がPC未処理を含んでいない**
  - **検索スコープ正典表は「宿題」ではなく実装から昇格可能**
    （search-results.html:524-576 の LABEL/UNIT/GRIDTYPE/DIGEST/SORT_OPTIONS が事実上の一元定義）
    ※ただしタブ順(log→lib)とダイジェスト順(lib→log)が食い違うため裁定1点必要
- **GPTの総合所見: 「注記で旧本文を覆う方式がまだ多く、正典本文だけ読めば安全という状態には達していない」**
- **App Schema: size_class / power_source / platform_slug のApp側実列追加DDLが未設計**
- **App↔Research 写像表（cross_ref）が未作成。**これが無いため本文中の `parts_masters` が
  App側（複数形）/ Research側 `part_masters`（単数形）のどちらを指すか曖昧な箇所が残る。
  db-schema-answers-v1 §0 は「写像表を作るまで同名別義のカラムを増やすな」と警告
- `images.alt`（画像代替テキスト）の追加要否 — App_Ready_Design_Rules から schema への申し送り
- **エラー色・成功色がどの正典にも未定義**（NG-7「黄赤青と白黒以外を足さない」との整合を含め未裁定）
- **`docs/design-rules.md` が repo 内に存在しない**（page-role-matrix `:246` `:354` が
  「Garage Detail 固有ルール」「Breakpoint正典」の参照先として指している）
- **cross-ref-category-names-v2.md が repo 内に存在しない**（v4 が「part_categories親14・
  rig_categories24 の詳細一覧はこちらが正」と参照している）
- **auth-guard-spec-v1 が前提とする5文書が repo 内に不在**
  （auth-onboarding-minimum-spec-v1 / nextjs-routing-table-v1 / appheader-interaction-spec-v1 /
  dialog-interaction-spec-v1 / error-states-decomposition-MR-AUDIT-002）
- **mobile-coverage-matrix の所在と版数が不整合** — 実体は _archive/knowledge-20260821/ にあるのに
  ACTIVE 2本が参照優先順位に据えている。参照版数も contract v0.5=v1.1 / pc-mobile=v1.2 と不一致
- design-nogo-list の実測値「黄 1.64」が同文書内の `#FBFF00`=1.08 と一致しない（要確認・結論には影響なし）
- design-nogo-list NG-7 の既知誤記（実体は通知色の自己矛盾と推定。上記HOLD参照）
- Operations Charter: v1.4/v1.5とも現状と乖離。CORE.mdが実質後継。
  Charter正式廃止はGPTクロスチェック後に裁定（下記・未決定事項）
- pc-mobile-spec-inheritance v1.1: 承認の有無が未確定のままACTIVE正典運用されている
  （文書側は2026-08-21に「イタヤ承認待ち」→「承認の有無が未確定」へ表現を修正済み）。
  正典として確定させるか参考文書に落とすか要裁定
- Mobile Contract v0.5 §7: Home視覚凍結の pixel diff 0 ゲートがv8適用前baseline前提。
  v8適用後baselineの再作成が必要（文書には注記済み・実作業は未実施）

## ローカル正典の是正項目（棚卸し 2026-08-21）

- Charter v1.5 が Research/_docs/ に誤配置（自己申告パスと不一致）
- Research/_docs/_INDEX.md: Knowledge v1.9記載/実体v1.10、Schema v1.1記載/実体v1.2
- 棚卸し時「37件受領/列挙39行」の差異 → 実体は41本と判明（検索系2本が後日追加分）。解消

## 解決済み（記録）

2026-08-21
docs/配下12本の本文精査を完了（Cowork）。GPT・Gemini・Claudeの3AIクロスチェック＋修正後の反証確認を実施。
バックアップ: _backup/audit_20260821/。精査サマリ: _audit/docs-audit-20260821.md。

初回精査の誤り2件はクロスチェックで棄却し、指摘どおりに訂正した（誤った指摘の側を採用していない）:
- 「LOGダーク #3F709E は未裁定」→ 誤り。NG-7職域表・NG-3に記載があり既裁定。color-token-v8を既裁定に訂正
- 「App_Ready参照先が存在しない」→ 断定しすぎ。正しくは「repo未収録、かつv4.2→v4.4_final_rev3の旧版参照」

NG-7操作色は4AI間で判断が割れたため断定せず、color-token-v8 §3に要確認注記を置いた（下記HOLD参照）。

反証確認で自ら作った不具合の是正: maintenance_logs表の分断、rig_type旧6値のApp側残存、
categories FK取りこぼし、変更履歴の順序逆転、size_class/event_tagsの実DB確認結果との不整合。
part_masters(Research・単数形)とparts_masters(App・複数形)の同名別義は、写像表(cross_ref)が未作成のため
一括置換せず注記にとどめた（下記「未解消」参照）。

2026-08-21
App/MOKUP/myrig_pc_Ver3/docs/ 消失問題を解決（P22-C28再編の誤退避。85ファイル復帰、
.vercelignore に docs 追加）。

2026-08-21
CURRENT同期方式確定: 本repo → push → Claude Project GitHub Integration Sync now。
revision 003/004 で追従テスト済み。web_fetch方式は不採用（ツール側キャッシュ）。

2026-08-21
Knowledge 24件ローカル未保存の原因: Coworkは project_write で
Knowledge にのみ書く仕様。今後の生成物は本repoへ書く。

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
_state/         生きた台帳: mobile-feedback-ledger（Knowledge接続対象）
_decisions/     裁定記録。経緯を辿る時だけ読む
_proposals/     未裁定の検討資料。正典扱い禁止
_archive/       退役文書。通常判断には使わない

Cowork生成物の新規保存先: 種別に応じて docs/ _state/ _decisions/ _proposals/ へ。
作業完了レポートは _archive/ 直行でよい。

## 未決定事項

- Operations Charter（v1.4/v1.5）の正式廃止とCORE.mdへの一本化。
  GPTクロスチェック待ち。
- 検索 _proposals 4文書の裁定（実機比較A/B/C → 確定 → docs/search/へ昇格）。
