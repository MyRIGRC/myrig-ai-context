# MyRIG CURRENT

revision: MYRIG-20260821-006
updated: 2026-08-21 16:00 JST

恒久ルールは MyRIG_CORE.md を参照。
このファイルは索引＋差分。詳細仕様全文は含まない。

## 現在地

フェーズ: モックアップ確認中（Next.js実装前）
PC mockup: 39ページ、SoT CSS/JS構成、概ね完成
Mobile mockup: 約55ページ、確認ダッシュボードで管理中
確認状況: 確定0 / PC版のみ6 / 要確認49 / 未着手0（2026-08-20時点）
確認ダッシュボード: デプロイ済（新規スレッド開始時に必ずURL・所在を確認すること）
Mockup shell: v0.5 / Home r14 / P22-C35

ナレッジ運用: 2026-08-21 CORE+CURRENT方式へ移行。
本repo（myrig-ai-context）が全AI共通の正本。
旧Claude Knowledge 41本は本repoへ分類移送済み（下記Task Routing参照）。

## Active Overrides

2026-08-19
カテゴリ色はToken Note v7ではなくv8裁定を採用。
v8: RIG=黄 / PARTS=赤 / LOG=スチールブルー。モバイル適用済み。
Token Note v7文書（緑/紫/橙）は失効し _archive へ退役。
正典は docs/design/color-token-v8.md。

2026-08-21
検索の現行確定仕様は docs/search/search-page-plan-v2.md。
_proposals/ の検索4文書（system-design-v3.1 / blueprint-v2 /
results-ux-v1 / contract-v1）はPROPOSAL。正典扱いしない。
ただし search-contract-v1 は主要7件イタヤ裁定済み・残3件照会中。

2026-07-30
FEED文法はモバイル基準（おすすめ/フォロー中タブ）。
PC版を差し替える（_decisions/p22-c21 参照）。実装待ち。

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

- design-nogo-list NG-7 の既知誤記修正（一般化は誤りと判明済み、本文未修正）
- Page Role Matrix: 冒頭注記を本文へ統合し版上げ
- Auth Guard: 冒頭注記を本文へ統合し版上げ
- Mobile Contract v0.5: search-page-plan v1参照が残存。v2へ更新
- pc-mobile-spec-inheritance v1.1: 「無限スクロールはFeedのみ」が
  Search Plan v2（検索種別タブ無限スクロール）と矛盾
- App Schema v1.6: App所有領域とResearch Master領域の責務分離改訂
- Operations Charter: v1.4/v1.5とも現状と乖離。CORE.mdが実質後継。
  Charter正式廃止はGPTクロスチェック後に裁定（下記・未決定事項）

## ローカル正典の是正項目（棚卸し 2026-08-21）

- Charter v1.5 が Research/_docs/ に誤配置（自己申告パスと不一致）
- Research/_docs/_INDEX.md: Knowledge v1.9記載/実体v1.10、Schema v1.1記載/実体v1.2
- 棚卸し時「37件受領/列挙39行」の差異 → 実体は41本と判明（検索系2本が後日追加分）。解消

## 解決済み（記録）

2026-08-21
App/MOKUP/myrig_pc_Ver3/docs/ 消失問題を解決（P22-C28再編の誤退避。85ファイル復帰、
.vercelignore に docs 追加）。

2026-08-21
CURRENT同期方式確定: 本repo → push → Claude Project GitHub Integration Sync now。
revision 003/004 で追従テスト済み。web_fetch方式は不採用（ツール側キャッシュ）。

2026-08-21
Knowledge 24件ローカル未保存の原因: Coworkは project_write で
Knowledge にのみ書く仕様。今後の生成物は本repoへ書く。

## Task Routing

このrepoの構成と参照ルール:

_AI/            CORE / CURRENT（常時読む。Knowledge接続対象）
docs/           ACTIVE正典（Knowledge接続対象）
  schema/       DB: myrig_db_schema_v1_6 / cross-ref-v4 / db-schema-answers-v1
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
