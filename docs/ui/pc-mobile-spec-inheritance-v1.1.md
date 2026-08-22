# PC→Mobile 仕様引き継ぎ規約 + PC版39ページ セクション設計図 v1

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> いまモックアップ制作フェーズ。デザインとサービス概念を**議論しながら作る**段階なので、
> 本書は「今こうなっている」という出発点であって、議論の打ち切りではない。
> **「既存仕様と異なる」ことだけを理由に案を捨てないこと。**
> 差分を明示すればイタヤ裁定で変更できる。
>
> ただし **§1 R2「無断省略の禁止」は L1**（PIT TABLE 欠落事故の再発防止）。
> 省略してよいかどうかを議論するのは自由。**黙って落とすのが禁止。**

**ID:** pc-mobile-spec-inheritance-v1
**作成:** 2026-07-24 (JST) / Claude
**状態:** v1.1（GPTクロスチェック反映）
✅ **2026-08-22 ACTIVE正典として確定**（旧「承認の有無は未確定」注記を削除）。
`docs/ui/` 配下に置かれ CURRENT の Task Routing に載っており、他の正典からも参照されている実態に合わせた。
拘束力は上記のとおり **L2**（提案歓迎。ただし §1 R2 のみ L1）。
**入力:** アーカイフ_.zip（PC版V3 39ページ現物を機械抽出＋目視照合）/ mobile-coverage-matrix v1.2 / page-role-matrix v1.5 / mobile-component-contract v0.5 / mobile-feedback-ledger

---

## 0. 目的と位置づけ

**発端:** モバイルgarage制作時、PC版garage-topの中核セクションであるPIT TABLEが無断で欠落した状態で作られ、事後指摘で復旧した。原因は「PC版のセクション構成を参照せずに、なんとなくモバイル版を新規設計した」こと。

**本書の役割:** PC版39ページの**ページ内セクション構成**を正典化し、モバイル着工前の照合と処遇宣言を義務化する。

**既存文書との分担（重複しない）:**

| 文書 | 粒度 |
|---|---|
| page-role-matrix v1.5 | ページの役割・URL |
| mobile-coverage-matrix v1.2 | ページ単位の処遇（full mock / skeleton / direct）<br>⚠️ 実体は `_archive/knowledge-20260821/mobile-coverage-matrix-v1.md` にあり、参照版数も contract v0.5 と不一致。**所在・版数とも要確認** |
| mobile-component-contract v0.5 | 部品（Shell・カード）の契約 |
| **本書** | **ページ内セクションの構成順と引き継ぎ処遇** |

---

## 1. 基本原則

- **R1 セクション正典性:** §5の上位裁定（feedback ledger・coverage matrix・r14凍結・現行契約等）が明示的に「置換・削除」した箇所を除き、**PC版のセクション構成はモバイルでもすべて有効**。「上位文書に書いていない＝省略してよい」ではなく、「上位文書が沈黙している＝PC版構成がそのまま生きている」と読む。
- **R2 無断省略の禁止:** PC版に存在するセクション・パネル・付随UIが、承認済みの merge / defer / drop または上位裁定なしにモバイルから欠落している状態は、**欠陥（バグ）**として扱う。
- **R3 着工前照合の義務:** §3の手順で処遇表を作ってから実装する。処遇表なしの着工は禁止。
- **R4 drop / merge / defer はイタヤ裁定必須:** 裁定は2026-07-22恒久ルールに従い、**実モックの比較モード（URLパラメータ切替）での実機裁定**で行う。処遇表の紙上承認だけで確定しない。比較モードは「PC要素をcarry/adaptした基準案」と「merge / defer / drop候補案」を同一データ・同一幅で切り替えられる状態とする。defer候補は、現Phaseでの受け皿表示または後続Phase送付を示すプレースホルダーを比較対象に含める。すでにfeedback ledger等で同一処遇が実機裁定済みの場合は、裁定ID・比較URLを処遇表に記載し、再裁定は不要。
- **R5 迷ったら止める:** 判断に迷うセクションは勝手に決めず「要確認」でイタヤに戻す。

---

## 2. 処遇語彙（処遇表で使う5語）

