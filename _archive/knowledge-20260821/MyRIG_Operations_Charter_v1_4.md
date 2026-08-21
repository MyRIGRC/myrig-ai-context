# MyRIG 運用憲章 v1.4（MyRIG Operations Charter）

バージョン: v1.4
発効日: 2026-06-25（JST）
前版: v1.3（2026-06-16）
位置づけ: MyRIG プロジェクト全体の**最上位運用正典**。本憲章は既存の全運用ルール文書（Operating_Rules / AI_Knowledge_Update_Rules / Source_of_Truth_Index / AI_Project_Knowledge / README）に**優先する**。
保管場所: `_CURRENT_RULES/MyRIG_Operations_Charter_v1.4.md`（COMMON / 全系統共通）
更新権限: 主査（Claude）が起草・改訂。版を上げて差し替え、旧版は `_archive/` 退避。

---

## 0. この憲章の目的と優先順位

MyRIG は **2系統のプロジェクト**（DBリサーチ系 / 実装系）が1つのフォルダに同居する。本憲章は、両系統が混ざらず、正典が常に最新で、完了物が視界から消え、外部AI引渡がスムーズに回る状態を**定義として固定**し、CC（Claude Code）がそれに従って自律実行できるようにする。

### 0.1 優先順位の明示【監査HIGH反映】
- 本憲章は **発効をもって、既存の役割定義・更新フロー・SoT索引の記述を上書きする**。
- 既存文書の記述が本憲章と食い違う場合、**本憲章が優先**する。
- 既存 `Operating_Rules` / `AI_Project_Knowledge` の役割表、`AI_Knowledge_Update_Rules` の更新フローは、本憲章への**ポインタまたは詳細手順**に格下げする（重複・二重管理を排除）。
- 既存文書側にも「本憲章を上位とする」旨を追記する（155で実施）。

### 0.2 役割定義【v1.3・§10.5に統一】
- Claude / GPT / Gemini は同等の3AIチームメンバー。恒久固定役割は持たない（詳細は §10.5）。
- タスク単位で「主査・独立監査・批判ゲート・Web調査」等を依頼文で明示指定する。指定が無い通常時は同等メンバーとして率直に判断。
- 不変の安全装置: DB書込・ファイル操作・大量変更はCC（Claude Code）が実行 / 最終判断はオーナー / AI間多数決で決めない。
- CC / Codex = ディスク実行・静的監査の実行レーン（カスタム指示・ナレッジ配布の対象外）。

達成すべき状態:
1. 実装系とDBリサーチ系が物理的に分離している
2. 各系統の正典が常に最新で、更新フローが定義されている
3. GPT/Gemini用の指示・引渡ファイルが指定フォルダに自動で書き出される
4. 完了済みデータはアーカイブに退避され、普段は視界に入らない（検索で辿れる）
5. フォルダ名・ファイル名を見れば「何の・どの系統の・現役か済みか」が分かる

---

## 1. 構造定義（Target構造）【監査HIGH反映・Target/Currentを明示】

以下は**Target構造（目標形）**。141-153の整理で大部分が達成済。未移行項目は §1.2 でPhase管理する。

```
MyRIG/
├── 00_START_HERE.md          ← 全体入口。2系統と正典所在への道標
├── _CURRENT_RULES/           ← 【共通(COMMON)正典】全AIが起動時に読む。XREF-文書もここ
│   └── MyRIG_Operations_Charter_v1.3.md  ← 本憲章（最上位）
│
├── Research/                 ← 【DBリサーチ系】git管理
│   ├── _docs/_INDEX.md       ← DB系正典の索引（正典探索は必ずここ経由）
│   ├── _apps/                ← 稼働 App V2（rc-master-data-app-v2）
│   ├── _Master/ _tools/      ← manifest・進捗 / scripts・migrations
│   ├── _handoff/             ← 【DB系】外部AI引渡（§5）
│   ├── _cc_archive/          ← 完了済 _CC_* の退避先（§4）
│   └── _archive/             ← DB系の旧版・退避物
│
├── App/                      ← 【実装系】Mockup + Next.js実装
│   ├── MOKUP/                ← モックアップ
│   ├── _docs/_INDEX.md       ← 実装系正典の索引（正典探索は必ずここ経由）
│   └── _handoff/             ← 【実装系】外部AI引渡（§5）
│
├── _assets/ _credentials/    ← 共通素材 / 認証（CCは開かない）
└── _archive/                 ← 全体アーカイブ（日付・種別管理／視界外・検索で辿る）
```

