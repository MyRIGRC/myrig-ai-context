# 正典 ⇄ モックアップ 全面照合 — 2026-08-21

**対象:** 正典 `docs/` 12本 ⇄ モック `App/MOKUP/myrig_pc_Ver3/`（PC 40ページ / モバイル 43ページ / CSS・JS）
**除外:** `_archive/` `_backup/` `_compare/`
**方法:** design / ui / schema / search+support の4カテゴリを並列で全件照合

## この照合をやった理由

これまでの監査（`docs-audit-20260821.md` / `gpt-review-20260821.md`）は**文書同士の突き合わせだけ**で、
実装を一度も見ていなかった。イタヤ指摘:

> モックで進めながら正典は更新されていくので、モックを見た上で正典と何が違うかを見てもらわないと
> 何とも判定出来ない。正典にこう書いてある、って言うのは、それだけ見ても何とも言えない。

**モック実装が実態、正典が追随する側**という前提で全面照合した。

---

# 最重要の発見

## ① log_type「5値論争」は誤診だった ★

文書監査では「schema(4値) ⇔ UI(5値) の矛盾。要裁定」と結論していたが、**実装を見ると別の話だった。**

| 場所 | 実態 |
|---|---|
| `pc/myrig-log-composer-modal-v0.3.9.html:1043` | `data-log-type="setup"` ラベル「セッティング」 |
| `pc/myrig-feed-v3.html:725` | 「log_type = maintenance/run/custom/memo。**以前ここにあった「セットアップ」は廃止された値**（スキーマ v1.2 で setup/other → custom/memo に変更済み）」 |

**5値目の実装値は `setting` ではなく `setup`。** `setup` は schema v1.2 で明示的に廃止済みで、
**同じモック内の別ページが「廃止値」と注記している。**

さらに実装内部でも分裂している:

| 面 | 値数 |
|---|---|
| PC LOG登録 composer `:1040-1044` | **5値**（5つ目 `setup`） |
| モバイルLOG登録 `register-log.html:381` | **5値**（値属性なし・ラベルのみ「セッティング」） |
| モバイルLOG絞り込み `garage-logs.html:273,277` | **4値** |
| PC検索 `pc/myrig-search-v3.html:947-953` | **4値** |
| PCフィード `pc/myrig-feed-v3.html:720-725` | **4値** |
| PCガレージLOG `pc/myrig-garage-logs-v6.html:517-520,568` | **4値** |

**結論: 正典（4値）が正しく、登録フォームに廃止済み `setup` が撤去されずに残っている。**
Search Contract §5-4「2026-07-28裁定で5値」は、この残骸を新しい5値目と誤読した可能性が高い。
`setting` という値は**モックにもDBにも存在しない**。文書上にしかない語。

→ **裁定案件ではなくモックのバグ。** ただし5値化する裁定を出す場合も `setup` は使用不可。

## ② 操作色 `--color-action-primary` は実装済みだった ★

正典は「実値の確定記録が見つからない・要確認」としていたが、**実装に存在する。**

```
css/mobile-shell.css:32  --color-action-primary: #1F2328;  /* 純黒より一段柔らかい黒 */
css/mobile-shell.css:47  --color-action-primary: #E6EDF3;  /* dark */
```

design-nogo-list NG-7 の記載値と**完全一致**。「緑のまま残っている可能性」は**否定された**。
Bottom Nav 中央「＋登録」も `mobile-shell.css:792-806` で**中立黒**＝B19但し書きのブランド色は**不採用**。

→ **正典を直す（要確認注記を外して確定へ）。**

## ③ v8カラーの適用範囲が正典の想定と全く違う ★

正典は「v7を廃止しv8へ版上げする」と全面適用を含意しているが、実態は**PC 40ファイル中 6ファイルのみ**。

| 状態 | 件数 | 代表例 |
|---|---|---|
| **v8完全適用**（`--cat-*-on` 完備） | **6** | home / feed / search / browse-category / browse-parts / preview |
| **v7旧値をページ内にローカル定義** | **14** | library系6枚、about, auth, error-states, notifications, settings, support-us, welcome-tour |
| v7共有CSS `SoT_tokens-v6.css:28,54` 継承 | 3 | log-composer, register-rig, register-parts |
| **`--cat-*` 未定義のまま参照** | **17** | garage系8枚、detail系3枚、public-garage系4枚 ほか |

