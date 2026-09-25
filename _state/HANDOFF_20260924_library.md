# Library レーン 現状レポート — 2026-09-24

- 作成: 2026-09-24 08:50 JST（生成: Cowork `ZoneInfo("Asia/Tokyo")`）
- 実測時点の正典: **GitHub main = MYRIG-20260919-116 / ローカル = 117（未 commit）**。mock も未 commit（untracked 5 本）。9/19 以降、両 repo に変更なし
- 本書は正典ではない。「いまどこにいるか」の正本は `_AI/MyRIG_CURRENT.md` NOW 節（117）
- 読者: 前半 = イタヤ（読み物）/ 後半 = GPT（技術詳細・レビュー依頼）

---

# 前半：イタヤ向け（読んで分かる版）

## 1. いま何をしているか（一言で）

MyRIG の「ライブラリ（カタログ）」7 ページを、PC から順に**作り直しています**。
9/19 に「作り直す方針」と「ページ全体の設計図」を決め、その日のうちに **製品カード → パーツカタログ一覧** まで新しい版を作りました。
まだ何も commit していません。GitHub 上の正典は 116 のままで、117 はあなたの Mac の中にだけあります。

## 2. なぜ作り直すのか（決めたこと）

きっかけは 3 つでした。

1. **メーカー画像は許可がないと使えない可能性が高い。** 今の 14 ページは「画像がある前提」で組まれていた
2. **ライブラリは Community（人が登録した RIG・パーツ・LOG）とは別の世界。** 同じ色分けやカードの作法を持ち込むと、かえって区別がつかなくなる
3. **ライブラリは「公式サイトや購入先へ送る」役割が強い。** カードで売り込むより、製品を見つけて理解してもらう面にした方が効く

そこで 117 として次を決めました（GPT のレビューとあなたの判定を経た版です）。

- **既存 14 ページは「情報構造の資料」として扱い、見た目は縛られない。** ただし「ゼロベース＝必ず新しくする」ではない。既存の製品情報ページの骨格（左に大きなビジュアル、右に製品情報）は残す候補
- **Community とは別のデザイン。** RIG 黄・PARTS 赤の色分けを使わない。「公式情報」のようなバッジも付けない。メーカー・型番・スペック・出典という**情報の並べ方**で「これは製品情報だ」と分からせる
- **画像ありきのレイアウト。ただし画像依存にはしない。** 画像の枠は常にあり、使える画像が無ければ `NO IMAGE`。枠があっても無くてもページの形は変わらない。「IMAGE PENDING（確認中）」のような運用状態は表に出さない
- **パーツ側から作る。** カード → パーツカタログ → パーツ製品情報 の順に新規ファイルで作り、実機で見て採否を決める。既存ファイルには手を付けない
- **「この製品を使っている RIG が N 台」はライブラリ最大の強み。** ただし PARTS 側は本番でまだ数えられない（HOLD H-1）。mock では 0 件と N 件の両方を作る
- **件数は出してよい。順位付けには使わない。** 「41 台の RIG で使用」「車種 248 / パーツ 1,842」は OK。「使用数順」「人気パーツ棚」「1 位」は NG（ランキング全廃方針）
- **画面の文言に「マスター」を使わない**（禁止語）。「製品情報 / 車種 / パーツ / メーカー」で通す

## 3. ここまでにできたもの

すべて `~/Desktop/MyRIG/App/MOKUP/myrig_pc_Ver3/pc/` の新規ファイルです。Launcher（index.html）には載せていないので、**ファイルを直接開いて**見てください。