**鉄則: DB系正典は `Research/_docs/` 以外、実装系正典は `App/_docs/` 以外、共通・横断(XREF)は `_CURRENT_RULES/` 以外に置かない。** 系統を跨いだ正典混在を禁止。

### 1.2 Phase管理（Current→Target移行）【監査HIGH反映】
- **達成済**: 2系統分離・myrig-app退避・docs版数解消・各_INDEX設置・_handoff(DB系)。
- **未達（移行中）**:
  - `App/_handoff/` 新設（現状は実装系引渡も未分離）→ §5で確定、155以降で適用
  - `Research_DB_Schema_v1.0.md` 未作成（§2でPLANNED扱い）
  - Knowledge v1.7化（§2でPLANNED）
- CCは現物構造を `Research/_docs/_INDEX.md` 等の**_INDEXで確認**してから動く。憲章の図と実体が食い違う場合は_INDEX（実体）を優先し、差異を報告。

---

## 2. 正典の所在と系統【監査HIGH反映・現行/PLANNED分離】

### 2.1 現行正典（ACTIVE＝正典として使用可）
**DB系（`Research/_docs/`）**
- `RC_MDR_DB_Research_Knowledge_v1_6.md`（ACTIVE）
- `RC_Master_Research_Rules_v4.4_final_rev2.md`（ACTIVE）
- `MyRIG_Category_Structure_v1.3.md`（ACTIVE・body mount=chassis-frame訂正済）

**実装系（`App/_docs/`。移行期は `App/MOKUP/myrig_pc_Ver3/docs/` も物理配置を許容）**
- `MyRIG_Token_Correction_Note_v7.md`（ACTIVE・現在は MOKUP配下に物理配置）
- アプリDBスキーマ・実装チェックリスト・App設計ルール（ACTIVE）
- ★物理位置の移行期例外: 実装系の一部正典は**移行期間中のみ** `App/MOKUP/myrig_pc_Ver3/docs/` への物理配置を許容する。ただし**正典判定は必ず `App/_docs/_INDEX.md` 経由**とし、最終的には `App/_docs/` へ集約する。§1の「App/_docs/以外禁止」原則は集約完了後に厳格適用。

**共通（`_CURRENT_RULES/`）**
- 本憲章（最上位）/ AI_Knowledge_Update_Rules / Source_of_Truth_Index / Operating_Rules / AI_Project_Knowledge

### 2.2 PLANNED（未作成・未配布＝正典扱い禁止）【監査HIGH反映】
- `Research_DB_Schema_v1.0.md` — **NOT ACTIVE**（未作成）。実体作成→_INDEX更新→配布キュー反映が完了するまで正典として参照しない。
- `RC_MDR_DB_Research_Knowledge_v1.7` — **PLANNED**（v1.6がACTIVE。v1.7発効まではv1.6が正典）。
- **規則**: PLANNED/NOT ACTIVE のものを「最新」として参照・依拠してはならない。ACTIVE のみが正典。

### 2.3 正典探索の鉄則【監査HIGH反映・最重要】
- **CCは正典をファイル名で推測・検索してはならない。** 必ず該当系統の `_INDEX.md` を経由してパスを取得する。
- 理由: 段階移行中は新旧命名（`v1_6` と `v1.6`、旧名と `DB-` 新名）が併存し、ファイル名/正規表現依存だと旧版を見落とす。
- 各 `_INDEX.md` は「Active 1件のみ」を保証し、Legacy name / Current name / status / supersedes の対応表を持つ（§3.3）。

---

## 3. 命名規則（名前で全部分かる）