**モバイルは `css/mobile-shell.css:1-18` で v8完全適用。**

未定義17ファイルは特に厄介で、`SoT_component-catalog-v6.css` が `--cat-*` を定義していないため
**ページCSSは無効化される一方、Web Component（`SoT_card-components.js`）は v8 をフォールバック内蔵**。
→ **同一ページ内でトークンの効き方が二重化**している。

→ **モックを直す。**共有 `SoT_tokens-v6.css:28,54` を直せば5ページが一括解決。

## ④ PC Feed が #28 未適用 ★

| 項目 | 正典 | PC実装 | モバイル実装 |
|---|---|---|---|
| タブ | おすすめ / フォロー中 | **All / Following / Trending**（`pc/myrig-feed-v3.html:700-702`） | おすすめ / フォロー中（`feed.html:77-78`）✅ |
| 画像 | グリッド＋Lightbox | **Lightbox 参照0件** | 完全実装（`:194-213, :680-766`）✅ |
| 追加ロード | 無限スクロール | **未実装** | IntersectionObserver（`:562-565`）✅ |

**モバイルが完全な参照実装として存在する**ので移植可能。
なお `pc-mobile-spec-inheritance-v1.1:127` は PC を All/Following/Trending と正しく記述しており、
**page-role-matrix §6 だけが全プラットフォーム断定形**になっている。

## ⑤ search-page-plan-v2 が実装から1世代遅れている ★

正典は 2026-07-23 時点。実装は **2026-08-21 P22-C29「D案」**へ移行済み（`search.html:203` 改訂履歴）。

| 項目 | 正典 | 実装（P22-C29 D案） |
|---|---|---|
| /searchトップ構成 | 種別で探す→人・ガレージ独立行→車種→パーツ→メーカー の一列 | **探し方4枚 ＋ 軸の開閉 ＋ ロゴ**（`search.html:244-266, :302-425`） |
| 「人・ガレージを探す」 | 独立行（P14追加） | **独立行は廃止**。探し方4枚の1枚へ移設（`:259` に移行コメント） |
| 型番で探す / 自分のRIGから探す | **記載なし** | 実装済み（`:251, :255`） |
| スケール軸 | **記載なし** | 実装済み（`:404-425`） |
| ソート | 関連度・新着・人気の3ピル | **廃止**（2026-08-21 イタヤ裁定⑦⑧）。「すべて」はソート行非表示、種別別ドロップダウン、**「人気順」不使用** |
| 広告枠 | 記載なし | **検索面は広告ゼロ**（裁定①） |

→ **正典を全面改稿。**

---

# カテゴリ別 差分一覧

## design

### 正典を直すべき
| # | 項目 | 内容 |
|---|---|---|
| D-1 | `--color-action-primary` 実値 | `mobile-shell.css:32,47` に `#1F2328`/`#E6EDF3` が**実装済み**。「要確認」注記を外して確定へ |
| D-2 | 通知色 | **PCは既にPARTS色と分離実装済み**。PC通知ページ `#cf222e`/`#f85149`、PCヘッダー `#dc2626`、モバイル `#D92D20`。正典「赤に集約 `#D92D20`」はモバイル片面の記述。実装は既に「別値」側に倒れている |
| D-3 | v8適用範囲 | 「v7を廃止しv8へ版上げする」だけでは、PC 40中6ファイルしか適用されていない現況が読めない。**段階適用中**と明記 |
| D-4 | NG-1・NG-6 の「処理完了」表現 | NG-1は「モバイル側は処理完了」と正しく限定しているが、**PC `library-v3` の `.door` が名指しの該当例のまま現存**。NG-6「P22-C12で是正」は実際は home のみ |
| D-5 | PC側にトークン層が無い事実 | PCには `--color-action-primary` も `--brand` も**皆無**。中立色は `#24292f`(6箇所)/`#1f2328`(3箇所)のハードコードで**light が2値に分裂**。正典は一切記述していない |
| D-6 | `--cat-*` 定義元の二重化 | `SoT_component-catalog-v6.css` は未定義／`SoT_card-components.js` に v8 フォールバック内蔵。「検査の盲点」節に追記価値 |

