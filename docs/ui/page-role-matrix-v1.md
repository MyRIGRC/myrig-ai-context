# MyRIG RC — Page Role Matrix / Final Sitemap v1

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> いまモックアップ制作フェーズ。デザインとサービス概念を**議論しながら作る**段階なので、
> 本書は「今こうなっている」という出発点であって、議論の打ち切りではない。
> **「既存仕様と異なる」ことだけを理由に案を捨てないこと。**
> 差分を明示すればイタヤ裁定で変更できる。

**作成日:** 2026-05-03
**最終更新:** 2026-09-30（公開 Legal 6 本・/reconsent・正典 123 下書き）／ 2026-09-29（Info / Legal / Support 行・正典 122）
**ステータス:** 確定ベース v1.7

**目的:** MyRIG全体のページ構成・役割・優先度を固め、V3モック制作・Next.js実装・管理画面設計で迷わない状態にする

> **⚠️ 本表に未捕捉のページ**
>
> ~~`/about` `/support` は pc-mobile-spec-inheritance #36 / #37 が PROPOSED として捕捉。~~ → 🔴 **2026-09-29（122）**: Info / Legal / Support 8 route を §2 / §3 に追加（裁定原本 `_decisions/2026-09-29_info-legal-support-v1.md` D1）。`/support` は `/support-us`。
> error-states / welcome-tour は同 #39 / #35 が捕捉。
> **`compare.html` はどの正典にも記載が無い**（要棚卸し）。`help.html` / `legal.html` は 122 で捕捉済み（Mobile の Info / Legal 面）。
> 実装側のファイル分割は本表と1対1ではない（`/search` は `search-results.html` と分割、
> モバイルHomeの実体は `index-e-roomclip.html`）。
>
> **`/rigs`**: contract §3.9 と pc-mobile-spec-inheritance 補助行A が参照しているが、
> **URL改訂候補#8 が未発効**のため本表の §2 URL一覧には置かない。発効時に追加すること。

---

## 1. Overview

### MyRIGのページ分類思想

MyRIG RC のページは「誰が・何をしに来るか」で 7 グループに整理する。

| グループ | 役割 | 主な訪問者 |
|---|---|---|
| **Browse / Discover** | 見る・探す・回遊する | 新規 / ライトユーザー / 常連 |
| **Detail** | 個別コンテンツを深く見る | 情報収集中のユーザー |
| **Garage / Manage** | 自分のRIG・PARTS・LOGを管理・公開 | 登録ユーザー |
| **Library / Master** | 公式製品データベースを調べる | 購入検討中・スペック確認 |
| **Relationship / Activity** | フォロー・更新履歴・保存を管理 | ヘビーユーザー |
| **Utility / Account** | 登録・編集・設定 | 全登録ユーザー |
| **Admin** | コンテンツ・マスター・セクション管理 | 運営 |

### Browse System Pages の位置づけ

INDEX / Category Top / Parts Browse Top は「Browse Section の集合」として設計される。
管理画面（`/admin/browse-sections`）でセクションの並び順・表示/非表示・layout_type・card_variant・query_config を編集可能にする設計（Phase 4 以降）。

Search Results は Browse Card を使うが、section-driven ではなく固定 UI（browse_md grid + list）とする。

---

## 2. Page Group Map

