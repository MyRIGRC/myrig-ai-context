# MyRIG SoT Decision Record — MR-ROOT-003B

作成日: 2026-05-19
作業: MR-ROOT-003B
ステータス: **SoT決定記録 / 物理整理未実行**

> ⚠️ 本ファイルは MR-ROOT-003A の差分レビュー結果をもとに確定した決定記録。
> ファイル移動・削除・AIナレッジ差し替えはまだ実行していない。物理整理は MR-ROOT-004 以降。

---

## 1. 目的

- MR-ROOT-003A のレビュー結果をもとに、Root / Mock / AI ナレッジに関わる SoT を確定記録する
- この文書は **決定記録** であり、ファイル移動・削除・配布物更新はまだ行わない
- 物理整理は **MR-ROOT-004 / MR-ROOT-005** で実施
- AI ナレッジ再生成は **MR-ROOT-006** で実施
- Claude Code は SoT 判断をしない。最終判断はイタヤ / GPT主査 / Claude監査が行った

---

## 2. 決定サマリー

| 項目 | 決定 | 根拠 | 後続タスク |
|---|---|---|---|
| Token Note v6 | **MOKUP/docs版をSoT** | MOKUP版のみ幅系トークン正典・最新Status Badge表記が反映済み（commit `1f0f7d3`） | MR-ROOT-006で配布版を差し替え |
| Category Structure v1.2 | **重要定義ファイル直下版をSoT** | 直下版 / Claude / GPT / Gemini配布版がsha256完全一致 | MR-ROOT-004で`_CURRENT_RULES`候補 |
| SKILL.md | **現行版をそのまま採用しない** | 現行運用14項目が不足。MR-ROOT-001以前の旧世代内容 | MR-ROOT-004で更新 / 統合 |
| Claude_Code指示ルール.md | **現行版をそのまま採用しない** | 現行運用14項目のうち13項目不足。参照先 `workflow-rules.md` が未作成 | MR-ROOT-004で更新 / 統合 |
| Sync Workflow | **現行版をそのまま採用しない** | 重要定義ファイル原本前提で現方針と乖離。配布リストに不適切なファイルあり | MR-ROOT-006で再設計 |
| _for_AI_projects 現行配布 | **旧版含む / 全体を更新対象** | Token Note旧版 / Gemini未配布 / AI別分割配布を廃止 | MR-ROOT-006で全AI共通ナレッジへ再生成 |

---

## 3. MyRIG_Token_Correction_Note_v6.md 決定

### 確定 SoT

```
MOKUP/myrig_pc_Ver3/docs/MyRIG_Token_Correction_Note_v6.md
  bytes : 8,036
  lines : 237
  mtime : 2026-05-18 18:56
  sha256: cb6e83d6
  git   : tracked (commit 1f0f7d3 / MR-MOCK-028)
```

### 旧版扱い（差し替え対象 / 物理移動は MR-ROOT-005）

```
重要定義ファイル/MyRIG_Token_Correction_Note_v6.md
重要定義ファイル/_for_AI_projects/Claude/MyRIG_Token_Correction_Note_v6.md
重要定義ファイル/_for_AI_projects/GPT/MyRIG_Token_Correction_Note_v6.md
  bytes : 7,527 (全3件sha256一致 bde7fb13)
  mtime : 2026-05-09
```

### Claude.ai / GPT Project / Gemini Project のナレッジについて

> ⚠️ Claude.ai プロジェクトナレッジ（`/mnt/project/` 経由で Claude が参照している版）も、
> `重要定義ファイル/` 版 / `_for_AI_projects/Claude` 版 / `_for_AI_projects/GPT` 版と
> **同じ旧版（7,527B / 2026-05-09）である可能性が高い。**

- MR-ROOT-003A で確認: MOKUP/docs 版のみが「幅系トークン正典」/ 最新 Status Badge 表記を含む
- したがって、Claude.ai / GPT Project / Gemini Project の既存ナレッジも **MR-ROOT-006 で最新版ナレッジへ置換する対象に含める**
- 実際の差し替え（旧ファイル削除・新ファイルアップロード）は **イタヤが各 AI 管理画面で手動実施**

### Gemini への Token Note 配布

- 現在 `_for_AI_projects/Gemini/` に Token Note v6 は存在しない
- 旧 Sync Workflow では Gemini は Research 寄り運用として Token Note を配布していなかった可能性がある
- **MR-ROOT-006 で Gemini への配布要否を再判断する**（共通ナレッジ化に伴い全 AI に配布する方向を基本とする）

### MOKUP 版が SoT である根拠

