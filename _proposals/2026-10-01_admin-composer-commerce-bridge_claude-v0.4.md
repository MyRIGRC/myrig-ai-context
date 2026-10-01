# Admin v0.4: Page Composer / Commerce / Research Bridge のデータの形（Claude 主査案）

> 作成: 2026-10-01 JST / Claude（Cowork）／ 正典 revision: MYRIG-20261001-134 で追加
> 状態: **PROPOSAL**。schema・migration・mock は未着手。Production DB 非接触
> 前提: 裁定原本 `_decisions/2026-10-01_admin-console-v1.md`（v1.0）。本書はその §9「次」の 3 つ
> 入力: Gemini SPARK の先行視点（型の決まった部品・ブロック単位の予約・下書きと公開・PR 表示の強制・リンクの集約・橋は片方向）と、GPT の「見る点」（将来の拡張性・Commerce と Research の境界・橋が二重の正本にならないか）
> 根拠の範囲: GitHub main（canon）確認済み ／ mock は GitHub push 済みの状態（30c89bd）だけ確認 ／ ローカル未確認

---

## 0. 正典から先に拾った決まり（これに反する設計をしない）

| 決まり | 出典 |
|---|---|
| カテゴリトップ・パーツトップの棚は、管理画面から WordPress のウィジェットのように入れ替えられる前提（`data-layout-type` / `data-card-variant` / `data-query-preset`） | p22-b11 :13-17・p22-b14 :87-90 |
| Section-driven にする面は 5 つ（INDEX / Category Top / SubCategory / Parts Top / Parts Sub）。面ごとに使える layout_type が決まっている。**検索結果と Feed は固定 UI**（管理画面で並べ替えない） | page-role-matrix §4 :185-202 |
| 並びの軸は「Domain × セクションのレジストリ（順番つき）」。意味の無い記号やレイアウトの型を軸にしない | browse-domain-scope §9 :252-284 |
| **ランキング全廃**。`*-ranking` プリセットを定義しない。件数で順位を付ける棚・並び順・「人気」「よく使われている」「1 位」も不可。件数を情報として出すのは可 | page-role-matrix §7 :350・library-zero-base D6 :78-91 |
| Library は Editorial な自由文を持たない・特集 / ランキング / セール棚を置かない | library hybrid D12・D14 |
| 自由 HTML を使わない | admin-console v1.0 |
| **MVP で AdSense なし** | info-legal-support :339 |
| 広告枠: ガレージ系に出さない・1 ページ 1 枠・PR 表示必須・本文とは別枠・位置は本文と関連の境目 | p22-b4 :13・:28-30・:41 |
| アフィリエイト: MyRIG はショップではない・押しの強い導線はしない・迷ったら出さない・BUY と INFO は別枠・PR を表示 | #34（mobile-feedback-ledger :142-147） |
| Library の購入導線は弱めない。購入先は実在するものだけ・アフィリエイトは明示・URL が無ければリンクにしない。共通部品 `<md-commerce>`（RIG と PARTS で別実装を作らない） | library hybrid D16・HANDOFF_20260924 :204・CURRENT :2905 |
| Detail 右レーンは「標準の並び ＋ 将来 Widget Stack」。識別子は `data-widget`、順番の正本は DOM の順 | CURRENT :3550-3560・:3697 |
| `master_aliases` は検索の展開専用。関係づけのキーにしない | library hybrid D17 |

---

## 1. Page Composer

### 1.1 考え方
- **「何が置けるか」はコード、「どこに何をどう置くか」は DB。**
  部品の種類・使える layout_type・カードの型・並べ方（プリセット）は、コード側のレジストリ 1 か所（Shared UI Single Source の型）で決める。管理画面は**その中から選ぶだけ**。v8 の規約から外れた組み合わせは、選択肢に出てこない
- **下書きと公開を分け、公開は版として残す。** 前の版に戻すのは「古い版をもう一度公開する」だけ
- **壊れても利用者の画面は落ちない。** 公開中の編成が読めなければ、コードの既定の編成で出す。1 つの棚が失敗したら、その棚だけ出さない
- **軽い。** 公開中の編成はキャッシュし、公開した瞬間にだけ作り直す。利用者の表示のたびに編成を DB に取りに行かない

### 1.2 データの形（`page_blocks` の作り直し・G11。本番に migration していないので作り直せる）