| もの | ファイル | 状態 |
|---|---|---|
| 製品カード v0 | `myrig-library-explore-parts-card-v0.html` | 画像枠なし。**不採用方向**（データ表に寄りすぎ、と判定） |
| 製品カード v1 | `myrig-library-explore-parts-card-v1.html` | **収束候補**。画像枠常設・NO IMAGE・RIG サムネ撤去・画像は切らずに収める（contain） |
| カードの共通 CSS | `assets/css/SoT_library-card.css` | v1 とカタログ一覧が同じものを読む |
| **パーツカタログ v4** | **`myrig-library-parts-v4.html`** | **あなたの実機判定待ち**。検索 ＋ 14 カテゴリ ＋ フィルター ＋ 全件一覧を同時に置いた面 |
| 記録 | `_state/LIBRARY_EXPLORATION_20260919.md` | 探索 1〜3 の経緯・判定・未決 |

正典（`~/Desktop/MyRIG/myrig-ai-context/`、未 commit）:

| もの | ファイル |
|---|---|
| 117 の裁定原本（D1〜D7） | `_decisions/2026-09-19_library-zero-base-v1.md` |
| ライブラリ全体の設計図 v2（PROPOSAL） | `_proposals/2026-09-19_library-ia_claude-v2.md`（v1 は superseded） |
| NOW 117 | `_AI/MyRIG_CURRENT.md` 冒頭 / `revision.txt` |

### パーツカタログ v4 はこういう面です

上から順に、共通ヘッダー → ライブラリのサブヘッダー（Top / 車種 / パーツ / メーカー）→ 見出し「パーツカタログ」と収録件数と検索窓 → 「カテゴリから探す」14 個のチップ（選ぶと子カテゴリのチップが出る）→ 左にフィルター（メーカー / 生産終了品も表示）、右に件数・並び替え（新着順 / 名前順）・カードのグリッド・ページ番号 → 共通フッター。

- 何も選ばなければ全件が新着順で出ます。カテゴリは「入口の関所」ではなく「近道」です（製品名や型番を知っている人はカテゴリを触らずに検索できる）
- カテゴリを選ぶと URL に `?category=…&sub=…` が付き、タブのタイトルも変わります。将来の `/library/parts/category/…` はこの「同じ面の状態違い」です
- 規格（径・ハブ・KV など）での絞り込みはまだ置いていません。製品仕様データが整うまで出さない、と面の中にも一文書いてあります
- 1280px で 4 列、少し狭いと 3 列、1024px 以下ではフィルターが「フィルター」ボタン → 引き出しに変わります（他の面と同じ共有部品）
- 横スクロールが出ないことは 1440〜375px で確認済みです

## 4. まだ決まっていないこと（あなたの判定・確認が要るもの）

**実機で見て判定してほしいこと（v4）**
1. カテゴリのチップが 2 行に折り返す。このままでよいか、1 行に収めるか
2. 見出し横の検索窓の位置。将来「ライブラリ検索」をサブヘッダーに置くなら、ここと重複する
3. 4 列の密度（カードの高さ約 330px）。3 列にするか
4. 使用例 0 件の文言「使用例はまだありません」

**データ側で確認が要ること（Research 照会）**
- 14 の親カテゴリと子カテゴリの正本（今の mock は仮の名前）
- 公開判定の行が無い製品を、そもそも一覧に出してよいか
- 「発売中 / 生産終了」の元になる列と語彙
- メーカーに国の列があるか（メーカー一覧の地域フィルターに使う）

## 5. これからの順番

1. **パーツカタログ v4 の実機判定**（今ここ）
2. **パーツ製品情報 v1** — 既存ページの骨格（左ビジュアル・右製品情報）を残しつつ、NO IMAGE 化、購入ブロックを Detail 共通部品に揃える、「使っている RIG」の節を 0 件 / N 件で
3. ライブラリ検索（すべて / 車種 / パーツ / メーカー）
4. 車種カタログ・車種製品情報（パーツ版を写す）
5. カタログトップ（検索と分類が最初に見える「製品 DB の操作盤」。大きな写真や特集は置かない）
6. メーカー一覧（名前順）・メーカー情報（製品の索引。会社紹介は書かない）
7. 既存 14 ページの片付け（古い Mobile CSS の重複、リンク切れ 15 件など）
8. Mobile 版
9. プラットフォーム（TRX-4 / SCX10 III 単位のページ）— 将来。パーツの互換データが整うまで作らない