| 差分内容 | MOKUP版 | 重要定義ファイル版 |
|---|---|---|
| 「幅系トークン正典」セクション | **あり**（+8行） | なし |
| `content-width-policy-v2.md` への参照 | あり | なし |
| `.gc__status--prerun` | **廃止済み** | まだ存在 |
| `.gc__status--building` コメント | **「ビルド中」** | 「組み立て中」 |
| `.gc__status--active` コメント | **「運用中」** | 「アクティブ」 |
| git 追跡 | **tracked** (commit `1f0f7d3`) | git 外 |

---

## 4. MyRIG_Category_Structure_v1.2.md 決定

### 確定 SoT

```
重要定義ファイル/MyRIG_Category_Structure_v1.2.md
  bytes : 21,093
  lines : 574
  mtime : 2026-05-09 10:49
  sha256: 25d123d4
  git   : git外（MyRIG ルートは非 git）
```

### AI 配布版（同一コピー）

```
_for_AI_projects/Claude/MyRIG_Category_Structure_v1.2.md   sha256: 25d123d4 ✅
_for_AI_projects/GPT/MyRIG_Category_Structure_v1.2.md      sha256: 25d123d4 ✅
_for_AI_projects/Gemini/MyRIG_Category_Structure_v1.2.md   sha256: 25d123d4 ✅
```

4ファイルすべて **sha256 完全一致**。内容は同一コピーとして扱う。

### 旧版（Archive 扱い確定）

```
重要定義ファイル/_archive/MyRIG_Category_Structure_v1.1.md
  bytes : 20,497 / sha256: 4a869499（v1.2 と異なる）
```

### v1_2 表記ゆれについて

`MyRIG_Category_Structure_v1_2.md`（アンダースコア表記）のファイルは実際には存在しない。
以前の audit で記録した「表記ゆれ懸念」は解消済み。

### 補足

- MOKUP docs 内に Category Structure のコピーは存在しない（Token Note v6 と異なりコピー問題なし）
- 内容の精読（RC カテゴリ構造が現行 App / Research / Mock に対して正しいか）は未実施
- **MR-ROOT-004 で `_CURRENT_RULES/` へ移送する際に内容確認を推奨**

---

## 5. SKILL.md 決定

### 現行ファイル

```
重要定義ファイル/SKILL.md
  bytes : 2,954 / lines: 67 / mtime: 2026-05-09 / sha256: 61e81973
  _for_AI_projects/Claude/SKILL.md: sha256一致（同一コピー）
  _for_AI_projects/GPT/SKILL.md: 存在しない（Sync Workflow でClaude専用と定義）
```

### MR-ROOT-003A で確認した不足項目（14項目すべて「なし」）

1. GPT主査 / Claude監査役 / Claude Code実行担当の役割定義
2. 最終判断はイタヤ
3. SoT / Template / Catalog / docs 確認前に設計判断しない
4. docs 全体を無差別に正典扱いしない
5. sot-inventory.md / root-inventory.md / 00_START_HERE を優先
6. SoT 編集は明示指示なし禁止
7. git add -A 禁止
8. commit / push 可否を明示
9. 指示番号必須
10. 推奨モデル / 思考コース明記
11. 完了報告は 1 コードブロック
12. 最後の行に「指示番号: xxx【完了】」
13. 認証情報ファイルの中身閲覧禁止
14. docs/workflow-rules.md（参照先）が未作成

### 決定

- **現行 SKILL.md をそのまま `_CURRENT_RULES/` へ採用しない**
- 現行 SKILL.md は「Claude 設計パートナーとしての行動指針」として一部有効だが、現行 Claude Code 運用ルールが未反映
- **MR-ROOT-004 で更新するか、新規 `MyRIG_Operating_Rules_Current.md` に統合する**
- 旧 SKILL.md は更新素材として扱う（削除しない）

---

## 6. Claude_Code指示ルール.md 決定

### 現行ファイル

```
重要定義ファイル/Claude_Code指示ルール.md
  bytes : 592 / lines: 10 / mtime: 2026-05-09 / sha256: fb20a0a3
  _for_AI_projects/Claude/Claude_Code指示ルール.md: sha256一致（同一コピー）
```

### MR-ROOT-003A で確認した不足（14項目中13項目が「なし」）

- 「docs/ ディレクトリ全ファイルを読む」のみ記述あり（1項目）
- 参照先 `docs/workflow-rules.md` は**未作成**（root-inventory.md §10 / root-cleanup-plan §7-B 参照）
- 「docs 全体を無差別に読む」という現行の指示方向は、`sot-inventory.md` / `root-inventory.md` を優先する現方針と**逆方向**

### 決定

