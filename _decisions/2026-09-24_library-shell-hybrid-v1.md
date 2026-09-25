# 裁定 — Library Shell ハイブリッド（Atlas 型 Shell ＋ v4 契約）v1

- 日付: 2026-09-24 JST（生成: Cowork `ZoneInfo("Asia/Tokyo")`）
- revision: MYRIG-20260924-118
- 前提正典: MYRIG-20260919-117（`_decisions/2026-09-19_library-zero-base-v1.md` D1〜D7）/ `_proposals/2026-09-19_library-ia_claude-v2.md`
- 裁定者: イタヤ（2026-09-24 口頭。Claude v4 全 8 面と Atlas v1 の 3 面比較を見て。同日 118 commit 前バッチで D7〜D11、役割裁定（GPT 文言）で D12〜D16 を追記）
- ⛔ Production DB 非接触。migration 未実行。物理 DELETE 禁止。

---

## 1. 発端

Library PC は Claude v4（全 8 面。App Shell 内に置いたカタログ）と、イタヤが別 AI「Atlas」に作らせた Atlas v1（1 枚 HTML ＋ `?view=`。
Library 専用 Context Header ＋ 左 Index Rail。serif / mono の独自書体）の 2 案が並んだ。GPT レビューと Claude 所見は一致して
「Shell は Atlas、データ契約・カード・Detail 骨格は v4」のハイブリッド。イタヤ判定:

- Atlas のパターンは **基本的に採用**（資料室モード / 左索引 / 直感的に使いやすい）
- ただし **ヘッダー・書体などは Atlas のままではなく MyRIG のトークンに合わせる**
- **色は引き継がない方がよい。** lib-hd の緑 / 紫のストライプは旧 token（`SoT_tokens-v6` の `--cat-rig:#66b900` 等。現行 v8 は RIG 黄 / PARTS 赤）で、
  Library にあってはならない。Community カードの緑「RIG」チップも Library には不要（Library は全部公式製品。RIG / PARTS / LOG のチップで目立たせる必要がない）
- **Library は新しいカラートークン概念を持ってよい**
- v4 の「上にカテゴリ chip、左に filter sidebar」は少しややこしい。Top も探し物にたどり着くまで面倒そう → Atlas の簡潔さを採る

## 2. 裁定

### D1. Library Shell = Atlas 型を **1 本の共有 Library Shell** として採用
- 構成: **Context Header**（MyRIG ロゴ / LIBRARY ラベル / Community ↗ / マイガレージ ↗ / テーマ切替）＋ **左 Index Rail**（Top / 車種 / パーツ / メーカー ＋ 一覧面ではカテゴリ索引）＋ main ＋ Library footer。
- 既存の App Header（グローバル検索 / 投稿 / 通知 / 共通 Drawer）と `lib-hd`（MyRIG CATALOG ＋ ストライプ）は **Library では使わない**。
- ⛔ 8 面をバラバラにデザインしない。Shell は `SoT_library-shell.css/js` の **1 本**。⛔ 別 Shell を増殖させない。
- ≤1024 は Rail を横タブ（Top / 車種 / パーツ / メーカー）に畳む。カテゴリ索引は filter 行の select へ。
- テーマ切替は app-shell と同じ `html[data-theme]` ＋ `localStorage('myrig-theme')` 契約を共有する（別の保存先を作らない）。

### D2. 書体は MyRIG の型システム
- Barlow Condensed（eyebrow / 番号 / 数字）/ Inter ＋ Noto Sans JP（本文）。Atlas の serif / mono は採らない。

### D3. 色は Library 専用の中立レイヤー（新概念）。カテゴリ色・チップ・ストライプは全廃
- `--lib-*`（paper / ink / muted / faint / wash / line / surface / action）を **MyRIG 基本トークン（`--color-*`）へのエイリアス**として定義し、light / dark 両対応。
- ⛔ `--cat-rig / --cat-parts / --cat-log` を Library で参照しない（117 D2 の再確認）。⛔ lib-hd のストライプを持ち込まない。
- Detail の「MyRIG で使われている RIG」= **Library → Community への Bridge Preview**という独立責務。
  Community Card そのものではなく、Community Card の無彩色 variant でもない。Library 内では中立表示（写真 / RIG 名 / モデル / ユーザー / 日付。バッジなし）、
  遷移先は Community RIG Detail。Community へ移動した後は Community 本来のカード / 色 / UI 語彙へ戻る。
  ※ 当初（v4）は Community の `myrig-rig-card` をそのまま置いていた。v5 の Library Shell を見て**設計探索の結果として判断を更新**した（誰かの前回判断の踏襲ではない）。
