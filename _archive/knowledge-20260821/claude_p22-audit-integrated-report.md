# P22 確定8ページ 徹底再検証 — 統合監査レポート

**日時:** 2026-07-25 09:11 JST
**対象:** 確定8面（Home r14凍結 / 探す2面 / Feed / RIG詳細 / パーツ詳細 / ログ詳細 / ガレージトップ / ガレージRIG詳細オーナー編集）
**手法:** Cowork＝8並列エージェント（PC正典7本との突合＋Playwright 440px実描画・elementFromPointヒット実測・`node --check`）＋ GPT WORK静的検証の統合。**本監査中のファイル編集・移動・削除はゼロ。**
**判定基準:** pc-mobile-spec-inheritance-v1 §1 R1/R2・§4 G1〜G10・ledger確定裁定優先（/tmp/audit/CRITERIA.md）

---

## 総括

一意指摘 約105件（Cowork 113件・GPT 12件から重複排除）。**GPTの結論「完成扱いの前に最低3点の修正・再監査が必要」に同意**し、Cowork実測でさらに機能欠陥3件（S1-2〜S1-4）を追加する。

| 区分 | 件数 | 代表 |
|---|---|---|
| S1 最優先（機能欠陥・P0相当） | 6 | 検索白画面 / 非公開化誤爆 / Public Garage導線 |
| S2 48px回帰（双方一致） | 約85要素 | P14ゲート未通過＋ヘルパー2系統＋隣接衝突 |
| S3 R2無断省略（要裁定） | 約40 | footer情報導線 全9ページゼロ ほか |
| S4 実装バグ（機能・整合） | 約20 | is-on不一致 / 5枚グリッド破綻 ほか |

---

## S1. 最優先（P0相当・6件）

| # | 検出 | 箇所 | 内容 | 修正方針 |
|---|---|---|---|---|
| S1-1 | **GPT→Cowork実ファイル確認済み** | `rig-detail.html:386,500` / `search-results.html:480` / `garage.html:559,600` | **Public Garage導線の誤結線**。他ユーザー（@crawler_junkie）の「ガレージを見る›」が2箇所とも `garage.html`（Own Garage）を指す。検索のユーザー結果は `href="#"`、garage.htmlの公開ページ導線は未処理 `data-href="/user/..."`。**Public Garageへ到達する経路がモック全体にゼロ** | Public Garage（/user/[username]）はモック未実装のため、①`user-garage.html` を早期に用意して結線するか、②暫定でtoast「公開ガレージは次Phase」＋data-href保持を裁定。**Own `garage.html` への誤結線だけは即時解消必須** |
| S1-2 | **Cowork単独（実測再現）** | `search.html:22` → `search-results.html:640` | 検索トップ「パーツ」チップが `?type=part`（正: `parts`）。`DATA[curType].length` で **TypeError→白画面**。Playwright実測でpageerror再現 | href を `?type=parts` に修正＋`setType()` 冒頭に `DATA[type] ? type : 'all'` の入力検証 |
| S1-3 | **Cowork単独（実測再現）** | `garage-rig-detail.html:890,896` | 設定変更シートの**キャンセルがフォーム値を巻き戻さない**。archived＋非公開→キャンセル→再度開いて保存で確定する**非公開化の誤爆経路** | `revertSetSheet()`（開いた時点のstatus/is_publicを保持・復元）を実装し `gdSetCancel` はそれ経由に |
| S1-4 | **Cowork単独（実測再現）** | `garage-rig-detail.html:929-930` | PIT未所属（`data-pit="off"`）で**メモ表示行と編集ボタンが両方消え、RIGメモが恒久到達不能**。ledger #36「PITから外してもRIG本体とメモは削除しない」と矛盾。PC QUICK NOTEは常設 | メモをPIT所属ブロック外の独立ブロックへ切り出し。off時はPIT設定ボタンのみ「PIT TABLEに追加」へ |
| S1-5 | **GPT→Cowork確認済み（一部訂正）** | `VERSION.txt` / `VERSIONS.md` / `index.html` | **正本バージョン表記の分裂**。VERSION.txt＝P18、VERSIONS.md現行の正＝P18（GPT報告のP17は誤り・実際はP18）、ランチャータイトル＝P20、履歴末尾＝P21-r2、_reports最終＝P16。**P19〜P21-r2の3版がVERSIONS.md台帳に未記帳**。追加検出: VERSIONS.md 1行目が「見# MyRIG…」と文字混入で見出し破損 | VERSIONS.mdへP19/P20（-r3/-r4含む）/P21（-r2含む）を追記し、VERSION.txt・ランチャーを P21-r2 で同期。1行目の「見」を除去。以後「版更新はVERSION.txt/VERSIONS.md/ランチャー3点同時」を運用規則化 |
| S1-6 | Cowork単独 | `garage-rig-detail.html:963` | 削除確認文言「削除すると元に戻せません」＝**物理DELETE前提**。schema v1.6（論理削除 `deleted_at`）・Charter（物理DELETE禁止）と矛盾 | 「ガレージから削除され、公開ページからも見えなくなります」等へ変更。関連ログ/パーツの扱いを1行明記 |

---

## S2. 48pxタップ領域の広範回帰（双方一致・最大の面的欠陥）

GPT指摘「P18以降の詳細画面で多数崩れ」を**Cowork 4エージェントが独立に440px実測で確認**。実態は3層の複合問題。

**① P14是正リスト未登録（P17以降の新規ページ全部）**
`mobile-shell.css:3390-3412` の拡張対象セレクタにP17〜P21の新規クラスが1つも入っていない。実測未達数: **garage-rig-detail 35要素**（`.gd-editbtn` 28px / パンくず19px / `.gd-manage__publink` 18px 等）/ **parts-detail 14** / **rig-detail 12** / **garage 9**（`.pit-card__btn` 32px / `.act` 39.6px）/ log-detail・feed・search-results にも各数件（`.btn-follow` 33px / `.rd-cmt-like` 24px / `.rd-cmt-menu` 32px / `.rd-fchip` 32px / `.rd-subhdr__back` 38px / `.feed-card__hdr` 44px）。

**② 拡張ヘルパー2系統併存**
`.tap48::after`（固定48px角・css:2147）と P14実寸拡張形（`min-*:48px`・css:3413-3441）。前者は元要素が48pxより広いと**ヒット領域が縮む**（`.feed-card__stat` 実効幅43.5px）。→ 実寸拡張形へ統一。

**③ 隣接::after衝突（elementFromPoint実測）**
「履歴を消す」実効36px＝下端タップで隣の**履歴チップ個別削除✕が誤発火**（search.html）。`.feed-card__rigchip` 実効41px。#29ゲートの測定が「要素自身のボックス」基準で衝突を見ていない。→ **#29ゲートをヒットテスト基準へ改訂**し、①のセレクタ追加後に全ページ再走。

---

## S3. R2無断省略（未裁定の欠落・イタヤ裁定が必要な項目）

**横断（全9ページ）**
- **footer情報導線ゼロ**（G2違反・双方一致級の最重量）: MyRIGとは？/使い方/運営情報/お問い合わせ/ヘルプセンター/プライバシーポリシー/利用規約/特定商取引法 への到達経路が0。言語切替（#24日英）の受け皿もなし。→ ユーザーメニューシート内「情報」ブロック案で1回裁定し全ページ共通実装。
- **アカウント導線（GPT・一部一致）**: userMenuSheetはHomeのみ。search/search-results等から設定・テーマ・通知へ到達不能。GPT指摘の「認証・設定・法務・エラー画面群がロードマップに不在」も妥当 → ロードマップへ追加を裁定。
- 右肩リンク文言が「すべて見る/もっと見る」2語混在（G8）→ 1語に統一。
- RELATED & RECOMMENDED 区切り帯が詳細3ページとも不採用（裁定記録なし）。

**feed**: AD枠2種（Affiliate/AdSense）ゼロ（G9）/ 種別フィルタ（整備/カスタム/走行/セットアップ/メモ）なし / 日付セパレータなし / skeletonなし（G10）/ Activityが登録・更新系イベント（rig_registered/part_added/rig_updated）を持たない / rigchip・コメントが実在ページ未結線。

**rig-detail**: **Library製品情報導線消失**（P19裁定は購入枠撤去のみ・parts側には現存＝**GPTとも一致**）/ AD枠ゼロ / 閲覧数なし（parts実装済み＝横断不整合）/ LOGフィルタ・sortなし / owner棚がRIG棚のみ / RECENT ACTIVITY受け皿なし / EXTERNAL LINKSのX行なし。

**parts-detail**: sort（新着/古い順）なし / セクション順 DESC→PN→SPEC がPC parts正典（DESC→SPEC→PN）と相違＝裁定記録なし / `?buy=sticky` 文言が「**Trail Finder 2**の購入先…」＝rig流用残り / **画像素材がPC正典と別製品（AXI・SAMIX等）＝GPTとも一致**。

**log-detail**: AD枠ゼロ（BUY/INFOも無いため収益枠の受け皿が完全ゼロ）/ h1が種別ラベルで実タイトルがh2（階層逆転）。

**garage（PIT TABLE）**: 「＋パーツ追加」CTAなし / **ピン解除✕なし** / **main・sub階層消失**（フラット配列）/ 折りたたみトグルなし / メモ編集ペンなし / サブ空スロット「＋追加」なし — いずれも#36裁定「Garage Top＝全体管理」と直結。ほか: RECENT ACTIVITY受け皿なし / 設定の写像先なし / **公開ページプレビュー導線なし**（data-href死にリンク）/ 保存済みが自分のデータ流用で他人コンテンツ表現喪失 / SAVEDの「もっと見る→」なし / カバー100px（裁定96px）・カバー画像が別ファイル（shakedown_24 vs _4）/ garage→garage-rig-detail **前方リンク0件**（往復不成立）。

**search**: skeletonなし / 空状態の「フィルターを外す」導線なし / 絞り込みシートにリセットなし / パワーソースfacet丸ごと欠落 / メーカー検索・件数・さらに表示なし / フィルター適用中表示なし / `role="search"`なし（2ページ）/ Most viewedソートなし。

---

## S4. 実装バグ（S1以外・修正のみで完結）

| 箇所 | 内容 |
|---|---|
| `log-detail:620` / `rig-detail:907` / `parts-detail:953` | `toggle('is-on')` だがCSSは `.is-on--like/--fav/--pin`・`.is-following` 待ち＝**ON状態が視覚反映されない**（3ページ共通） |
| `log-detail:468` | 画像5枚以上でX型グリッド破綻（2×2固定前提）→ `Math.min(n,4)`＋「+N」オーバーレイ |
| `garage-rig-detail:804` | オーナー版コメント⋯に「報告する」なし（#35共通部品仕様・rig-detailから移植可） |
| `garage-rig-detail:801` | 削除確認シートでフォーカスがダイアログへ移動しない（**GPT「Dialog Controller管理外」指摘と同根**） |
| `garage-rig-detail:422,442,477` | 英字見出し（BUILD PARTS MAP/LOG/PARTS）に `--ja` 誤適用（#32②タイポ4層違反）。garage.htmlは逆に全見出し一括上書き＝3方式分裂 |
| `garage-rig-detail:328` | 統計表示が操作用 `.action-bar` クラス流用＋インラインstyle＝共通化時の取り違えリスク |
| コメントいいね | プレビュー↔全件シートで非同期・再描画で巻き戻り（rig/garage-rig共通） |
| `garage.html:616` | `#gptCreateShortcut` 参照デッドコード / `?view=compact` 残置経路がレイアウト崩壊状態で到達可能（#30⑤前倒し推奨） |
| BottomNav | garage-rig-detailのみ `aria-current="page"` 欠落。詳細3面は現在地なし＝方針不統一 |
| `mobile-shell.css:2152` | safe-area `env()` フォールバック値なし2箇所（他13箇所は `,0px` 付き） |
| Home（r14凍結） | ダーク時 `.a2hs__body` が明色残り（#30④対象に明記） |
| `href="#"` | 全9ページで306/379本（80.7%）。`.html`リンク切れは0件 |

---

## S5. GPT単独指摘の検証結果

| GPT指摘 | Cowork判定 |
|---|---|
| P0 Public Garage導線誤り | ✅ **確認**（S1-1） |
| P0 正本バージョン混在 | ✅ **確認**（S1-5。VERSIONS.md=P17は誤りで実際はP18。分裂の事実は正） |
| P0/P1 48px崩れ | ✅ **確認・拡張**（S2。GPTの静的推定を実測で裏付け） |
| P1 Home横スクロール（360px=362） | ✅ 既知（P14レポートread-only検出）・未解消。#30へ |
| P1 アカウント導線欠落 | ✅ 一部一致（検索2面のuserMenuSheet不在を独立検出）。画面群のロードマップ不在指摘も妥当 |
| P1 保存済み内部URL（/garage/favorites・/pins維持すべき） | ✅ 事実確認（タブ統合・URL未保持・href="#"）。**設計論点＝要裁定**（見た目統合でもルーティングは正式仕様を維持するか） |
| P1 Detail Header独自 `.rd-subhdr` | ✅ 事実確認（Header 3 variant のうち rd-subhdr は4詳細面の独自実装・back 38px）。**共通仕様のvariantに戻すか、rd-subhdrを正典化するか要裁定** |
| P1 ダイアログのController管理外 | ✅ 一部確認（focus不移動を実測。フォーカストラップ・単一表示の統一は#30課題） |
| P2 Theme永続化なし | ✅ **確認**（mobile-shell.js に theme の localStorage 保存なし・遷移でlight戻り） |
| P2 alt=""・フォームラベルなし | ✅ alt側は確認（ギャラリー主画像等）。ラベル側は妥当・未実測 |
| パーツ画像の別製品素材 | ✅ 一致（Cowork parts監査も独立検出） |