### 3.1 版表記の統一【最優先】
- すべての版は `_vMAJOR.MINOR`（ドット）に統一。例 `_v1.6` `_v4.4` `_v7.0`。
- アンダースコア版（`v1_6`）・略記（`v7`）は次回版上げ時に正規化。版なし正典を禁止。

### 3.2 正典ファイル名（段階移行）
- 形式: `<系統prefix>-<文書名>_<v版>.md`。prefix: `DB-` / `APP-` / `COM-` / `XREF-`。
- **既存正典の一斉改名はしない。次回版上げのタイミングで新命名へ移行。**

### 3.3 新旧混在期間の解決規則【監査HIGH反映】
- 各 `_INDEX.md` に **Legacy name / Current name / status / supersedes** の対応表を必須化。
- **「同一文書の ACTIVE は常に1件のみ」** を保証。旧版は status=SUPERSEDED とし _archive へ退避。
- CCは§2.3の通り、名前でなく_INDEX経由で現行を特定する。

### 3.4 作業物・引渡・アーカイブ
- 作業物: `_CC_<指示番号>_<内容>.<ext>` / `_CC_<番号>_work/` / `_CC_<番号>_backup_<対象>.json`
- 引渡: `_handoff/<系統prefix>-<指示番号>_<対象>/`【監査LOW反映・系統prefix必須】
  - 例 `DB-136_samix` / `APP-MR-MOCK-057_xxx` / `XREF-001_xxx`
- アーカイブ: `_archive/<YYYY-MM-DD>_<種別>/`（元パスをファイル名にエンコードして衝突回避）
- staging中間物: `_staging/<型>/<slug>/`（bundle並列生成・identity確認後 `_patch/` へ昇格）
  - `run_type=staging_parallel_bundlegen` の manifest.conf を持つものが対象。
  - 昇格前（`_staging/` 配下）は apply_insert.sh の委譲・apply 実行の対象外。
  - CC自走可（DB非接触・変換＋bundle生成のみ）。apply_insert.sh委譲は `_patch/` 昇格後。

---

## 4. アーカイブ自動退避規則（現役だけ見える状態の維持）

### 4.1 原則
- 現役フォルダ直下に完了済み作業物を残さない。完了物は退避先へ移し、普段は視界外・検索でのみ辿れる状態にする。

### 4.2 _CC_* 作業物の退避と保持例外【監査HIGH反映・機械判定可能化】
- 退避先: `Research/_cc_archive/{work,backup,reports}/`
- **保持例外は機械判定可能にする**:
  - `Research/_active_manifest.md`（新設）に「現役に残す _CC_*」を明示列挙。
  - これに無い完了済 `_CC_*` は退避可。例外への追加・判断は主査確認。
  - 「最新2世代」等の主観条件は廃し、`_active_manifest.md` 記載の有無で機械的に判定。
- 退避タイミング: 整理サイクル（数指示ごと、または主査指示）。

### 4.3 退避時のリンク切れ対策【監査MED反映】
- 退避対象が**他の現役ドキュメントから参照（相対リンク）されている場合は、退避せず据置**（リンク切れ防止）。
- どうしても退避する場合は、参照元のパス書き換えを同一指示内で行う。判断は主査確認。

### 4.4 正典旧版の退避
- 正典が版上げされたら旧版は即 `_archive/<日付>_superseded/` へ退避。現役の_docs/に旧版を残さない。

### 4.5 退避の鉄則 / Archiveの読み取り【監査MED補足反映】
- **物理DELETE 永久禁止。退避＝mv。** 消さず検索で辿れる場所へ移すだけ。
- 退避前に premove manifest（パス・md5・サイズ・退避先）を記録。移動先同名md5不一致なら停止。
- **Archive配下は現行正典として読まない。** ただし、明示された復元・監査・照合タスクでは読んでよい。

---

## 5. 外部AI引渡ルール（GPT/Gemini）【監査HIGH反映・系統別】