- 主ボタンは ink（黒）。`--color-accent`（青）は focus ring と本文リンクだけ。

### D4. 一覧の操作は簡潔に（Atlas 型）
- カテゴリ索引は **左 Rail**（親）＋ 親選択時の子 chip 行。上部の chip Directory と左 filter sidebar（`SoT_filter-sidebar`）は Library では使わない。
- filter は **select 型**（メーカー / 車種は 形態・駆動）＋「生産終了品を除く」チェック。Facet 件数は Q8 のまま（初期状態だけ・条件が乗ったら消す）。
- **RIG catalog に scale filter を置かない。** Research 契約では `scale` はメーカー公称の表示用、filter 軸は `size_class` で、その値集合は HOLD。
  カード / Detail で公称 scale を「表示」することは可。`size_class` HOLD 解除後に filter として追加する（構造は残す。UI には出さない）。
- 表示は **グリッド（3 列）/ リスト** の切替。並びは新着 / 名前のみ。番号ページネーション。
- カードは **枠なし**（画像スロット 4:3 常設 / NO IMAGE / メーカー / 製品名 / Item No. / 3 語 / 使用 RIG N 台）。単一入口。

### D5. 戻り状態保持（Library UX 契約）
- Detail へのリンクは一覧の URL（q / category / sub / maker / 各 facet / sort / page / view）を `from=` で持ち、Detail の「← 一覧に戻る」がそれを復元する。
- `from` は Library 内の URL だけを受け付ける（それ以外は該当カタログの先頭へ）。
- 本番 URL は 117 / 設計図 v2 の個別 route を維持（Atlas の 1 枚 HTML は探索表現。SPA 化しない）。

### D6. データ契約・Detail 骨格は v4 を維持
- Q8 件数 / publication 画像判定（fixture 1 か所）/ H-1 / 「マスター」不使用 / 型番一致は候補表示 / 一覧エンジン `SoT_library-catalog.js`。
- Detail = Visual Stage（左）＋ Identity ＋ **製品データ表（ゼブラ）** ＋ Variant（state）＋ 公式 / 購入先（Library Bridge 契約）＋ CTA（右）→ 使用例 → 関連 → 別名 → 出典。ページ内アンカー行を持つ。

### D7. Library PC は fluid / wide layout（09-24 追記・イタヤ裁定）
- Library Shell 全体は固定コンテンツ幅を持たない。左 Index Rail は固定幅、右 Main は viewport に追随する（Home / Browse / Search と同じく、広げた分が「一度に見える情報量」として返る）。
- Catalog / Search / Top / Maker Detail の製品 grid は固定列にせず、**カードの適正幅を守りながら列数が増減**する（CSS Grid `auto-fill / minmax()` の考え方。カード最小幅は幅帯ごとに段階的に上げ、超ワイドでカードが際限なく増えない・巨大化しないようにする）。
- **Product Detail だけは例外**: Shell は fluid のまま、本文（Visual Stage / Identity / 製品データ / 説明的 section）は読みやすい内部 max-width に止める。Maker Detail も intro / summary / 分類リストは読み幅で制御し、製品 grid は fluid。
- list view も Main 幅を有効活用する。Grid / List 切替の state は URL に保持。
- ⛔ 列数・px・breakpoint の数値は正典化しない（mock 実装を仕様とする）。

### D8. 不正 ID / slug で別 entity を表示しない（09-24 追記）
- RIG / PARTS 製品情報・Maker Detail は、存在しない Item No. / slug のとき **Not Found**（「製品が見つかりません」＋ 一覧へ戻る導線）。
- ⛔ `|| L.MAKERS[0]` のような先頭 entity への fallback を置かない（fixture でも禁止）。

### D9. Library Search は Variant Item No. も対象（09-24 追記）
- 検索対象 = 製品名 / メーカー / base Item No. / alias（`master_aliases` 相当）/ **Variant Item No.**。部分一致と完全一致候補の両方に含める。
- SKU 完全一致でも Detail へ自動転送しない（最上位候補として表示）。Variant 一致は **該当 Master Detail ＋ 該当 Variant state**（`?variant=`）へ到達できる。

### D10. Maker の country / region は Research 正本確認まで Public UI に出さない（09-24 追記）
- `manufacturers.country` 等の正式契約が未確認のため、Maker Index / Maker Detail から国表示・地域 filter・その注記を外す。
- Maker Index は **名前順 / 名前・製品ライン検索 / 頭文字索引** で成立させる。Research で契約が確認できた場合だけ後から戻す。