### モックを直すべき
| # | 優先 | 項目 |
|---|---|---|
| D-7 | **高** | **PC 34ファイルの v7→v8 移行**。共有 `SoT_tokens-v6.css:28,54` を直せば5ページ一括解決 |
| D-8 | **高** | **`--cat-*-on` 敷設と `color:#fff` 一掃**。`garage-favorites:246-248`、`garage-pins:246-248`、`SoT_component-catalog-v6.css:68`。黄地に白文字 CR1.08 |
| D-9 | **高** | **NG-7 アバター違反**: `pc/myrig-notifications-pc-v0.1.1.html:476-482` に紫`#7c3aed`・緑`#15803d`・橙を含む**7色グラデーションアバター**。「無彩色3階調」「緑紫橙 全廃」に正面衝突。モバイルは準拠 |
| D-10 | 高 | **NG-1 生存**: `pc/myrig-library-v3.html:233-241` `.door--rig{border-top:4px solid var(--cat-rig)}` ＋ `border-radius:xl`。**正典が名指しした当のコードが未撤去**。他に `rig-detail-v6:20`、`garage-rig-detail-v6:55`、`garage-parts-detail-v6:55`、`SoT_component-catalog-v6.css:246` |
| D-11 | 中 | **NG-7 操作色**: `home-v3.html:49-51` / `preview.html:49-51` の登録CTA背景が `var(--cat-*)` のまま（文字色だけ `-on` 化済み） |
| D-12 | 中 | **NG-2**: カテゴリ色を文字色・線色に使用が **PC 46箇所・モバイル 14箇所**。v8適用済みページ内の残存（`browse-category:39,59,69`）が最優先 |
| D-13 | 中 | **NG-6**: `SoT_browse-sidebar.css:382` が二次色のまま。browse 2枚は現在地のみ是正、search は宣言なし |
| D-14 | 低 | PC中立操作色 `#24292f` → `#1F2328` へ統一 |
| D-15 | 低 | 死んだ宣言の掃除（`home:1249-1250,2235`、`SoT_browse-sidebar.css:304,308`、`feed-v3:224-227` の v7フォールバック）。NG-1 定期grepが毎回誤検出する |

## ui

### 正典を直すべき
| # | 項目 | 内容 |
|---|---|---|
| U-1 | **page-role-matrix §3「未作成」が誤り** | Parts Master Detail・Maker Detail は **PC/モバイル両方に実装済み** |
| U-2 | page-role-matrix のページ表が実装を捕捉しきれていない | `about` `support-us` `error-states` `welcome-tour` は inheritance 側だけが捕捉。**`compare.html` `help.html` `legal.html` はどちらの正典にも記載なし** |
| U-3 | `/rigs` の三者不整合 | contract §3.9・inheritance 補助行A が参照する `/rigs` が matrix の URL 表に無い。実装側の参照は**0件**（URL改訂候補#8 未発効なので正しい） |
| U-4 | auth-guard §3.1/§3.3 本文統合が未実施 | 実装と contract v0.5 だけが #14 裁定に追随。**ステータス「確定 v1」が実態と乖離** |
| U-5 | inheritance L133 の参照先 `matrix §0.5` が存在しない | `§6 / §7` へ修正 |
| U-6 | matrix §6 Feed が全プラットフォーム断定形 | PC正本が #28 未適用である事実を但し書きすべき |
| U-7 | contract §4 禁止語のスコープ | コード内コメントまで含むのか不明瞭 |

### モックを直すべき
| # | 優先 | 項目 |
|---|---|---|
| U-8 | **最高** | **PC Feed の #28 未適用**（上記④）。タブ3本・Lightbox無し・無限スクロール無し |
| U-9 | **高** | **ランキング表現の残存**。`pc/myrig-home-v3.html:3209` `data-query-preset="weekly-like-ranking-rig"`、`:3213`「いいね数ランキング」、`:3283`「登録RIG数ランキング」、`:3441`「使用人数ランキング」／`browse-category-v3:810,814,882,953`／`preview.html:2963-3195`。モバイルも `browse-category.html:264,290,317` に文言3件。**正典は「`*-ranking` プリセットは定義しない」と明言** |
| U-10 | 中 | **Library URL の単数形残存**。`index.html:954,960,966`（`/library/maker/[slug]` `/library/rig/[sku]` `/library/part/[sku]`）、`library-maker-detail.html:521-523`。正典は複数形統一 |
| U-11 | 低 | 禁止クラス `.mobile-avatar-btn` のCSS定義が `mobile-shell.css:140,153,2286` に残存（HTML/JS使用は0件） |
| U-12 | 低 | 禁止語「マスター」がLibrary系7ファイル `:220` のCSSコメントに残存（UI文言ではない） |