- **現行 Claude_Code指示ルール.md をそのまま採用しない**
- **MR-ROOT-004 で現行運用に合わせて更新、または `MyRIG_Operating_Rules_Current.md` へ統合する**
- 旧版は更新素材扱い（削除しない）

---

## 7. MyRIG_Sync_Workflow_Rules.md 決定

### 現行ファイル

```
重要定義ファイル/MyRIG_Sync_Workflow_Rules.md
  bytes : 7,684 / lines: 158 / mtime: 2026-05-09 / sha256: ac56cfc7
  配布先: GPT / Gemini（Claude は管理主体のため配布外）
```

### MR-ROOT-003A で確認した不足・乖離

| 確認項目 | 問題 |
|---|---|
| `_CURRENT_RULES/` の概念 | なし（MR-ROOT-002で設計された概念が未反映） |
| MOKUP docs 版 Token Note 優先 | なし（重要定義ファイルを原本と定義 → 現実と乖離） |
| Research専用ファイルを Mock 作業に混ぜない | なし（全AI に Research_Workflow_Rules を配布） |
| AI ナレッジは入口・参照先のみ | なし（仕様ファイルを丸ごと配布する設計） |
| 配布リストに要確認ファイル4件 | `myrig_workflow_rules.md` / `App_Ready_Design_Rules.md` / `Research_Workflow_Rules.md` / `Claude_Code_MasterAdmin_Spec.md` |

### 決定

- **現行 Sync Workflow をそのまま `_CURRENT_RULES/` へ採用しない**
- **MR-ROOT-006 で AI ナレッジ再生成方針に合わせて再設計する**
- 旧 Sync Workflow は更新素材扱い（削除しない）

---

## 8. _for_AI_projects 更新方針

### 基本方針: AI 別ナレッジ廃止 → 全AI共通ナレッジへ

**決定:**

- 今後、GPT / Claude / Gemini に役割別の異なるナレッジファイルを入れない
- **全 AI に対して、同一の最新版ナレッジセット（Common Knowledge Pack）を配布する**
- AI ごとの役割差（GPT主査 / Claude監査 / Claude Code実行 / Gemini Deep Research 等）は、恒久ナレッジではなく **その都度の作業指示・プロンプトで制御する**
- 旧 Sync Workflow の「Claude用9ファイル / GPT用7ファイル / Gemini用5ファイル」という**分割配布は廃止候補**
- **MR-ROOT-006 で共通ナレッジセットを 1 つ作成し、GPT / Claude / Gemini に同じ内容で配布する方針に変更する**

### 更新対象

| 対象 | 内容 |
|---|---|
| Common Knowledge Pack（全AI共通） | 新規作成（MR-ROOT-006で確定） |
| Claude.ai Project Knowledge | 手動差し替え（イタヤが管理画面で実施） |
| GPT Project Knowledge | 手動差し替え（イタヤが管理画面で実施） |
| Gemini Project Knowledge | 使う場合は同一内容を投入 |
| ローカル `_for_AI_projects/` | AI別フォルダを維持する場合でも中身を同一化 / または `_for_AI_projects/Common/` に一本化 |

### Common Knowledge Pack ファイル名候補（MR-ROOT-006で確定）

```
MyRIG_AI_Project_Knowledge_Current.md
MyRIG_Operating_Rules_Current.md
MyRIG_Source_of_Truth_Index_Current.md
```

実際のファイル構成は **MR-ROOT-006 で確定する**。

### AIナレッジ更新が必要な場面

- SoT の正本が変わったとき
- 重要定義ファイル / MOKUP docs / myrig-app docs の優先順位が変わったとき
- GPT主査 / Claude監査 / Claude Code実行など運用体制が変わったとき
- 古いナレッジが実害を出したとき
- Archive / superseded / DO_NOT_USE を誤読するリスクが増えたとき

### ナレッジ更新の運用ルール

- GPT または Claude がナレッジ更新の必要性に気づいた場合、**勝手にナレッジを更新せず**、Claude Code 向けにナレッジ生成・差し替え用の指示書を作成する
- Claude Code はその指示書に基づき、共通ナレッジファイルを生成する
- **最終的な GPT / Claude / Gemini へのアップロード・差し替えはイタヤが行う**

---

## 9. _CURRENT_RULES への移送方針

### 現時点でそのまま移送してよいもの

| ファイル | 移送先候補 | 備考 |
|---|---|---|
| `MyRIG_Category_Structure_v1.2.md` | `_CURRENT_RULES/MyRIG_Category_Structure_v1.2.md` | sha256一致確認済み / 内容精読を推奨 |

### 更新後に移送する候補