## 6. 守っていること

- commit / push はあなたの `mockup` で。私は勝手にやらない
- Production DB 非接触。既存の v3 ページは無変更。Register / Detail / Launcher は掘り返さない
- 探索中のページは Launcher に載せない（採用が決まったら通常導線へ）
- カードは Detail への入口 1 つだけ。購入ボタンはカードに置かない（購入は製品情報ページの責務）

## 7. 今すぐやってほしいこと

1. `pc/myrig-library-parts-v4.html` を実機で開く（Vercel に上げるには `mockup` の実行が要ります。**`mockup` を実行すると canon 側の 117 も一緒に GitHub へ push されます**。117 の内容で問題なければそのまま実行してください。117 を見直したいなら、先に言ってください）
2. §4 の判定 4 点を返す
3. OK なら私はパーツ製品情報 v1 に進みます

---

# 後半：GPT 向け（技術詳細・レビュー依頼）

## A. 正典状態

- GitHub main: `MYRIG-20260919-116`（088dde8）。ローカル: `MYRIG-20260919-117`（`revision.txt` / CURRENT 冒頭とも。未 commit）
- 未 commit（canon）: `_AI/MyRIG_CURRENT.md`（NOW 117 ブロック追加）/ `revision.txt` / `_decisions/2026-09-19_library-zero-base-v1.md` / `_proposals/2026-09-19_library-ia_claude-v1.md`（superseded）/ `_proposals/2026-09-19_library-ia_claude-v2.md`
- 未 commit（mock）: `pc/myrig-library-explore-parts-card-v0.html` / `-v1.html` / `pc/myrig-library-parts-v4.html` / `pc/assets/css/SoT_library-card.css` / `_state/LIBRARY_EXPLORATION_20260919.md`
- 117 の裁定（D1〜D7）:
  - D1 ゼロベース再設計（既存 14 面は情報構造・データ項目・導線の参照資料。ゼロベース ≠ 必ず新しくする）
  - D2 Community と別デザイン体系（`--cat-*` 不使用・強いバッジ無し・情報構造で示す）
  - D3 画像ありき・画像依存にしない（スロット常設 / NO IMAGE / Detail 最大 2 枚 / `IMAGE PENDING` 廃止 / 運用状態を Public UI に出さない）。**publication は画像スロットの中身に限定**して記述（B-4 の公開面 gate は別判定。publication 行なし master は Research 照会）
  - D4 PARTS から探索（Card → Detail → Catalog の順。実際は Card → Catalog を先に実施し Detail は次）
  - D5 MyRIG 使用例は H-1 依存（mock は 0 / N 両状態）
  - D6 件数は metadata として可、Rank には使わない（matrix §7）＋ UI 文言「マスター」不使用（mobile contract §4）
  - D7 IA = Find / Identify / Bridge。固定 3 点: カテゴリ着地 = preset state / カード = Detail 単一入口 / Platform = 将来枠

## B. mock 実装の詳細

**`SoT_library-card.css`（共有）**: `.mcard` 縦型。`.mcard__visual` aspect 4:3、`img{object-fit:contain;padding:6%}`、`.mcard__visual--none` ＋ `.mcard__noimg`（Barlow Condensed・文字のみ）、`.mcard__eol`（生産終了のみ）、`.mcard__name` 2 行 clamp 固定高、`.mcard__spec` 1 行 3 語、`.mcard__use`（`<b>N</b><small>台の RIG で使用</small>` ＋ `→`。`data-count="0"` は淡字・リンク無し）、`.mcard__link` 全面 overlay。カテゴリ色・バッジ・購入 CTA・RIG サムネ無し。