```
Public Browse
├─ /                                    INDEX（総合トップ）
├─ /category/[rigType]                  Category Top（例: /category/rock-crawler）
├─ /category/[rigType]/[categorySlug]   SubCategory Top（例: /category/rock-crawler/comp）
├─ /parts                               Parts Browse Top
├─ /parts/category/[partCategorySlug]   Parts SubCategory（例: /parts/category/tire）
├─ /feed                                Feed（LOG中心 / おすすめ・新着・フォロー中の3タブ）
└─ /search                              Search Results

Detail
├─ /rig/[rigId]                         RIG Detail（ユーザーRIG個別ページ）
├─ /parts/[partId]                      PARTS Detail（ユーザーパーツ個別ページ）
└─ /log/[logId]                         LOG Detail（整備・走行・カスタムログ個別ページ）

Garage
├─ /garage                              Own Garage Top（ピットテーブル / 管理）
├─ /garage/rigs                         Own RIG 一覧
├─ /garage/rigs/[rigId]                 Own RIG 編集・管理
├─ /garage/parts                        Own PARTS 一覧
├─ /garage/parts/[partId]               Own Parts 編集・管理
├─ /garage/logs                         Own LOG 一覧
├─ /garage/favorites                    お気に入り一覧（Session 86確定）
├─ /garage/pins                         ピン留め一覧（Session 86確定）
└─ /user/[username]                     Public Garage（他人のガレージ公開ビュー）

Library
├─ /library                             Official Library Top
├─ /library/rigs                        RIG Masters 一覧（公式DB / Session 113新規）
├─ /library/rigs/[masterSlug]           RIG Master Detail（車種スペック）
├─ /library/parts                       Parts Masters 一覧（公式DB / Session 113新規）
├─ /library/parts/[masterSlug]          Parts Master Detail（パーツスペック）
├─ /library/makers                      Makers 一覧（公式DB / Session 113新規）
└─ /library/makers/[makerSlug]          Maker Detail（後回し可）
※ URL は複数形統一（旧: `/library/rig/[masterId]` / `/library/maker/[makerId]` → 廃止）

Utility / Account
├─ /register/rig                        RIG 登録フォーム
├─ /register/part                       PARTS 登録フォーム
├─ /register/log                        LOG 登録フォーム
├─ /settings                            Settings
├─ /notifications                       通知
├─ /login                               ログイン
├─ /signup                              新規登録
├─ /onboarding                          初期設定ウィザード（最後に「登録の前に」= 地域・年齢・同意と確認・🔴 123）
└─ /reconsent                           規約を改定したときの再同意（🔴 123・Auth Shell）

Info / Legal / Support（🔴 2026-09-29・122。全部 P3 公開。サイドバー・BottomNav なしの中央本文型）
├─ /about                               MyRIG とは（運営者の節を含む）
├─ /help                                使い方・FAQ
├─ /contact                             お問い合わせ（?kind= で種別。feedback / data_request もここ）
├─ /report                              通報（URL 指定。主経路は面内ダイアログ）
├─ /news                                お知らせ（announcements の公開 read-only）
├─ /legal/terms                         利用規約
├─ /legal/guidelines                    投稿ガイドライン（🔴 123）
├─ /legal/privacy                       プライバシーポリシー
├─ /legal/cookies                       Cookie と外部送信（🔴 123・外部送信規律の公表）
├─ /legal/reporting                     通報と申し立ての仕方（🔴 123）
├─ /legal/notice                        運営者情報（🔴 123）
├─ /legal/tokushoho                     特定商取引法（予約・非公開）
└─ /support-us                          応援する（作るが MVP 公開時は非表示・3′）
※ redirect: /terms → /legal/terms、/privacy → /legal/privacy、/support → /support-us。/feedback /guide /company は作らない

Admin
├─ /admin/master                        Master DB 管理（RIG Master / Parts Master）
├─ /admin/browse-sections               Browse Section 並び順・設定管理
├─ /admin/moderation                    コンテンツモデレーション
└─ /admin/reports                       レポート・統計
```

---

## 3. Page Role Matrix