### 契約遵守（合格）
- §3.1b Header Search Overlay: `openHeaderSearch` / `.header-search-overlay` / `#headerSearchInput` / `#headerSearchSuggestions` **4点すべて実在**
- §3.2 BottomNav 5項目・`.mobile-bottom-nav__avatar` **完全一致**
- §6 JS API **6関数完全一致**（`js/mobile-shell.js:642-649`）
- §7 静的ゲート **合格**（ヒットは変更履歴コメント1件のみ＝契約が除外を明記）
- LoginRequiredModal・context 8種／文言5グループ・`?guest=1`・新規登録ピル **すべて契約どおり**

## schema

### 三者バラバラで裁定が必要
| # | 固定値 | 正典 | 実DB | 実装 |
|---|---|---|---|---|
| S-1 | **`log_type`** | 4値（HOLD） | 未確認 | **登録5値（`setup`）／閲覧・検索4値**で内部分裂 |
| S-2 | **`size_class`** | 13値（HOLD） | **18パターン自由記述** | **検索13値 / PC登録7値 / モバイル登録0（欄なし）/ 旧テンプレ5値** |
| S-3 | **`event_tags`** | 12種 | **列が存在しない** | **検索UIに4値**（RECON G6/ヒルクライム/耐久/デモリション） |
| S-4 | `condition` | new/used/modded | — | 検索3値 / PARTS登録は `new`/`used_good`/`used_ok` の別3値 |

### 一致（対応不要）
- **`rig_type`**: 新5値のみ。**`miniz` は指定パス配下で見つからない** ✅
- **`surface` 10値 / `weather` 6値**: 検索・フィード側は完全一致 ✅
- **RIGカテゴリ24件**: 検索・ブラウズとも24件 ✅
- **パーツカテゴリ親14**: 検索・ブラウズとも14件 ✅
- **Mini-Z の扱い**: `size_class` 側に正しく配置。大分類タイルに無い ✅
- **`build_tags`**: `mini-micro`・「ミニマイクロ」は**指定パス配下で見つからない**。deprecated 除外後の4値と一致 ✅

### モックを直すべき
| # | 優先 | 項目 |
|---|---|---|
| S-5 | **最高** | **PC composer `:1043` の `data-log-type="setup"`**（廃止値）。同 `:1299` の分岐も連動。モバイル `register-log.html:381` も同様（値属性なし） |
| S-6 | **高** | **登録フォームが検索側と別タクソノミー**: PARTS登録 `register-parts:2273-2284` が独自10カテゴリ（正本は親14）。RIG登録カスタムフォーム `register-rig:2499-2509` が英語8カテゴリ（正本は24件） |
| S-7 | **高** | **PC composer に `surface`/`weather` の構造化入力が無い**。自由テキスト `#runSurface` 1本に天候まで混在（`:1143-1144`）。**検索側は10/6値で確定しているのに登録経路がそれを生成できない＝データが永久に入らない構造** |
| S-8 | 中 | RIG登録カスタムフォーム `register-rig:2513-2522` のスケール7値（`1/6 1/12 1/14 1/16 1/27 その他` 欠落） |
| S-9 | 低 | `search.html:376` の `data-pcat="メンテ・工具・ピット"` とラベル「メンテ・工具」の不一致 |
| S-10 | 低 | `pc/myrig-search-v3.html:839` の注記「モバイル search.html の21件・タイル12件は誤り」は**注記が古い**（実装は24件に是正済み） |

## search + support

### 正典を直すべき
| # | 優先 | 項目 |
|---|---|---|
| P-1 | **最高** | **search-page-plan-v2 の /searchトップ構成を全面改稿**（上記⑤）。P22-C29 D案へ |
| P-2 | **最高** | **ソート仕様の差し替え**。「関連度・新着・人気」3ピルは 2026-08-21 裁定⑦⑧で廃止済み。**「人気順」を意図的に使わない**方針も未記載 |
| P-3 | 高 | 「人・ガレージを探す」独立行の記述を撤回 |
| P-4 | 中 | 欠落の追記: 適用中条件チップ／件数バッジ、**ユーザータブはFEATURED対象外**、**検索面は広告ゼロ**（裁定①）、`/search/categories`・`/search/parts-categories` |
| P-5 | 中 | **検索スコープ正典表は「宿題」ではなく実装から昇格可能**。`search-results.html:524-576` の `LABEL/UNIT/GRIDTYPE/DIGEST/SORT_OPTIONS` が事実上の一元定義。**ただしタブ順（log→lib）とダイジェスト順（lib→log）が食い違うため裁定1点必要** |
| P-6 | 中 | `--cat-*` 禁止の線引き明記。**禁止対象はハイライトのみ。種別チップの選択色は `--cat-*` が正** |
| P-7 | 中 | implementation_checklist Cookie同意の**自己矛盾**（「続行することで同意」型の文言と「同意するボタン」実装要件が同一節に併記） |
| P-8 | 低 | モック配信環境は意図的に全面 noindex（`robots.txt` は `Disallow: /`）である旨を注記 |