**`page_layouts`**（ページ × 版）
| 列 | 中身 |
|---|---|
| id | |
| page_type | index / category_top / subcategory_top / parts_top / parts_subcategory（将来: detail_rail・library_top ほか） |
| page_ref_id | カテゴリなど。index は NULL |
| version | 1, 2, 3… |
| status | draft / scheduled / published / archived |
| publish_at | 予約公開の日時（scheduled のとき） |
| published_at・published_by | 公開した日時と人 |
| note | 何を変えたかのメモ |

- 1 つのページで published は常に 1 つ。新しい版を公開すると、前の版は archived（消さない）
- 下書きはいくつでも作れる。**プレビュー**は下書きの id を付けて利用者の画面を開く（管理者のログインがあるときだけ効く）

**`page_layout_blocks`**（版の中の棚）
| 列 | 中身 |
|---|---|
| id・layout_id・sort_order | |
| block_type | 下の 1.3 の決まった値だけ |
| layout_type | hero_shelf / horizontal_shelf / compact_shelf / feature_banner / editorial_banner / library_links / ad_slot（page_type ごとに使えるものが決まる） |
| card_variant | 例 browse_hero / browse / browse_sm（layout_type ごとに使えるものが決まる） |
| query_preset | レジストリのプリセット名（下 1.4） |
| params | プリセットが受け付ける値だけ（カテゴリ・メーカー・件数など）。**型はプリセットごとにコードで検査** |
| heading・subheading | 見出し（短い文。HTML 不可） |
| starts_at・ends_at | **棚ごとの表示期間**（イベントの切り替え・期間限定の特集） |
| is_enabled | ON / OFF |

### 1.3 部品の種類（block_type・自由 HTML なし）

| block_type | 中身 | 置ける面 |
|---|---|---|
| content_shelf | RIG / PARTS / LOG の棚。中身は query_preset で決まる | 5 面すべて |
| manual_pick | 運営が選んだ投稿の棚（「注目に選ぶ」= Content 領域 B）。**選んだ id の並びをそのまま出す。件数や人気で並べ替えない** | INDEX・Category Top |
| feature_banner | 画像 ＋ 見出し ＋ 行き先（サイト内のページ） | INDEX・Category Top・Parts Top |
| editorial_banner | 短い文 ＋ 画像 ＋ 行き先。**Library には置けない**（D12） | INDEX |
| library_links | Library への入口 | INDEX |
| announcement_slot | お知らせ（announcements）から 1 件。Home の 1 行とは別（G18 は Home レーン） | INDEX（C） |
| ad_slot | 広告枠。中身は Commerce の配信が決める（§2.4）。**配信が無ければ何も出さない** | INDEX・Category・SubCategory・Parts Top（**MVP では出さない**） |

### 1.4 並べ方（query_preset のレジストリ）
- 名前は**何で並んでいるかをそのまま言う**: `newest_rigs`・`newest_parts`・`newest_logs`・`recently_updated_rigs`・`category_newest(category)`・`manufacturer_newest(maker)`・`rigs_using_master(master)`・`logs_of_category(category)`・`manual(ids)`
- **定義しないもの**: popular_* / most_liked / most_commented / *-ranking（ランキング全廃）
- 件数（「N 台」など）はカードの情報として出してよい。**並べる理由にはしない**
- 🔴 **G27（新）**: mock の Home / Browse に、正典と食い違う「ランキング」の見出しと popular 系プリセットが残っている（`pc/myrig-home-v3.html` :2804 `weekly-like-ranking-rig` ほか・page-role-matrix §7 違反）。mock の作り直しは帰宅後・Home / Browse レーンを開けるとき。**Composer のレジストリには入れない**

### 1.5 将来（設計だけ）
- Detail 右レーンの Widget Stack: page_type = `detail_rail`。block = `data-widget` の識別子（builder / entity-actions / base-model / …）。並びの正本を DOM の順から DB に移すのは、このときだけ（C）
- Library Top の編成（今は固定・POST-MVP で見直し）
- セクションの並びのパターン（3 つ目の RIG Category を作るときに再開・browse-domain-scope §9）

### 1.6 MVP
- **MVP は今の固定の編成（コードの既定）で出す**。データの形と、コードのレジストリの決まりはここで確定（B）
- 公開後に作る順: 下書き / 公開 / 戻す → プレビュー → 棚の期間 → 予約公開

---

## 2. Commerce

### 2.1 考え方
- **広告とアフィリエイトの表示は、必ず 1 つの部品を通す**（`<md-commerce>` と広告枠の部品）。部品が「PR」「広告」の表示を必ず出し、**消す設定を作らない**（景品表示法のステルスマーケティング規制・Gemini）
- **URL を投稿や画面に直接書かない。** 表示のたびに「今の購入先」を引く。提携が終わった・URL が変わったときに 1 か所で直せる（Gemini）
- **迷ったら出さない。** 当て方に自信が無いもの・リンクが切れているもの・廃盤品は出さない（#34）
- **Commerce は Research ではない。** Master を参照するが、Master の正本は書かない。購入先は App が持つ（G12）

