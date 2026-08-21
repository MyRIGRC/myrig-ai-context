# GPT 外部監査（修正後の docs/12本） — 2026-08-21

**位置づけ: 監査済み・未反映。** commit `af06428`（CURRENT 007）で確定した正典に対する
GPTの独立監査結果。**本レポートの指摘はまだ本文へ反映していない。**

**検証状況**: 最優先4系統＋主要指摘は、Cowork が現物で裏取り済み（下表「検証」列）。
GPT の指摘は Gemini と異なり、実在する記述を正確に引用している。

---

## 最優先4系統（Next.js実装前に解消すべき）

### A. Auth middleware — 全体ガードが機能しない ⚠️検証済み・事実

`docs/ui/auth-guard-spec-v1.md` §5 skeleton は Maintenance / Suspended の**全体ガード**を
middleware 内に置いているが、`config.matcher` は `/garage/:path*` `/settings/:path*` のみ。
**公開ページでは middleware 自体が実行されないため、メンテナンスモードが効かない。**

- **これは今回の監査が悪化させた**（修正前も matcher は4パスのみで、既に公開ページは対象外だった。
  2パスへ狭めたことで顕在化）。**新規に作った矛盾ではないが、放置は不可。**
- 対処案: matcher を広げて内部で P1 だけ分岐するか、全体ガードと P1 ガードを別 middleware に分離
- **要裁定**（実装方式の選択のため）

### B. 物理DELETE禁止 ⇔ 解除操作の実装手段が無い ⚠️検証済み・事実

`_AI/MyRIG_CORE.md:28`「物理DELETEは禁止。inactive / NULL / archive / mv で代替する」
に対し、`likes` / `favorites` / `pins` / `follows` の4テーブルは
**`deleted_at` も `is_active` も持たない**（現物確認済み・4テーブルとも0件）。

いいね解除・フォロー解除・ピン解除は**物理DELETEでしか実現できない構造**。
さらに schema §RLS は DELETE ポリシーを「運用・移行時の最終手段」として残しており、
CORE の無条件禁止と一致しない。

- 対処案: (1) 4テーブルに soft-delete 列を追加 / (2) CORE 側で「関係テーブルの解除は例外」と正式裁定
- **要裁定**（CORE = L1恒久ルールの改訂を伴うため、Cowork では決められない）

### C. RLS が private データを保護していない ⚠️検証済み・事実

| # | 対象 | 現状 | 問題 |
|---|---|---|---|
| C-1 | `pins` | 定義は「**非公開**・一時保存」（schema 314行）／RLSは「**SELECTは全公開**」（601行） | 定義と RLS が正面から矛盾 |
| C-2 | `favorites` / `pins` | 個別行が全公開 | page-role-matrix は Public Garage で Favorites/Pins を**非表示**と規定。**DB直叩きで迂回可能** |
| C-3 | `images` | SELECT全公開（「Cloudflare URLの推測不可能性で担保」） | 親RIG/PART/LOGが**非公開でも**行が読め、URLを取得できる |
| C-4 | `comments` | 公開判定が `status='published'` のみ | **親コンテンツの公開可否を検査していない** |

**UI非表示はアクセス制御にならない。** モック段階では見えないが、実装前に要解消。

- 対処案: 個別保存関係は owner 限定にし、公開が必要なのは集計値のみ別経路。
  polymorphic 親の閲覧可否を共通関数で判定
- **要裁定**（セキュリティモデルの設計判断）

### D. `master_aliases.entity_type` の誤記 ⚠️検証済み・事実

`docs/schema/db-schema-answers-v1.md:170` が `'part'` と記載。
実DB一次記録 F-1 の実在値は **`part_master`(190件)**。
（他3値 `rig_master_variant` / `manufacturer` / `rig_master` は一致）

- **単純な誤記。裁定不要で修正可能。**

---

## その他の指摘（文書整理系・裁定不要）