### 5.1 引渡先は系統別【Research固定の抜け穴を解消】
- **DB系** = `Research/_handoff/DB-<番号>_<対象>/`
- **実装系** = `App/_handoff/APP-<番号>_<対象>/`
- **横断** = `_handoff/XREF-<番号>_<対象>/`（プロジェクトルート直下）
- 実装系レビュー・Mock監査をResearch配下に入れない。

### 5.2 パッケージ構造
```
<handoff>/<prefix>-<番号>_<対象>/
  OUT_gemini/  OUT_gpt/          … 各AIへ渡すbrief + 添付
  IN_gemini_findings.{tsv|md}    … 回答（受領後保存。未受領は *.ABSENT.md）
  IN_gpt_findings.{tsv|md}       … 同上【監査LOW反映・md許容】
  MANIFEST.md                    … 渡した物・受領状況・findings形式・突合・マーカー反映
```

### 5.3 findings保存の必須化【139-140教訓・致命的手続き欠陥の再発防止】
- 外部AIのfindingsは**必ずローカル保存→part_id突合→突合結果をマーカー反映**。
- brief（送信指示書）だけ保存してfindingsを保存し損ねる事故を、MANIFESTの受領欄で機械検出。
- ゲート是正指示書は「IN_*_findings を読込→突合→マーカー反映」を必ず明示。
- findings形式は tsv/md どちらも可。MANIFESTに形式を記録。

---

## 6. 正典更新フロー（作業のたびに最新化し、AIへ反映）

本憲章§6が更新フローの**上位定義**。既存 `AI_Knowledge_Update_Rules` は本§の**配布作業の詳細手順**に格下げ【監査HIGH反映・二重化解消】。

```
① 作業中に正典更新の必要が生じる
② 主査が該当系統の正典mdを更新し、版を上げる（DB→Research/_docs / 実装→App/_docs / 共通→_CURRENT_RULES）
③ 該当系統の _INDEX.md を更新（Active 1件保証・対応表更新）
④ 上位 SoT索引を更新（各_INDEXへのポインタ）
⑤ 旧版を _archive/<日付>_superseded/ へ退避（DELETE禁止）
⑥ 配布キュー(_distribution_queue.md)に「要再配布」記録
⑦ 運営者が該当AIプロジェクト(Claude/Gemini のDB系・実装系)に最新版を貼り直し、旧版を外す
⑧ 反映完了したら配布キューのフラグをクリア
```

### 6.1 配布版台帳 + 作業開始ゲート【監査HIGH反映・両AI一致の最重要指摘】
- `_CURRENT_RULES/_distribution_queue.md` に、各正典の「ディスク最新版」と「各AIプロジェクト最終配布版」を1表で記録。版上げで「要再配布」を立て、反映後クリア。
- **作業開始ストップガード**: 対象系統の配布キューに**未反映フラグがある場合、CCは正典更新系・WRITE系タスクを主査指示なしに進めてはならない**。冒頭で未反映を警告し、READ ONLY調査タスクのみ許可。
- 外部AIへ渡すbriefにも「配布未反映あり/なし」を記載。

### 6.2 現在の同期ズレ（154で判明・155で解消開始）
| 正典 | AI配布版 | ディスク最新(ACTIVE) | 状態 |
|---|---|---|---|
| Knowledge | v1.5 | v1.6 | 要再配布 |
| Rules | v4.4_draft | v4.4_final_rev2 | 要再配布 |
| Category | v1.2 | v1.3 | 要再配布 |
| Token | v6 | v7 | 要再配布 |
→ 憲章発効後、最初の反映サイクルで全AIプロジェクトを最新へ更新。

---

## 7. CC自律実行の規律と権限境界

### 7.0 owner判断カテゴリ（DOC-SYNC-001 / 2026-06-24確定）【P1・最頻参照】

CCが自律実行可能な範囲を原則として定義する。以下**3カテゴリのみ** owner 判断に戻す。
それ以外はすべて CC 自走可（§7.1 の列挙はこの原則の具体例）。

