# Claude精査結果サマリ — docs/精査 2026-08-21（3AIクロスチェック用）

CURRENT: MYRIG-20260821-006 時点での精査。対象は `myrig-ai-context/docs/` 配下12本＋`_AI/`2本。

---

## docs/design/（color-token-v8.md / design-nogo-list.md）

| # | ファイル | 行 | 内容 | 種別 | 推奨処遇 |
|---|---|---|---|---|---|
| 1 | design-nogo-list.md | 204-212 | NG-7表「操作：中立」が確定事項として断定記載。出典のP22-B19原文（_archive/knowledge-20260821/claude_p22-b19-mobile-v8-rollout.md）では操作色はA/B/C三案の**未裁定**（「次にやること」#1が「イタヤ裁定→実値確定」）。CURRENT.mdにも操作色の確定裁定は存在しない | 誤記（未裁定を確定に一般化） | 修正：NG-7「操作」行を「未裁定（A現状緑維持/B中立ソリッド/C中立アウトライン）」に差し戻し |
| 2 | design-nogo-list.md | 208 | NG-7表にB19原文の但し書き「Bottom Nav中央の『+登録』のみブランド色を残す余地あり」が未反映 | 陳腐化 | 修正：但し書きを本文へ統合 |
| 3 | color-token-v8.md | 71 | 「ブランド色：未裁定」がdesign-nogo-list NG-7の操作色未裁定と同一論点なのに別文書に分散 | 過剰な縛り予備軍 | 統合：一本化 |
| 4 | design-nogo-list.md | 241-321 | 「検査の盲点」「コード衛生上の既知問題」はNGリスト（表現禁止事項）ではなく実装手順ノート。タイトルの趣旨と不一致 | 過剰な縛り（範囲逸脱） | 統合：docs/support/へ切り出し |
| 5 | color-token-v8.md | 34-36 | LOGダーク背景差2.92（基準割れ）が「未裁定」のまま放置。CURRENT.mdのHOLDにも未掲載 | 陳腐化/放置 | HOLD追加 |

**既知の疑い#1（NG-7誤記）：事実だった。** design-nogo-listのNG-7は、P22-B19原文のClaude推奨案（B）を裁定確定として本文の表に組み込んでしまっている。

---

## docs/ui/（page-role-matrix-v1 / auth-guard-spec-v1 / mobile-component-contract-v0.5 / pc-mobile-spec-inheritance-v1.1）

| # | ファイル | 行 | 内容 | 種別 | 推奨処遇 |
|---|---|---|---|---|---|
| 1 | pc-mobile-spec-inheritance-v1.1.md | 78 | G7「無限スクロールはFeedのみ」が、search-page-plan-v2.md（10行目「種別タブ=無限スクロール／#26裁定でページネーション原則廃止」）と矛盾。本書の作成日(07-24)はv2確定(07-23)より後なのに未反映 | 矛盾（陳腐化） | 修正：G7を「Feed（#25）・検索種別タブ（#26）の限定例外」に更新 |
| 2 | page-role-matrix-v1.md | 219-257 | §6 Feed Definition本文（All/Following Feedの旧名称・定義）が#28裁定後も未改訂。冒頭注記1本のみが「旧定義」と警告する構造 | 陳腐化（実害あり） | 修正：本文を「おすすめ/フォロー中」定義に差し替え |
| 3 | mobile-component-contract-v0.5.md | 85 | §3.5「/searchトップの下部構成…はsearch-page-plan v1 §2のまま有効」と、supersededなv1を参照し続けている | 陳腐化 | 修正：参照をv2へ更新 |
| 4 | page-role-matrix-v1.md | 266 | `/ranking`吸収記述の`*-ranking`プリセットが「改訂対象」注記のみで本文据え置き | 陳腐化 | 修正：本文から削除 |
| 5 | pc-mobile-spec-inheritance-v1.1.md | 78 | 「無限スクロールはFeedのみ」の言い切りが今後の検討余地を閉ざす | 過剰な縛り | 修正：「原則ページング。例外は個別裁定で追加可」に緩和 |

**既知の疑い#2（page-role-matrix：冒頭注記と本文の食い違い）：事実だった。**
**既知の疑い#3（auth-guard-spec：冒頭注記と本文の食い違い）：対応は良好。** 本文中に個別インライン注記あり（108行目・128行目）。ただし5文言グループの実体はmobile-component-contract-v0.5 §3.7にのみ存在し参照が分散。
**既知の疑い#4（mobile-component-contract：失効済みv1参照）：事実だった。**
**既知の疑い#5（無限スクロール矛盾）：事実だった。**