| # | ファイル | 内容 | 種別 |
|---|---|---|---|
| 1 | `color-token-v8.md` 17行 | 「両モードで同じ値を使う」と断定した直後、§1でLOGのみ dark 例外を定義。17行だけ読むと誤る | 矛盾 |
| 2 | `db-schema-answers-v1.md` | 冒頭にHOLD表を足したが、**本文は旧「確定」のまま**（size_class 13値固定・spec_schema「列は存在」・event_tags 12種）。注記で本文を覆う方式が残存 | 矛盾 |
| 3 | `page-role-matrix-v1.md` | `docs/design-rules.md` を参照するが repo 内に不在 | 陳腐化 |
| 4 | `mobile-component-contract-v0.5.md` 151行 / `pc-mobile-spec-inheritance-v1.1.md` | `/rigs` を仕様として持ち込んでいるが、URL正本の page-role-matrix に `/rigs` は存在しない | 矛盾 |
| 5 | Coverage Matrix 参照 | contract=v1.1 / pc-mobile=v1.2 で不一致、かつ実体は `_archive/`（退役） | 矛盾 |
| 6 | `auth-guard-spec-v1.md` | ステータス「確定 v1」／本文＝旧仕様／冒頭＝新仕様、の三重状態が残る | 矛盾 |
| 7 | `myrig_db_schema_v1_6.md` | `parts_masters` の owner 未確定なのに FK・INDEX・ER・マイグレーション順に実行可能な形で残存 | 要隔離 |
| 8 | `cross-ref-category-names-v4.md` | 親14・RIG24 の詳細正本が repo 不在の v2 に依存 | 根拠なき断定 |
| 9 | `App_Ready_Design_Rules.md` | Rule1（Server Actions にも client 版を持て）⇔ Rule7（将来共有する読み取りだけでよい）が矛盾 | 矛盾 |
| 10 | `App_Ready_Design_Rules.md` | 「認証チェックは middleware で一元管理」⇔ Auth Guard の P2/P3 は page/component 側 | 矛盾 |
| 11 | `implementation_checklist.md` | rate limit値・10MB・10枚・8000px・variant寸法が強制値だが裁定根拠が正典内に無い | 過剰な縛り |
| 12 | `implementation_checklist.md` | Auth方式がHOLDなのに「確認メール・パスワードリセット」の Email Templates 設定を無条件で指示 | 矛盾 |
| 13 | `implementation_checklist.md` | Cookie同意が「同意ボタン後にロード」と「続行することで同意」の2モデル混在 | 矛盾 |
| 14 | `implementation_checklist.md` | 特商法表記を法的義務として固定しているが正典内に根拠資料が無い | 根拠なき断定 |
| 15 | `design-nogo-list.md` | 「黄・赤・青と白黒以外を足さない」の射程が広すぎる（ブランド色未裁定・ステータス色別系統と併存） | 過剰な縛り |
| 16 | `design-nogo-list.md` | 「検査の盲点」「コード衛生」は実装・QAノートであり恒久NGと混在 | 過剰な縛り |
| 17 | `pc-mobile-spec-inheritance-v1.1.md` | R1「上位文書が沈黙＝PC構成がそのまま有効」＋ merge/defer/drop 原則すべて実機裁定必須は、モバイル独自UXの自由度を強く拘束 | 過剰な縛り |

---

## GPT が「妥当」と評価した本日の修正

- Page Role Matrix の Feed / ranking / i18n を冒頭注記だけでなく**本文へ統合**
- Mobile Contract の **v7カラー → v8**
- search-page-plan **v1参照 → v2**
- G7 を Feed＋検索種別タブの2例外へ修正し、**#25/#26 の全体衝突は HOLD に残した**
- App Schema から **Research所有マスター定義を切り離した**
- size_class / event_tags を**実DB一次記録に合わせて HOLD へ戻した**
- App_Ready の images / notifications / URL / domain を各正典参照へ降格
- 認証方式を初回案の「Google/X/Facebook へ統一」ではなく **HOLD へ戻した判断は正しい**
- NG-7 を「中立方向は確定、具体値は未確認」と整理した最終訂正も妥当

## GPT の総合所見（原文要約）

> 全体として前よりかなり健全になっている。特に一次DB記録を基準にHOLDへ戻した判断と
> Research/App責務分離は妥当。
> 一方、**注記で旧本文を覆う方式がまだ多く、「正典本文だけ読めば安全」という状態には達していない。**
> 最優先は Auth middleware、物理DELETE方針、RLSのprivateデータ制御、`part_master`誤記の4系統。

---

## 参考: Gemini の監査結果は不採用

同じZIPを Gemini にも投げたが、**ZIPを解読できないまま11件の指摘を生成**していた。
Cowork が固有名詞を全件検証した結果、`フリーワード検索禁止` / `bg-surface-elevated` /
`spec_details` / `legacy_tag_id` / 「RLSは全テーブルで有効化・テスト済み」等は
**すべて repo 内に存在しない**（ヒット0件）。偶然一致した指摘もゼロ。

Gemini 自身が後に「ファイルを読み取れておらず、文脈から推測して生成した」と認めている。
**本レポートには一切反映していない。**

教訓: 監査AIを増やすほど安全になるとは限らない。**指摘は必ず現物で裏取りしてから採用する。**