| Page | URL | Group | Role | Primary User Intent | MVP Priority | V3 Mock Priority | Notes |
|---|---|---|---|---|---|---|---|
| INDEX | `/` | Browse | サイト全体の玄関。Browse Section 集合。新規流入・回遊促進 | 新着を眺める / おすすめを探す | Must | S | ✅ v3.1 完成済み。section-driven |
| Category Top | `/category/[rigType]` | Browse | 特定RIGカテゴリの専門トップ。INDEXと同じBrowse System | そのカテゴリの最新・人気を探す | Must | S | **次のV3作成対象** |
| SubCategory Top | `/category/[rigType]/[categorySlug]` | Browse | カテゴリ内サブ絞り込み。Category Top の派生 | さらに絞り込む | Should | B | Category Top V3 完成後に考える |
| Parts Browse Top | `/parts` | Browse | パーツ専用Browse Top。ブランド・カテゴリ・使用RIG数を軸に | パーツを探す / 使用人数を見る | Must | A | 収益導線・Library連携の中核 |
| Parts SubCategory | `/parts/category/[partCategorySlug]` | Browse | パーツカテゴリ別Browse。タイヤ・ESC・ショック等 | 特定カテゴリのパーツを比較する | Should | B | Parts Browse Top V3 完成後 |
| Search Results | `/search` | Browse | 目的検索の固定UI。browse_md grid + list。フィルター・ソート優先 | 特定のRIG・パーツ・ユーザーを探す | Must | A | section-driven ではない。固定UI |
| Feed | `/feed` | Relationship | LOG中心のアクティビティフィード。**Feed 単体で目的を完結できるトップレベル体験** | 最新ログを眺める / フォロー中の動向を見る | Should | A | **「おすすめ / 新着 / フォロー中」の3タブ**（2026-09-07 イタヤ裁定。#28 の2タブは失効。§6参照）。PC 正本 `myrig-feed-v3.html` / Mobile `feed.html` とも適用済み |
| RIG Detail | `/rig/[rigId]` | Detail | ユーザーRIG個別ページ。スペック・パーツ・ログ・写真 | このRIGの構成を見る | Must | S | ✅ v6 完成済み |
| PARTS Detail | `/parts/[partId]` | Detail | ユーザーパーツ個別ページ。スペック・使用RIG・レビュー | このパーツの詳細を見る | Must | S | ✅ v6 完成済み |
| LOG Detail | `/log/[logId]` | Detail | 整備・走行・カスタムログ個別ページ | このログの内容を読む | Must | S | ✅ v6 完成済み |
| Own Garage Top | `/garage` | Garage | 自分のガレージTop。ピットテーブル / 管理ハブ | 自分のRIGを管理する | Must | S | ✅ v6 完成済み。ピットテーブル設計確定 |
| Own RIG 一覧 | `/garage/rigs` | Garage | 自分の全RIG一覧。ステータス管理 | RIGを一覧・並び替える | Must | S | ✅ v6 完成済み |
| Own PARTS 一覧 | `/garage/parts` | Garage | 自分の全PARTS一覧 | パーツを一覧・整理する | Must | S | ✅ v6 完成済み |
| Own LOG 一覧 | `/garage/logs` | Garage | 自分の全LOG一覧 | ログを振り返る | Must | S | ✅ v6 完成済み |
| Own Favorites | `/garage/favorites` | Garage | お気に入り一覧（RIG/PARTS/LOG/Users） | 保存したコンテンツを見る | Must | S | ✅ v6 完成済み |
| Own Pins | `/garage/pins` | Garage | ピン留め一覧（RIG/PARTS/LOG/Users） | 後で見るコンテンツを管理 | Must | S | ✅ v6 完成済み |
| Public Garage | `/user/[username]` | Garage | 他人のガレージ公開ビュー。Pins/Favorites/下書き非表示 | このユーザーのRIG構成を見る | Must | A | Own Garage との表示分岐。別ページではなくビュー切替 |
| Official Library | `/library` | Library | 公式DB+製品検索+ユーザー実例+外部送客起点。MVP: 製品を探す・識別する・実例を見る・購入先へ送る | 製品情報を調べる / 購入先を見る | Must | A | ✅ v1.3 MVP再構築済み（Session 115） |
| RIG Masters 一覧 | `/library/rigs` | Library | RIG Master公式DB一覧。View Master + 購入先を見る 2-CTA | RIG製品を探す | Must | A | ✅ v1.1 MVP再構築済み（Session 115） |
| Parts Masters 一覧 | `/library/parts` | Library | Parts Master公式DB一覧。VIEW + 購入先を見る 2-CTA | パーツ製品を探す | Must | A | ✅ v1.1 MVP再構築済み（Session 115） |
| Makers 一覧 | `/library/makers` | Library | Maker公式DB一覧。Official site + VIEW MAKER 2-CTA | メーカーを調べる | Must | A | ✅ v1.1 MVP再構築済み（Session 115） |
| RIG Master Detail | `/library/rigs/[masterSlug]` | Library | **Lite版**: 画像2枚・スペック8項目・Variants・User Examples 4件・購入先導線。詳細は公式サイト/提携ショップへ | このモデルの基本情報を確認 / 購入先へ進む | Must | A | ✅ v1.1 Lite 完成（Session 115）。Full Scope版は将来実装候補 |
| Parts Master Detail | `/library/parts/[masterSlug]` | Library | パーツの公式スペック・購入先導線（Lite方針）| このパーツの基本情報を確認 / 購入先へ進む | Must | B | ✅ 実装済み（PC `myrig-library-parts-master-detail-v3.html` / モバイル `library-parts-master-detail.html`） |
| Maker Detail | `/library/makers/[makerSlug]` | Library | メーカー情報・製品ライン・公式サイト導線 | このメーカーの製品を見る | Later | Later | ✅ 実装済み（PC `myrig-library-maker-detail-v3.html` / モバイル `library-maker-detail.html`） |
| Notifications | `/notifications` | Relationship | いいね・お気に入り・コメント・返信・フォローの通知（アプリ内のみ） | 通知を確認する | Must | B | 🔴 **2026-09-28 改訂（120）**: アプリ内通知を MVP に含める（裁定 D1）。旧記述「MVP後半で整備」・Priority Should は失効。PC はベル → 本ページへ直接移動、Mobile は自分のガレージから入る |
| RIG 登録 | `/register/rig` | Utility | RIG登録フォーム（**連続 Editor / progressive**。段階開示） | RIGを登録する | Must | B | フォーム系は別まとめ。🔴 **2026-09-18 改訂（113）**: 旧記述「ステップ式」は失効。PC v3.1（110）も Mobile（113）も強制ウィザードではない。裁定 **D-B** |
| PARTS 登録 | `/register/part` | Utility | PARTS登録フォーム | パーツを登録する | Must | B | |
| LOG 登録 | `/register/log` | Utility | LOG登録フォーム | ログを記録する | Must | B | |
| Settings | `/settings` | Utility | プロフィール・公開とコメント・興味とおすすめ・通知・ログインとセキュリティ・アカウントの管理 | 設定を変更する | Should | B | 🔴 **2026-09-28 改訂（120）**: 通知設定は MVP に含める（裁定 D5・20:30 改訂）。興味とおすすめ = マイカテゴリ 5 つまで・順序あり（D10・2026-09-29）。テーマ・言語は設定ではなく Shell の表示操作。旧記述「通知・テーマ設定」は失効 |
| Login | `/login` | Utility | ログイン | ログインする | Must | Later | |
| Signup | `/signup` | Utility | 新規登録 | アカウントを作る | Must | Later | |
| Onboarding | `/onboarding` | Utility | 初期設定ウィザード（RIG登録誘導） | サービスを使い始める | Should | Later | 🔴 **2026-09-30（123 下書き）**: 最後に「登録の前に」（お住まいの地域 日本 / 日本以外・年齢の区分・大事な点 5 つ・規約とガイドラインへの同意・プライバシーの確認）。みなし同意は廃止。裁定原本「CLOSE」節 |
| Reconsent | `/reconsent` | Utility | 規約・ガイドラインを改定したときの再同意 | 変わった規約に同意する | Must | Later | 🔴 **2026-09-30（123 下書き）**。auth-guard: Maintenance > Suspended > 登録状態 > 同意が要る。止めるのは書き込みだけ（閲覧・お問い合わせ・通報・ブロックとミュート・ログアウト・退会は塞がない）。mock `pc/myrig-auth-reconsent-v1.html` |
| Admin: Master | `/admin/master` | Admin | RIG Master / Parts Master の追加・編集 | マスターデータを管理する | Must | Later | |
| Admin: Browse Sections | `/admin/browse-sections` | Admin | INDEX / Category Top のセクション並び順・設定管理 | ページを構成する | Should | Later | Phase 4 候補 |
| Admin: Moderation | `/admin/moderation` | Admin | 報告されたコンテンツ・ユーザー・問い合わせの確認と処置（通報 3 表 ＋ `support_inquiries` を VIEW / UNION で 1 queue・🔴 122）。削除は論理削除 | コンテンツを管理する | Should | Later | |
| About | `/about` | Info | サービス紹介・運営者の節。未ログイン・初訪問者向け | MyRIG が何かを知る | Must | A | 🔴 **2026-09-29（122）** 裁定 D1。PC `pc/myrig-about-v0.1.html` / Mobile `about.html`。#36 PROPOSED → 確定 |
| Help | `/help` | Info | 使い方・FAQ（アコーディオン）。CTA = お問い合わせ / フィードバック / 通報 | 使い方を確かめる | Must | A | 🔴 122。PC は support 面 `?view=help` / Mobile `help.html?view=help`。「使い方」「ヘルプセンター」「/guide」は全部これ |
| Contact | `/contact` | Info | 運営への問い合わせ。kind = account / content / bug / feedback / data_request / rights / other。未ログイン可 | 運営に連絡する | Must | A | 🔴 122 裁定 D4。受け皿 `support_inquiries`（schema r11）。Suspended は kind=account だけ。/feedback 面は作らない |
| Report | `/report` | Info | URL 指定の通報。ログイン中は面内ダイアログと同じ。未ログインは `support_inquiries(kind=rights)` | 不適切な投稿・ユーザーを知らせる | Must | A | 🔴 122 裁定 D5。主経路は ⋯ メニューの `SoT_report.js`。通報 3 表（comment / content / user） |
| News | `/news` | Info | 全員宛てのお知らせ（`announcements`）の公開一覧。障害・規約改定・重要な変更 | 何が起きているか知る | Must | B | 🔴 122 裁定 D1。未ログインでも読める。緊急告知をキャッシュしない。通知の「重要なお知らせ」の行き先 |
| Terms | `/legal/terms` | Legal | 利用規約 | 条件を確かめる | Must | A | 🔴 122。[DRAFT]・法務未確認。PC は legal 面 `?view=terms` / Mobile `legal.html?view=terms`。決まった事実の一覧 = 裁定原本 D6 |
| Guidelines | `/legal/guidelines` | Legal | 投稿ガイドライン（規約の個別ルール・普通の言葉） | 投稿のルールを知る | Must | A | 🔴 **123 下書き**。同上 `?view=guidelines` |
| Privacy | `/legal/privacy` | Legal | プライバシーポリシー | 個人情報の扱いを確かめる | Must | A | 🔴 122。同上 `?view=privacy`。Cookie 同意モデルは checklist 4-1 HOLD |
| Cookies | `/legal/cookies` | Legal | Cookie と外部送信（端末に保存するもの・ほかの事業者へ送られる情報） | 送られる情報を確かめる | Must | A | 🔴 **123 下書き**。電気通信事業法の外部送信規律の公表。**表は公開前に実測で確定**（公開条件 R4） |
| Reporting | `/legal/reporting` | Legal | 通報と申し立ての仕方（窓口・急いで対応が必要なもの・流れ・7 日の確認） | 申し立ての手順を知る | Must | A | 🔴 **123 下書き**。Footer・メニューには出さない（ヘルプ・通報・お問い合わせ・規約本文・法務のタブから） |
| Notice | `/legal/notice` | Legal | 運営者情報（屋号・日本・東京都・窓口・提供の状況） | 運営者を知る | Must | A | 🔴 **123 下書き**。氏名と住所は請求があれば回答（個人情報保護法 32 条）。屋号の正式名は要確認 |
| Tokushoho | `/legal/tokushoho` | Legal | 特定商取引法に基づく表記 | — | Later | Later | 🔴 122 裁定 D7。**route 予約・面は非公開**。enabled = 決済先決定 ＋ 法務確認。要否も要確認 |
| Support Us | `/support-us` | Support | 応援する。外部サービス（未確定・第一候補 OFUSE）へ 1 本の CTA。金額ボタンなし | プロジェクトを応援する | Should | A | 🔴 122 裁定 D7（3′）。**作るが MVP 公開時は非表示**（site-links `enabled`）。出す時期はイタヤ。特典は Phase 2 `/plans`（MR-PLAN-002）。#37 の `/support` → `/support-us` |
| Admin: Reports | `/admin/reports` | Admin | PV・登録数・カテゴリ分布の統計 | サイト状況を把握する | Later | Later | |