| 語 | 意味 | 裁定 |
|---|---|---|
| **carry** | PC版の構成・要素をそのまま縦積みに翻訳 | 不要 |
| **adapt** | モバイル文法へ変換（§4の既定変換に従う。逸脱時は内容明記） | 処遇宣言は必要。既定内は追加裁定不要 |
| **merge** | 他セクションへ統合（統合先を明記） | **必須** |
| **defer** | 後続Phaseへ送る（Phase明記。設計図からは消さない） | **必須** |
| **drop** | モバイル非搭載 | **必須** |

---

## 3. 着工前チェック手順（Cowork / Claude Code 共通）

1. 本書§6の該当ページ設計図を開く。
2. PC版HTML現物を開き、設計図と突合する。**食い違いはPC現物が正**（本書の改訂を起票）。
3. 設計図の**矢印で区切られた各セクション、各左右列パネル、各付随UIを1行ずつ**展開し、処遇（carry / adapt / merge / defer / drop）を宣言した処遇表を作る。棚×N・モーダル群・複数パネルを1行にまとめない。右列パネルは「挿入位置」も宣言する。
4. merge / defer / drop がある場合はイタヤ裁定（R4方式）を取ってから実装する。
5. 実装。
6. 納品前に処遇表↔モックを突合する。carry / adapt 行の欠落＝欠陥。

処遇表フォーマット例:

```
| PCセクション / パネル / 付随UI | 処遇 | モバイルでの受け皿・位置 | 根拠 | 裁定状態・比較URL |
|---|---|---|---|---|
| PIT TABLE | carry | garage-cover直下。折りたたみトグル維持 | PC現物 / 本書#9 | 不要 |
| 右列 RECENT ACTIVITY | adapt | 本文末尾に移設 | G1 | 不要 |
| home-dir（左ツリー） | merge | Header Search Overlayのカテゴリ導線へ | G3 | 要裁定 / `?compare=home-dir` |
```

---

## 4. PC→モバイル共通変換文法（既定のadapt。処遇表ではadapt宣言が必要、変換詳細の追記は不要な範囲）

- **G1 カラム:** PC 2〜3カラム→1カラム縦積み。**右列パネルは削除対象ではなく、本文への挿入位置を宣言する対象。**
- **G2 共通Shell写像:** PC `app-header / app-nav / footer / drawer-overlay` の**共通クローム** → mobile Header + BottomNav（契約v0.5）。§6の設計図からは除外済み（毎ページ暗黙carry）。ただし、footer内のAbout / Help / Terms / Privacy等の情報導線はBottomNavへの自動吸収とみなさず、契約上の受け皿を別途確認する。
- **G3 サイドナビ写像:** `gs-nav`・home-dir（カテゴリツリー）などの**ナビゲーション部分**は、モバイルに重複ナビを新設せず既存Shell・ページ内タブ・ハブページへ写像し、写像先を処遇表に明記する。`gs-profile`（アバター、名前、bio、所在地、フォロー数、統計、アクション）はナビではなくページ固有コンテンツであり、別途carry / adapt対象とする。
- **G4 パンくず:** Header戻る導線へ写像。階層ラベルが意味を持つページはチップ等で残す判断可（宣言必須）。
- **G5 hover専用情報:** tap / 明示UIへ置換。表示手段を宣言。
- **G6 PCモーダル・ドロワー:** 契約のbottom-sheet / Dialog Controller管理へ。
- **G7 追加ロード:** ページング／もっと見るが基本。無限スクロールは**限定例外**であり、
  例外の追加は正規の裁定を経ること（勝手に広げない）。現時点の例外は以下2つ。
  - **Feed**（台帳 #25）
  - **検索の種別タブ**（#26。search-page-plan-v2 §確定仕様。※「すべて」タブは増量ダイジェスト29件で無限にしない）

  ⚠️ **「ページングが基本」という前提自体が未確定**（台帳#25 と #26 が食い違っており要調停。詳細は CURRENT の HOLD）。
  現状は既裁定の2例外のみを列挙し、それ以外へ広げるにはイタヤ裁定を要する扱いとする。
  ※ここでの #25/#26 は **mobile-feedback-ledger の番号**。§6 のページ番号とは別体系。