GPT検証の限界申告（localhost遮断＝実機動作系は未保証）どおり、**S1-2/S1-3/S1-4・is-on不可視・5枚グリッド破綻・::after衝突などの動的欠陥はGPTパスに含まれず、Cowork実測のみが検出**。両者は補完関係にある。

---

## S6. 問題なしを確認した点（回帰なしの記録）

- garage-rig-detail: 右列5パネル全受け皿あり（**統計は落ちていない**）・本文8セクション全存在・PC編集6キーとdata-edit 1対1一致・編集モードOFF=0/ON=6実測・hidden×display衝突全ガード済み（過去バグ再発なし）・440px横overflowなし
- 全9ページ: JS構文エラー0・pageerror 0・id重複0・`.html`リンク切れ0・alt属性欠落0
- G7: 無限スクロールはfeed（#25）とsearch-results種別タブ（#26）のみ＝逸脱なし
- ledger裁定済み事項（#28タブ・#32 Own View・#35簡約・#36一括編集drop等）の誤報告なし
- ※監査配布物に `img/`・`icons/`・`manifest.webmanifest` が未同梱で242枚中227枚が404だったが、**実ツリー側は健全**（配布手順の問題）

---

## S7. 推奨対応順

1. **B1 機能欠陥即時修正**（S1-1誤結線解消・S1-2・S1-3・S1-4・S1-6文言＋S4のis-on/5枚グリッド）— コード修正のみで完結・裁定不要
2. **B2 版管理正常化**（S1-5: VERSIONS.md追記・3点同期・「見」除去・運用規則化）
3. **B3 48px一括是正**（S2①セレクタ登録＋②ヘルパー統一＋③衝突解消→ヒットテスト基準の#29ゲート再走・全ページ）
4. **B4 裁定会**（S3の要裁定項目＋GPT設計論点2件（保存済みURL・rd-subhdr）を一括でイタヤ裁定→carry/adapt/drop/deferを記録）
5. **B5 品質バッチ**（#30拡張: aria・focus・デッドコード畳み込み・Dark仕上げ・Home横スクロール）

## S8. ledger起票案（承認後にproject_write）

- **#37 P22統合監査**: 本レポート全体を親項として起票。S1各件を①〜⑥、S2を⑦（#29ゲート改訂を含む）、S3裁定リストを⑧、S4を⑨（#30へ振替可）として管理。
- #19（PC改訂キュー）へ追加: search表示切替トグル不在・PC parts セクション順の追従判断。
- #29 改訂: ゲート測定をヒットテスト基準へ／対象セレクタの登録漏れ防止（新規ページ着工時のチェック項目化）。
- #30 追加: ⑦a2hsダーク・⑧env()フォールバック・⑨compact畳み込み前倒し・⑩Home横スクロール。

---

## 付録 — ページ別エージェント原票（8本・全文）


### 付録A — rig-detail

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | 欠落 | `mb/rig-detail.html:406`（コメントのみ・実体なし） | Library製品情報導線（MyRIG内の製品マスタへの遷移）がページ全体から消失。P19の裁定は「購入枠（アフィCTA）の撤去」であり、`buy-info__master`＝MyRIG内コンテンツ導線は購入導線ではない。ptbの「Trail Finder 2」は`span.chip`（非リンク）のため、RIG詳細から製品情報へ到達する経路がゼロ | PC `pc/myrig-rig-detail-v6.html:411` `a.buy-info__master`「Trail Finder 2 製品情報／MyRIG内／仕様・スペック・関連RIGを見る」（撤去対象の`a.buy-info__cta--buy`はL419で別要素）。`mb/parts-detail.html:427` には `.rd-buy-master__name`「VS4-10 Pro 製品情報 MyRIG内」が現存＝rig側のみ欠落 | 製品情報導線（アフィCTAなし・PR表示不要）を単独行としてSPEC直後または関連棚前に復活。台帳#35③との齟齬も併せて更新 |
| 2 | HIGH | 欠落 | `mb/rig-detail.html`（全文） | AD／Affiliate枠が1つも無い。`ad-placeholder` / `ad-slot` / `Affiliate` / `広告` すべて0ヒット。parts-detailは台帳#35で「AD/Affiliate独立枠は省略（BUY/INFOに集約）」と裁定済みだが、rig-detailはそのBUY/INFO自体がP19で撤去されたため受け皿が消滅 | PC `pc/myrig-rig-detail-v6.html:452` `div.ad-placeholder`「AD / Affiliate」。CRITERIA G9「クラス名にかかわらず存在をcarry」 | 枠を1つ設置（位置はモバイル側裁定）するか、rig-detailのAD非搭載を台帳に明示裁定として起票 |
| 3 | MID | 欠落 | `mb/rig-detail.html:359-388` `.action-bar` | 統計のうち「閲覧数」が無い（♥142／★45／📌22／💬5のみ）。`閲覧`・`👁` ともに0ヒット。正典は訪問者にも3統計を表示 | PC `pc/myrig-rig-detail-v6.html:402-406` `.stats-triplet`（閲覧1,284／お気に入り47／ピン留め22）。`mb/parts-detail.html:366` は `.rd-ptb__views` 「👁 847」を実装済み＝横断不整合 | parts-detailと同じ `.rd-ptb__views` をptb行に追加 |
| 4 | MID | 欠落 | `mb/rig-detail.html:469-487`「このRIGのログ」 | LOGのフィルタ／並べ替えが無い。12件中3件のみ表示で絞り込み手段なし | PC `pc/myrig-rig-detail-v6.html:275-282` `.filter-btns`（全て/整備/カスタム/走行/メモ）＋`.sort-select`（新着順/古い順）。`mb/parts-detail.html:473-478` は `.rd-fchip`（全て/整備/カスタム/走行）を実装済み＝横断不整合 | parts-detailの `.rd-fchip` を流用してフィルタ行を追加（並べ替えはdefer可・要裁定） |
| 5 | MID | 欠落 | `mb/rig-detail.html:499-507`「@crawler_junkie のガレージ」 | owner-sectionが RIG棚1本のみに簡約され、オーナーの「パーツ」棚・「ログ」棚への導線が無い。parts-detailには台帳#35で「簡約=owner-sectionタブ→単一棚」の明示裁定があるが、rig-detailには同等の裁定記録が無い | PC `pc/myrig-rig-detail-v6.html:339-343` `.owner-section__tabs`（RIG／パーツ／ログ）＋L345-372 各タブ実体（RIG5点・パーツ4点・ログ4点） | タブまたは横スクロール棚でパーツ／ログを追加、もしくはrig-detailの単一棚簡約を台帳に裁定記録 |
| 6 | MID | バグ | `mb/rig-detail.html:244` `.rd-cmt-like` / `:247` `.rd-cmt-menu` / `css/mobile-shell.css:1427` `.btn-follow` | タップ領域が契約48px未達（440px実測: `.rd-cmt-like` 24px、`.rd-cmt-menu` 32px（min-height:32px指定）、`.btn-follow` 33px＝min-height指定なし） | 台帳#29①「48pxタップ領域の全面是正（P14で504要素・未達0件）」。本ページはP18実装で当該ゲート後に追加。`.btn-follow` は feed / log-detail / parts-detail / search-results にも同一定義で波及 | 3クラスに `min-height:48px`（または疑似要素でヒット領域拡張）。btn-followはshell CSS側で是正し全ページ再測定 |
| 7 | LOW | 整合 | `mb/rig-detail.html:534,538` `.rd-links-wrap` | EXTERNAL LINKSの項目が2件（YouTube／Instagram）で、X（twitter）行が無い | PC `pc/myrig-rig-detail-v6.html:445-449` `.ext-list` は youtube／instagram／twitter の3件 | X行を追加（受け皿自体は存在するため項目追加のみ） |
| 8 | LOW | 整合 | `mb/rig-detail.html:135-145,215,224-237,266-290` | P19の購入枠撤去後も未使用CSSが残存（`.rd-buy-master*` / `.rd-pr` / `.rd-buybar*` / `.g-sec--shop` / `.rd-shop__*`）。対応マークアップは0件。ヘッダコメントL317・L322も「軸B ?buy= / 残軸: B ?buy=top」と現状（L739「?buy=sticky も廃止」）に矛盾 | 実装コメント `mb/rig-detail.html:406` `:739` と、マークアップ側grep0件の突合 | 台帳#30⑥（畳み込み削除バッチ）に本件を追記して一括削除。指摘1の対応で製品情報導線を戻す場合は `.rd-buy-master*` のみ再利用 |
| 9 | LOW | 文言 | `mb/rig-detail.html:470,489` `.g-sec__ttl--ja` | セクション見出し文法が同一ページ内で不統一。DESCRIPTION／PHOTO NOTES／SPEC／BUILD DETAILS／COMMENTSは英字のまま、LOGとPARTSのみ「このRIGのログ」「装着パーツ」とJA化 | PC `pc/myrig-rig-detail-v6.html:273` `LOG`＋`.section__count`、`:297` `PARTS`＋`View all →`。CRITERIA G8「英字タイトル＋件数＋右肩リンクはワンセットでcarry」 | JA化を維持するなら台帳#32②タイポ4層ルールの適用範囲として条文化、しない場合はLOG／PARTSを英字へ戻す（要裁定） |