---

## 4. Browse System Pages

Browse Section System（`data-layout-type` / `data-card-variant` / `data-query-preset` による section-driven 設計）を使うページ。

| Page | URL | section-driven | layout_types 使用 | 備考 |
|---|---|---|---|---|
| INDEX | `/` | ✅ | hero_shelf / horizontal_shelf / compact_shelf / feature_banner / editorial_banner / ad_slot / library_links | ✅ v3.1 完成 |
| Category Top | `/category/[rigType]` | ✅ | hero_shelf / horizontal_shelf / compact_shelf / feature_banner / ad_slot | 対象カテゴリで query_config を絞り込む |
| SubCategory Top | `/category/[rigType]/[categorySlug]` | ✅（派生） | horizontal_shelf / compact_shelf / ad_slot | Category Top より演出少なめ |
| Parts Browse Top | `/parts` | ✅ | horizontal_shelf / compact_shelf / feature_banner / ad_slot | 軸はパーツカテゴリ / ブランド / 使用RIG数 |
| Parts SubCategory | `/parts/category/[slug]` | ✅（派生） | horizontal_shelf / compact_shelf | タイヤ・ESC・ショック等カテゴリ別 |
| Search Results | `/search` | ❌（固定UI） | — | browse_md grid + browse_list を使うが section-driven ではない |
| Feed | `/feed` | ❌（固定UI） | — | LOG カード + Activity が流れる。Browse Section ではない |