**`myrig-library-parts-v4.html`（⑤）**
- 依存: `SoT_tokens-v6` / `SoT_app-shell`（css+js）/ `SoT_library-header` / `SoT_filter-sidebar`（css+js。Drawer 変身は共有契約 1024 境界）/ `SoT_library-card` / `SoT_footer`（css+js）。global header と `lib-hd` の markup は v3 から流用（共有 shell）。page-local CSS は catalog 固有（head / directory chips / layout / toolbar / grid / pager）のみ
- 構造: `.lc-head`（h1 ＋ 収録件数 ＋ `role=search`）→ `nav.lc-dir`（親 14 chip `aria-pressed` ＋ 子 chip 行）→ `.lc-layout`（`aside.myrig-filter-sidebar[data-fs-drawer]` ＋ `main`。`[data-fs-toolbar-anchor]` を `.lc-toolbar` に付与し共有 js の Toolbar 挿入点にしている）→ `<site-footer>`
- 状態: `S={q,cat,sub,makers:Set,eol:true,sort:'new',page,per:24}`。URL query（`category / sub / q / maker`）を初期化・`history.replaceState`・`document.title` に反映（`/library/parts/category/[parent]/[sub]` の mock 表現）
- 件数の母集団: Directory / メーカーの件数は「検索語 ＋ 発売状態」を掛けた母集団で数える（カテゴリ・メーカー自身の選択は掛けない）。toolbar 件数は全条件適用後
- 並び: `new`（added desc）/ `name`（maker+name localeCompare ja）。⛔ use 順なし。spec facet なし（B-6。sidebar 末尾に文言）
- 画像判定: `canShowImage(p)` = `p.img && pub.display_status==='approved_image' && pub.effective_display_mode==='image_enabled'`（109 §9 の模倣。ここ以外で判定しない）
- fixture: 36 件 / 親 14（**仮**。Category v1.4 正本と突き合わせ要）/ 画像あり 6（`img/` の製品写真のみ）/ 生産終了 3 / use 0 が 7。Detail リンクは暫定で `myrig-library-parts-master-detail-v3.html?sku=`
- 実測: overflow-x 0 @1440/1280/1100/1024/900/768/375。列 ≥1280 4 / 1025–1279 3 / ≤1024 3 → ≤820 2。カード高 均一（1280: 331px）。console error 0（ローカル file 起動時の `ERR_FILE_NOT_FOUND` 2 件は shell の外部参照で v3 と同条件）
- 直した罠: (1) `*{box-sizing:border-box}` の全体 reset を page-local に持つ前提（app-header が 36px overflow）(2) sidebar 列幅は `var(--home-sidebar-width,260px)`（共有 css が幅を自身で持つ）(3) Drawer 帯で page-local `position` を上書きすると sidebar が in-flow 化して main が消える → rail 帯（≥1025）だけ sticky (4) 共有 `.fs-more-btn` の `display` が `[hidden]` に勝つ → `style.display` で制御

## C. 設計図 v2 の要点（`_proposals/2026-09-19_library-ia_claude-v2.md`）

Sitemap: `/library`（Top = 製品 DB のコントロールパネル。Hero / Editorial 無し）/ `/library/search?q=&type=`（すべて / 車種 / パーツ / メーカー。SKU 一致は最上位候補表示・自動転送なし）/ `/library/rigs`（＋ `/category/[category]` preset）/ `/library/rigs/[masterSlug]` / `/library/parts`（＋ `/category/[parent]/[sub]` preset）/ `/library/parts/[masterSlug]` / `/library/makers`（名前順 Default）/ `/library/makers/[makerSlug]`（製品索引）/ FUTURE `/library/platforms(/[slug])`。
Variant = Detail 内 state（別 URL 無し）。`page_blocks.library_top` は今は schema 化しない。Detail の購入は `md-commerce` 共有 ＋ Library variant。Master Detail 骨格 = 既存 Visual Stage ＋ 製品情報を維持候補。
matrix からの変更提案: 一覧 2-CTA → 単一入口 / Maker Detail は Later のまま薄く索引化 / `IMAGE PENDING` → `NO IMAGE`。

## D. 未決・依存

