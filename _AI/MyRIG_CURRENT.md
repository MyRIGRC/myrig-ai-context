# MyRIG CURRENT

revision: MYRIG-20260905-060
updated: 2026-09-05 11:48 JST（生成: Cowork ZoneInfo("Asia/Tokyo")）

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

---

## 🔴 いまやっていること（NOW）

> **スレッドをまたぐとき最初に読む節。** イタヤは環境（デスクトップCowork / ブラウザCowork /
> ブラウザ通常チャット）を切り替えながら作業するため、**前スレッドの記憶に依存せず
> ここだけ読めば再開できる**状態を保つこと。作業の区切りで必ず更新する。

**最終更新: 2026-09-05 / revision 060（Mobile ロゴ lockup CLOSE。可視ブランドバーも是正済み。次は Phase 3）**

> 📌 **057 の内容:** GPT の再確認で 056 本文後段に2-D 以前の古い記述が2箇所残っていた
> （MVP Phase 表の「残り: 2-D」／横断部品表 Header 行の「Home だけ page-local が残る」）。
> 実装のやり直しではなく**記述の整合のみ**。あわせて `header_propagation_result.json` の
> 85 PASS/6 FAIL が「2-D 前を基準にした historical audit evidence」であり 057時点の
> 回帰基準ではないことを mockup 側 Map（§8.7）に明記した。

> **いま止まっている場所:** **Phase 2 は 2-A〜2-E すべて CLOSE。Mobile ロゴ lockup の小バッチも CLOSE。
> 次は Phase 3（LOG Detail PC → Gate 5）。**
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
> **Mobile は今回のスコープ外。** PC とロゴの比率が違う（PC マーク23px・すき間1px / Mobile 20px・2px）ため
> 別アセット・別バッチとする。
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
| 2 | ✅ **2-A / 2-B / 2-C / 2-D / 2-E すべて CLOSE。** 次は Phase 3 | Home 等の再設計 |
| 3 | LOG Detail PC（確定 Detail System から作る）→ Gate 5 | 第3の Detail 文法の発明 |
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
| Header | ✅ **2-D CLOSE。** PC 35面が `app-header`、詳細2面が `.cx--quiet`。**PC 全33面で page-local ロゴ責務 0**。ロゴは lockup 画像（タグライン「RC GARAGE」を画像に内包）を `SoT_app-shell.css` §2.1 の**既定へ統合**（`--lockup` variant は残していない）。展開前の実体調査で**別のロゴ variant を必要とする consumer は 0件**と確認済み。**Mobile は比率が違うため別バッチのまま未着手** |
| Detail SoT | `SoT_detail-markup.js` ほかを使うのは RIG v15 / PARTS OPEN の2面。Phase 3 で LOG が3面目 |
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
| Gate 5 | LOG Detail を VISUAL LOCK する前 | Detail 3面の横断整合 |

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
| 送り先 | 論点 A（`dt-*` と catalog-v6 の統合）と B（Gallery 1枚規則を catalog-v6 へ移すか）は **Gate 5 で実施可否を決める**。OPEN 型・Quiet Rail の RIG への波及も Gate 5 |

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

**DECISION — Gallery は Cover 1 ＋ Sub 最大8（合計最大9）。Photo Notes は Sub が対象で最大8。** この対応関係を崩さない。

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