### Browse Section System 非採用ページの理由

- **Search Results**: 演出より「フィルター・ソート・一覧性」が優先。section 順序の管理よりクエリ結果の精度が重要。
- **Feed**: リアルタイム性・時系列性が基本構造。管理画面で並び替えるものではない。

---

## 5. Garage View Split

Own Garage と Public Garage（`/user/[username]`）は同じコンポーネント群を共有し、表示差分をビューレベルで制御する。別々の完全別ページとして独立させない。

| Item | Own Garage (`/garage`) | Public Garage (`/user/[username]`) |
|---|---|---|
| RIG 一覧 | 全件表示（非公開含む） | 公開分のみ表示 |
| PARTS 一覧 | 全件表示（非公開含む） | 公開分のみ表示 |
| LOG 一覧 | 全件表示（下書き含む） | 公開分のみ表示 |
| ピットテーブル | 表示（管理・編集可） | 非表示 |
| Pins（ピン留め） | 表示（→ `/garage/pins` にリンク） | 非表示 |
| Favorites（お気に入り） | 表示（→ `/garage/favorites` にリンク） | 非表示 |
| 下書き | 表示 | 非表示 |
| Edit ボタン群 | 表示 | 非表示 |
| Follow ボタン | 非表示 | 表示 |
| プロフィール情報 | 表示（編集リンクあり） | 表示（閲覧のみ） |
| 作業ステータスバッジ | 表示（全5状態） | 表示（公開状態のみ） |
| 非公開バッジ | 表示 | 非表示 |