統合の余地：Feed仕様（#28裁定）がpage-role-matrix・pc-mobile-spec-inheritanceに断片的に残置。1箇所へ一本化推奨。

---

## docs/schema/（myrig_db_schema_v1_6 / cross-ref-category-names-v4 / db-schema-answers-v1）

| # | ファイル | 行 | 内容 | 種別 | 推奨処遇 |
|---|---|---|---|---|---|
| 1 | myrig_db_schema_v1_6.md | 56, 109 | `rig_type` CHECK制約が`('rc','mini4wd','miniz','drone','airplane','boat')`のまま。db-schema-answers-v1.md 83行目で「App側v1.6のこの値は**全面的に旧**」と明言済み（正は`rc-car`/`mini4wd`/`drone-fpv`/`rc-airplane`/`rc-boat`の5値）。**CURRENT.mdにも未計上** | 陳腐化（未計上・重大） | 修正：CHECK制約を5値へ更新 |
| 2 | myrig_db_schema_v1_6.md | 220-236 | `rig_masters`に`size_class`列が存在しない（db-schema-answers-v1.md Q1「App側に無いのは欠落」） | 欠落 | 修正：列追加 |
| 3 | myrig_db_schema_v1_6.md | 234 | `rig_masters.platform`が単一TEXT列のまま。裁定は`platform_slug`/`platform_name`/`platform_name_ja`への分離 | 陳腐化 | 修正：列分割 |
| 4 | myrig_db_schema_v1_6.md | 全体 | `power_source`列が存在しない（裁定「撤去は誤り。データ層は必ず持つ」） | 欠落 | 修正：列追加 |
| 5 | myrig_db_schema_v1_6.md / db-schema-answers-v1.md | — | rig_masters/parts_mastersの列定義がApp側文書とResearch裁定文書の両方に存在し、後者が前者を広範囲に上書きしているのに未統合 | 矛盾 | 統合：Research領域はdb-schema-answers-v1.mdを正とし、v1.6側は参照に差し替え |

**既知の疑い#6（App所有スキーマとResearch Masterスキーマの同居）：事実だった。**
**既知の疑い#7（log_type 4値/5値）：事実、根が深い。** cross-ref-category-names-v4.md（2026-08-20更新版）は「4値・確定・App管轄」と明記する一方、`_proposals/db-research-inquiry-spec-data-v1.md`（**2026-08-21起票・未回答の照会中**）では「2026-07-28裁定・App UI実装ともに5値」と記載。cross-ref-v4の「4値確定」自体の正しさが、イタヤ・Claude自身によって8/21付で疑問視され照会に出されている状態。
**既知の疑い#8（size_class 13値確定/審議中）：事実だった。** 同じ照会文書D節に「2026-07-30：13値確定」「2026-08-20：審議中」の2回答が並記され、8/21付で正式照会中（未回答）。
**既知の疑い#9（aliases: parts_masters.aliases vs master_aliases）：断定不可、HOLD継続が妥当。** db-schema-answers-v1.mdはrig_mastersについてのみ「master_aliasesが正本」と明言。parts_masters.aliasesを名指しで廃止/存置判定した記述はどちらの文書にもなし。

---

## docs/support/（App_Ready_Design_Rules / implementation_checklist）＋ docs/search/（search-page-plan-v2）

| # | ファイル | 行 | 内容 | 種別 | 推奨処遇 |
|---|---|---|---|---|---|
| 1 | App_Ready_Design_Rules.md / implementation_checklist.md / pc-mobile-spec-inheritance-v1.1.md | AR#Rule6, IC#1-1, PMI#33 | OAuthプロバイダの記述が3文書で不一致。App_Ready_Design_Rules（策定2026-05-02・最古参）は`'google'/'github'`、implementation_checklistは「Google OAuth＋メール/パスワード認証＋メール確認フロー」、pc-mobile-spec-inheritance #33は「Google・X・Facebook OAuth。**メール認証なし**」（#34特記で「現行仕様を正とする」と明言） | 矛盾（重大・実害あり） | 修正：Google/X/Facebook・メール認証なしに統一 |
| 2 | App_Ready_Design_Rules.md | 172-186 | `images`テーブルSQL定義が独自（列名・`user_avatar`含む）で、正典のmyrig_db_schema_v1_6.md（列名不一致・「プロフィール画像は含まない」と明記）と食い違う | 矛盾 | 修正：schema v1.6参照に差し替え |
| 3 | App_Ready_Design_Rules.md | 266-275 | `notifications.type`が`'comment' / 'like' / 'pin' / 'follow'`（`favorite`なし）。schema v1.6は`'like' / 'favorite' / 'follow' / 'comment' / 'comment_reply'`（`pin`なし） | 矛盾 | 修正：schema v1.6に合わせる |
| 4 | App_Ready_Design_Rules.md | 51-73 | Rule2「URL構造は一度確定したら凍結」と明記するが、実際にはpage-role-matrix Session86で`/saved`→`/garage/favorites`等への変更が既発生 | 陳腐化／過剰な縛り | 修正：凍結文言を削除、参照に差し替え |
| 5 | App_Ready_Design_Rules.md | 299-300 | 「関連ファイル」参照先2件がrepo内に存在しない（検索確認済み） | 参照切れ | 削除 |