1. **Production接触**（ojvnisnbguxrkbptafze 等・Research 外 project）
2. **DB WRITE / DDL / GRANT / DELETE 系**（不可逆操作・commit 確認語 1 回必須）
3. **identityが危ない判断**（他社混入 / メーカー同一性不明 / 公式URL誤帰属疑い）

これ以外（lint / dedup / dry-run / bundle生成 / READ診断 / RUN_SUMMARY 等）は CC 自走。
本フレームは §7.1 / §7.2 / §7.3 の詳細列挙と整合する上位集約定義である。

### 7.1 CCが自律実行してよいこと【監査MED反映・対象を限定】
- 本憲章の構造・命名・アーカイブ規則に従った、**`_CC_*` 完了物の退避**（§4.2・_active_manifest基準）
- 外部AI引渡パッケージの `_handoff/` 生成（§5）
- READ ONLY の調査・棚卸し・診断
- ★**正典・入口(00_START_HERE)・_INDEX・App参照パス・manifest類の移動は自律不可**。premove manifest作成までは自律可、移動は主査確認。

### 7.2 必ず主査確認を要すること
- 正典の内容変更（版上げ・本文改訂）= 主査起草案件
- スキーマ変更・DB書込を伴う是正
- 系統判定が曖昧なファイルの移動（迷ったら据置＋報告）
- 指示と実体（_INDEX/マスター台帳）が食い違う場合（**盲従せず据置＋報告**）
- 稼働中アプリ（App V2）の参照パスを壊しうる移動
- 配布キューに未反映がある状態でのWRITE系タスク（§6.1ゲート）
- DB WRITE commit は「確認語（confirm-token）1回」を owner が明示入力する。
  フォーマット正典: `APPLY <bundle_id> <expected> COMMIT`（`_tools/capsules/README_capsule.md` §確認語 参照）。
  フォーマット変更は GPT監査要件⑥の更新と同時に行う。
  週次merge等で別フォーマット（`CONFIRM-WEEKLY-MERGE-*`等）を使う場合は当該capsuleに明示する。

### 7.3 不変の鉄則
- 物理DELETE 永久禁止（inactive/NULL/mv で代替）
- 書込前 backup 必須・上書き禁止
- **異常検知・検証エラー時、CCは自律復元を行わず、変更を据え置いて主査に報告・判断を仰ぐ**【監査LOW反映】
- `git add .` 禁止（pathspec限定）・push禁止（主査許可時のみ）
- 作業dir確認・指示番号ガード
- DB = Research（ualrrrsmhlnpwfqrrsjc）。**Production接触は、明示指示・目的・対象テーブル・backup方針が揃う場合のみ例外**【監査LOW反映】
- 推測で正典を書かない（公式根拠 > HOLD > 空欄 > 推測 > 偽値）

### 7.4 owner/service_role 使用制限【⑥・AUDIT-001 follow-2 基準】
owner（postgres）/ service_role を用いた DB アクセスは、以下 (a)〜(g) を全て満たす場合のみ許可する。違反は即時中止・主査報告。
(a) 目的は READ ONLY に限る。WRITE は本条の対象外（別途 §7.2 の主査確認 + freeze 状態に従う）。
(b) 接続時に transaction_read_only=on を強制し、その事実を記録する。
(c) ref guard を実行し Research（ualrrrsmhlnpwfqrrsjc）であることを確認。Production（ojvnisnbguxrkbptafze）接触は禁止。
(d) 実行は SELECT 限定。INSERT/UPDATE/DELETE/DDL/RPC/GRANT/REVOKE を全面禁止。
(e) トランザクションは明示 rollback で終える。
(f) DSN・PW 実値はチャット・ログ・成果物に出力しない。供給は Option A（/tmp に chmod 600 で書出し）、終了時 trap 削除。
(g) current_user・スコープ・時刻を監査記録として残す。
- GRANT/REVOKE は WRITE 系であり freeze 下では実行不可。
- 本条の基準適合の先行事例 = AUDIT-001 follow-2（2026-06-16）。
- 適用範囲は DB系 owner/service_role read に限定。App側 service_role/delete UI/auth 不在は別トラック（Track A）。

---

## 8. この憲章の運用