### D11. 使用例の「すべて見る」は正式 route がある場合だけ（09-24 追記）
- Browse 側に master 単位の filter の正式な受け口は現状無い（2026-09-24 に mock の `pc/*.html` / `assets/js` と Browse 裁定を実体確認: `?base=` / `?master=` の処理なし）。
- → 架空 URL を作らず **PENDING**。Public UI にリンクを出さない（受け口が決まったら fixture の `BROWSE_MASTER_ROUTE` に URL 生成を置く）。各 preview は実在の Community RIG Detail へ遷移する。
- PARTS 側の使用例は H-1 未解消。fixture で N 件状態を見せるが、「本番で既に集計可能」と読める実装・記録にしない。

### D12. Library の役割（09-24 追記・イタヤ裁定 ＋ GPT 文言）
- **MyRIG Library は、MyRIG 内で使用・登録される製品を識別し、Community 実例・Register・公式情報・購入先へ接続する公開製品索引である。独立した製品メディアやショップを目的としないが、製品 Detail における購入導線は主要なマネタイズ導線として明確に扱う。**
- Library は製品を識別・検索するための構造化された事実データを主体とする。メーカー紹介・沿革・レビュー・評価・特集等の Editorial な自由文は原則持たない。製品画像は publication 条件を満たすものだけを表示し、出典・外部参照は製品単位を基本とする。
- 主導線は MyRIG 内部（RIG Detail → 使用パーツ → 製品 Detail → 購入 / Register → 製品検索 → 確認 → 登録）。外部流入は拒否しないが、MyRIG 自身を絶対的な製品情報源として位置づけない。
- 外部流入向けの距離感は注記で示す（Top / 製品 Detail 末尾）: 「メーカー公表情報などをもとに MyRIG で整理した参照データです。最新の仕様・対応状況はメーカー公式情報をご確認ください。」大きな免責は置かない（Detail の信用を落とさない）。
- 濃さの配分: **Top / Maker は薄く、Product Detail だけ濃く**（製品名・型番・Variant / 最低限の仕様 / MyRIG 使用例 / 購入先 / メーカーサイト / ガレージへ登録）。

### D13. Maker Detail は製品索引として薄く維持（09-24 追記）
- 構成 = メーカー名 / 「MyRIG で N 台が使用」/ 車種 N 件 / パーツ N 件 / 車種一覧 / パーツ一覧（分類から）。
- **製品ライン紹介（chips）は持たない**。Maker Index の検索もメーカー名のみ（製品ライン検索を外す）。
- **公式サイトは補助的な外部参照としてのみ残す**: 製品一覧より下に小さなテキストリンク「メーカー公式サイト ↗」。紹介 CTA ボタンにしない。会社紹介・沿革は掲載しない。

### D14. Library Top からメーカーグリッドを撤去（09-24 追記）
- Top = 検索 / 車種を探す / パーツを探す / 新着 / パーツの索引。主入口は **車種 / パーツの 2 扉**。
- メーカーは補助導線: 検索欄近くに小さく「メーカー索引を見る →」＋ 左 Rail 03。巨大な第 3 の扉にしない。メーカーという検索軸そのものは格下げしすぎない（「Axial の製品を見る」は普通にある）。
- 特集・ランキング・セール棚は置かない。

### D15. メーカーロゴは MVP では使用・管理しない（09-24 / 09-25 改訂）
- メーカー名はテキストのみ。ロゴ画像を取得・保持・表示しない（fixture にも `logo` フィールドを持たない）。取得 / 更新 / 表示条件 / ブランドガイドライン / 許諾の管理を発生させない。

**09-25 改訂（イタヤ裁定）**: 将来はロゴを表示する。MVP ではやらない、が線引き。
- **MVP: 表示しない**（現状維持。fixture にも `logo` を持たない）
- **将来**: メーカーへ**画像類の使用許可を取るときに、ロゴの使用許可も同時に取得**する。許諾が取れたメーカーだけ表示する（全社一律ではない）
- 理由（イタヤ）: 検索結果で車種・パーツ・メーカーが混在して並ぶと、ロゴが無いと識別に手間がかかる
- ⛔ **NO IMAGE スロットをロゴで埋めない**（製品写真としてロゴを使うことになり、許諾の範囲を外れる）。画像が無い製品は灰色のまま
- 解禁の条件: ① 書面許諾に「MyRIG のライブラリ内で製品識別のために使用。提携・推奨を示すものではない」を明記 ② メーカー配布の公式アセットのみ・改変しない ③ フラグ 1 つで即時撤去できる構造
- **表示箇所は解禁時に別途裁定**（第一候補 = メーカー詳細の 1 箇所。一覧・検索結果に出す場合は「ロゴあり / なしの混在が序列に見えない」設計を先に決める）