| 項目 | 状態 |
|---|---|
| H-1（`parts_master_id`） | ⑥ 使用例 / Community PARTS → ⑥ 扉 / ⑨ パーツ節 の本番前提。mock は両状態 |
| `compatible_platforms` XREF | ⑥ 対応節・⑤ 対応 filter・⑨ を出さない |
| `part_categories` 0 行 / Category v1.4 の 14・90 | mock は仮 |
| publication 行なし master の一覧掲載可否 | Research 照会 |
| 発売状態の列・語彙 | Research 照会 |
| `manufacturers.country` | 要確認（`is_active` は使わない） |
| Browse の master フィルタ（④⑥「すべて見る」） | Browse 裁定 037/039 と要整合 |
| 新 gate `library_check.py` | 方向決定後に新設（現状 Library 専用 gate 無し） |
| Launcher / compare.html への登録 | 採用後 |

## E. GPT へのレビュー依頼

1. ⑤ の「件数の母集団」の取り方（Directory / メーカー件数は検索語＋発売状態のみ掛ける）が Search / Browse 側の facet 件数の考え方（db-schema-answers Q8）と矛盾しないか
2. `.lc-head` の検索と設計図 ② の lib-hd 検索の重複。⑤ の検索を lib-hd に上げるべきか、⑤ に残して ② はヘッダー検索の結果面にするか
3. Detail リンクを暫定で既存 v3 に向けていること（⑥ v1 まで）が「既存 14 面を触らない」と衝突しないか（読み専リンクなので衝突しないと判断している）
4. 117 を commit する前に、D3 の publication 記述と D6 の文言に不足が無いか


## F. 追記（2026-09-24 17:44 JST）— 役割裁定 D12〜D16 を 118 に統合

- Library = MyRIG 内部導線から到達する公開製品索引。**Top / Maker は薄く、Product Detail だけ濃く。購入導線は主要なマネタイズ導線として弱めない**（Official ≠ Buy、アフィリエイト明示、価格・ランキング・セール棚なし）。
- 実装反映: Maker Detail の製品ライン撤去・公式サイトは最下部の参照リンクへ / Maker Index の製品ライン列・検索撤去 / Top のメーカー扉・グリッド撤去（2 扉 ＋ 「メーカー索引を見る →」）/ ロゴ不使用 / Top・Detail 末尾の参照データ注記。
- gate L21〜L23 追加。v5 FAIL 0 / selftest 23 / v4 FAIL 0。render 4 面 × 6 幅 issue 0。
- PENDING P1: 主導線・グローバルナビ上の Library の扱い → レーン CLOSE 後の横断ナビ監査（Library 作業中に Main Navigation を変えない）。
- まだ commit / push / mockup は未実行（イタヤ実機確認待ち）。

- （17:56）非採用案を `_archive/20260924_library-v4-atlas-close/` へ退避（v4 8 面 ＋ 資産 / explore v0・v1 / Atlas v1 一式）。`pc/` の Library は v5 8 面 ＋ v3 7 面（現行・参照あり）。gate から `--v4` を撤去。

## G. Research レーンへの申し送り（2026-09-25 13:21 JST・118 D15 改訂）
- メーカーへ**画像類の使用許可を依頼するとき、ロゴの使用許可も同じ依頼に含める**（イタヤ裁定）。取れた社だけ後から表示する運用
- 許諾文面に「MyRIG のライブラリ内で製品識別のために使用。提携・推奨を示すものではない」を明記
- 必要になるデータ項目（正式名は Research で確定）: ロゴ許諾の状態 / 公式アセットの所在 / 失効日 / 使用条件メモ
- App 側は MVP では表示しない。解禁時に表示箇所を裁定（PENDING P3）