### 2.2 アフィリエイト・購入先（`affiliate_links` の作り直し）

**`commerce_merchants`**（お店）: id・名前（Amazon / 楽天 / AliExpress / メーカー直販 …）・**提携の種類（affiliate / direct / official）**・提携の状態（申請中 / 有効 / 停止）・国

**`commerce_offers`**（Master ごとの購入先）
| 列 | 中身 |
|---|---|
| target_type・target_id | rig_master / rig_master_variant / part_master / part_master_variant（Research の PK。論理参照） |
| merchant_id | お店 |
| country_code | JP / US / GLOBAL（市場の軸。言語・法域とは別） |
| url | 購入先 |
| is_affiliate | **true なら「PR」を必ず出す**（お店の提携の種類から決まる。手で外せない） |
| priority・is_active | 並び・ON / OFF |
| link_status・checked_at | リンクの確認（ok / broken / unknown）。**broken は出さない** |

- 表示: Library Detail と RIG のベースモデルの購入先（`<md-commerce>`）。BUY と INFO（公式）は別枠
- 出し方: **表示のときにサーバーで今の購入先を引く**（中継の URL を挟まない）。クリックの数を取るなら、表示の部品から軽い記録を送る
- ⚠️ 中継の URL（`/go/xxx` のような転送）にするかは、**各提携先の規約で転送が許されるかを確かめてから**（要確認・R3 と同じ確認の枠）

### 2.3 キーワードとの結びつけ（C・設計だけ）
- Master に紐付かない投稿（Custom の名前・LOG の本文）に、購入先を出すかのルール
- **`commerce_keyword_rules`**: 当てる言葉（`master_aliases` の別名を使う）・除外する言葉・当てる先（Master）・出してよい面・確度・有効 / 無効
- 決まり: **当てた先が 1 つに決まらないときは出さない**。利用者の投稿の本文に、購入先のリンクを差し込まない（本文の自動リンク禁止と同じ考え・LOG composer :310）。出すのは投稿の外の別枠だけ

### 2.4 企業バナー・有料バナー・AdSense（C・設計だけ）

| 表 | 中身 |
|---|---|
| `ad_advertisers` | 広告主・連絡先・契約のメモ |
| `ad_campaigns` | 期間・状態（下書き / 承認 / 配信中 / 終了）・出す面・回数の上限 |
| `ad_creatives` | 画像・短い文・行き先・代替テキスト。**運営の承認を通ったものだけ配信** |
| `ad_placements` | 広告を出せる場所のレジストリ（Composer の ad_slot・Detail 右レーンの ad・Feed 右レールの 2 枠）。ガレージには作らない |
| `ad_daily_stats` | 日ごとの表示とクリックの合計（1 回ごとの記録は持たない = 軽い） |

- 枠の中身は **sponsor / adsense / none** のどれか。**MVP は全部 none**（何も出さない）
- AdSense を出すときは Cookie の同意（既存の HOLD）とセット
- 決まり（B4 Q2・#34）: 1 ページ 1 枠・本文とは別枠・「広告」の表示・ガレージに出さない・頻度の上限
- **検索結果の広告**: B4 は「1 枠」、proposal（search-results-ux・search-system-design v3）は「0」で未決着 → **MVP は 0**（AdSense なしと同じ理由）。スポンサーを始めるときに決め直す（§5 Q-C2）

### 2.5 MVP
- 区分: 購入先（2.2）= **Q-K5 次第**（A or B）／ キーワード・バナー・AdSense = C
- どの場合でも、**PR の表示を強制する部品と `commerce_offers` の形は今決める**

---

## 3. Research Bridge（橋）

### 3.1 考え方（片方向 2 本・二重の正本を作らない）
| 流れ | 中身 | 正本 |
|---|---|---|
| **App → Catalog（吸い上げ）** | Master に無いもの・表記の揺れの候補・誤りの報告・0 件の検索語を「調査の依頼」として渡す | 候補 = App ／ 依頼と結果 = Research |
| **Catalog → App（反映）** | Research で確定して公開された Master を、App へ読み取り専用で同期 | Master = Research |

- **App から Master を直さない。Catalog から利用者の投稿を直さない**（Gemini の非対称）
- 依頼の状態は **Research 側だけが持つ**。App 側の候補は「渡した / 渡していない」と依頼の ID だけを持ち、結果は写さない（写すと二重の正本になる。GPT の見る点）
- 結果が App に届くのは**同期だけ**。Master が増えたら、本人の Custom に「候補」として出る（v1.0 Q6。自動で書き換えない）