### モックを直すべき
| # | 優先 | 項目 |
|---|---|---|
| P-9 | **高** | **`myrig-rc.com` 残存4箇所**: `pc/myrig-auth-onboarding-pc-v0.2.html:1132, 1206, 1312, 1600`。**ユーザー名入力のURLプレビュー＝ユーザーの目に直接触れる** |
| P-10 | 中 | **PCプレースホルダ2種の不統一**: `pc/myrig-search-v3.html:1173`「RIG・パーツ・**ログ・ユーザー**を検索」→ 同 `:677` およびモバイル統一済みの「RIG・パーツ・**LOG・製品情報**を検索」へ。**正典が「未確定」としている当の項目だが、モバイルは統一済みでPCだけ取り残されている** |
| P-11 | 中 | **48px未満のタップ要素4件**: `search.html:114` `.rc__chip`(30px) / `:120-121` `.rc__x`(44px) / `:122-124` `.rc__clear`(約16px) / `:161-162` `.ax-tools__b`(約28px)、`mobile-shell.css:2295` 非fill `.seg-tab`(44px)。**周囲は de-clip 技法で48px化済みで、この4件だけ取り残されている** |
| P-12 | 中 | **Cookie同意バナー未実装**（バナーDOM・localStorage・同意ゲートいずれも指定パス配下で見つからない） |
| P-13 | 低 | **PCフッターの特商法リンクにパス未設定**。20ファイル超で `href="#"` のみ。モバイルは `js/mobile-shell.js:45` で `/legal/tokushoho` を保持済み |

---

# 未確認範囲

- **ブラウザでの計算値は未取得。** `!important` の後勝ち判定は宣言順の静的読み取りに基づく（home の743個は全数追跡していない）
- **shadow DOM 内の最終描画色は未実測**
- **実DBにおける `log_type` / `surface` / `weather` / `build_tags` / パーツ子カテゴリ90件の実値分布は未確認**
  （`_decisions/2026-08-21_db-inquiry-002-realdata.md` は event_tags・size_class 中心）
- **画像／Cloudflare Images／`images` テーブル前提の照合は実質不能**。モックは静的HTML＋ローカルダミー画像で、
  DB前提に触れる実装コメントは `pc/myrig-settings-pc-v0.2.5.html:1047` の1行のみ
- **Next.js middleware は未実装**のため auth-guard §5 の実挙動は検証不能
- `_archive/` `_backup/` `_compare/` `board/` `docs/` は対象外
- `mobile/0709 mobile/` 配下に HTML は0件（空のデプロイ用ディレクトリ）。**モバイルHTMLはルート直下にのみ存在**

---

# 総括

**文書監査だけでは判定できなかった項目が、実装を見て決着した。**

- `log_type` → **裁定不要のモックのバグ**（廃止値 `setup` の残骸）
- 操作色実値 → **実装済み。正典の「要確認」を外せる**
- 通知色 → **PCは既にPARTS色と分離実装済み。裁定材料が実装側にあった**

逆に、**文書監査では見えなかった重大な乖離**も出た。

- v8適用がPC 40中6ファイルのみ（正典は全面適用を含意）
- PC Feed が #28 未適用
- search-page-plan-v2 が実装から1世代遅れ（P22-C29 D案が未反映）
- 登録フォームと検索フォームが**別タクソノミー**で動いている（PARTS 10 vs 14、RIG 8 vs 24）
- `surface`/`weather` は検索側で確定しているのに**登録経路が存在しない＝データが永久に入らない**

**教訓: 「正典にこう書いてある」は、実装を見るまで判定材料にならない。**