## H. Research レーンへの申し送り（2026-09-25 13:42 JST・117 D3 改訂）
- **製品画像は master あたり 1 枚**（代表画像）で確定。2 枚目以降は取得・許諾の対象外
- 画像許諾の依頼は「代表画像 1 枚ぶん」で足りる（G のロゴ許諾と同じ依頼にまとめられる）
- App 側は `display_status='approved_image'` かつ `effective_display_mode='image_enabled'` の 1 枚だけを表示。満たさなければ NO IMAGE

## I. Research レーンへの照会（2026-09-25 13:59 JST・118 D17）
1. **`master_aliases` の manufacturer 運用**: `entity_type='manufacturer'` の alias を、日本語の呼ばれ方（`タミヤ` / `田宮` / `トラクサス` 等）の登録先として使ってよいか。`alias_kind` / `locale` の値set
2. **日本語の正式表記**（副表示に使う 1 つ）の持ち方: `manufacturers.name_ja` 相当の列か、`master_aliases` の locale 指定で表すか
3. **読みキー**（`reading_ja` / `sort_key_ja` 相当）: 五十音索引（PENDING P4）に必要。App 側で推測生成しないため、正本での持ち方を確定させたい
- App 側の前提: 推測カナを作らない / 表示は根拠のあるものだけ / 検索の正規化は NFKC・casefold・記号・ひらがな→カタカナまで

## ~~J. App 側の実装前提~~ ⚠️ **失効（2026-09-25 20:14）— K の 6 を正とする**
> 下記のうち「URL の `?maker=` も id」は ASTRA 監査対応で変更済み。現在は **URL = slug / 関係 = manufacturer_id**（fixture でも id・slug・name は別値）。関係付けの原則（master_aliases は検索展開専用・表示文字列で relation を張らない）は有効。

### （旧）J. App 側の実装前提（2026-09-25 14:08 JST・D17 追加確認）
- `master_aliases` は**検索展開専用**。製品 ↔ メーカーの関係は `manufacturer_id` で引く（表示文字列・alias・name_ja を relation のキーにしない）
- 一覧 / 件数 / 絞り込み / 関連製品はすべて id。URL の `?maker=` も id（表示名を URL に入れない）
- mock fixture の manufacturer alias・日本語副表示は**仮**。Research 確定値として引き写さないこと

## K. ASTRA 監査対応で出た Research / 本番への申し送り（2026-09-25 17:23 JST）
1. **Variant で変わる値の持ち方**: master の名前・仕様は family 共通の値だけにし、Variant で変わる値（コンパウンド・色・形態 等）は Variant 側に持つ前提で App を組んだ。正本の master 名に Variant 固有の語が入っている場合（例: `Hyrax 1.9" G8 …` のような名前）の扱いを照会
2. **compatible_platforms**: 表示は根拠（source）つきの値だけ。仕様の「対応」も同じ扱いにした（fixture の fits は仮・非表示）。source の持ち方（公式ページ URL / 確認日 等）を照会
3. **購入先 URL**: 購入先は affiliate_links の url から出す。URL の無い購入先はリンクにしない（mock は「未接続」表示）
4. **メーカーの「MyRIG で N 台」**: mock は製品別使用数の単純合計（重複 RIG を数える）。本番は **DISTINCT RIG 数**で出す必要がある。PARTS 側は H-1 未解消
5. **publication の受領境界**: mock が保証するのは画像条件の判定だけ。`master_publication_effective` による掲載可否の選別は本番接続時に受領境界で検査する（App 側で契約を新設しない）
6. **manufacturer の id / slug**: App は URL = slug、関係 = manufacturer_id で組んだ（fixture でも別値）。本番の slug の有無・一意性を照会

## L. ASTRA 再監査への対応（2026-09-25 20:14 JST）
- Variant 型番で検索したとき、通常カード（横断検索・カタログのグリッド / リスト）も特定した Variant へ着地するように修正（入口で着地先が変わらない）。曖昧な語（例: `PRO10128`）は代表のまま
- 本番でも「検索語から Variant を特定できたら、一覧カードの遷移先に Variant を渡す」契約が必要（Search API が一致した Variant を返す形）