- **G8 セクションヘッダ文法:** 英字タイトル＋件数＋右肩リンク（もっと見る→ / View all→）はワンセットでcarry。
- **G9 AD枠:** PC版の `ad-placeholder` / `ad-slot-section` / `feed-ad-slot` 等、広告・アフィリエイト枠はクラス名にかかわらず「存在」をcarry。位置はモバイル側で裁定。
- **G10 空状態・skeleton:** PC版に定義がある場合はcarry（empty-state-spec-v1準拠）。

---

## 5. 参照優先順位（coverage matrix §2に本書を挿入した形）

1. r14凍結Home → 2. Coverage Matrix → 3. Page Role Matrix v1.5 → 4. auth-guard等の現行仕様 → **5. 本書§6設計図** → 6. PC版39ページHTML現物 → 7. v0.2 9ページ版等（参考のみ）

- feedback ledgerの確定裁定は該当箇所で常に優先。
- 5と6が食い違えば6が正（本書改訂を起票）。
- 1〜4が沈黙している範囲では5・6が全面有効（R1）。

---

## 6. ページ別セクション設計図（PC 39ページ）

凡例: **主列**＝本文の構成順 / **右列**＝サイドパネル（挿入位置宣言の対象） / **付随**＝モーダル・ドロワー・空状態 / **特記**＝既裁定・注意点。番号はcoverage matrixの#と同一。共通Shell（G2）は全ページで省略。

### Browse / Relationship

**#1 myrig-home-v3.html — `/`**
- 主列: idx-hero（コピー＋CTA）→ cat-strip（カテゴリ帯）→ browse棚×5（今週の注目RIG / 新着RIG / Rock Crawler注目車種 / 今週の注目ログ / 新着パーツ）→ Editorial（edit-banner 3枚）→ browse棚×4（人気タイヤ / Drift新着RIG / 新着ログ / Buggy人気RIG）→ lib-section（RC製品カタログ導線）→ AD枠
- 左列: home-dir（カテゴリツリー＋パーツリスト）
- 特記: **モバイルはr14（Plan E改）で確定凍結。本設計図はPC参照用であり、モバイルHomeの改変根拠にしない。**

**#2 myrig-search-v3.html — `/search`** ✅済（P11〜P14）
- 主列: srch-head（タイトル＋クエリ＋件数＋フィルタートグル＋再検索）→ srch-result-meta＋表示切替 → **混在検索結果**（RIG / PARTS / LOG / Library / Usersを単一グリッドまたは単一リストで表示）→ ページネーション → 空状態 / skeleton
- 左列: searchFilterSidebar（コンテンツタイプ All/RIG/Parts/Logs/Library/Users、ソート Relevance/Newest…、メーカー等）。モバイルでは契約に従いフィルタードロワーへadapt
- 特記: モバイルは単一サーフェス化（#15）・Header Search Overlay（#18）裁定済み。現行モバイル確定形が優先。

**#3 myrig-feed-v3.html — `/feed`** ✅済（P13系）
- PC構成: 3カラム（左＝フィルターパネル: FEEDタブ All/Following/Trending＋種別 / 中央＝日付セパレータ＋フィードカード・アクティビティ行＋loading skeleton＋empty state / 右＝Trending RIGs＋Suggested Users＋広告枠2種［Affiliate / AdSense］）
- 特記: モバイルはタブ「おすすめ/フォロー中」置換（#28）・グリッド画像＋ImageLightbox・無限スクロール（#25例外）で**確定固定済み**。PC右列（おすすめユーザー/トレンド）の受け皿は現行モバイル確定形に従い、改修時のみ処遇宣言。

**#4 myrig-browse-category-v3.html — `/category/[rigType]`**
- 主列: cat-hero（カテゴリ名＋説明＋統計 RIGS/PARTS/LOGS/車種＋検索＋Open Library）→ browse棚×8（今週の注目 / 新着 / 人気車種 / 最近の整備・走行ログ / よく使われているタイヤ / メーカー別新着×2 / 注目モデル）→ lib-section（カテゴリLibrary導線）→ AD枠 → Editorial
- 左列: home-dir（#1と共通）
- 特記: v0.2旧版を参考にする場合ランキング表現の持ち込み禁止（matrix §0.5）。`/rigs`（補助行A）と構造共用・役割別。

