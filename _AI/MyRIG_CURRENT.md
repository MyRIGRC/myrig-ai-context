# MyRIG CURRENT

revision: MYRIG-20260821-005
updated: 2026-08-21 15:30 JST

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

## 現在地

フェーズ: モックアップ確認中（Next.js実装前）
PC mockup: 39ページ、SoT CSS/JS構成、概ね完成
Mobile mockup: 約55ページ、確認ダッシュボードで管理中
確認状況: 確定0 / PC版のみ6 / 要確認49 / 未着手0（2026-08-20時点）
確認ダッシュボード: デプロイ済（新規スレッド開始時に必ずURL・所在を確認すること）
Mockup shell: v0.5 / Home r14 / P22-C28

## Active Overrides

2026-08-19
カテゴリ色はToken Note v7ではなくv8裁定を採用。
v8: RIG=黄 / PARTS=赤 / LOG=スチールブルー。モバイル42ページ適用済み。
Token Note v7文書（緑/紫/橙）は失効。正典への正式反映は未完。

2026-08-21
検索の現行確定仕様は search-page-plan-v2。
claude_search-system-design-v3.1 はPROPOSAL（未裁定）。正典扱いしない。

## HOLD

size_class
7/30「13値で確定」と8/20「これから審議」が矛盾。後者を尊重しHOLD。

log_type
App Schema v1.6は4種（maintenance/run/custom/memo）。
後続記録に5種（+setting）の記述あり。cross-ref v4は4種。未解消。

aliases
App Schema v1.6は parts_masters.aliases TEXT[]。
db-schema-answers-v1は master_aliases が正本としJOIN利用。
Research現行Schema実体の確認待ち。

event_tags
8値がResearch側で未確定。次回weekly gate優先度2。

## Pending Canonical Updates

正典側に未反映の確定事項。

- Token Note v7 → v8正式版の作成
- design-nogo-list NG-7 の既知誤記修正（一般化は誤りと判明済み、本文未修正）
- Page Role Matrix: 冒頭注記（おすすめ/フォロー中）を本文へ統合し版上げ
- Auth Guard: 冒頭注記（Mobile Contract v0.5優先）を本文へ統合し版上げ
- Mobile Contract v0.5: search-page-plan v1参照が残存。v2へ更新
- pc-mobile-spec-inheritance v1.1: 「無限スクロールはFeedのみ」が
  Search Plan v2（検索種別タブ無限スクロール）と矛盾
- App Schema v1.6: App所有領域とResearch Master領域を1冊で定義している。
  責務分離した改訂が必要
- Operations Charter v1.4: 現状認識が旧世代（Research_DB_Schema未作成等）

## 棚卸しで判明した是正項目（2026-08-21）

- Charter v1.5 が Research/_docs/ に配置されているが、本文の自己申告パスと不一致。
  _CURRENT_RULES/ への移動またはパス記述の訂正が必要
- Research/_docs/_INDEX.md: Knowledge v1.9記載/実体v1.10、Schema v1.1記載/実体v1.2。
  いずれも1版遅れ
- Claude Project Knowledge側: Charter v1.4 → ローカルv1.5へ差し替え要
- Claude Project Knowledge側: cross-ref-category-names-v3 は superseded、v4が最新
- 棚卸し時「37件受領/列挙39行」の差異が未特定

## 解決済み（記録）

2026-08-21
App/MOKUP/myrig_pc_Ver3/docs/ 消失問題を解決。
原因: 2026-08-20 P22-C28再編時、PC root重複退避の際に docs/ を巻き込んだ。
docs/ は複製ではなく唯一の実体だった。
対応: 正典パスへ復帰（85ファイル）、git追跡復帰、.vercelignore に docs 追加。

2026-08-21
Knowledge 37本中24件がローカル未保存だった原因判明。
Coworkは project_write で claude.ai Knowledge にのみ書き込み、
Macローカルには落ちない仕様。事故ではない。

## Task Routing

CURRENT同期: myrig-ai-context repo → push → Claude Project で Sync now
詳細仕様の参照先は正典棚卸し完了後に追記予定。

## 未決定事項

Cowork生成物（p22系・search系等）の置き場所。
docs/ 直下に混ぜると正典と検討資料が区別できなくなる。
候補: docs/claude/ に分けるか、docs/ の外（_work/ 等）に置くか。
決定後、CoworkがKnowledgeとローカル両方へ書く運用へ変更可能。
