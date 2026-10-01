# HANDOFF — 次スレッド: 運営側の管理画面（/admin）の設計とモックアップ（2026-10-01 15:43 JST / Claude）

> revision: **MYRIG-20261001-126**（この引き継ぎの push で採番）。直前 = 125（情報・法務・サポートの mock = PC / Mobile とも CLOSE・Launcher 確定）
> 対象 = **MyRIG のユーザー向けではなく、運営（イタヤ）が使う管理画面**。ユーザー画面の mock とは別レーンとして扱う（App / UI と DB Research を混ぜない原則どおり、Admin も混ぜない）

## 1. 最初にやること
1. revision.txt と _AI/MyRIG_CURRENT.md 冒頭の revision が一致することを確認（CDN キャッシュ対策）
2. 本書と、下の §3 の正典を読む
3. **進め方 = 議論 → 設計 → mock の順**。イタヤの希望は「設計を議論して深めてからモックアップ」。いきなり画面を作らない
4. 最初の議論の入口（Claude 案）: 運営が 1 日に何をするか（朝の確認 / 通報・問い合わせの処置 / お知らせ配信 / Master 追加 / 緊急停止）を並べ、頻度と事故の重さで画面の優先度を決める

## 2. いま分かっている /admin の面（page-role-matrix v1.7）
| route | 役割 | 優先 |
|---|---|---|
| /admin/moderation | 通報 3 表 ＋ support_inquiries を VIEW / UNION で 1 queue。削除は論理削除 | Should |
| /admin/master | RIG Master / Parts Master の追加・編集 | Must |
| /admin/browse-sections | INDEX / Category Top のセクション並び順・設定 | Should（Phase 4 候補） |
| /admin/reports | PV・登録数・カテゴリ分布 | Later |
- ガード: auth-guard-spec §5.2（/admin/* は認証 ＋ is_admin・非管理者は 403・未ログインは /login）。**管理者の 2 段階認証と操作の記録は未設計**（security-baseline の項目 ②）

## 3. 読む正典
- _decisions/2026-09-29_info-legal-support-v1.md（通報・問い合わせ・権利侵害申し立て・Legal Hold・保存期間・停止/再開）
- _decisions/2026-09-28_settings-notifications-v1.md（D8 退会 30 日確定・D9 開示請求 = 運営が /admin で書き出す）
- docs/schema/myrig_db_schema_v1_6.md（user_reports / support_inquiries / comment_reports / content_reports / legal_acceptances / announcements 周辺）
- docs/ui/page-role-matrix-v1.md / docs/ui/auth-guard-spec-v1.md
- docs/legal/（規約 v0.5・設計書・対応集 = 運営手順の元）

## 4. /admin レーンで決める必要がある PENDING（canon に既出）
1. 通報・問い合わせ・権利侵害申し立ての **1 queue の形**（4 表 UNION・担当・期限・一時非表示・結果の通知）
2. **停止中アカウントの 30 日確定処理を止めるか**・Legal Hold の除外（legal_holds 表 or hold_until 列）
3. **退会確定処理**（30 日後の匿名化・画像の実ファイル削除・コメント status='withdrawn'・Auth ソフト削除・username HMAC 予約）の運営側の見え方
4. **データの開示請求（D9）の書き出し**と本人確認の手順（🔴 HOLD・法務）
5. **お知らせの配信**: announcements の入力画面。schema r12 候補 = kind / area / images（3 枚）/ cta_label・cta_target / starts_at・ends_at（mock の /news はこの列を想定済み）
6. **Master 管理**: RIG Master / Parts Master の追加・編集と Research（DB 系）との境界（research-app-boundary-contract）
7. **管理者の認証・権限・操作ログ**（2 段階認証・誰が何を処置したかの記録・権限の分割が要るか）
8. 保存期間の自動処理（お問い合わせ 1 年・通報と申し立て 3 年・アクセスの記録 6 か月）の運営側の見え方
9. **Home 上部の 1 行お知らせ**（重要なお知らせ）を運営がどこで ON / OFF するか（Home レーン側の候補）

## 5. この後に控える全体作業（/admin ができたあと）
- **セキュリティの全体設計** docs/security/security-baseline-v1.md（CURRENT の PENDING・始める時期 = /admin ができた後）
- 全モック完成後の総合監査

## 6. 守ること
- Production DB には触れない（明示指示のみ例外）/ 物理 DELETE 禁止（例外 = ストレージ実ファイル）/ secret をチャットに出さない
- 法務文面は [DRAFT] を外さない（Claude は法律の専門家ではない）
- 色・形は v8 トークン（RIG #FBFF00 / PARTS #D92D20 / LOG #2F5F8F・NG-1〜7）。**管理画面は運営の道具なのでカテゴリ色を飾りに使わず、状態（要対応・期限切れ等）を主役にする**（Claude 案・要裁定）
- Shared UI Single Source（中身を 1 か所から描く）の型を踏襲（SoT_*.js）
- 主査チェック 5 つ（既存正典検索 / 約束ごとの DB・権限・処理 / 書き込み経路を全部数える / 新しい列の読み手 / 採らない別案 1 つ）
- commit / push: canon は Claude が各チェックポイントで実行（常設許可）。mock は Mac の mockup()（Claude は Mac の mock repo で git を叩かない）
- ⚠️ 管理画面は **PC 前提**が自然（Mobile を作るかは最初の議論で決める・Claude 案 = まず PC のみ、緊急停止と通報確認だけ Mobile を後で検討）

## 7. 作業環境のメモ
- canon: MyRIGRC/myrig-ai-context（main）。mock: ~/Desktop/MyRIG/App/MOKUP/myrig_pc_Ver3 → https://myrig-mobile-mock.vercel.app
- mock の gate: _state/ 配下の *_check.py（info_browser / launcher_link / auth_check ほか）。新レーンは専用 gate を新設する
- 既知の未解決（このレーン外）: hit_test HT4 myrig-library-search-v2.html @720（既存）/ 実機 Safari・VoiceOver 未確認