**#5 myrig-browse-parts-v3.html — `/parts`**（skeleton）
- 主列: parts-hero → 棚×5（Featured Parts / Popular in Rock Crawlers / Recently Added Parts / Tires & Wheels / Electronics & Power）→ Used in Real RIGs → パーツカタログ（lib-section）→ Parts Guide（Editorial）
- 左列: home-dir（#1・#4と共通）

### Detail

**#6 myrig-rig-detail-v6.html — `/rig/[rigId]`**
- 主列: breadcrumb → ptb（RIGバッジ＋メーカー＋チップ群: カテゴリ/スケール/シャーシ/ボディ/日付＋タイトル＋キャッチコピー）→ gallery（メイン画像＋サムネ）→ DESCRIPTION → PHOTO NOTES（fotonote grid）→ SPEC（spec-grid）→ BUILD DETAILS（アコーディオン）→ LOG（フィルタボタン＋log-card）→ PARTS（part-card grid＋View all）→ COMMENTS（comment＋続きモーダル）→ owner-section（ガレージ導線＋RIG/パーツ/ログタブ）→ 関連棚2つ（同じ[シャーシ]のRIG / 同じ[メーカー]のRIG。各棚内にrecommend-scroll）
- 右列（上から）: ownerミニパネル → アクション（いいね/お気に入り/ピン留め）→ 統計（閲覧/お気に入り/ピン留め）→ BUY / INFO（Library製品情報導線＋アフィCTA）→ SHARE → EXTERNAL LINKS → AD枠 → RECENT ACTIVITY
- 付随: img-modal（Lightbox）/ cmt-modal
- 特記: エンゲージ数は訪問者にも表示（正典）。アフィCTA（E4）はモバイル再利用部品。

**#7 myrig-parts-detail-v6.html — `/parts/[partId]`**
- 主列: breadcrumb → ptb → gallery（#6と同文法）→ DESCRIPTION → SPEC → PHOTO NOTES → USED IN RIG → LOG → COMMENTS → owner-section（ガレージ導線＋RIG/パーツ/ログタブ）→ 関連棚: @userの他の登録パーツ → 同じ[メーカー]の人気パーツ → このパーツが使われている人気RIG（recommend）
- 右列: ownerミニパネル → アクション（いいね/お気に入り/ピン留め）→ 統計 → BUY / INFO → SHARE → EXTERNAL LINKS → AD枠 → HISTORY
- 付随: img-modal / cmt-modal

**#8 myrig-log-detail-v6.html — `/log/[logId]`**（direct）
- 主列: breadcrumb → log-title＋メタ → 本文＋画像 → LINKED TO（対象RIGカード）→ COMMENTS → 関連棚: @userの他のRIGのログ → 同じRIGの他のログ → 同じカテゴリの最近のログ（recommend）
- 右列: ownerミニパネル → アクション（いいね/お気に入り/ピン留め）→ 統計 → SHARE → このRIGのログ → AD枠
- 付随: img-modal / cmt-modal

### Garage（Own）

**#9 myrig-garage-top-v6.html — `/garage`** ✅モック済（P15/P16・Own View裁定#32待ち）
- 全幅: garage-cover（カバー画像）
- 左列: gs-profile（アバター＋表示名＋@handle＋bio＋所在地＋開始年月＋フォロー/フォロワー数＋RIG/PARTS/LOG数＋公開ページをプレビュー＋編集）→ gs-nav → RECENT ACTIVITY
- 主列: **PIT TABLE**（pit-section: 折りたたみトグル / pit-hero＝メインピンRIG［ステータス●＋最終更新＋ピン解除✕＋画像＋メーカー行＋RIG名＋一行メモ＋パーツ数・ログ数＋「ログを書く」「＋パーツ追加」「詳細を見る→」］/ pit-subs＝サブピンRIG / pit-memo）→ RIG（もっと見る→）→ PARTS（〃）→ LOG（〃）→ SAVED（お気に入り＋ピン留め集約、各もっと見る→）→ FOLLOWING（フォロー中の新着＋フィードを見る→）
- 特記: **今回の欠落事故の対象ページ。PIT TABLEはgarage-topの中核であり無断dropは禁止。** #11（通知・保存導線の集約）はSAVED/FOLLOWINGで表現。