### 付録B — parts-detail

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | バグ | `mb/parts-detail.html` `.rd-subhdr__back`(L296, 実測38px) / `.btn-follow`(css/mobile-shell.css:1427, 33px) / `.rd-fchip`(L310, 32px) / `.rd-cmt-menu`(L247, 32px) / `.rd-cmt-like`(L244, 24px) / `.rd-ptb__cat`(L101, 18px) | 契約48pxのタップ領域を満たさない対話要素が6種。Playwright 440px実測（base/?guest=1/?owner=1 全状態で再現）。疑似要素によるヒット領域拡張もCSS上に無い | ledger #29①「48pxタップ領域の全面是正＋ゲート閾値の訂正（44→48）・504要素実測で未達0件」＝2026-07-23完了。本ページはP18-2（07-24）で新規作成され当該ゲートを通っていない。`mb/rig-detail.html` も同一要素で同値（back38/follow33/cmt-menu32/cmt-like24）＝#6にも波及 | 48px是正バッチを詳細ページ2枚に適用（浮遊円backは視覚38pxのまま`::after`でヒット48px確保が現実的）。ledger #29のゲートを再実行 |
| 2 | MID | バグ | `mb/parts-detail.html:762` `.rd-buybar__t` | `?buy=sticky` の下部ミニ購入バー文言が「**Trail Finder 2** の購入先・製品情報を見る ›」＝RIG詳細からの流用残り。パーツ詳細（Vanquish VS4-10 Pro Portal Axle Set / L368）で別製品名の購入CTAが出る | PC `myrig-parts-detail-v6.html:344-359` BUY / INFO は当該パーツ（VS4-10 Pro）を指す。`mb/rig-detail.html` の同コードが原文 | 文言をパーツ名に差し替え（例「VS4-10 Pro の購入先・製品情報を見る ›」）。ledger #34の枠組み変更は不要 |
| 3 | MID | 整合 | `mb/parts-detail.html` L405 DESCRIPTION → L414 PHOTO NOTES → L420 BUY/INFO → L442 SPEC | PC parts正典は **DESCRIPTION → SPEC → PHOTO NOTES**。モバイルは **DESCRIPTION → PHOTO NOTES → SPEC** で、RIG詳細（PC: DESC→PN→SPEC）の並びに揃えている。ヘッダコメント原則④（PHOTO NOTES＝フルワイド既定）とrig-detail一式流用の結果で、偶然ではなく**意図的な横並び統一**と読めるが、PC parts現物との差分を裁定した記録がledgerに無い | PC `myrig-parts-detail-v6.html:183`(SPEC) → `:204`(PHOTO NOTES)。PC `myrig-rig-detail-v6.html:188`(DESC)→`:196`(PHOTO NOTES)→`:213`(SPEC)。CRITERIA 参照優先順位「5と6が食い違えば6（PC現物）が正」＋ledger #35 parts項に順序の記載なし | 「詳細系はDESC→PHOTO NOTES→SPECで統一（PC parts側を#19キューで追従）」としてledgerに1行裁定を残す。裁定しないならモバイルをPC parts順へ戻す |
| 4 | MID | 欠落 | `mb/parts-detail.html:515`（HTMLコメントのみ）／関連3棚 L517・L524・L532 の直前 | 関連棚群の前段にある可視の区切り「**RELATED & RECOMMENDED**」が無い。棚3本の中身（@userの他の登録パーツ／同じVanquishの人気パーツ／このパーツが使われている人気RIG）はPCと**完全一致**しており、欠けているのは区切り帯のみ | PC `myrig-parts-detail-v6.html:401-405` `.related-section-divider` / `.related-section-divider__label` = "RELATED &amp; RECOMMENDED"。PC L412/L427/L442 の3棚見出しとモバイル L517/L524/L532 が一致することは確認済み。`mb/rig-detail.html:508` も同様にコメントのみ | 関連棚の直前に区切り帯（ラベル＋罫）をcarry。rig-detailと同時適用 |
| 5 | MID | 欠落 | `mb/parts-detail.html:476-481` `#logFilter` | LOGセクションの**並び替え（新着順／古い順）**が無い。モバイルはフィルタチップ4種（全て/整備/カスタム/走行）のみ | PC `myrig-parts-detail-v6.html:237` `<select class="sort-select"><option>新着順</option><option>古い順</option></select>`（`.section__controls` 内、フィルタとワンセット）。ledger #35 parts項の記載は「LOGフィルタ（全て・整備・カスタム・走行）」のみでsortの処遇裁定なし＝R2の無断省略 | ソートをチップ列右端のセグメント or シートとしてadapt。または「モバイルはsort drop」をledgerに裁定記録 |
| 6 | MID | 文言 | `mb/parts-detail.html:460` 「このパーツを使っているRIG」／`:475` 「このパーツのログ」 | PC英字セクション見出しが和文に置換されており、同一ページ内で DESCRIPTION / PHOTO NOTES / BUY / INFO / SPEC / COMMENTS / SHARE & LINKS は英字のまま＝見出し文法が二重 | PC `myrig-parts-detail-v6.html:219` `<h2 class="section__title">USED IN RIG</h2>`＋`.section__count`「2台」、`:229` `LOG`＋「3件」。CRITERIA G8「英字タイトル＋件数＋右肩リンクはワンセットでcarry」。`mb/rig-detail.html:470/489`（このRIGのログ／装着パーツ）も同様 | どちらかに統一。英字carryなら `USED IN RIG` / `LOG`＋件数へ戻す。和文統一を採るならledger #32②タイポ4層ルールの適用範囲としてセクション見出し方針を条文化 |
| 7 | MID | 欠落 | `mb/parts-detail.html:16`（`css/sot/SoT_footer.css` 読み込みのみ・footer DOMなし） | PC footerの情報導線（MyRIGとは？／使い方／運営情報／お問い合わせ／ヘルプセンター／プライバシーポリシー／利用規約／特定商取引法）の受け皿が無い。`grep -l 'site-footer__' mb/*.html` = **0件**でモバイル全ページ共通の欠落 | PC `myrig-parts-detail-v6.html:621-700` `.site-footer`。CRITERIA G2「footer内の About/Help/Terms/Privacy 等の情報導線はBottomNavへの自動吸収とみなさず、受け皿を別途確認する」。ledgerに当該裁定なし | 詳細ページ単体ではなくShell契約課題として起票（簡易フッタ or 設定/その他ハブ）。本ページ側の是正は不要 |
| 8 | LOW | 整合 | `mb/rig-detail.html`（CSS L183-195 `.rd-recent` / 対応表コメント L325 のみ・DOM 0件） | 本ページ（parts-detail L558-568）は HISTORY を `<details class="rd-recent">` で正しく受けているのに対し、rig-detail は同じCSSと「RECENT ACTIVITY→折りたたみ」の対応表記述を持ちながらDOMが無い＝右列末尾パネルの受け皿欠落＋死にCSS | PC `myrig-rig-detail-v6.html:454` `<h3 class="panel__title">RECENT ACTIVITY</h3>`。PC `myrig-parts-detail-v6.html:389` HISTORY はモバイルL559で存在確認済み | #6（rig-detail）側の指摘として起票。parts-detailの `.rd-recent` 実装をそのまま複製 |
| 9 | LOW | バグ | `mb/parts-detail.html:330`（軸コメント）と `:674`（`var BUY`） | コメントは「B `?buy=` spec（既定・SPEC直後）/ top（アクション行直下）」「残軸: B ?buy=top のみ」と記すが、実装は `P.get('buy')==='sticky' ? 'sticky' : 'inline'` で **`?buy=top` は分岐が存在せず無効**。DOM位置も写真直後固定でコメントの「SPEC直後」と不一致 | 同ファイル L674、L756-757（r3コメント「本文中は写真直後の別枠カード1回のみ」）。ledger #35は本文BUY/INFOの別枠カード＋PR表示を確定済みで、旧軸記述だけが残存 | ヘッダの軸コメントを現状（sticky比較のみ）に更新。ledger #30⑥と同種の畳み込み残り |
| 10 | LOW | 整合 | `mb/parts-detail.html:462`(rig-detail.html) vs `:466`・`:484/488/492`・関連3棚全カード（すべて `href="#"`） | 内部リンクの粒度が不揃い。USED IN RIG 1件目のみ `rig-detail.html` に結線され、2件目・LOG3件・RELATED全カードは `#` のまま。`log-detail.html` は同ツリーに存在するが参照0件 | `grep -c 'log-detail.html' mb/parts-detail.html` = 0、`mb/` に `log-detail.html` は実在。CRITERIA 観点6「リンク切れ（href先が `#` のまま）」 | 代表1件ずつを `rig-detail.html` / `log-detail.html` / `parts-detail.html` に結線し、モック内回遊を成立させる |
| 11 | LOW | 要確認 | `mb/parts-detail.html:689-695` `var IMGS` / `:783-788` `var PN` | ギャラリー5枚・PHOTO NOTES 4枚の画像がPC正典と別製品。モバイルは `AXI03030_PP_06` / `AXI03025T1` / `AXI03028` / `samix` / `dtxc4034-b`（Axial・Duratrax系）で、Vanquish製品ページにAxial製品写真が並ぶ。キャプション文言はPCから正確に流用されている | PC `myrig-parts-detail-v6.html:161-172` gallery = `VP_Fordyce_Cover_Shot_1000x1000` / `IMG_4294` / `IMG_4501` / `lonk_polish_13` / `IMG_4283`、`:204-215` fotonote も同素材。※監査コピーに `mb/img/` が同梱されておらず実表示は未検証＝**要確認** | 素材差し替えの可否を確認（ledger #22 Tier1視覚監査の対象）。意図的な代替素材なら不要 |
| 12 | LOW | 文言 | `mb/parts-detail.html:677` コメント `/* RIG所有者として閲覧＝… */` | パーツ詳細なのに「RIG所有者」。rig-detailからのコメント流用残り（実挙動は正しくコメント削除モデレーション） | `mb/rig-detail.html:661` に同一文。ledger #35「rig-detail一式流用」 | コメント文言を「パーツ所有者」に修正 |

（補足: JS構文＝`node --check` PASS・`pageerror` 0件、id重複0件、`img` alt欠落0件、`[hidden]`とdisplayの衝突なし（L126/159/162/170/235/256/258 で全てガード済み）、無限スクロール不使用（IntersectionObserver 0件・G7適合）、統計（閲覧847/お気に入り31/ピン留め8）はaction-bar＋`.rd-ptb__views`に、SHARE/EXTERNAL LINKSは `SHARE & LINKS`（L543-557）に、HISTORYは L558-568 に受け皿あり。owner-section→単一棚化とAD枠省略はledger #35裁定済みのため非報告。PC parts-detailに空状態・skeleton定義は0件のためG10該当なし。）