### コンポーネント設計方針

```
GarageShell（共通ラッパー）
  ├─ OwnGarageView（/garage 専用 — ピットテーブル / 編集ボタン / Pins / Drafts あり）
  └─ PublicGarageView（/user/[username] 専用 — 公開コンテンツのみ / Follow ボタンあり）
```

`GarageShell` はヘッダー・ナビ・プロフィール描画を共通化。ビューレベルで条件分岐する。

### 保存系URL設計（Session 86 確定 / v1.2 補正）

ピン留め・お気に入りの正式 URL は `/garage/favorites` / `/garage/pins`（Garage グループの一部）であり、Saved 系URLは廃止する。

| 項目 | 正式 URL | 旧URL（廃止） |
|---|---|---|
| お気に入り一覧 | `/garage/favorites` | `/saved`, `/saved/favorites`, `/saved#favorites` |
| ピン留め一覧 | `/garage/pins` | `/saved/pins`, `/saved#pins` |

**理由**: 保存系は「自分のガレージ管理」の一部であり、Garage Sidebar との整合性を保つため `/garage/*` 配下に統一する。`/saved` を独立URLにすると、ガレージナビと別動線が生まれて UX が破綻する。

**Redirect 方針（実装時）**: `/saved` および `/saved/*` 配下にアクセスがあった場合は、対応する `/garage/favorites` または `/garage/pins` へ 301 リダイレクトする。

既存 MOK ファイル `myrig-garage-favorites-v6.html` / `myrig-garage-pins-v6.html` は `/garage/favorites` / `/garage/pins` のpage.tsx として実装する。

### Garage Shell 分離（Session 86 確定）

GarageShell は **GarageShell-List**（一覧・管理ハブページ群）と **GarageDetailShell**（RIG / Parts 個別管理ページ）の 2 種類に分離する。

| Shell | 対象 URL | Sidebar | Context Bar | Right Panel |
|---|---|---|---|---|
| **GarageShell-List** | `/garage`, `/garage/rigs`, `/garage/parts`, `/garage/logs`, `/garage/favorites`, `/garage/pins` | `<GarageSidebar>`（フル表示） | なし | なし |
| **GarageDetailShell** | `/garage/rigs/[rigId]`, `/garage/parts/[partId]` | `<GarageSidebar>`（軽量版：Hamburger クリックで展開） | あり（戻り導線 / breadcrumb） | あり（MANAGE / BUILD / STATS / QUICK NOTE / LINKS） |

詳細は `docs/design-rules.md` の「Garage Detail 固有ルール」セクション参照。

---

## 6. Feed Definition

**本§は 2026-09-07 イタヤ裁定による現行定義。**
裁定原本: `_decisions/2026-09-07_feed-tabs-v1.md`。
🔴 **#28裁定（2026-07-23）の「2タブ・全投稿時系列を置かない」は失効。**

### Feed の基本思想

Feed は「SNS的な拡散の場」ではない。
そして **Browse や Library の派生でもなく、Feed 単体で目的を完結できるトップレベル体験**である
（`_decisions/2026-09-07_toplevel-surfaces-v1.md`）。
人・RIG・LOG の活動を時間軸で見る面として、読む → reaction → comment → 次の投稿、が Feed 内で完結する。

### タブ構成 = 「おすすめ / 新着 / フォロー中」の3本

**3つは入口の性質が違う。**

| タブ | 何を基準に並ぶか |
|---|---|
| おすすめ | **興味・発見ベース** |
| 新着 | **全公開 LOG の純時系列**（加工なし） |
| フォロー中 | **social graph ベース** |

🔴 **なぜ #28 の2タブから変えたか（2026-09-07）**

「現行実装が3タブだから正典を合わせた」のではない。
**Feed / LOG Detail continuity を詰めた結果、Feed の独立性を再検討して裁定を更新した。**