**#10〜12 myrig-garage-rigs / parts / logs-v6.html — `/garage/rigs` `/garage/parts` `/garage/logs`**（direct）
- 全幅: garage-cover（カバー画像）
- 主列: page-title-bar（タイトル＋件数）→ カード一覧（context=owner: ステータスバッジ付き）→ 追加ロード導線（現物にあるページのみ）
- 左列: myrig-garage-sidebar（gs-profile＋gs-nav＋RECENT ACTIVITYパネル）
- 右列: 絞り込みパネル（RIG絞り込み / パーツ絞り込み / ログ絞り込み＋リセット）
- 特記: directでも実装時は本設計図で欠落チェック（一覧＝カードだけ、ではない）。

**#13 myrig-garage-favorites-v6.html — `/garage/favorites`**（direct）
- #10〜12と同構造。絞り込み: 並び順（追加が新しい/古い順）＋種別（すべて/RIG/PARTS/LOG）＋カテゴリ＋ブランド。カード3種混在。

**#14 myrig-garage-pins-v6.html — `/garage/pins`**（direct）
- #13と同構造（ピン留め絞り込み）。

**#15 myrig-garage-rig-detail-v6.html — `/garage/rigs/[rigId]`**（skeleton代表）
- 主列: ptb（編集）→ gallery（画像編集）→ DESCRIPTION → PHOTO NOTES → SPEC → BUILD DETAILS → LOG → PARTS → COMMENTS。各本文セクションに**編集ボタン（section-edit-btn）**が付くOwn View
- 右列: MANAGE（primary）→ BUILD PARTS MAP → 統計 → QUICK NOTE → LINKS & SHARE
- 付随: garage-drawer（＋overlay）/ img-modal / cmt-modal / edit-modal（編集）
- 特記: GarageDetailShell（戻り導線 / 保存状態 / 管理タブ / 公開設定 / 危険操作）のモバイル配置確認がskeletonの目的（matrix #15）。

**#16 myrig-garage-parts-detail-v6.html — `/garage/parts/[partId]`**（direct）
- 主列: ptb（編集）→ gallery（画像編集）→ DESCRIPTION → PHOTO NOTES → SPEC → USED IN RIG → LOG → COMMENTS（各本文セクションに編集ボタン付き）
- 右列: MANAGE → 統計 → QUICK NOTE → INFO → LINKS & SHARE
- 付随: #15と同じ

### Public Garage

**#17 myrig-public-garage-v3.html — `/user/[username]`**（skeleton）
- 左列: gs-profile（アバター＋名前＋@handle＋bio＋所在地・開始年月＋SNSリンク行＋統計 RIG/PARTS/LOG＋訪問者アクション（フォロー等）＋…メニュー）→ gs-nav → Latest Logsウィジェット
- 主列: RIG → PARTS → LOG（各グリッド/リスト）
- 特記: #32④ 推奨版採用時、カバー画像・自己紹介・フォロー数はPublic側に残す責務分離。Followボタンはfollow-button-spec-v1。

**#18〜20 myrig-public-garage-rigs / parts / logs-v3.html**（included state）
- 主列: pg-title-bar（@handle階層＋RIG / PARTS / LOG＋件数）→ 一覧（RIG grid / PARTS grid / LOG list）
- 右列: pg-sub-filter（#18＝ステータス＋カテゴリ＋並び順 / #19＝カテゴリ＋使用RIG＋並び順 / #20＝タイプ＋使用RIG＋並び順）
- 特記: 正典どおりモバイルは#17内のタブ状態として表現（別ページ化しない）。フィルターも各タブ状態の受け皿を処遇表で宣言する。

### Library

**#21 myrig-library-v3.html — `/library`**（skeleton代表）
- 主列: hero（コピー）→ 探し方を選ぶ（3カード: 車種カタログ / パーツカタログ / メーカー一覧。各カード＝英字ラベル＋説明＋件数＋一覧を見る→）→ 人気カテゴリから探す（Quick Exploreチップ）→ 収録範囲 → 最近追加された製品情報