### 付録C — feed

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | 欠落 | mb/feed.html 全域（該当要素なし） | PC右列の広告枠2種（Affiliate / AdSense）がモバイルに1つも存在しない。`ad-slot` / `adsense` / `affiliate` / `広告` を mb/feed.html・css/・js/ 全域 grep して0件を実測確認 | pc/myrig-feed-v3.html:1107-1113 `.feed-right__section > .feed-ad-slot`（"AD — AFFILIATE BANNER" / "AD — GOOGLE ADSENSE"）。G9「クラス名にかかわらず存在をcarry」。ledger #34・#35はrig/parts詳細のBUY/INFOに関する裁定で、Feedの独立AD枠は未裁定 | 縦積み時の挿入位置（例: nカードごと／タブ直下）を裁定して2枠をcarry。搭載しないなら drop裁定を明文化 |
| 2 | HIGH | 欠落 | mb/feed.html:70-73 `.seg-tabs--fill`（種別UIなし） | PC左列の種別フィルタ（整備/カスタム/走行/セットアップ/メモ）がモバイルに無い。ledger #28はタブ「All/Following/Trending→おすすめ/フォロー中」の置換裁定であり、種別フィルタは裁定対象外。モバイル側には確立済み文法（search-results.html:57-62 `.filter-chip`、parts-detail.html:477-480 `.rd-fchip` 全て/整備/カスタム/走行）が既にあるのにFeedのみ未搭載＝横断整合ずれも伴う | pc/myrig-feed-v3.html:451-476 `.fs-section` 種別 ＋ `input[data-feed-type="maintenance/custom/run/setup/memo"]`（PC script:1255 でハンドラも定義） | seg-tabs直下に `.filter-chip` 列としてadapt（parts-detailの `.rd-fchip` と文法統一）。defer/dropとするなら処遇を宣言 |
| 3 | MID | 欠落 | mb/feed.html:77 `#feedListReco` / :83 `#feedListFollowing`（区切り要素なし） | 日付セパレータの受け皿が無い。モバイルはカード内の相対時刻（"3時間前"/"昨日"/"2日前"）のみで、日付境界の視覚区切りが存在しない（`date-sep` 系 grep 0件） | pc/myrig-feed-v3.html:500 / 605 / 704 / 795 / 886 `.feed-date-sep`（Today / Yesterday / 2 days ago）。CSS定義 pc:170-181 | 日付境界に区切り行をcarry（sticky不要）。カード内相対時刻へmergeするなら merge宣言を記録 |
| 4 | MID | 欠落 | mb/feed.html（skeleton定義なし） | loading skeleton がモバイルに存在しない。mb/*.html・css/・js/ 全域で `skel` grep 0件（全ページで0件）。`.feed-spinner`（css/mobile-shell.css:3143）は無限スクロール追記中のスピナーで、初期ロード用のカード型skeletonの代替宣言はされていない | pc/myrig-feed-v3.html:998-1046 `#feedSkel .feed-skel`（`.skel-card`/`.skel-av`/`.skel-line`/`.skel-img-ph`/`.skel-rxn`）、PC script:1281 `showState('loading')`。G10「PC版に定義がある場合はcarry」 | カード型skeletonをモバイル幅にadaptして初期ロード状態に搭載。spinnerで代替するなら G10の例外として明文化 |
| 5 | MID | 欠落 | mb/feed.html:356-373 `ACTIVITY_RAW` / :394 `VERB` | Activity行の対象イベントがPCと別物。PCは登録・更新系イベント（RIG登録/パーツ追加/RIG更新/LOG投稿）を `.act-badge`＋`.act-thumb`＋`.act-meta`＋変更点＋CTA付きで流すが、モバイルは like/fav/pin/follow のリアクション系のみ。RIG登録・パーツ追加・RIG更新（変更点差分）がフィードに一切流れない。ledgerに該当裁定なし | pc/myrig-feed-v3.html:728 `.act-item--rig_registered` / :772 `--part_added` / :818 `--rig_updated` / :956 `--log_posted`、:832・:946 `.act-changes`（"Servo → Futaba BLS172SV · ESC → ..."）、`.act-cta` View RIG / View Part / View Log | 登録・更新系イベント4種の受け皿を activity-row にadapt（最低でも rig_registered / part_added）。リアクション系のみに絞るなら drop裁定を明文化 |
| 6 | MID | 要確認 | mb/feed.html:55-59 `.hdr-search`（feed内検索なし） | PC左列の「フィード内を検索」の受け皿が無い。モバイルのヘッダー検索はグローバルスコープ（"RIG・パーツ・LOG・製品情報を検索"）でfeedスコープではない。ledger #19⑥の検索スコープ正典表（5スコープ）にもfeed内検索は未収載 | pc/myrig-feed-v3.html:478-489 `.fs-section` 検索 ＞ `.fs-search input[type="search"] placeholder="フィード内を検索..."` | #19⑥の検索スコープ正典表策定時にfeedスコープの要否を裁定。搭載しないなら drop として記録 |
| 7 | MID | バグ | mb/feed.html:471 `.feed-card__rigchip` / :644 コメントtoast | 詳細ページが同梱済みなのにリンクが `#` のまま。RIGチップは `href="#"`（rig-detail.html は mb/ に実在し、log-detail.html:—・parts-detail.html:— から実リンク済み）。コメント押下時のtoastは「(LOG詳細はパッケージ外のためデモのみ)」だが log-detail.html は mb/ に実在し index.html からハブ導線済み。P13b時点の記述がP18以降の実装に追随していない | 実測: mb/ に rig-detail.html・log-detail.html が存在。他ページは `href="rig-detail.html"` で実結線（garage-rig-detail.html・log-detail.html・parts-detail.html）。PC側も `.act-cta href="#"` だが、モバイルは実ファイルがある点で状況が異なる | `.feed-card__rigchip` → `rig-detail.html`、コメント/カード本文 → `log-detail.html` へ結線し、toast文言を撤去 |
| 8 | MID | バグ | mb/css/mobile-shell.css:3127 `.feed-card__hdr` / mb/feed.html:468 `.m-readmore.feed-card__more` | タップ領域48px未達。440px実測で `.feed-card__hdr` = 416×44px（`min-height:44px` 指定・`.tap48` 未付与）、`.m-readmore.feed-card__more` = 60×44px。他の `.tap48` 付与要素（`__stat`/`__rigchip`/`btn-follow`）は `::after` で48px確保済み | 契約48px。ledger #29①「閾値44→48訂正・504要素実測で未達0件」と実測が矛盾（Playwright 440px計測・`.tap48::after` を加味した実効サイズで判定） | `.feed-card__hdr` を `min-height:48px` へ、`.feed-card__more` に `.tap48` 付与。#29①のゲート再走 |
| 9 | MID | 要確認 | mb/feed.html（footerなし） | PC footerの情報導線（MyRIGとは？/使い方/運営情報/お問い合わせ/ヘルプセンター/お知らせ・更新情報/プライバシーポリシー/利用規約/特定商取引法 等）の受け皿が無い。mb/*.html 全10ファイルで `site-footer` grep 0件＝Shell横断案件 | pc/myrig-feed-v3.html `.site-footer__col-links` / `.site-footer__legal-links`。G2「footer内の情報導線はBottomNavへの自動吸収とみなさず、受け皿を別途確認する」 | Shell契約側で受け皿（設定/その他ハブ等）を1回裁定。Feed単体の是正ではなくShell議題として起票 |
| 10 | LOW | 欠落 | mb/feed.html:591 `.m-empty__text` | empty stateにCTAボタンが無い。PCは2ボタン構成。モバイルは説明文のみで、直後の「おすすめのガレージ」3件が Find builders 相当を担うが、Create log 相当のCTAは無い（BottomNav中央「登録」への暗黙依存） | pc/myrig-feed-v3.html:990-994 `.feed-empty__actions` ＞ `.feed-empty__btn`「Find builders」/ `.feed-empty__btn--primary`「Create log」 | `.m-empty` 内に「LOGを投稿」1次CTAを追加、またはBottomNav吸収を merge として明記 |
| 11 | LOW | 要確認 | mb/feed.html:460-481 `cardHtml()` | フィードカードにタグchipsが無い。PC各カードは `chips` 属性を保持（モバイルには `.chip` 文法が rig-detail.html:352 に存在）。ただしPC側の `SoT_card-components.js` が本パッケージに未同梱のため `variant="feed"` でchipsが実描画されるか実測不能 | pc/myrig-feed-v3.html:519・540・561・581・601 ほか `myrig-log-card ... chips="ポータルギア,RC4WD,TRX-4"`。描画有無は `./assets/js/SoT_card-components.js`（未同梱）依存 | PC実物でchips描画を実測してから carry / drop を裁定 |

### 付録D — search

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | バグ | `mb/search.html:22`（`<a href="search-results.html?type=part">`）／`mb/search-results.html:640` `setType(params.get('type')…)` | 検索トップ「パーツ」チップの遷移先が `?type=part` だが、結果ページの種別キーは `parts`（`search-results.html:58` `data-type="parts"`／`:419` `DATA.parts`）。`appendBatch()` 内 `DATA[curType].length` で **TypeError: Cannot read properties of undefined** が発生し、結果0件・タブ非アクティブ・空状態も非表示の白画面になる。Playwright実測（440px, `?type=part`）で pageerror 再現・`resultArea` 空・`activeChip` null。未知値（例 `?type=bogus`）も同様に例外 | PC正典 `myrig-search-v3.html:501-508` コンテンツタイプ facet（All/RIG/Parts/Logs/Library/Users）の受け皿が機能しない＝R2機能欠陥。`mb/garage.html:774` も `k:'parts'` でキー統一済み | `search.html` のhrefを `?type=parts` に修正。併せて `setType()` 冒頭で `DATA[type] ? type : 'all'` の入力検証を追加 |
| 2 | MID | 欠落 | `mb/search-results.html`（`skel-` 系クラスがCSS/HTML全域で0件） | ローディングskeletonがモバイル全域に存在しない。PC正典は検索中スケルトン8枚を定義 | PC `myrig-search-v3.html:1115` `.srch-skeleton#viewSkel` ＋ `.skel-card/.skel-img/.skel-line`（同ファイル内定義）。G10「PC版に定義がある場合はcarry」 | 検索結果の初回ロード・種別切替時に `.gcard` 相当のskeletonを表示。共通クラス化（feed/一覧系と共用） |
| 3 | MID | 欠落 | `mb/search-results.html:89-92` `.m-empty` | 空状態の復帰導線がPCの4項目に対しモバイルは「カテゴリから探す ›」1本のみ。特に**フィルター適用中に0件になった場合の「フィルターを外す」導線が無い**ため、絞り込み後の詰みから復帰できない | PC `myrig-search-v3.html:1091-1113` `.srch-empty__list`（スペル確認／カテゴリーフィルターを外す／すべての結果を見る（フィルターなし）／RC製品カタログで検索）。G10 | 「フィルターを解除して再検索」「製品情報から探す」をCTAとして追加（最低でもフィルター解除は必須） |
| 4 | MID | 欠落 | `mb/search-results.html:198-233` `#filterSheet` | 絞り込みシートに**リセット導線が無い**。ヘッダーは「絞り込み」＋×のみで、選択したチップを一括解除できない | PC `myrig-search-v3.html:496` `.fs-header__reset`「リセット」 | `bottom-sheet__hdr` に「リセット」を追加（`filter-sheet__apply` の対として） |
| 5 | MID | 欠落 | `mb/search-results.html:207-227` `.filter-sheet__body` | PCフィルターの **パワーソース facet（Electric/Nitro/Gasoline）が丸ごと欠落**。モバイルはカテゴリ／メーカー／スケールの3facetのみ | PC `myrig-search-v3.html:611-618` `.fs-section` パワーソース（`.fs-chip`×3） | パワーソースのチップ行を絞り込みシートへ追加（drop裁定を取るなら台帳へ起票） |
| 6 | MID | 欠落 | `mb/search-results.html:214-220`（メーカー facet） | メーカー facet が固定4チップのみで、PCが持つ**メーカー内検索欄・件数表示・「さらに表示」が全て欠落**。実データでは数十メーカーになるため4件固定では選べない | PC `myrig-search-v3.html:526` `.fs-search`（メーカー検索input）／`:537-562` `.fs-check-item__count`／`:565` `.fs-more-btn`「+ さらに表示」 | メーカー行に検索入力＋件数＋「さらに表示」をadapt（シート内スクロール領域として実装） |
| 7 | MID | 欠落 | `mb/search-results.html:78` `#resultCount`／`js/mobile-shell.js:187` `on($('filterApply'),'click',closeAll)` | 絞り込み適用後に**適用中であることを示す表示が無い**（件数行は「「TRX-4」の検索結果 84件」のみ）。`filterApply` は `closeAll` のみで状態を一切反映しないため、フィルターが効いているか判別不能 | PC `myrig-search-v3.html:665` `.srch-result-meta__sub` = `for "servo"（フィルター適用中）` | 件数行に適用中サブラベル、または絞り込みボタンに適用中バッジ（`.notif-badge` 既存）を出す |
| 8 | MID | 整合 | `mb/search.html:36-46`／`mb/search-results.html:39-49` `.search-field--lg`（`<div>`・`role`なし） | 検索入力が `<form role="search">` に包まれていない。同一モバイル群の他ページはすべて `role="search"` 付きフォーム。支援技術に検索領域として認識されず、Enterキー以外の送信手段も無い | PC `myrig-search-v3.html:648` `<form class="srch-head__form" role="search" aria-label="再検索">`。mb側前例: `feed.html:55`／`garage.html:339`／`index-e-roomclip.html:76` が `<form role="search">` | 検索フィールドを `<form role="search" aria-label="検索">` でラップ（submitハンドラは既存keydown処理を移設） |
| 9 | MID | 欠落 | `mb/search.html` / `mb/search-results.html`（`userMenuSheet`・`themeToggleInMenu`・ヘルプ/利用規約リンクが0件） | SearchHeader variant によりアバター＝ユーザーメニューが無く、この2ページからは**テーマ切替・設定・ヘルプ・利用規約/プライバシー等の情報導線に一切到達できない**（Home `index-e-roomclip.html:509-533` にのみ存在）。BottomNavにも受け皿なし | PC `myrig-search-v3.html:478` `#themeToggle`（app-header）／`:1179-` `.site-footer`（MyRIGとは？/使い方/運営情報/お問い合わせ/ヘルプセンター/プライバシーポリシー/利用規約/特定商取引法）。G2「footer内の情報導線はBottomNavへの自動吸収とみなさず受け皿を別途確認」 | 検索2ページにも `userMenuSheet` を同梱するか、BottomNav「探す」配下等に情報導線の受け皿を1箇所定義（契約§3.1の裁定として起票） |
| 10 | LOW | 欠落 | `mb/search-results.html:68-72` `.search-sort-row__group` | ソートが「関連度／新着／人気」の3つで、PCの4つ目 **Most viewed（閲覧数順）** が欠落 | PC `myrig-search-v3.html:516-520` `<option value="relevance/newest/likes/views">` | 「閲覧数」チップを追加（横スクロール行のため追加コスト小） |
| 11 | LOW | バグ | `mb/search-results.html:68-72`, `:207-230` | ソートチップと絞り込みシート内チップに**専用ハンドラが無い**。`js/mobile-shell.js:455` の `[data-chipgroup]` による `is-active` 見た目切替のみで、結果は一切変わらない（種別タブは `setType` で機能するため、挙動が非対称） | 同ページ `:592-596` は `data-type` チップにのみ `setType` を結線。PC側も静的モックだが、モバイルは種別タブのみ機能する不均衡 | モックでも並び替え（配列sort）と絞り込み（件数変化）のダミー反映を入れる。少なくとも「未実装」であることが分かる状態にする |
| 12 | LOW | 欠落 | `mb/search-results.html:89` `#emptyState` | 空状態に `aria-live` が無く、0件へ切り替わったことがスクリーンリーダーに通知されない | PC `myrig-search-v3.html:1085` `<div class="srch-empty is-hidden" id="viewEmpty" aria-live="polite">` | `#emptyState` に `aria-live="polite"` を付与（件数行 `#resultCount` も同様が望ましい） |
| 13 | LOW | 整合 | `mb/search-results.html:259-263`（Enterハンドラ） | 検索結果ページから再検索した語が「最近の検索」に保存されない。検索トップからの検索（`search.html:303-307` で `myrigRecentSearches` へunshift）とは非対称で、履歴が欠ける | `mb/search.html:303-307` `saveRecent(arr.slice(0,8))` | 結果ページのEnterハンドラにも同一の履歴保存処理を追加（共通関数化） |
| 14 | LOW | 要確認 | `mb/search-results.html:60`（種別チップ「LOG」）／`:447` `LABEL={log:'LOG'}` | 種別ラベルが `LOG`（英字）だが、ガレージ確定形は `ログ`（JA）。同一エンティティのタブ表記が2系統ある | `mb/garage.html:774` `{ k:'log', label:'ログ' }`。台帳 #32③「タブ表記はJA主体（英語はブランド語RIGのみ）」＝ガレージ限定裁定のため検索への適用可否が未確定 | 検索タブもJA「ログ」へ統一するか、#32③の適用範囲を検索に及ぼさない旨を台帳で明示（イタヤ裁定） |
| 15 | LOW | 要確認 | `mb/search-results.html`（grid/list 切替UIなし） | 継承規約§6は「srch-result-meta＋表示切替」を主列構成に挙げるが、**PC現物にはトグルUIが存在しない**（`#viewGrid`／`#viewList` のDOMのみで、切替ボタンもJSも無し）。参照優先順位「5と6が食い違えば6が正」に従い欠落判定はしていない | PC `myrig-search-v3.html:674` `#viewGrid`／`:961` `.srch-list.is-hidden#viewList`。トグル要素は同ファイル内grepで0件 | 表示切替（グリッド/リスト）をPC正典に実装するか、§6の設計図記述から削除するかを#19（PC改訂候補キュー）へ起票 |
| 16 | LOW | バグ | `mb/css/mobile-shell.css:3400-3440`（P14透明48px拡張）／`mb/search.html:55`, `:57` | P14の透明`::after`拡張は隣接要素同士で重なるため、実効タップ高が48px未満になる箇所がある。440px実測（elementFromPointによるヒット判定）で `#clearHistoryBtn`（履歴を消す・実効36px／下方向は隣の `.recent-chip__x` が奪う）、`.recent-chip__link`（実効41px／下方向は次行のリンクが奪う） | 台帳 #29①「48pxタップ領域の全面是正・504要素実測で未達0件」の実測は要素自身の`::after`ボックス基準と推定され、重なりによる実効値は未検証 | 「最近の検索」行の行高（`.search-chips` のrow-gap／`.recent-chip` のmin-height）を上げて拡張領域の衝突を解消。P14ゲートの測定方法をヒットテスト基準へ改訂 |

### 付録E — log-detail

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | 欠落 | `/tmp/audit/mb/log-detail.html`（SHARE節 L328-341 の後・末尾 L343） | PC右列末尾のAD枠がモバイルに一切無い。`ad`/`affiliate`/`ad-slot`/`ad-placeholder`/`sponsor`/`PR` の全綴りでgrepして0件（他モバイル4ページも0件）。本ページはBUY/INFO枠も持たないため収益枠の受け皿が完全にゼロ | PC `/tmp/audit/pc/myrig-log-detail-v6.html:284` `<div class="ad-placeholder">AD / Affiliate</div>`（CSS定義 L58-59）。CRITERIA G9「クラス名にかかわらず存在をcarry」。ledger #35のAD省略裁定は parts-detail（BUY/INFOへ集約）限定で、BUY/INFOの無いlog-detailには及ばない | RELATED3棚の後（SHARE前）に `ad-placeholder` 相当の1枠をcarry。位置はモバイル側裁定 |
| 2 | MID | バグ | `log-detail.html:620`（`.action-bar__btn[data-p3]` / `.btn-follow`） | ON状態が視覚に反映されない。JSは `el.classList.toggle('is-on')` だがCSSは `.action-bar__btn.is-on--like/--fav/--pin` と `.btn-follow.is-following` を待っている。440px実測: いいねタップ後 `class="action-bar__btn is-on"` / color=`rgb(31,35,40)`（hover色）で正典色 `#fa4549` にならず。フォローはbg `rgb(102,185,0)`・ラベル「フォロー」とも変化なし | `css/mobile-shell.css:1470-1472`（`.is-on--like/--fav/--pin`）・`:1442`（`.btn-follow.is-following`）。同一バグが `rig-detail.html:907` / `parts-detail.html:953` にも存在（横断整合） | `data-p3` の値から `is-on--like`/`is-on--fav`/`is-on--pin`、followは `is-following`＋ラベル「フォロー中」へ切替。共通部品化時に1箇所で是正 |
| 3 | MID | バグ | `log-detail.html:192` `<h1 class="rd-subhdr__ttl">走行ログ</h1>` vs `:228` `<h2 class="ld-title">` | ページ唯一のh1がログ種別ラベル「走行ログ」で、実タイトルがh2。見出し階層が実体と逆転（SEO/スクリーンリーダーの主見出しが誤る） | PC `myrig-log-detail-v6.html:186` は `<h1 class="log-title">Hyraxへ交換してロック路面を試走</h1>`。モバイル `rig-detail.html:331` / `parts-detail.html:344` はh1＝実体名（製品名）で運用しており本ページのみ逆転 | `.ld-title` をh1に、SubHeaderタイトルは `<span>`/`<h2>`＋`aria-label` へ降格（rig/parts方式に合わせるなら SubHeader を実タイトルにする案も可） |
| 4 | MID | 文言 | `log-detail.html:267` `<h2 class="g-sec__ttl g-sec__ttl--ja">紐づいているRIG</h2>` | PCの英字セクション見出し `LINKED TO` を和訳して置換。同一ページ内の COMMENTS / SHARE は英字のまま残っており不統一 | PC `myrig-log-detail-v6.html:200` `<h2 class="section__title">LINKED TO</h2>`。モバイル rig-detail/parts-detail はPC英字見出し（DESCRIPTION / PHOTO NOTES / SPEC / BUILD DETAILS / COMMENTS）をそのままcarry。CRITERIA G8＋ledger #32②タイポ4層（英語のみ見出し=`--font-section`） | `LINKED TO` に戻し `g-sec__ttl--ja` を外す（和訳が必要なら4層ルール側で一括方針化） |
| 5 | MID | バグ | `log-detail.html:468`（`IMGS.slice(0, n === 3 ? 3 : n)`）＋CSS `:96` `.ld-media--4plus` | 5枚以上で枚数別グリッドが破綻。CSSは `grid-template-columns:1fr 1fr; grid-template-rows:1fr 1fr; aspect-ratio:4/3` の2×2固定前提だがJSは4枚超も全件描画。440px実測: N=5でコンテナ高627px（4枚時416px）・3行目に1枚だけの半端行、`aspect-ratio` は暗黙行生成で無効化。X型は4枚上限＋「+N」オーバーレイ | 実測（Playwright 440px, N=1/3/4/5/6）。ledger P20-r4「メディア枚数別X型グリッド」の採用自体には抵触せず、4枚超の実装漏れ | `IMGS.slice(0, Math.min(n,4))` にし、4枚目に残枚数「+N」オーバーレイを表示。Lightboxは全枚数を渡す |
| 6 | MID | バグ | `.btn-follow`（`css/mobile-shell.css:1427` / 使用 `log-detail.html:207`） | タップ領域48px未達。440px実測 **82×33.2px**。`padding:6px 16px`＋`font-size:12px` で高さが確保されていない | 契約48px（ledger #29①で閾値44→48訂正・全面是正済みのはず）。他ページと同一の未達 | `min-height:48px` もしくは `.tap48`（`mobile-shell.css:2140`）付与。見た目維持なら透明当たり判定拡張 |
| 7 | MID | バグ | `.rd-cmt-like`（`log-detail.html:132` / 生成 `:503`） | タップ領域48px未達。440px実測 **26×24.4px**。`padding:3px 0`＋14pxアイコンのみ | 契約48px（ledger #29①）。コメント共通部品（rig/parts/log共通想定・ledger #35）なので3ページ同時是正対象 | `padding` 拡張または `.tap48` 付与で48×48確保 |
| 8 | MID | バグ | `.rd-cmt-menu`（`log-detail.html:135` / 生成 `:504`） | タップ領域48px未達。440px実測 **36×32px**（CSS `min-width:36px; min-height:32px`）。⋯報告メニューの起動点 | 契約48px（ledger #29①）。同上・共通部品 | `min-width/min-height:48px` へ |
| 9 | LOW | 欠落 | `log-detail.html:302`（RELATED 3棚の直前） | PCの「本文ゾーン↔関連ゾーン」区切りディバイダが無く、`このRIGの他のログ`＋関連3棚が同じ `g-sec` の連続として並ぶ | PC `myrig-log-detail-v6.html:288-292` `.related-section-divider__label` = `RELATED & RECOMMENDED`。rig-detail(PC L470)/parts-detail(PC L404)にも同要素があり、モバイル側は3ページとも不採用＝横断的な省略（裁定記録なし） | 3ページ一括で扱う。採用するならディバイダ相当の区切り、しないならledgerに drop として1行記録 |
| 10 | LOW | バグ | `.action-bar__btn[data-p3]` / `.btn-follow` / `.rd-cmt-like`（`log-detail.html:250-262, 207, 503`） | トグルボタンに `aria-pressed` が無く、ON/OFF状態が支援技術に伝わらない。`aria-pressed` はlog-detail/rig-detail/parts-detail/feed とも0件 | CRITERIA 検証観点6（aria/role欠落）。#2の是正と同一箇所で対応可能 | 状態トグル時に `aria-pressed` を同期（共通部品化時に一括） |
| 11 | LOW | 要確認 | `log-detail.html:279`（`#cmtInline`）／`:466`（`renderMedia` n=0） | 空状態の定義なし。COMMENTS 0件時は空の `div` と「コメントをすべて見る・書く（0件）」ボタンのみ、画像0枚時は `ld-media--1` の空divが残る。`empty`/`skeleton`/`まだ`/`ありません` の各綴りでgrepして0件 | PC `myrig-log-detail-v6.html` にも空状態/skeleton定義なし＝G10の carry 対象は無い（＝正典違反ではない）。ただし `.m-empty` は `css/mobile-shell.css:2112` に存在し `garage.html:728` で使用済み | G10非該当のため必須ではない。実装時にCOMMENTS 0件・画像0枚の表示をどうするかだけ裁定しておく |

### 付録F — garage-rig-detail

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | バグ | mb/garage-rig-detail.html:890,896 `closeSetSheet` / `#gdSetCancel` | 「設定変更」シートの**キャンセルがフォーム値を巻き戻さない**。Playwright実測: ステータスを`archived`・公開トグルOFFにして**キャンセル**→本文ラベルは「セッティング中／公開中」のままだが、シート内の`#gdStatus`は`archived`・`#gdVisibility`は`unchecked`のまま残留。再度開いて（何も触らず）保存すると「アーカイブ／非公開」が確定する。**非公開化の誤爆経路**。 | PC `.g-status-select` / `.toggle-switch`（pc/myrig-garage-rig-detail-v6.html:576-589）は右列に常設で確定値そのものを操作するためキャンセル状態が存在しない。継承規約G6でPC常設UI→シート化した際に生じたモバイル固有の欠陥 | `closeSetSheet()`にキャンセル時のロールバック（開いた時点の`status`/`is_public`を保持して復元）を実装。`gdSetCancel`は`closeSetSheet`直呼びではなく`revertSetSheet()`経由にする |
| 2 | HIGH | 欠落 | mb/garage-rig-detail.html:929-930 `renderPitState()` | PIT TABLE **未所属（`data-pit="off"`）にすると、メモ表示行`#gdPitMemoRow`と「メモを編集」`#gdPitMemoBtn`が両方`hidden`になり、ページ上からRIGメモの受け皿が完全に消える**（実測: `memoRowVisible=false / memoBtnVisible=false`、他にnoteフィールドなし）。PC右列の**QUICK NOTE パネルは所属状態に関係なく常設**。 | PC pc/myrig-garage-rig-detail-v6.html:658-671 `panel > QUICK NOTE`（`.quick-note__textarea` 内容「VS4-10 Portal換装済み。リアショック油量調整が次の課題。」）＝PC garage-top L462 `.pit-memo` と同一ソース。ledger #36 は「**PITから外してもRIG本体とメモは削除しない**」と明記しており、UIから恒久的に到達不能になるのは裁定と矛盾 | `off`時もメモ行と「メモを編集」は残す（PIT所属ブロックの外＝独立した1ブロックへ切り出す）。PIT設定ボタンのみ「PIT TABLEに追加」に切替える |
| 3 | MID | 整合 | mb/garage-rig-detail.html:804-809 `menuHtml()` | コメント⋯メニューが**「このコメントを削除」＋キャンセルのみ**で、**「報告する」＋理由選択（スパム/嫌がらせ・誹謗中傷/不適切・その他）が無い**。オーナーページでも他ユーザーのコメントに対する通報導線は必要。 | ledger #35 確定「コメント＝⋯メニュー→**報告**（スパム/嫌がらせ・誹謗中傷/不適切/その他→運営送信）」「**コメントは共通部品想定**（rig/parts/log共通）」。実装差分: mb/rig-detail.html:845-861 は `data-cact="report"`＋`data-creason` の理由選択シートを持つ。schema v1.6 `comment_reports` も既存 | rig-detail.html:845-861 の `menuHtml()` / 理由選択シートをそのまま移植し、削除（オーナー権限）と報告を併存させる |
| 4 | MID | 欠落 | mb/garage-rig-detail.html:443-449 `#logFilter` | LOGセクションに**並び替え（新着順/古い順）が無い**。フィルタchips（全て/整備/カスタム/走行/メモ）だけ移植されている。 | PC pc/myrig-garage-rig-detail-v6.html:513 `<select class="sort-select"><option>新着順</option><option>古い順</option></select>`（`.section__controls`内、フィルタとワンセット）。ledgerに sort の drop/defer 裁定なし＝R2無断省略 | フィルタchips行の右端にソートchip（タップでアクションシート「新着順/古い順」）を追加。mb/rig-detail.html（公開版）も同じ欠落のため共通部品として同時是正 |
| 5 | MID | バグ | `.gd-editbtn`(L99, 28px) / `.gd-galedit`(L80, 32px) / `.gd-pit__btn`(L195, 34px) / `.gd-action-btn`(40px) / `.gd-danger`(46px) / `.gd-context a`(19px) / `.gd-manage__publink`(18px) | **タップ領域48px未達を440px実測で35要素検出**。うち本ページ固有の新規要素が主。実測値: `.gd-editbtn`（本ページの主要編集導線・6個）**28×62px**、`.gd-galedit` 32×100、`#gdSettings`/`#gdPitConfig`/`#gdPitMemoBtn` 34、`#gdAddLog`/`#gdAddPart`/`＋ログを追加`/`＋パーツを追加`/`構成パーツを管理→` 40、`#gdDeleteRig` 46、パンくず`.gd-context a` **19×48**、`.gd-manage__publink` **18×101** | ledger #29「48pxタップ領域の全面是正・**ゲート閾値44→48訂正**・504要素実測で未達0件」（✅完了）。本ページはその後の新規実装で閾値を満たしていない。参考実測: rig-detail=12件 / parts-detail=14件 / garage=9件に対し本ページ35件と突出 | `min-height:48px`（またはパディング/`::after`擬似要素でヒット領域拡張）を `.gd-editbtn` `.gd-galedit` `.gd-pit__btn` `.gd-action-btn` `.gd-danger` `.gd-context a` `.gd-manage__publink` に適用。#30の品質バッチで rd-cmt-like(24px)・rd-fchip(32px)・build-group__hd(42px) 等の共通部品も一括是正 |
| 6 | MID | 整合 | mb/garage-rig-detail.html:422,442,477 `.g-sec__ttl--ja` | **英語のみの見出しに日本語混在フォント`--ja`修飾子を付けている**：`BUILD PARTS MAP` / `LOG` / `PARTS` が `g-sec__ttl g-sec__ttl--ja`（L96で`--font-title-mixed`＋`text-transform:none`）。同ページの `DESCRIPTION` `PHOTO NOTES` `SPEC` `BUILD DETAILS` `COMMENTS` `LINKS & SHARE` は無印（`--font-section`/Barlow）で、同一ページ内でフォントが割れている。 | ledger #32②「**タイポ4層ルール**: … 英語のみ見出し（PIT TABLE等）=`--font-section`(Barlow)。**4つを混ぜない**」。継承元 mb/rig-detail.html:470,489 では `--ja` は「このRIGのログ」「装着パーツ」＝**日本語見出しに付いていた**。本ページで見出し語だけ英語化した際に修飾子を外し忘れている | `BUILD PARTS MAP` / `LOG` / `PARTS` から `g-sec__ttl--ja` を削除（PC `.section__title` と同じBarlow大文字に揃う） |
| 7 | MID | 文言 | mb/garage-rig-detail.html:963 `#gdDeleteRig` 確認シート | 削除確認の文言が「**このRIGを削除すると元に戻せません。**本当に削除しますか？」＝**物理DELETE前提の表現**。またLOG・パーツ・PITメモがどうなるかの説明がない。（確認ステップ自体は2段階＝削除する/キャンセルで実装済み・誤爆防止は成立） | schema v1.6 命名規則「**論理削除: `deleted_at` NULLABLE（物理削除しない）**」、RLS共通原則「全SELECTに`deleted_at IS NULL`」。Charter運用ルール「**物理DELETEは禁止。必要な場合は inactive / NULL / archive / mv で代替**」。PC pc/myrig-garage-rig-detail-v6.html:717 は `このRIGを削除…` のみで断定表現なし | 文言を論理削除に整合させる（例:「このRIGはガレージから削除され、公開ページからも見えなくなります」）。復旧可否・保持期間の仕様が未確定なら「元に戻せません」は使わない。関連ログ/パーツの扱いも1行明記 |
| 8 | MID | バグ | mb/garage-rig-detail.html:801 `openAct()` | アクションシート`#rdActSheet`（`role="dialog" aria-modal="true"`）を開いても**フォーカスがダイアログ内へ移動しない**（実測: 削除確認を開いた直後の`document.activeElement`＝トリガーの`.gd-danger`のまま）。危険操作の確認ダイアログでキーボード/スクリーンリーダー利用者が確認文に到達できない。 | 同ページのコメント全件シートは `openCmtSheet()` で `document.getElementById('rdSheetClose').focus()` を実行しており（L~790）、ImageLightboxも`.focus()`する。同一ページ内で挙動が不統一 | `openAct()`に先頭ボタンまたはシート自身への`focus()`を追加。あわせて `aria-labelledby` で確認文と紐付け |
| 9 | LOW | 欠落 | mb/garage-rig-detail.html:849 `gdEditTextarea.maxLength` / `#gdEditSheet` | PIT TABLEメモ編集で `maxLength=120` を設定しているが、**文字数カウンタも保存状態表示も無い**（実測: `#gdEditSheet`内に counter/status 要素0）。上限に達しても無通知で入力が止まる。※保存フィードバック自体はトースト（`#stateInd` aria-live、mobile-shell.js:98-102）で成立しており「保存インジケータ皆無」ではない | PC pc/myrig-garage-rig-detail-v6.html:663-666 `.quick-note__textarea maxlength="120"` ＋ `.quick-note__count`「30 / 120」＋ `.quick-note__status`「保存済み」 | 編集シートのフッタに `n / 120` カウンタを追加（`pit-memo`キーのときのみ表示） |
| 10 | LOW | 整合 | mb/garage-rig-detail.html:328 `.action-bar` | 統計（閲覧1,284/いいね47/お気に入り22）の受け皿は**存在するが**、公開版のリアクション用クラス `.action-bar` を `style="justify-content:space-around"` で流用し、中身は全要素インラインstyleのベタ書き。**操作可能なアクションバーと非操作の統計表示が同じクラス**になっており、Next.js共通化時に取り違える | PC pc/myrig-garage-rig-detail-v6.html:638-657 Panel 3 は独立した `.panel > .g-stats-bar > .g-stat-cell(.g-stat-num/.g-stat-label)`。mb/rig-detail.html の `.action-bar` は♥★📌の操作行 | `.gd-stats` / `.gd-stat-cell` 等の専用クラスへ切り出し、インラインstyleをCSSへ移設 |
| 11 | LOW | バグ | mb/garage-rig-detail.html:835 `EDIT_TITLES` | 一括編集キー `'all': 'RIG情報をまとめて編集'` が辞書に残存しているが、`data-edit="all"` を持つ要素はページ内に0個＝到達不能なdead code。**ledger #36で一括編集はdrop裁定済みのため機能欠落ではない**が、正典差分の意図が読めなくなる | ledger #36「モバイルは一括編集を持たず…**edit-modalの処遇＝drop**」。PC側の対応キーは pc/myrig-garage-rig-detail-v6.html:601 `onclick="openEditModal('all')"` | `'all'` エントリを削除し、drop裁定の根拠コメント（ledger #36）を1行残す |
| 12 | LOW | バグ | mb/garage-rig-detail.html:790-796 `[data-clike]` ハンドラ | コメントのいいねが**中段プレビューと全件シートで同期しない**（実測: プレビューでc1をいいね→シート内の同一c1は`5`のまま、プレビューは`6`）。`COMMENTS`配列も更新されないため再レンダリングで巻き戻る。**mb/rig-detail.html でも同一挙動（9 vs 8）＝共通部品側の既存問題**で本ページ固有ではない | ledger #35「コメントは共通部品想定（rig/parts/log共通・**Next.js共通化必須**）」 | いいね状態を`COMMENTS`データ側に持たせ`renderComments()`で再描画する方式へ。rig-detail / parts-detail と同時是正（#30品質バッチ推奨） |
| 13 | LOW | バグ | mb/garage-rig-detail.html:702（ギャラリー）, 736（PHOTO NOTES）, LOG/PARTSサムネ | コンテンツ画像がすべて `alt=""`。PHOTO NOTESは`figcaption`があるため許容範囲だが、ギャラリー主画像・LOG/PARTSサムネは代替テキストなし | PC pc/myrig-garage-rig-detail-v6.html:375 は `alt="RC4WD Trail Finder 2 Mojave"` を付与 | 少なくともギャラリー1枚目にRIG名を`alt`として付与。LOG/PARTSサムネは記事タイトルがリンクテキストにあるため`alt=""`維持で可 |
| 14 | 参考 | 要確認 | LOG/PARTS「すべて見る」`href="#"`（L442,477）、`構成パーツを管理 →`（L430） | `garage.html?tab=` は ledger #36 で実装済み（`HYBRID_TABS` = all/rig/parts/log/saved、garage.html:938-941）だが、本ページの「すべて見る」3導線は `#` のまま。ただしこれらは**RIG単体スコープの一覧**であり garage.html のガレージ全体タブとは対象が異なるため、単純結線が正解か不明 | mb/rig-detail.html も同箇所は `#`（`href="#"` 計21件）で確定済み＝一貫はしている | RIGスコープの一覧ページ（/garage/rigs/[rigId]/logs 等）の要否をPage Role Matrixで確認。不要ならセクション内展開（もっと見る）へ寄せる |

**検査済み・問題なし（誤検出防止のため記録）**: 右列5パネル全て受け皿あり（MANAGE L288 / BUILD PARTS MAP L421 / **統計 L327＝落ちていない** / QUICK NOTE→PIT memo L307 / LINKS & SHARE L491）｜本文8セクション全て存在（DESCRIPTION・PHOTO NOTES・SPEC・BUILD DETAILS・LOG・PARTS・COMMENTS＋ptb/gallery）｜PC `section-edit-btn` 対象6キー（rig-name/gallery/description/photo-notes/spec/build-details）と mobile `data-edit` が**1対1完全一致**（`all`はledger #36でdrop裁定済み）｜編集モード実測 OFF=編集ボタン0個・ON=6個すべて`display:flex`｜`hidden`属性は `.rd-sheet-ov[hidden]`(L113) `.rd-sheet[hidden]`(L115) `.rd-actsheet-ov[hidden]`(L128) `.rd-actsheet[hidden]`(L130) `.gd-pit__row[hidden]`(L187) `.img-lightbox[hidden]`/`.img-lightbox__nav[hidden]`(mobile-shell.css:3220,3244) で**全てガード済み＝過去バグの再発なし**｜インライン`<script>`・mobile-shell.js とも `node --check` PASS｜id重複0｜440px横overflowなし（scrollWidth=clientWidth=440）｜ナビ統一（戻る＝`garage.html?tab=rig` L692、パンくず＝ガレージ/RIG一覧の2分岐 L257-263）はledger #36どおり｜画像404はaudit配布物に`img/`が同梱されていないためで実装欠陥ではない

### 付録G — garage-top

| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | 欠落 | mb/garage.html:787-813 `pitTableHtml()` / `.pit-card__acts` L805-808 | PIT TABLEのCTAが「ログ」「詳細」の2つのみで、**「＋パーツ追加」が無い**。`パーツ追加`/`＋ パーツ`/`追加` の3綴りでgrepしてもPIT内に0件 | PC L468-472 `.pit-hero-actions` に `pit-btn--primary`「ログを書く」/`pit-btn`「＋ パーツ追加」/`pit-btn`「詳細を見る →」の3本。サブ側もL491-495・L512-516で「ログ/パーツ/詳細」の3本 | pit-card__acts に「パーツ」ボタンを追加し3本構成へ復元（PC hero/sub と同数） |
| 2 | HIGH | 欠落 | mb/garage.html:794-810 `.pit-card` | **ピン解除（✕）が無い**。`unpin`/`ピンを外`/`ピン留めを外`/`✕`/`&times;`/`外す` の6綴りでgrep 0件。PIT TABLEからの取り外し操作がモバイルに存在しない | PC L456 `.pit-unpin`（hero, title="ピン留めを外す"）・L482/L503 `.pit-unpin`（sub×2）。ledger #36「**Garage Top＝全体管理**（並び替え・メイン交代・サブ枠・一覧的な追加削除）」で garage top 側が担当と裁定済み | pit-card に取り外し導線（✕ もしくは「PIT TABLEを編集」シート）を追加。#36の役割分担どおり全体管理をgarage topに置く |
| 3 | HIGH | 欠落 | mb/garage.html:512-515 `PIT_RIGS` / :789-812 | **メインピン/サブピンの階層が消失**。PIT_RIGSはフラット配列で `role`/`main`/`sub` 相当のフィールドも表示もなく、横スクロール2枚が等価に並ぶ。`メイン`/`サブ`/`main`/`sub` grepでPIT関連0件 | PC L448 `.pit-hero`（メインピン・大カード・`gc__status--lg`）と L476-519 `.pit-subs > .pit-sub`（サブ2台・`gc__status--sm`）が明確に別階層。ledger #36「最大3台・**メイン1＋サブ2**・既にメインありなら以降サブ・メイン交代はGarage Top」 | pit-card に main/sub の視覚差（サイズ or ラベル）と「メインにする」操作を実装。DBは #19⑩ の `garage_pit_rigs.role` 前提 |
| 4 | MID | 欠落 | mb/garage.html:789-791 `.pit__hdr` | **PIT TABLEの折りたたみトグルが無い**。`たたむ`/`ひらく`/`collapse`/`toggle`/`折りた`/`畳` でgrepしても該当0件（`classList.toggle` L893 のみ） | PC L444 `<button class="pit-collapse-btn" id="pitToggle">たたむ</button>` ＋ L660-682 の開閉JS（max-height遷移） | pit__hdr に開閉トグルを追加（横スクロール1画面ぶんとはいえPC正典の付随UI。dropなら要裁定） |
| 5 | MID | 欠落 | mb/garage.html:802 `.pit-card__memo` | PITメモが**表示専用**で編集導線が無い（鉛筆アイコン/「メモを編集」が0件） | PC L462 `.pit-memo__edit`（hero）・L488/L509（sub×2）の編集ペン。ledger #36「PITメモはgarage top側 PIT_RIGS の `memo` と**同一ソース（表示連動）**」、RIG詳細側には「メモを編集」あり＝garage top側だけ編集不能 | pit-card__memo に編集ペン（ボトムシート）を追加 |
| 6 | MID | バグ | mb/garage.html:795 `.pit-card__media` href="#" / :807 「詳細」href="#" / :676,686 `.gcard` href="#" | 同ディレクトリに **`garage-rig-detail.html` が実在するのに garage.html からの前方リンクが0件**（`grep -c 'rig-detail' garage.html` = 0）。逆方向（詳細→garage.html?tab=rig）だけが結線済みで往復が成立しない | ledger #36「**戻る矢印＝`garage.html?tab=rig`固定**／パンくず=ガレージ→garage.html」で往復ナビを確定。他ページは実結線済み（rig-detail.html参照: garage-rig-detail 6件・parts-detail 2件・log-detail 1件） | PITカード・RIGカードの href を `garage-rig-detail.html` へ結線 |
| 7 | MID | 欠落 | mb/garage.html:848-860 `hybridAllPaneHtml()` | **RECENT ACTIVITY の受け皿が無い**。「最近のログ」(L856) はPC LOGセクションの写像であり、対象RIG名・種別タグ（整備/カスタム）・相対時刻・「→ すべて見る」を持たない別物 | PC L385-430 `.gs-panel`＋`.gs-panel__head`「RECENT ACTIVITY」＋`.gs-activity-item`×5（`gs-activity-target`/`gs-activity-title`/`gs-activity-time`/`gs-activity-tag`）＋`.gs-panel__foot`「→ すべて見る」。ledger #32の構成列挙にも本項目の記載なし＝merge/drop裁定は未取得 | 作業台ゾーン直下に RECENT ACTIVITY を追加、または「最近のログ」への正式merge裁定を取得 |
| 8 | MID | 欠落 | mb/garage.html:771-777 `HYBRID_TABS` / 全ページgrep | gs-nav 7項目のうち**「設定」の写像先が実在しない**。`設定`/`/settings` は `/settings/profile`（＝プロフィール編集）3件のみで、設定ハブはタブにもBottomNavにも無い | PC L378-381 `.gs-nav-item[data-nav-key="settings"]`「設定」。G3「gs-nav等ナビ部分は既存Shell・ページ内タブ・**ハブへ写像**」 | 設定への写像先を宣言（プロフィール編集画面内に集約するなら要裁定） |
| 9 | MID | 欠落 | mb/garage.html:862-865 `savedFullPaneHtml()` / :857 保存済みセクション | SAVED配下の**「もっと見る→」が両列とも無い**。`secHtml` を moreLabel 引数なしで呼んでおり (L863-864)、生成されるのはタイトル＋件数のみ。「すべて」ペインの保存済みセクション(L857)も件数・右肩リンクの両方なし | PC L587-590/L602-605 `.saved-col-head` 内の `.saved-col-more`「もっと見る →」×2。G8「英字タイトル＋件数＋右肩リンクは**ワンセットでcarry**」 | お気に入り／ピン留め各列に「すべて見る ›」を付与。保存済みセクション見出しにも件数を付与 |
| 10 | MID | 欠落 | mb/garage.html:600 `.cw-prof__id` | **「公開ページをプレビュー」の可視導線が無い**。アバター行の `aria-label="… の公開ページ"` と `data-href` のみで、ラベル表示なし・`href="#"` の死にリンク。Own Viewから自分の公開見えを確認する手段が実質存在しない | PC L336-339 `.gs-profile-btn`「公開ページをプレビュー」（目アイコン付き独立ボタン）。ledger #32のプロフィール行列挙（アバター/名前/件数/フォロワー/編集）にも本項目の記載なし＝drop裁定は未取得 | プロフィール行または編集横に「公開ページを見る ›」を明示。維持するなら drop 裁定を起票 |
| 11 | MID | 整合 | mb/garage.html:489 `ME.cover` | カバー画像が **PC正典と別ファイル**（mobile: `img/RC4WD MOJAVE shakedown_24.webp` / PC: `shakedown_4.webp`）。加えてURLの空白が未エンコード（PCは `%20`） | PC L301 `.garage-cover > img src="./img/RC4WD%20MOJAVE%20shakedown_4.webp"`。ledger #32「薄型カバー96px（**PC `.garage-cover`と同一画像・同一クロップ center 40%**）」 | ME.cover を `img/RC4WD%20MOJAVE%20shakedown_4.webp` に修正 |
| 12 | MID | 整合 | mb/garage.html:28 `--cw-cover-h: 100px` | カバー高が裁定値と不一致（100px）。自ファイルのヘッダーコメント L294 も「薄型バナー(96px)」と書かれており自己矛盾 | ledger #32「**薄型カバー96px**」で確定固定 | `--cw-cover-h: 96px` に是正（または裁定値を100pxへ改訂） |
| 13 | MID | バグ | mb/garage.html:228 `.g-cw .pit-card__btn { min-height:32px }` / 実測 | **タップ48px未達**。440px実測で「ログ」44.5×32px、「詳細」44×32px（PITカード2枚ぶん計4要素）。`.pit__scroll` が `overflow-x:auto` のため透明::after拡張も効かない | ledger #29「48pxタップ領域の全面是正・**504要素実測で未達0件**」達成後、P17のPIT TABLE追加で新規混入した回帰 | P14手法（padding＋負margin de-clip ＋ 透明::after）でpit-card__btnを48pxへ。`.pit__scroll` も de-clip 対象に追加 |
| 14 | MID | バグ | mb/garage.html:232 `.g-cw .act` / 実測 | **タップ48px未達**。「今週の動き」帯（リンク全体）が440px実測で 416×39.6px | 同上 ledger #29（契約48px） | `.act` に min-height:48px もしくは透明::after拡張 |
| 15 | MID | 整合 | mb/garage.html:862-864 `savedFullPaneHtml()` | 保存済みが**他人のコンテンツである表現を喪失**。お気に入り=`MY_RIGS`（自分のRIG）の流用、ピン留め=`MY_LOGS`（自分のLOG）の流用で、種別バッジ混在・投稿者@user・保存日が一切出ない | PC L592-597/L607-612 の SAVED は `variant="sm-meta"` の RIG/PARTS/LOG **混在**カードで `badge`・`user="@trail_master"` 等・`date` を全件保持 | 保存済みダミーを他ユーザー混在データへ差し替え、カードに種別バッジ＋@user＋日付を表示 |
| 16 | MID | 欠落 | mb/garage.html 全体（footer相当なし） | PC footerの**情報導線の受け皿が全モバイルページに無い**。`terms`/`privacy`/`利用規約`/`プライバシー`/`運営`/`About` を mb/*.html 全grepしても該当0件（index-e-roomclip.html:526 の「ヘルプ」1件のみ） | PC L685-756 `.site-footer`：`site-footer__col-links`（MyRIGとは？/使い方/運営情報/お問い合わせ/ヘルプセンター/お知らせ）・`site-footer__legal-links`（プライバシーポリシー/利用規約/特定商取引法）・`site-footer__sns`×4・`site-footer__lang`（言語切替）。G2「footer内の情報導線はBottomNavへの自動吸収とみなさず、受け皿を別途確認する」 | モバイル用の情報導線受け皿（設定ハブ内リンク集 or 簡易footer）を1箇所定義。`site-footer__lang` は ledger #24（日英2言語・MVP時点）の必須導線 |
| 17 | MID | 要確認 | mb/garage.html:599-613 `.cw-prof` | PCプロフィールの**所在地（東京）・開始年月（2024.06 から）** に対応する表示が無い。bio・フォロー中数は ledger #32⑤＋構成列挙で裁定済みと解したが、この2項目はledger内に一切言及がない | PC L315-324 `.gs-profile-facts > span`（ピンアイコン＋「東京」/カレンダーアイコン＋「2024.06 から」） | Own Viewでの要否を裁定（carry / Public側へdrop）。契約v0.6の #32 条文化に併せて明記 |
| 18 | LOW | 欠落 | mb/garage.html:80-83（PC CSS参照） | PIT サブ枠の**空スロット表現が無い**。PC側にCSS定義があるが、モバイルは PIT_RIGS 2件をそのまま並べるのみで「＋ 追加」枠が出ない | PC CSS L80-83 `.pit-sub-empty` / `.pit-sub-empty-icon`（＋）/ `.pit-sub-empty-text`。ledger #36「最大3台・**サブ枠**・3台埋まりは『PIT TABLEを編集』を開く」。G10 | 横スクロール末尾に「＋ PIT TABLEに追加」枠を追加（PC本体HTMLでは未使用のためPC側の実装状況も要確認） |
| 19 | LOW | 整合 | mb/garage.html:854-856 `switchSecHtml(...)` | セクション件数から**単位が落ちている**（mobile: 7 / 312 / 84 の素数値） | PC L535/L553/L569 `.section__count`「7台」「312点」「84件」。G8 | 件数に単位を付与（またはEN辞書 #24 と併せて表記規則を確定） |
| 20 | LOW | 欠落 | mb/garage.html:831-846 `followingHtml()` | FOLLOWINGの**本文抜粋（1行）が無く**、件数もPC3件に対し2件 | PC L631/L640/L649 `.following-copy`（各itemに本文1行）、`.following-item`×3 | fol__row に抜粋1行を追加（`-webkit-line-clamp:1`）。件数3件に揃える |
| 21 | LOW | 整合 | mb/garage.html:806 `href="/register/log"` | PITカードの「ログ」CTAが**RIG非スコープ**。2枚のカードとも同一URLでRIG文脈が渡らない | PC L469 `.pit-btn--primary`「ログを書く」はそのRIG（pit-hero）のコンテキスト内アクション | `/register/log?rig=<id>` 相当のRIG文脈付きURLに（モックでもクエリで表現） |
| 22 | LOW | バグ | mb/garage.html:884 `role="tabpanel"` / :878-880 `.seg-tab` | tabpanel に **`aria-labelledby` が無く**、seg-tab 側にも `id` が無い（`aria-controls` のみ片方向）。同種部品で実装が食い違っている | feed.html:76/82 は `role="tabpanel" aria-labelledby="feedTabReco/Following"` を付与済み。CRITERIA検証観点6（aria欠落）・7（横断整合） | seg-tab に id を振り、tabpanel に aria-labelledby を付与 |
| 23 | LOW | バグ | mb/garage.html:939-942 `initTab` | `?tab=` は5タブ全て（all/rig/parts/log/saved）で動作しバリデーションも有効（不正値`?tab=zzz`は「すべて」にフォールバック）＝実測PASS。ただし**タブ切替時にURL/historyへ反映されない**ため、詳細ページから `?tab=rig` で戻った後に別タブへ移動すると端末バックで状態が復元されない | ledger #36「garage.htmlに `?tab=rig` を実装（起動時に指定タブを開く）」＝起動時指定は要件充足。history同期は未定義 | `history.replaceState` でタブ状態をURLに同期（任意・#30品質バッチで可） |
| 24 | LOW | バグ | mb/garage.html:616-623 `wireGptHeader()` | 存在しない `#gptCreateShortcut` を参照する**デッドコード**（L935 で毎回呼ばれ即return） | 同ファイル内に `gptCreateShortcut` の生成箇所なし（grep 1件＝参照のみ） | 関数と呼び出しを削除（#30⑤の畳み込みバッチに含める） |
| 25 | LOW | 要確認 | mb/garage.html:921-927 `?view=compact` 経路 | 残置中の compact 経路が**レイアウト崩壊**する。440px実測で `.pit-card__media` 38.4px・`.fol__row` 16px・`.pit-card__btn` 28×16px（v4スタイルが全て `.g-cw` スコープのため compact では未適用） | ledger #30⑤「garage.htmlの旧比較コード（?view=compact / ?layout / ?notif / ?qa と関連関数）の完全畳み込み」＝残置自体は裁定済み。ただし「崩れる状態でURL到達可能」は本監査で新規検出 | #30⑤の畳み込みを前倒し、または即時に compact 分岐を無効化（`?layout`は既にコード上未読み取り＝実質死んでいる） |
| 26 | LOW | 整合 | mb/garage.html:763,854 `OPT.empty` | `?empty=1` の空状態が**RIGのみ**。RIG0台なのに PARTS 312／LOG 84／お気に入り31／ピン留め14／今週の動き（12いいね・342閲覧）が満杯で、空状態デモとして矛盾 | G10「PC版に定義がある場合はcarry」。PC側 `.pit-sub-empty` 以外に空状態定義なし＝モバイル独自デモの内部整合の問題 | `?empty=1` 時は各セクション件数を0にし、PARTS/LOG/保存にも `m-empty` を出す |

### 付録H — 横断Shell



| # | 深刻度 | 種別 | 箇所（file:行 or セレクタ） | 指摘 | 根拠 | 推奨処置 |
|---|---|---|---|---|---|---|
| 1 | HIGH | 欠落 | 全モバイル9ページ（`footer`不在） | **footer情報導線の受け皿が全ページで0件**。「MyRIGとは？/使い方/運営情報/お問い合わせ/ヘルプセンター/プライバシーポリシー/利用規約/特定商取引法」の8項目を全9ページで文字列grepし**到達経路0**を実測（index-e-roomclipの「ヘルプ」1件はユーザーメニューの別項目で、法務・運営情報へは非到達）。BottomNav・ユーザーメニューシートいずれにも受け皿なし | PC `myrig-feed-v3.html:1121 <footer class="site-footer">`（L1134 MyRIGとは？/L1154 ヘルプセンター/L1185 特定商取引法）、`myrig-rig-detail-v6.html:683`、`myrig-garage-top-v6.html:685` — PC全8ページに存在。G2「footer内のAbout/Help/Terms/Privacy等の情報導線はBottomNavへの自動吸収とみなさず、受け皿を別途確認する」 | 受け皿を1つ裁定（ユーザーメニューシート内「情報」ブロック案）し全9ページへ共通実装。裁定まではR2欠陥として起票 |
| 2 | HIGH | バグ | `search.html` `.home-section__more`（履歴を消す）/ `.recent-chip__x` | **48px拡張ヒット領域が相互に重なり誤タップが発生**。`.home-section__more`は正しく48px `::after`を持つ（computed height:48px）が、隣接`.recent-chip__x`の48px `::after`が下側から被さり実効ヒット高**36.0px**。elementFromPoint実測で「履歴を消す」の下端+2〜+14pxが**`.recent-chip__x`（履歴チップ個別削除×）に当たる**＝一括削除を押したつもりで個別削除が発火 | `css/mobile-shell.css:3402,3411`（`.home-section__more`/`.recent-chip__x`を同一`::after`拡張リストに登録）、L3435-3440（`min-width/min-height:48px`・z-index指定なし）。台帳#29「48px…504要素実測で未達0件」の判定が重なりを見ていない | 重なる要素間で`::after`の拡張方向を非対称化（上方向のみ／左右のみ）するか、行レイアウトの実寸間隔を48px以上に確保。#29の検証手法に「隣接ヒット領域の衝突検査」を追加 |
| 3 | HIGH | バグ | `feed.html` `.feed-card__rigchip.tap48` / `.feed-card__stat.tap48` | 同種の**48px拡張の衝突**。`.feed-card__rigchip.tap48`は実効ヒット高**41.0px**（上は`.thumb-img`、下は`.feed-card__stat.tap48`の`::after`に侵食）。`.feed-card__stat.tap48`は高49.0pxだが**実効幅43.5px**（隣接statどうしの`::after`が横で衝突）。RIGチップ下端タップが♥いいね／コメントを誤発火する | `css/mobile-shell.css:2147-2149 .tap48::after{width:48px;height:48px}`（z-index指定なし）、L3128 `.feed-card__stat{min-height:32px}`、L3126コメント「小さい rigchip は既存の .tap48 透明拡張ヘルパーで中心48px角を確保する」＝意図どおり機能していない | statボタン間のgapを実寸で確保し、rigchipとaction行の縦間隔を8px以上に。`.tap48::after`にも衝突検査を通す |
| 4 | HIGH | バグ | `rig-detail.html` / `parts-detail.html` / `log-detail.html` / `garage-rig-detail.html` / `garage.html`（計14要素） | **Phase2詳細ページ群がP14の48px是正リストに未登録**（`::after` computed content が全て`none`＝拡張ゼロ）。サブピクセル実測値: `.rd-ptb__cat` 18.6px（rig/parts/garage-rig）/ `rig-detail.html:386`の無クラス`<a>`「ガレージを見る ›」14.1px（parts 14.4 / log 19.4）/ `.btn-follow` 34.2px（3ページ）/ `.rd-subhdr__back` 38×38px / `.rd-subhdr__edit` 35px / `garage-rig-detail.html:260` パンくず`<a>`「RIG一覧」20.2×48.5px（「ガレージ」20.2×49.0）/ `.gd-pit__btn` 34.8〜35px×3 / `.gd-action-btn` 41px×2 / `.gd-manage__publink` 18.6px / `.pit-card__btn` 33×45px×2 / `.act` 40.6px | `css/mobile-shell.css:3390-3412` の拡張対象セレクタ一覧に上記クラスが**1つも含まれない**（grep実測）。契約§7-4の閾値48px・台帳#29で44→48へ訂正済み | 上記14セレクタを L3390-3412 の`::after`拡張リストへ追加。ただし#2/#3の衝突を再発させないため、追加後に隣接衝突検査を必須ゲート化 |
| 5 | HIGH | バグ | 全9ページ（`img/` ディレクトリ不在） | **画像・PWA資産が全滅**。`/tmp/audit/mb/img/` が存在せず、レンダリング済み`<img>` **242枚中227枚が naturalWidth=0**（rig-detail 30/30・garage 36/36・log-detail 16/16・parts-detail 27/27・search-results 26/26・index-e-roomclip 55/60・garage-rig-detail 22/24・feed 15/23）。加えて全9ページが参照する `manifest.webmanifest` と `icons/apple-touch-icon.png` も不在＝404 | Playwright実測（naturalWidth判定）＋`ls /tmp/audit/mb/img` → No such file or directory。`alt`欠落は0件で属性側は健全 | 監査配布物への`img/`・`icons/`・`manifest.webmanifest`同梱漏れであれば配布手順を是正。実ツリー側の欠落なら P0 | 
| 6 | MID | バグ | `garage-rig-detail.html` BottomNav `.mobile-bottom-nav__item[data-nav="garage"]` | **現在地表示が片肺**。`is-active`クラスは付くが`aria-current="page"`が**null**。他の現在地表示ページ（index-e-roomclip / search / search-results / feed / garage）は全て`is-active`＋`aria-current="page"`の両方を持つ＝本ページのみスクリーンリーダーに現在地が伝わらない | Playwright DOM実測（9ページ横断）。BottomNavマークアップ自体は全9ページで**構造完全一致**（差分は空白のみ）＝本属性だけが逸脱 | `aria-current="page"` を追加。併せて rig/parts/log-detail は現在地無し・garage-rig-detailのみ点灯という**方針の不統一**も裁定要（詳細面を親タブに帰属させるか否か） |
| 7 | MID | 整合 | `rig-detail.html` `.g-sec__ttl`「SHARE & LINKS」/ `parts-detail.html` 同 / `garage-rig-detail.html`「LINKS & SHARE」 | **同一部品の見出し語順がページ間で逆転**。rig-detail・parts-detailは「SHARE & LINKS」、garage-rig-detailは「LINKS & SHARE」。さらにPCではrig/partsとも**SHARE と LINKS が別セクション**であり、モバイル側で無裁定にmergeされている | PC `myrig-rig-detail-v6.html:427 SHARE` / `:443 LINKS`（別セクション）、`myrig-parts-detail-v6.html:363 SHARE` / `:379 LINKS`（別セクション）、`myrig-garage-rig-detail-v6.html:674 LINKS &amp; SHARE`（PC側で統合済み＝モバイルの語順が正）。`myrig-log-detail-v6.html:258 SHARE`のみ＝モバイルlog-detail「SHARE」は正 | 語順を「LINKS & SHARE」に統一（PC garage-rig-detail準拠）。rig/parts のSHARE＋LINKS統合はG8のmergeにあたるため裁定を取る |
| 8 | MID | 整合 | `garage.html:268` `.g-cw .g-sec__ttl` / `garage-rig-detail.html:96` `.rd .g-sec__ttl--ja` | **セクション見出しのフォント切替が3方式に分裂**。正は`.g-sec__ttl`（`css/mobile-shell.css:3471` Barlow Condensed・uppercase・letter-spacing .05em）＋和文時のみ`--ja`修飾子（Noto Sans JP・text-transform:none）。ところが①garage.htmlは`--ja`を使わず`.g-cw .g-sec__ttl`で**全見出しを一括上書き**（英字見出しもuppercase喪失）②garage-rig-detailは**英字見出しに`--ja`を誤適用**（`LOG` `PARTS` `BUILD PARTS MAP`がNoto Sans JP・非uppercaseで描画）— 同じ「LOG／PARTS」棚がrig-detailでは和文`このRIGのログ12`/`装着パーツ11`＝表記自体も不一致 | PC `myrig-garage-top-v6.html:535/553/569/582/621` は `section__title` = **RIG / PARTS / LOG / SAVED / FOLLOWING**（英字）＋`section__count`別span。PC `section__title--ja` の使用は8箇所すべて和文（`myrig-log-detail-v6.html`・`myrig-parts-detail-v6.html`・`myrig-rig-detail-v6.html`） | `.g-cw .g-sec__ttl` 一括上書きを撤去し和文見出しへ`--ja`付与へ統一。garage-rig-detailの英字見出しから`--ja`を除去。rig-detail↔garage-rig-detailでLOG/PARTS棚の表記（英字 or 和文）を一本化 |
| 9 | MID | 文言 | `.g-sec__more` 全詳細ページ | **右肩リンク文言がページ内で混在**（G8）。rig-detail=「すべて見る」×2のみ／parts-detail=「すべて見る」×1＋**「もっと見る」×3**／log-detail=「すべて見る」×1＋**「もっと見る」×3**／garage-rig-detail=「すべて見る」×2＋「もっと見る（+8件）」×1。同一ページ内で2語が併存している | PC正典の右肩リンクは**「もっと見る →」13箇所**が優勢（`View all →`2 / `すべて見る →`1）。G8「英字タイトル＋件数＋右肩リンク（もっと見る→ / View all→）はワンセットでcarry」。search-results側の「◯◯の結果をすべて見る→」は search-page-plan-v2 で確定済みのため対象外 | 詳細ページの右肩リンクを1語に統一（PC優勢の「もっと見る →」推奨）。search-results の確定文言とは役割が異なる旨を注記 |
| 10 | MID | 整合 | 全9ページ `href="#"` 306件 | **DOM実測でアンカー379本中306本（80.7%）が`href="#"`のまま**。内訳（`#`件数/総a数・多い順）: index-e-roomclip 101/106・garage 41/57・search 30/46・search-results 29/37・parts-detail 28/38・feed 22/30・rig-detail 21/30・garage-rig-detail 18/30・log-detail 16/25。頻出セレクタ上位は `.gcard`（78件・カード全体リンク）、`.catsheet-item`（44）、`.sr-row`（27）、`.ctp`（15）、`.g-sec__more`（16）、`.feed-card__hdr`(8)/`__stat`(8)。**なお`.html`を指すリンクは全9ページで実在ファイルに解決し、リンク切れ0件** | 静的grep＋Playwright DOM実測の両方で一致。台帳#21のFeedリンク切れは解消済みを確認（feed.html=200・BottomNav全遷移可） | モックとして許容範囲か、カード系（`.gcard`/`.sr-row`）だけでも遷移先を結線するかを裁定。Next.js実装時のルーティング表の起点として件数を記録 |
| 11 | LOW | 整合 | `css/mobile-shell.css:2147-2149` vs `:3413-3441` | **48px拡張ヘルパーが2系統併存**。`.tap48::after`は`width:48px;height:48px`の**固定48px角**、P14で追加された一覧側（L3413-）は`width:100%;height:100%;min-width/min-height:48px`の**実寸拡張**。前者は元要素が48pxより広い場合に**ヒット領域が縮む**（`.feed-card__stat`の実効幅43.5pxの一因） | grep実測。L3126のコメントは両者を同一の「既存ヘルパー」として扱っており、差異が認識されていない | `.tap48::after` を L3413 側の実寸拡張形（`width:100%;height:100%;min-*:48px`）へ統一 |
| 12 | LOW | バグ | `index-e-roomclip.html` `.a2hs__body` | ダークテーマ時に`background: rgb(242,244,248)`の**明色カードが暗背景上に残る**（`data-theme="dark"`実測・body背景 rgb(17,19,24)）。文字色は`rgb(20,23,28)`で自己完結しており可読性は保たれるがテーマ整合を欠く。他8ページで検出された明色面（`.filter-chip.is-active` / `.rd-fchip.is-on`）は**選択状態の意図的な反転**でありこれのみが逸脱 | Playwright computed-style実測（全要素走査・輝度0.72超）。台帳#30④「Dark Mode仕上げ」は起票のみ＝未消化キュー。Homeはr14凍結のため即時修正対象外 | 台帳#30④の対象に`.a2hs`を明記して次バッチで処理 |
| 13 | LOW | バグ | `css/mobile-shell.css:2152-2153` | `.mobile-bottom-nav{padding-bottom:env(safe-area-inset-bottom)}` と `.mobile-shell-header{padding-top:env(safe-area-inset-top)}` のみ**フォールバック値が無い**。同ファイル内の他13箇所は全て`env(..., 0px)`形式（L116/646/650/658/740/2227/2885/2971/3046 等）。env()非対応環境では宣言ごと無効化される | grep実測（`safe-area-inset` 全15箇所を照合）。Shell CSS以外では rig/parts/log/garage-rig-detail のページローカル`.rd-sheet`/`.rd-actsheet`も無フォールバックだが4ページ間で相互に一致 | `env(safe-area-inset-bottom, 0px)` / `env(safe-area-inset-top, 0px)` へ統一 |
| 14 | LOW | 要確認 | `garage-rig-detail.html:157` `var(--ms-surface, var(--ms-bg))` | `--ms-*`トークン15種のうち**`--ms-surface`のみ定義が存在しない**（CSS/HTML全走査で定義0・使用1）。ただし契約v0.5 §2実装注記「fallback付き参照形式で正規化・新token追加禁止」に沿ったフォールバック付き参照であり**現状は仕様適合**。他14種は全て定義済み | grep実測（使用15／定義14／差分1）。契約v0.5 rev.1 §8-1「未定義token 4種…§2に正規化表を追加」で既知処理済み | 対応不要。正規化表の対象であることの確認のみ |

**（機械実測で異常なしを確認した項目）** JS構文＝全10ファイル・14インラインscriptブロック＋`js/mobile-shell.js`で構文エラー0／Playwright実行時`pageerror` 0件・`console.error` 0件（404を除く）／`id`重複＝静的・DOM実測とも全9ページ0件／未定義ID参照＝`pnMore`(rig/parts)・`tileRank`(Home)・`gptCreateShortcut`(garage)の4件は**全てnullガード付き**でnull参照リスクなし（`gptCreateShortcut`は台帳#30⑤の旧比較コード畳み込み対象）／BottomNavマークアップ＝全9ページで構造完全一致（差分は空白のみ）・Header は3variant（`mobile-shell-header` / `--search` / `rd-subhdr`）で意図的差異／`.html`リンク切れ0件／`alt`欠落0件／**G7無限スクロール＝`feed.html`（台帳#25の限定例外）と`search-results.html`の種別タブ（台帳#26で裁定済み）の2ページのみ。`search-results.html:592-593`で「すべて」タブは`renderDigest()`分岐＝ダイジェスト型を実装で確認しており#26と一致、逸脱なし**。