Feed が独立したトップレベル体験であるなら、Feed 内に3つの入口が要る。
とくに **MVP の「おすすめ」は完全な推薦アルゴリズムではない**（下記のとおり擬似ミックス）ため、
**加工されていない全公開 LOG の時系列入口を残す価値がある**。
#28 が退けた「すべて（全投稿時系列）」は X 型の拡散導線としての話であり、
本裁定の「新着」は**推薦が未成熟な間の素の入口**という別の役割で置く。

### おすすめ（デフォルト）

- 流れるコンテンツ: **LOG のみ**
- ユーザーフォロー関係に関係なく全公開LOGが対象
- 基準は**興味・発見**
- **MVPのおすすめロジックは「新着＋人気の擬似ミックス」で可**
- 🔴 **マイカテゴリ（2026-09-29 裁定 D10）**: ユーザーの興味カテゴリ（`profile_private.rig_category_slugs`・最大 5）は**順位を押し上げるシグナルで、フィルターではない**。ほかのカテゴリーを除かない。0 件なら上の擬似ミックスのまま。ミュートの除外はその前に効く
- card_variant: **`log-feed`**（`myrig-log-card variant="feed"`）
  - ⚠️ `browse`（browse_md）は Browse ページ用。Feed では使用しない
  - `feed` variant は `SoT_card-components.js` に実装済み（旧 `myrig-log-feed`）

### 新着（タブ切替）

- 流れるコンテンツ: **全公開 LOG**
- 並びは**投稿日時順のみ。推薦・人気による加工をしない**
- card_variant: **`log-feed`**（おすすめタブと同じ）
- 🔴 おすすめが本格的な推薦になっても、**「素の時系列を見る入口」として残す**

### フォロー中（タブ切替）

- 流れるコンテンツ: フォロー中ユーザーの **LOG + Activity**
- Activity の種別:
  - `rig_registered` — 新しい RIG を登録した
  - `part_added` — パーツを RIG に追加した
  - `rig_updated` — RIG の構成を更新した
  - `log_posted` — 新しい LOG を投稿した
- LOG card_variant: **`log-feed`**（おすすめタブと同じ）
- Activity card_variant: **`activity_item`**（軽量専用コンポーネント / 将来設計）— `myrig-activity-item` 想定
  - Activity は LOG より小さく・情報量少なめ
  - SoT_card-components.js への追加は Feed V3 モック制作時に設計する

### 画像表示・追加ロード（#28 / #25）

- 画像は**グリッド型 ＋ ImageLightbox**（1枚=単体 / 2枚=2列 / 3枚=1大＋2小）
- 追加ロードは**無限スクロール**（#25・Feed限定例外。pc-mobile-spec-inheritance G7 参照）

### Feed に含めないもの

| 機能 | 理由 |
|---|---|
| Repost / 引用 | SNS 的拡散は MyRIG の方向性ではない |
| DM / メッセージ | コミュニケーション機能はスコープ外（MVP）|
| RIG / PARTS ブラウズカード混在 | Feed は LOG 中心。RIG/PARTS は Browse / Search から |
| ランキング表示 | **ランキング機能は全廃方針** |

---

## 7. Pages Not To Create Separately

以下は独立ページとして作成しない。理由を明記する。

| ページ候補 | 理由 |
|---|---|
| `/ranking` | **作成しない。ランキング機能そのものが全廃方針。** `*-ranking` プリセットは定義しない。ランキング表現はモバイル契約§4でも全部品で禁止 |
| `/logs`（LOG Browse Top） | LOG 一覧・回遊は Feed で吸収する。LOG 専用 Browse Top は MVP では不要。必要なら Category Top の entity_type: log として対応可能 |
| `/makers`（Maker 一覧） | Library 内（`/library/makers` / `/library/makers/[makerSlug]`）で扱う。Maker 一覧は Library の補助コンテンツ |
| `/favorites` | 独立 URL としては不作成。`/garage/favorites`（Garage グループ）として実装。旧 `/saved` タブ統合案は Session 86 で廃止 |
| `/pins` | 独立 URL としては不作成。`/garage/pins`（Garage グループ）として実装。旧 `/saved` タブ統合案は Session 86 で廃止 |
| `/saved`（および `/saved/*`） | **廃止（Session 86 確定）**。`/garage/favorites` / `/garage/pins` への 301 リダイレクト。`/saved` を独立URLにすると Garage ナビとの動線が分裂するため廃止 |
| User Profile（`/profile/[username]`） | Public Garage View（`/user/[username]`）が兼務。別途プロフィール専用ページは作らない |

---

## 8. Recommended Next Mock Order

