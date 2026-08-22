# MyRIG CORE

MyRIGプロジェクトの恒久ルール。頻繁には変更しない。
現在地・最新裁定・HOLDは MyRIG_CURRENT.md を参照。

## プロジェクト概要

MyRIG RC（myrigrc.com）はRC車両の愛好家向けプラットフォーム。
ユーザーがRIG（車両）・パーツ・活動ログを登録するガレージ管理＋コミュニティ。
日本語・英語のバイリンガル対応。

最終判断者はイタヤ（板谷広智）。GPT・Geminiはクロスチェック、Claudeが裁定案を出す。

## 責務分離

App領域とResearch領域を混同しない。

- App: user / content系スキーマ、UI、モックアップ、実装
- Research: master系スキーマ（manufacturers / rig_masters / part_masters / rig_categories /
  part_categories / master_aliases 等）、マスターデータ収集ルール、カテゴリ構造
  ※Research側は part_masters（単数形）。App側の parts_masters（複数形）とは同名別義。混同しない

App相談とDB Research作業を同一セッションで混ぜない。
UI設計とDB設計を混同しない。

## DB恒久ルール

- 物理DELETEは禁止。inactive / NULL / archive / mv で代替する
- Production DBへの接触は禁止。明示指示がある場合のみ例外
- 値の優先順位: 公式根拠 > HOLD > 空欄/unknown > 推測 > 偽値
- compatible_platforms を公式根拠なしに具体補完しない
- 不確実なものは採用せず、HOLDまたは後続キューに回す
- 表示事故につながるリンク・画像・entity参照を軽視しない

## 正典の拘束力と、いま求められている姿勢

**現在はモックアップ制作フェーズ。** デザインとサービス概念を議論しながら作る段階であり、
**AIが自由に意見を出せることが価値**。Next.js実装に入れば厳密な正典が要るが、今は違う。

docs/ の各文書は冒頭に拘束力を明示している。

| レベル | 意味 | AIの振る舞い |
|---|---|---|
| **L1** | 恒久ルール・逸脱禁止 | 従う。提案しない |
| **L2** | 現在の確定仕様 | **出発点。より良い案があれば提案する** |

L2に対して、**「既存仕様と異なる」ことだけを理由に案を捨てないこと。**
これは禁止事項ではなく**義務**。正典に書いてあるからという理由で沈黙するのは、
「ガードレールが線路になっている」状態であり、避けたい失敗そのもの。

提案するときは差分を明示し、採用はイタヤ裁定を待つ。

**L1はごく少ない。** design-nogo-list 全体、各文書のセキュリティ節、
DB禁止事項、責務分離、HOLD原則。それ以外は議論してよい。

## HOLD原則

未裁定事項は推測で埋めない。HOLDとして明示し、判断を止める。
「たぶんこうだろう」で仕様を作らない。
矛盾を発見したら進めず報告する。

## 実行レーン分離

- 判断レーン（通常チャット）: 設計判断、仕様検証、整合性確認、文書作成
- 実行レーン（Cowork / Claude Code）: ファイル操作、コミット、実装

DB書込・ファイル操作・大量変更は実行レーンに渡す。
チャット内で「実行済み」と言わない。
commit / push は明示指示がある場合のみ。
git add -A は使わない。常に明示的にファイルを列挙する。

## GitHub複数WRITE経路の競合防止

本repositoryには複数のWRITE経路が存在しうる。

- Cowork / Claude Code:
  ローカルcloneを編集し、git commit / push
- GPT:
  GitHub API / connector経由でrepositoryを直接更新

そのため、WRITE開始前には必ずGitHub mainの最新版との同期を確認する。

### Cowork / Claude Code

ファイル編集前に必ず、

1. working treeに未処理変更がないか確認
2. git fetch
3. local HEAD と origin/main の差分確認
4. 必要ならgit pull

を行う。
origin/mainがローカルより進んでいる状態で、
古いローカル内容を基準に編集・commitしてはならない。
未commit変更等により安全にpullできない場合は、
勝手にmerge/rebaseせず停止して報告する。

### GPT

GitHub WRITE前に必ず、

1. revision.txt
2. _AI/MyRIG_CURRENT.md
3. 書き換える対象ファイル

をGitHub mainから再取得する。
取得済みの古い内容・古いSHAを使ってWRITEしてはならない。
更新APIがSHA不一致・競合を返した場合は、
再取得して勝手に上書きせず停止し、競合を報告する。

### 共通

- WRITE経路にかかわらず、最新mainを確認してから編集する
- 他AIが直前に更新した可能性を常に考慮する
- force push / 強制上書きは禁止
- commit / push / GitHub WRITEはイタヤの明示指示がある場合のみ

## セキュリティ

secret / DSN / API key をチャットに出さない。
これらをCORE.md / CURRENT.md に書かない。

## 回答姿勢

- 結論を先に出す
- 日本語で簡潔に
- 不明な点は推測で埋めず「不明」「要確認」と言う
- 指示が矛盾したら進めず止める
- ユーザーに不要な判断を戻さない
- 複数案を出す場合は最終推奨案を明示する
- 別AIへ貼る文面は完成形をコードブロックで出す

## 検証原則

少数の確認結果や推測から全体を断定しない。
「問題ない」「完了」「全て正常」「存在しない」は十分な根拠がある場合のみ使う。
「見つからない」を「存在しない」と判断しない。
標準機能・公式仕様・既存機能を確認せず独自実装へ進まない。
実装前に影響範囲と副作用を確認する。
作業完了後、自分の結論が間違っている前提で反証確認を1度行う。

## ファイル生成

タイムスタンプは必ずJST(UTC+9)で記録する。
datetime.utcnow() や naive な datetime.now() は使わず、
ZoneInfo("Asia/Tokyo") で明示的にJSTを取得する。
xlsx生成は xlsxwriter を使う（openpyxlはMac Excelで修復警告が出る）。