### D16. Product Detail の購入導線は意図的に弱めない（09-24 追記・イタヤ強調）
- Library は RIG Detail / マイガレージから飛んできた人のマネタイズ動線。「Library をすっきりさせる」勢いで購入ブロックを削らない。
- **Official（情報確認）と Buy（購入行動）を混ぜない**: `rail-info`（メーカーサイト）と `rail-buy`（購入先を見る）を分離して置く。購入先は存在するものだけ（Amazon / 楽天 / AliExpress / メーカー直販 / 販売店 等）、アフィリエイト対象は明示。
- ショップ化しないための線引き = 価格を並べない / ランキング・セール棚を作らない（D14・page-role-matrix §7）。目的を持って Detail まで来た人に購入先を分かりやすく出すのは正しい。
- 購入ブロックは Shared Component として育てる（markup 契約は Library Bridge のまま）。

### D17. メーカーの日本語表記と検索（2026-09-25 追記・イタヤ裁定）
「検索」と「索引の並び」を**別問題として扱う**。検索を先に完成させ、並びは後から決める。

**1. 検索**
- 対象 = `manufacturer.name` ＋ **Research 正本の `master_aliases`（`entity_type='manufacturer'`）**。⛔ App 側に manufacturer 専用の alias 配列を新設しない
- 正規化は NFKC / casefold / 空白・記号 / **ひらがな → カタカナ** まで
- ⛔ ローマ字 → カナ変換、発音揺れ（トラクサス / トラクザス 等）を汎用アルゴリズムで推測吸収しない（別語の誤マッチを生む）。**実際の呼ばれ方を alias として明示的に持つ**
- 例: `Tamiya / タミヤ / たみや / ﾀﾐﾔ / 田宮` → 同一 manufacturer へヒット。メーカーの alias で**その製品にも**当たる

**2. 表示**
- **正式メーカー名を主表示**（実製品と同じ表記で識別する索引だから）
- 公式 / Research で**根拠のある日本語表記がある場合だけ**小さく副表示（例: `Tamiya タミヤ`）
- ⛔ 海外メーカーへ推測カナを生成・表示しない（`Vanquish Products` のままでよい）。カナは検索 alias としてだけ持ち、表示しない

**3. identity / relation の境界（イタヤ追加確認）**
- `master_aliases` は **検索展開専用**。製品との関係付けキーには使わない
- 解決の順序: 「タミヤ」→ `master_aliases` から **manufacturer を解決** → **`manufacturer_id` を得る** → 製品は **`manufacturer_id` で取得**
- ⛔ `alias_value` / `name_ja` / 表示文字列どうしの一致を identity や relation の境界に使わない
- fixture もこの責務が分かる形にする（製品は `maker_id` を持ち、表示名フィールドを持たない。表示は `makerName(p)` で解決）
- **mock fixture を Research 確定値として扱わない**（alias_kind / locale / 日本語正式表記の持ち方 / `reading_ja`・`sort_key_ja` は PENDING）

**4. 索引**
- 現時点は **A–Z を維持**し、上部に並びのモード（`A–Z`）を明示する
- 五十音順は App 側で読みを推測生成しない（信頼できる読みキーが前提）

### D18. 製品 Detail の H1 は別の表示契約（2026-09-25 イタヤ裁定・P5 を確定）
- 共通の見出し契約（`.lib-pagehead` ＋ `.lib-h1`）は **Level-1 の 5 面（Top / 車種 / パーツ / メーカー索引 / 検索）＋ メーカー詳細**に適用する
  - 文字サイズ: 幅 721px 以上は **32px**、720px 以下は **26px**（狭幅指定。`SoT_library-shell.css`）
  - 位置: Level-1 の 5 面は **H1 Y=100・操作部 Y=192 で一致**（1440px 基準）。メーカー詳細は**パンくずの下**に H1 を置くため、パンくずぶん下がる（1440px で Y=136。下位面の正しい差）
  - （2026-09-25 20:44 JST 記述を実表示に合わせて明確化。ASTRA 最終確認 SHOULD）