| ファイル | 更新方針 | 移送先候補 |
|---|---|---|
| `SKILL.md` | MR-ROOT-004 で更新、または統合 | `_CURRENT_RULES/SKILL.md` または `MyRIG_Operating_Rules_Current.md` |
| `Claude_Code指示ルール.md` | MR-ROOT-004 で更新、または統合 | `_CURRENT_RULES/Claude_Code指示ルール.md` または `MyRIG_Operating_Rules_Current.md` |
| `MyRIG_Sync_Workflow_Rules.md` | MR-ROOT-006 で再設計 | `_CURRENT_RULES/` または廃止 |

### 移送しないもの（旧版 / 別領域）

| ファイル | 理由 |
|---|---|
| `重要定義ファイル/MyRIG_Token_Correction_Note_v6.md`（旧版） | MOKUP docs 版が SoT。旧版は Archive へ（MR-ROOT-005） |
| `_for_AI_projects/` 配布版 Token Note | 同上 |
| `Research_Workflow_Rules.md` | Research 専用 → MR-RESEARCH-001 |
| `RC_Master_Research_Rules` 系 | Research 専用 → MR-RESEARCH-001 |

---

## 10. 後続タスクへの影響

### MR-ROOT-004（次候補 / 最優先）

- `_CURRENT_RULES/` フォルダ作成
- `MyRIG_Category_Structure_v1.2.md` を `_CURRENT_RULES/` へ移送
- `SKILL.md` / `Claude_Code指示ルール.md` の更新または `MyRIG_Operating_Rules_Current.md` へ統合
- 入口ファイル 3 件作成（Root全体 / Mock / App）— Research 入口は MR-RESEARCH-001
- 認証情報隔離確認（イタヤ実施）
- **Research 入口は MR-ROOT-004 スコープ外**（MR-RESEARCH-001 で作成）

### MR-ROOT-005（MR-ROOT-004 完了後）

- 旧版 Token Note（重要定義ファイル版 / AI 配布版）を `重要定義ファイル/_archive/` へ移送
- Sessions 旧版を `_ARCHIVE/` へ
- MOKUP zip 群の削除可否をイタヤが判断・実施

### MR-ROOT-006（MR-ROOT-005 完了 / SoT 確定後）

- AI ナレッジ再生成（全 AI 共通ナレッジセット作成）
- `_for_AI_projects/` 差し替え
- Sync Workflow 再設計
- Claude.ai / GPT Project / Gemini Project のナレッジ差し替え（イタヤが手動実施）

### MR-MOCK-030（再開可否 = 再判断）

- **MR-ROOT-003B 完了後、再開可否を再判断する**
- Token Note v6 の SoT は MOKUP/docs 版で確定済みとして扱う
- MR-ROOT-004 で正式な `docs/00_START_HERE.md` が作成されるまでは、**暫定入口ルールとして `docs/sot-decision-record-MR-ROOT-003B.md` を参照する**
- MR-MOCK-030 を実行する指示書では、参照すべき SoT / docs / 禁止ファイルを明示すること

### MR-RESEARCH-001（並走可）

- `Research_Workflow_Rules.md` SoT 確定（10,516B vs 15,209B diff 比較）
- `RC_Master_Research_Rules_v4.4_draft.md` の「draft」表記解除要否
- `Research/00_START_HERE_RESEARCH.md` 作成（MR-ROOT-004 スコープ外）
- Research_backups/ 22 件の選別

---

## 11. まだ実行しないこと

| 作業 | 実施タスク |
|---|---|
| 旧 Token Note の Archive 移動 | MR-ROOT-005 |
| `_CURRENT_RULES/` 作成 | MR-ROOT-004 |
| SKILL.md / Claude_Code指示ルール.md の内容更新 | MR-ROOT-004 |
| `_for_AI_projects/` 差し替え | MR-ROOT-006 |
| AI ナレッジ再生成 | MR-ROOT-006 |
| Sync Workflow 再設計 | MR-ROOT-006 |
| Research 内部整理 | MR-RESEARCH-001 |
| myrig-app 内部整理 | MR-APP-ROOT-001 |
| 認証情報ファイルの移動 | イタヤが直接実施（外部保管） |

---

## 12. 次アクション

1. 本記録を GPT / Claude / イタヤで確認
2. 問題なければ **MR-ROOT-004** へ進む（`_CURRENT_RULES/` 作成 / 入口ファイル / SKILL 更新）
3. **MR-MOCK-030** の再開可否は MR-ROOT-004 完了後に再判断
4. AI ナレッジ差し替えは **MR-ROOT-006** で行う
5. Research_Workflow_Rules の SoT 確定は **MR-RESEARCH-001** で扱う（MR-ROOT-003 スコープ外）