- CCは新規セッションで、まず本憲章を読み、構造・命名・アーカイブ・引渡・規律・**配布キュー状態**を把握する。
- 主査は指示書で「憲章§Nに従って実行せよ」と参照でき、毎回細部を書かずに済む。
- 憲章自体の更新は主査が版を上げて差し替え、旧版は `_archive/` 退避。
- 本憲章は `_CURRENT_RULES/SoT_Index` の最上位にリンクされる。
- 既存運用ルール文書は本憲章への整合（ポインタ化/格下げ）を完了する（155）。

---

## 10. 配布・運用の恒久ルール【AUDIT-001 / GPT条件付きGO反映】
### 10.1 配布正本の一元化
- 配布の唯一の正本は `_CURRENT_RULES/_DISTRIBUTION/`。`_common`（全AI共通）/`_db`（DB系のみ）で構成。
- 正典更新時、主査/CCは本フォルダを必ず同時最新化する。旧スナップショットは `_archive/` へ退避し `README_DO_NOT_DISTRIBUTE.md` で再配布禁止を明示。
### 10.2 配布方式
- 差分判断せず、該当セットを全消し→丸ごと再投入する。差し替え対象はプロジェクトKnowledgeのみ。Custom Instructions/Instructions/説明欄/手順欄は触らない。
- 投入後チェック必須: 旧Charter残存なし / _common入り / DB系は_db入り / App系に過剰投入なし / Knowledge旧版なし。
### 10.3 配布先（確定6プロジェクト = 3AI × 2系統）
- MyRIG系（App / Claude・GPT・Gemini）= _common
- RC Database Research系（DB / Claude・GPT・Gemini）= _common + _db
- CC / Codex は配布対象外（指示文を都度渡す）。App系はDB仕様タスク時のみ主査判断で_db追加可。
### 10.4 正典の優先順位
- 正典4点（Charter / Schema / Rules / Category）＞ Knowledge（運用補助）。矛盾時は正典4点を優先。
### 10.5 AI役割と安全装置
- 3AI（Claude/GPT/Gemini）の恒久固定役割は撤廃。タスク単位で主査・独立監査・批判ゲート・Web調査等を依頼文で明示指定する（監査モード自体は存置）。
- 不変の安全装置: DB書込・ファイル操作・大量変更はCC経由 / 最終判断はオーナー / AI間多数決で決めない。三層構造（AI判断→CC実行→オーナー承認）を維持。

---

## 9. 版履歴
| Version | Date | 変更 |
|---|---|---|
| v1.0 | 2026-06-13 | 初版。2系統分離・命名・アーカイブ・引渡・更新フロー・自律規律を統合定義。 |
| v1.1 | 2026-06-13 | GPT/Gemini独立監査のHIGH/MED/LOW反映。①既存文書への優先・役割上書き明記 ②現行/PLANNED分離（未作成版の正典化禁止）③正典探索を_INDEX経由に固定（名前推測禁止）④保持例外を_active_manifestで機械判定化 ⑤配布キュー未反映時の作業開始ストップガード ⑥_handoff系統別分離 ⑦Target/Current Phase管理 ⑧退避リンク切れ据置 ⑨XREF配置・findings md許容・異常時非自律復元・Production例外条件。発効前微修正: GPT再監査MED1（実装系正典の物理位置の移行期例外と判定経路分離を§2.1に明記）。MED2（155自身のストップガード例外）は155指示書で対応。 |
| v1.2 | 2026-06-16 | §7.4 owner/service_role使用制限を追加（⑥/AUDIT-001 follow-2基準）。 |
| v1.3 | 2026-06-16 | 配布一元化・全消し再投入・3AI役割固定撤廃(監査タスクは存置)・CC実行レーン/オーナー最終判断の不変化を条文化。 |
| v1.4 | 2026-06-25 | §3.4にstaging中間物定義追加（ルール①）。§7.0にowner判断3カテゴリ集約フレーム新設（ルール③/DOC-SYNC-001）。§7.2にDB WRITE確認語フォーマット明示（ルール④）。 |