- **製品 Detail の H1 は製品名**（意味上も HTML 上も H1。ページに 1 つ）。ただし表示は**別の契約** = Identity 見出し `.ld5-id__name`・26px・画像の右
- 理由: 長い製品名を 32px にすると行数が増え、ITEM・仕様・**購入先ボタンが押し下げられる**（Detail はマネタイズ面）。「例外」ではなく「別契約」と明文化して、後から誤って 32px に揃えられるのを防ぐ（gate L49）

### D19. 購入 CTA の色は青（2026-09-25 イタヤ裁定）
- Library の「購入先を見る」を **青（`--lib-buy` = `--color-accent-fill` #0969da / 文字 白・5.19:1、light・dark 共通）**にする。「明確にマネタイズ（アフィリエイト）導線」として目立たせる
- Community / Garage の購入ボタン（`SoT_detail-rail.css` の `--dt-action` = #0969DA）と**同じ値** → MyRIG 全体で「購入 = 青」がそろう（Library だけ黒だった）
- D3（中立レイヤー）の**唯一の例外**。⛔ 購入 CTA 以外に `--lib-buy` を使わない（gate L56）。⛔ 価格・ランキング・セール棚は引き続き置かない（D16）
- 横断 PENDING: `--dt-action` と `--lib-buy` を将来 1 つのグローバルトークン（例 `--color-buy`）に寄せる（Front-wide 監査 / Next.js 移行時）

### D20. 見えない土台を先に作る — リフォームしやすい設計・仕組み・命名（2026-09-25 イタヤ方針）
- 前提: Next.js 実装後もページのデザインは**ちょこちょこ更新していく**。見た目を変えても壊れない土台を、見た目より先に固める
- Library で既に効いている形（これを崩さない）
  1. **値はトークンに**: 色・余白・見出しは `--lib-*` と共通契約で定義し、面ごとに数値を持たない
  2. **部品は 1 か所**: Shell / Card / Catalog / Detail / 検索照合（`productMatch`）/ 画像判定（`canShowImage`）/ Variant（`applyVariant`）
  3. **表示・関係・URL を分ける**: name（表示）/ id（関係）/ slug（URL）。alias は検索展開専用。仕様はキー付き
  4. **決めたことは機械で守る**: gate（L01〜L56）＋ selftest（故障注入）
- Next.js へ移すときの注意（命名）: mock の接頭辞 `lt5-` `lc5-` `ld5-` や `-v5` のような**版番号を component 名・class 名・トークン名に持ち込まない**。役割名（例: `LibraryShell` / `ProductCard` / `CatalogFilters` / `ProductDetail` / `BuyPanel`）にする。版番号が名前に入っていると、次のリフォームで全置換が必要になる
- CORE 候補: 他レーンにも同じことが言えるので、CORE（恒久ルール）へ上げるかは GPT / イタヤで判断

### PENDING（118）
- **P1 Library への主導線（RIG Detail / Register からの到達）と、グローバルナビ上の Library の扱い**は、Library レーン CLOSE 後の横断ナビ監査で判断する。Library 作業中に Main Navigation を変えない。
- P2 使用例「すべて見る」（D11）。
- **P4 日本語 locale の「A–Z / 五十音」切替（D17）**: 表示名は変えず、グループ見出しだけ切り替える形。実装には manufacturer の信頼できる読みキー（`reading_ja` / `sort_key_ja` 相当）が必要 → **Research へ持ち方を照会**。App 側で推測生成しない。
- ~~P5 H1 契約の適用範囲~~ → **D18 で確定（2026-09-25）**
- **P3 メーカーロゴの表示（D15 改訂）**: 許諾取得後に解禁。表示箇所・一覧での扱い・データ項目（許諾状態 / 公式アセット / 失効日 / 使用条件）は解禁時に裁定。Research レーンの画像許諾フローに「ロゴ許諾も同時取得」を載せる必要がある。

## 3. 状態
- Claude v4（8 面）と Atlas v1 は比較案として mock に残していたが、**2026-09-24 イタヤ指示で `_archive/20260924_library-v4-atlas-close/` へ退避**（mv のみ・削除しない。explore card v0・v1 も同梱）。v3 7 面は Launcher / `SoT_footer.js` / 35 面から参照されるため、リンク切替の横断作業まで `pc/` に残す。
- ハイブリッド v5 を 8 面で作り、イタヤが通しで評価する。採用後に Launcher / compare ペア更新 → Mobile。

## 4. やらないこと
- App Header の Library 内復活 / lib-hd ストライプ / カテゴリ色 / serif 書体 / SPA 化 / ランキング棚 / 価格表示。