既知の疑いの記載なし（引き継ぎ文書に本カテゴリの疑いリストなし）。search-page-plan-v2.mdは「未確定」欄が明記済みで健全。

---

## 全体まとめ・CURRENT反映候補（未承認・要イタヤ裁定）

1. myrig_db_schema_v1_6.md rig_type CHECK制約が全面的に旧（db-schema-answers-v1.md裁定で判明済みだが未計上）
2. OAuthプロバイダ記述が3文書で不一致（Google/GitHub・Google/X/Facebook・メール認証あり/なし）
3. design-nogo-list NG-7「操作＝中立」がB19原文では未裁定のまま断定記載されていた実体を特定
4. pc-mobile-spec-inheritance G7「無限スクロールはFeedのみ」がsearch-page-plan-v2（#26裁定）と矛盾

自動確定はしていません。承認後に本文修正へ進めます。

---

# 追記: クロスチェックと反証確認の結果（2026-08-21 夕方〜夜）

上記は**初回精査**の内容。その後 GPT / Gemini / Claude の3AIクロスチェックと、
修正後の反証確認2回を実施した。**初回精査の判定のうち以下は誤りだったため撤回している。**

| 初回精査の判定 | 結果 | 正しい理解 |
|---|---|---|
| design #1「NG-7 操作色＝中立 は未裁定の断定＝誤記」 | **一部撤回** | 中立（B案）という**方向は2026-08-17イタヤ裁定で確定**。ただし実値 `#1F2328`/`#E6EDF3` の確定記録は見つからず、B19の但し書き（＋登録のみブランド色）の採否も不明。**要確認としてHOLD** |
| design #5「LOGダーク2.92は未裁定・HOLD追加」 | **撤回** | NG-7職域表・NG-3に `#3F709E` の記載があり**既裁定**。color-token-v8側が陳腐化していた |
| support #5「参照先2件はrepo内に存在しない」 | **撤回** | 断定しすぎ。正しくは「repo未収録、かつ v4.2 → v4.4_final_rev3 の**旧版参照**」 |
| schema #8「size_class は7/30と8/20が矛盾」 | **理由を訂正** | 13値の**出典は db-schema-answers-v1 §2 に実在する**。HOLDの理由は出典不在ではなく、実DB確認（実データ18パターン）との乖離 |
| schema #9「aliases は断定不可」 | **過小評価だった** | `master_aliases` が正本であること自体は確定済み。未裁定なのは `parts_masters.aliases` の物理列の処遇 |

## 初回精査が見落としていた重要項目（3AI指摘）

- **mobile-component-contract-v0.5 §2 に v7カラー（緑/紫/橙）が「3系統厳守」として生存**（最重要・実装事故源）
- auth-guard middleware の自己矛盾（matcher例が全URL即redirect ⇔ §5.1が除外を示唆）
- page-role-matrix の日英i18n（本文と注記が同一行で逆転）
- ドメイン表記 `myrig-rc.com` ⇔ `myrigrc.com`
- App_Ready のトークン色 `#1A73E8` 等（Material既定色そのまま）
- event_tags / size_class の実体（`_decisions/2026-08-21_db-inquiry-002-realdata.md` = **初回精査時には未コミットだった一次記録**）

## 反証確認で自ら作った不具合（是正済み）

maintenance_logs 表の分断 / rig_type 旧6値のApp側残存 / categories FK の取りこぼし /
part_masters・parts_masters の中途半端な置換 / 変更履歴の順序逆転 / CURRENT の✅欄への誤登録。

## この監査の教訓

**一次記録を読まずに二次記録（CURRENT の要約・他AIの指摘）だけで判断すると誤る。**
`_decisions/` を読んでいれば size_class / event_tags の判定は初回で正しく出せた。
また「存在しない」「確定済み」の断定は、根拠を確認できた範囲に限ること。