**#22〜24 myrig-library-rigs / parts / makers-v3.html**（direct）
- 左列: myrig-filter-sidebar（#22＝メーカー検索＋メーカー/カテゴリ/スケール/駆動/STATUS/プラットフォーム等、#23＝メーカー検索＋メーカー/カテゴリ/用途/STATUS等、#24＝地域/種別/取扱カテゴリ）
- 主列: list-head（英字eyebrow＋タイトル＋説明＋件数）→ マスター一覧カード（2-CTA構成）→ disclaimer。#24のみ一覧後に「メーカーの探し方について」注記ブロック。

**#25 myrig-library-rig-master-detail-v3.html — `/library/rigs/[masterSlug]`**（skeleton・製品詳細テンプレ）
- 主列: master-catalog-box（master-title＋画像＋メーカー＋チップ＋**公式スペック表 master-spec-table＋公式情報/購入CTA**）→ MyRIG登録状況 → 同シャーシ・関連モデル → このベース車両のRIG → よく使われているパーツ → 関連する整備ログ → master-nav-links
- 購入導線: JPN / US / GLOBAL タブ＋アフィCTA（Amazon/楽天等）＋master-modal（外部リンク集）
- 特記: メーカー公称値のみ（推測補完禁止）。

**#26 myrig-library-parts-master-detail-v3.html — `/library/parts/[masterSlug]`**（skeleton・同テンプレ共用）
- 主列: #25と同文法（公式スペック表・公式情報/購入CTAはmaster-catalog-box内）。UGC棚: このパーツが使われている車種 → 使っているユーザーRIG → 関連パーツ・バリアント → 関連する整備ログ → master-nav-links

**#27 myrig-library-maker-detail-v3.html — `/library/makers/[makerSlug]`**（direct・Later）
- 主列: maker-hero（メーカー名＋概要＋About this page注意書き）→ stats-section（収録車種/パーツ/登録RIG/関連ログ）→ 車種カタログ（Featured）→ パーツカタログ（Featured）→ 公式情報とユーザー投稿

### Utility / Account / Info

**#28 myrig-register-rig-v2.9.5.html — `/register/rig`**（full mock・Phase 3-1）
- 主列: 写真ブロック（カバー＋ドロップ＋写真グリッド＋表示位置調整）→ 基本情報（ベース車種選択→chassisモーダル［メーカー→シリーズ→候補 / あいまい候補 / カスタムfallback］＋基本項目＋表示名ヒント）→ さらに詳しく記録する（advanced: Electronics & Power［ESC/Motor/Servo/Battery/Receiver・Radio］/ Drivetrain & Chassis［Shocks・Springs / Axle・Linkage / Transmission・Driveshaft］/ Wheels & Tires / Body・Exterior［Body / Bumper・Mount / Interior・Driver］/ Accessories［LED・Light / Winch / Scale Accessories］/ その他［カテゴリからパーツを追加］）→ 所有情報 → 公開・リンク（公開設定＋外部リンク）→ FAB（sticky保存）
- 付随: modal--chassis / modal--part-picker / 外部リンク追加 / 表示位置調整 / pub-toast
- 特記: 下書き・離脱警告・キーボード対応はモバイル再利用要件（matrix #28）。

**#29 myrig-register-parts-v0.1.10.html — `/register/part`**（full mock・Phase 3-2）
- 主列: 写真ブロック（Cover＋サブ最大4枚＋Photo Notes＋表示位置調整）→ 基本情報（製品選択＋表示名＋説明・メモ）→ さらに詳しく記録する（02 使用先・使用メモ［使用中RIG＋使用状況］→ 03 所有情報 → 04 公開・リンク）→ FAB（保存状態＋公開）
- 付随: 「パーツの製品を選ぶ」（modal--part-master）/「使用中のRIGを選択」（modal--rig-link）/ 外部リンク追加 / 表示位置調整 / pub-toast

