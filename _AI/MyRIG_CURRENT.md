# MyRIG CURRENT

revision: MYRIG-20260829-035
updated: 2026-08-29 (JST)（生成: Cowork ZoneInfo("Asia/Tokyo")）

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

---

## 🔴 いまやっていること（NOW）

> **スレッドをまたぐとき最初に読む節。** イタヤは環境（デスクトップCowork / ブラウザCowork /
> ブラウザ通常チャット）を切り替えながら作業するため、**前スレッドの記憶に依存せず
> ここだけ読めば再開できる**状態を保つこと。作業の区切りで必ず更新する。

**最終更新: 2026-08-29 / revision 035**

> 📌 **スレッドをまたぐときは `_state/HANDOFF_20260825.md` も読む。**
> 本節が「いまどこにいるか」の正本。HANDOFFはそれを補う会話レベルの文脈
> （なぜそうなったか・進め方の約束・踏んだ罠・やらないこと）。HANDOFFは正典ではない。

### 進行中のレーン

| レーン | 状態 | 次のアクション |
|---|---|---|
| **検索 SEARCH-UPDATE-001** | ✅ **CLOSE（2026-08-25）** | 追加監査・cleanup・改善探索を行わない。軽微/横断は Web文法キューへ |
| **Browse整合（BROWSE-CONTRACT-001）** | 🔴 **進行中（Cowork主査 / 2026-08-29〜）** | **いまここ。** 実測完了・表示契約 v1 策定済み。次は検証スクリプト → 修正。**このレーンの間、GPTはBrowse系ファイルを触らない**（§下記） |
| **Home 実画面レビュー** | 🔵 進行中（GPT＋イタヤ主導） | イタヤが実画面を見て指摘 → 実装 |
| MyRIG Web文法（横断設計） | 🟡 DRAFT v0 作成済み・**一旦停止** | 追加調査・文書拡張はしない。Homeレビューで判断材料が出たら再開 |
| Web文法 実装バッチ1 | ✅ 完了・deploy済み（`054e6e0`） | PC app-nav 90本を実結線 / PCへ未実装route共通handler / Home切替の hidden 破れ修正 |
| モック全体の第2周 | ⚪ 未着手 | ページ単体ではなくフロー単位で確認する体制へ移行 |

**ライブ**: `myrig-mockup` = `054e6e0`（production READY）
スクリーンショット撮影は不要（2026-08-25 以降、イタヤが直接ライブを見る運用）。

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

### 🔴 Browse整合 BROWSE-CONTRACT-001（2026-08-29 / Cowork主査）

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