現時点での推奨 V3 モック制作順。設計ドキュメント・正典ファイルの更新は各モック完成時に行う。

| 順 | ファイル | URL | 優先理由 |
|---|---|---|---|
| 1 | ~~`myrig-category-top-v3.html`~~ ✅ 完成 | `/category/[rigType]` | INDEX の section-driven 設計をそのまま流用。Browse System Pages 中で最も INDEXに近い構造。差分設計が明確 |
| 2 | `myrig-parts-browse-v3.html` | `/parts` | 収益導線・Library 連携の中核。Parts Master との接続設計を固める |
| 3 | `myrig-search-v3.html` | `/search` | 固定 UI（section-driven 非使用）のテンプレートを確立。browse_md grid + browse_list |
| 4 | Global Layout / App Shell | — | **前倒し（↑ 6位→4位）。** Header / Sidebar / Drawer / Bottom Nav の設計。Feed・Public Garage など関係系ページのナビ構造を先に固める。現 V3 モックは Main Content only |
| 5 | `myrig-feed-v3.html` | `/feed` | 「おすすめ / 新着 / フォロー中」3タブ構成（2026-09-07 裁定）。LOG カード `log-feed` variant を主役に。`activity_item` コンポーネント設計もここで行う |
| 6 | `myrig-public-garage-v3.html` | `/user/[username]` | Own Garage v6 との表示分岐整理。GarageShell の共通化方針確定 |
| 7 | `myrig-library-v3.html` 系 | `/library/*` | RIG Master / Parts Master Detail。SEO・アフィリエイト収益の中核 |

### Feature Banner Catalog（並行タスク）

- `myrig-index-v3.html` の `feature_banner` は **暫定（feature_banner_split 仮採用）**
- split / overlay / compact の 3 案を比較するカタログセクションまたは専用ページを作成し、確定後に各 Browse System Page の feature_banner を差し替える
- Category Top V3 作成時に仮採用のまま進めてよい

---

## 9. URL 設計メモ

| 判断 | 内容 |
|---|---|
| RIG カテゴリ slug | `/category/rock-crawler`, `/category/drift`, `/category/buggy` 等。英語 kebab-case |
| PARTS カテゴリ slug | `/parts/category/tire`, `/parts/category/esc` 等 |
| ユーザー識別 | `/user/[username]`（@ なし）。`@` は表示のみ |
| Master 識別 | `/library/rigs/[masterSlug]`, `/library/parts/[masterSlug]`, `/library/makers/[makerSlug]`。**slug を使う想定**（UUIDは露出させない）。※ただし schema 側は masters を UUID PK で定義しており、**master 用 slug 列の定義は未確認**。実装前に schema と突き合わせること。**全エンティティで複数形に統一**（旧: `/library/rig/`, `/library/maker/` → 廃止） |
| Admin プレフィックス | `/admin/*`。認証 middleware で保護 |
| i18n | ~~MVP時点から日英2言語公開（#24裁定 2026-07-23）~~ → 🔴 **2026-09-30 改訂（イタヤ裁定・正典 123）: MVP は日本語だけで正式に提供する。** 海外からのアクセス・登録は止めない（ブラウザの翻訳で使う人がいる前提・規約で自動翻訳は公式でないと明記）。**作りは i18n-ready のまま**（`/en/*` プレフィックス方式の routing を後から開けられる・画面の文字を直書きしない・カテゴリのコードは言語に依存しない）。**英語版の公開 = Global Launch Gate**（日付は約束しない・公開後 2〜3 か月を目安に判定日を置く）。製品の方針（日英 2 言語）は変えず、公開の順番だけを変える。裁定原本 `_decisions/2026-09-29_info-legal-support-v1.md`「#24 改訂」節 |

---

---

※ Breakpoint（現状の実測値。⚠️ 2026-09-12 訂正: `docs/design-rules.md` は repo に存在せず、具体 px は正典化しない方針 — `region-behavior-matrix-v1.md` §0）: desktop=1025px↑ / tablet=721-1024px / mobile=720px↓ は Shell の切替値。面ごとの実測境界（Detail 1050 / Library 980 等）は Matrix §4 を参照

※ **幅ごとに各領域が何へ変身するか**は `docs/ui/region-behavior-matrix-v1.md`（L2 / 2026-09-12）が持つ。
　 PC 面の **W / M / PC Narrow Fallback** と、専用 Mobile 面の **C** は別契約。
　 ⛔ PC HTML を 720px 以下へ縮めた状態は C ではない（= PC Narrow Fallback）。

*Page Role Matrix v1.5 — 作成: 2026-05-03 / 最終更新: 2026-08-22*