**#30 myrig-log-composer-modal-v0.3.9.html — `/register/log`**（full mock・Phase 3-3）
- モーダル構成: ヘッダー（LOGを投稿＋保存状態）→ 種別タブ（整備 / カスタム / 走行 / メモ）＋日付 → 本文・メモ（必須・500字）→ 画像（最大3枚）→ タイトル（任意）→ 関連付け（場所 / 走行時間 / 路面コンディション）→ 対象RIG＋関連パーツ選択 → フッター（保存状態＋公開切替＋投稿）
- **`log_type` は4値（maintenance / run / custom / memo）が正。**「セッティング」タブは作らない（`setup` は廃止値）。
- 特記: モバイルはbottom-sheet化（G6）。ホスト画面（LOG一覧）は表示確認用。

**#31 myrig-settings-pc-v0.2.5.html — `/settings`**（direct）
- 左列: settings-nav / 主列: 5セクション（プロフィール［プレビューframe＋フォーム］/ アカウント / 投稿設定 / 表示・言語 / データ・退会）のカード群

**#32 myrig-notifications-pc-v0.1.1.html — `/notifications`**（direct）
- 主列: ページヘッダー（通知＋すべて既読にする）→ タブ（すべて / 未読）→ 日付グループ見出し → 通知行（未読ドット＋アクターアバター＋種別アイコン＋本文＋時刻）→ 未読空状態
- 付随: ヘッダー通知ドロップダウン（最新8件＋すべて既読にする＋すべての通知を見る）併載 — ガレージ配下導線（#11）と整合させる。

**#33 myrig-auth-v1.html — `/login` `/signup`**（direct）
- ログイン / 新規登録カード（Google・X・Facebook OAuth。メール認証なし）

**#34 myrig-auth-onboarding-pc-v0.2.html — `/onboarding`**（direct）
- showcase（左ビジュアル＋tagline）＋auth-card（おかえりなさい / ガレージを作る）→ ガレージの名前札を作る（ユーザー名 / 表示名 / 国・地域 / 興味カテゴリ）→ エラー3種（This bay is empty / Locked garage / Something stalled）
- 特記: PC現物のOAuthはGoogle / Appleで、#33のGoogle / X / Facebookと不一致。モバイル実装ではPC現物から継承せず、現行Auth仕様を正とする。

**#35 myrig-welcome-tour-v0.1.html**（direct）
- wt-modal 5ステップ（ようこそ → RIGをまとめる → 製品情報を見る → 整備・走行を残す → まずは1台）＋背景Home初回表示シミュレーション

**#36 myrig-about-v0.1.html — `/about`（PROPOSED）**（direct）
- 主列: about-hero → 3つの柱 → できること → 「SNSでは流れる、MyRIGでは残る」→ カタログ×ユーザーRIG連携 → 開発中告知 → final CTA

**#37 myrig-support-us-v0.1.html — `/support`（PROPOSED）**（direct）
- 主列: headline（一回だけ、無理なく応援する）＋応援カード → Why Support（受け取れる形の説明）→ お礼と使い道 → FAQ → closing
- 特記: 「継続支援」フレーミング。機能ゲート表現の禁止（Charter）。

**#38 myrig-support-legal-report-pc-v0.1.html**（direct）
- タブ7面: Help / Contact / Feedback / Report / Terms（9条）/ Privacy（8条）/ Empty State
- 特記: PC現物ではReportはタブ内フォームであり、通報モーダルは収録されていない。別途P3仕様でモーダルを採用する場合は、上位裁定として処遇表に根拠を明記する。

**#39 myrig-error-states-v0.1.html**（direct）
- 6画面: 404 / 非公開 / 読み込み失敗 / アカウント制限 / ガレージ不明 / メンテナンス（empty-state-spec-v1準拠）

### 補助行（PCファイルなし）

- **A `/rigs`:** #4と構造共用・役割別（曖昧統合禁止）。
- **B CreateActionSheet:** ✅済（P05・契約§3.6）。
- **C LoginRequiredModal:** ✅済（P05・#14文言5グループ）。
- **D SubCategory:** direct。Category Top派生。

---

## 7. 運用

- 本書は§6の設計図を含めて正典補助文書。PC正本HTMLの改訂時は本書§6の該当行を同時改訂する。
- 済みページ（#1 #2 #3 #9・補助B/C）の**改修時**も§3の手順を踏む。
- Cowork / Claude Code への指示書には「pc-mobile-spec-inheritance-v1 §3の手順で処遇表を作成してから着工」と明記する。