### 3.2 データの形

**App 側: `catalog_candidates`**（候補を束ねたもの）
| 列 | 中身 |
|---|---|
| kind | unmatched_custom（Master に無い）/ alias（表記の揺れ）/ correction（誤りの報告）/ zero_result（0 件の検索語） |
| normalized_text | 正規化した文字（例: 「ﾊｲﾗｯｸｽ」→「ハイラックス」） |
| target_hint | 関係しそうな Master（あれば・論理参照） |
| occurrence_count・first_seen・last_seen | 何件・いつから |
| status | new / sent / dismissed |
| research_request_id | 渡した先の依頼（Research の ID・論理参照） |

- 元になる行（Custom の投稿・報告）を指すだけで、利用者の文やユーザー ID を写さない
- **0 件の検索語**: 利用者を特定しない形で、言葉と日ごとの件数だけ残す（**G25**）。保存期間を持つ

**App 側: 誤りの報告の受付**（mock の Draft `master_correction_reports` を App の受付として作り直す・G10）
- 利用者が Library で送る → App に受付 → `catalog_candidates`（kind=correction）へ → Research へ
- Draft の「運営が Master を手で直す」部分は失効

**Research 側: `research_requests`**（Research の DB）
- 依頼の種類・中身（正規化した文字・件数・ヒント）・状態（open / researching / added / rejected / duplicate）・結果の Master・調査指示書（Claude Code へ渡す文・旧アプリの考え方を引き継ぐ）

**Research 側: 画像の許諾の記録**（R9）
- `master_images` の画像ごとに、出典・許諾の根拠（メーカーの利用条件・確認した日・確認した人）・止めた日と理由（メーカーからの指摘・対応集 S13）
- 列の全体は Research の正本で決める（App は決めない）

### 3.3 同期（Catalog → App）
- 片方向・読み取り専用。App 側の同期コピーを書くのは同期だけ（A-3）
- 実行と失敗は `admin_jobs`（v1.0）。止まったら Operations に出す
- 同期の方式（複製・頻度）は App 側の未決事項（schema :442）。**MVP までに決める**

### 3.4 MVP
- 候補を集める（0 件の検索語の記録を含む）= B ／ 依頼と調査指示書 = B ／ 同期の方式 = **MVP までに必須**（Master を App に出す前提だから）

---

## 4. 正典の抜け（v0.4 で足したもの）

| # | 抜け | 扱い |
|---|---|---|
| G27 | mock の Home / Browse に「ランキング」の見出しと popular 系プリセットが残る（page-role-matrix §7 違反） | Composer のレジストリに入れない。mock は Home / Browse レーンを開けるときに直す（**帰宅後確認**に積む） |
| G28 | 検索結果の広告が B4（1 枠）と proposal（0）で未決着 | MVP は 0（§5 Q-C2） |
| G29 | 購入先の転送 URL が提携先の規約で許されるか未確認 | 要確認。MVP は転送しない |
| G30 | Research 側の `master_images` / `master_external_links` / `master_publication` の全列が canon に無い | Research の正本で確認してから Catalog 区画を設計 |

---

## 5. イタヤに決めてほしいこと（推奨つき）

| # | 問い | Claude の推奨 |
|---|---|---|
| Q-P1 | `page_blocks` を `page_layouts`（版・下書き / 公開）＋ `page_layout_blocks`（棚・期間）に作り直す | **はい** |
| Q-P2 | 部品の種類は §1.3 の 7 つに限る（自由 HTML なし）。「注目に選ぶ」は選んだ順のまま出す | **はい** |
| Q-C1 | 購入先は表示のときにサーバーで引く（転送 URL を挟まない。挟むかは規約を確かめてから） | **はい** |
| Q-C2 | 検索結果の広告は MVP では 0 | **はい** |
| **Q-K5** | 公開時に購入先（アフィリエイト）を出すか | **出す。ただし提携が「有効」になったお店の購入先だけ・PR 表示つき・Library Detail と RIG ベースモデルだけ**。提携がまだのお店は出さない（公式サイトへのリンクは INFO として別枠で出せる）。迷ったら出さない（#34） |
| Q-B1 | 橋は片方向 2 本・依頼の状態は Research だけが持つ | **はい** |
| Q-B2 | 0 件の検索語を、利用者を特定しない形で残す | **はい**（保存期間は v1.0 Q9 と一緒に法務の確認へ） |
