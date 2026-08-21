# MyRIG RC — Page Role Matrix / Final Sitemap v1

**作成日:** 2026-05-03 (Session 100)
**最終更新:** 2026-05-12 (Session 86 — URL設計統一 / /saved廃止・/garage/favorites・/garage/pins 正式化 / GarageShell-List・GarageDetailShell 分離確定)
**ステータス:** 確定ベース v1.3（保存系URL統一 + GarageShell分離）
**目的:** MyRIG全体のページ構成・役割・優先度を固め、V3モック制作・Next.js実装・管理画面設計で迷わない状態にする

> **⚠️ 改訂注記（2026-07-23 Coworkナレッジ監査 — 本文は未改訂・本注記が優先）**
>
> 1. **§3 Feed行・§6 Feed Definitionのタブ構成「All / Following」は旧定義。** #28裁定（2026-07-23 イタヤ実機裁定・P13b r2実装済み）で置換: タブは**「おすすめ / フォロー中」の2本**（「すべて＝全投稿時系列」タブは置かない — X型。おすすめのMVPロジックは新着＋人気の擬似ミックスで可）。画像は**グリッド型＋ImageLightbox**（1枚=単体/2枚=2列/3枚=1大＋2小）。Feed追加ロードは**無限スクロール**（#25・Feed限定例外）。詳細は mobile-feedback-ledger #25/#28。
> 2. **§7の`/ranking`吸収記述にある`*-ranking`プリセット定義はランキング機能全廃方針により改訂対象**（#19キューに登録済み）。
> 3. 本文の書き換えは正典改訂手続き（#19キュー消化・Next.js実装前・PC正本改訂と同時）で実施する。

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
├─ /feed                                Feed（LOG中心 / Following タブあり）
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
└─ /onboarding                          初期設定ウィザード

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
| Feed | `/feed` | Relationship | LOG中心のアクティビティフィード。Following タブあり | 最新ログを眺める / フォロー中の動向を見る | Should | A | All Feed + Following Feed の2タブ（**旧定義 — #28裁定で「おすすめ/フォロー中」に置換。冒頭の改訂注記参照**） |
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
| Parts Master Detail | `/library/parts/[partSlug]` | Library | パーツの公式スペック・購入先導線（Lite方針）| このパーツの基本情報を確認 / 購入先へ進む | Must | B | 未作成 |
| Maker Detail | `/library/makers/[makerSlug]` | Library | メーカー情報・製品ライン・公式サイト導線 | このメーカーの製品を見る | Later | Later | 未作成 |
| Notifications | `/notifications` | Relationship | いいね・コメント・フォロー通知 | 通知を確認する | Should | Later | MVP後半で整備 |
| RIG 登録 | `/register/rig` | Utility | RIG登録フォーム（ステップ式） | RIGを登録する | Must | B | フォーム系は別まとめ |
| PARTS 登録 | `/register/part` | Utility | PARTS登録フォーム | パーツを登録する | Must | B | |
| LOG 登録 | `/register/log` | Utility | LOG登録フォーム | ログを記録する | Must | B | |
| Settings | `/settings` | Utility | アカウント・プロフィール・通知・テーマ設定 | 設定を変更する | Should | Later | |
| Login | `/login` | Utility | ログイン | ログインする | Must | Later | |
| Signup | `/signup` | Utility | 新規登録 | アカウントを作る | Must | Later | |
| Onboarding | `/onboarding` | Utility | 初期設定ウィザード（RIG登録誘導） | サービスを使い始める | Should | Later | |
| Admin: Master | `/admin/master` | Admin | RIG Master / Parts Master の追加・編集 | マスターデータを管理する | Must | Later | |
| Admin: Browse Sections | `/admin/browse-sections` | Admin | INDEX / Category Top のセクション並び順・設定管理 | ページを構成する | Should | Later | Phase 4 候補 |
| Admin: Moderation | `/admin/moderation` | Admin | 報告されたコンテンツの確認・削除 | コンテンツを管理する | Should | Later | |
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

> **⚠️ 本§のタブ構成は旧定義。#28裁定（2026-07-23）で「おすすめ / フォロー中」の2タブに置換済み（冒頭の改訂注記参照）。本文改訂は#19キューで実施。**

### Feed の基本思想

Feed は「SNS的な拡散の場」ではなく「フォローしているガレージの更新履歴」として設計する。

### All Feed（デフォルト）

- 流れるコンテンツ: **LOG のみ**
- 整備ログ・走行ログ・カスタムログが時系列で流れる
- ユーザーフォロー関係に関係なく全公開LOGが対象
- card_variant: **`log-feed`**（`myrig-log-card variant="feed"`）
  - ⚠️ `browse`（browse_md）は Browse ページ用。Feed では使用しない
  - `feed` variant は `SoT_card-components.js` に実装済み（旧 `myrig-log-feed`）

### Following Feed（タブ切替）

- 流れるコンテンツ: フォロー中ユーザーの **LOG + Activity**
- Activity の種別:
  - `rig_registered` — 新しい RIG を登録した
  - `part_added` — パーツを RIG に追加した
  - `rig_updated` — RIG の構成を更新した
  - `log_posted` — 新しい LOG を投稿した
- LOG card_variant: **`log-feed`**（All Feed と同じ）
- Activity card_variant: **`activity_item`**（軽量専用コンポーネント / 将来設計）— `myrig-activity-item` 想定
  - Activity は LOG より小さく・情報量少なめ
  - SoT_card-components.js への追加は Feed V3 モック制作時に設計する

### Feed に含めないもの

| 機能 | 理由 |
|---|---|
| Repost / 引用 | SNS 的拡散は MyRIG の方向性ではない |
| DM / メッセージ | コミュニケーション機能はスコープ外（MVP）|
| RIG / PARTS ブラウズカード混在 | Feed は LOG 中心。RIG/PARTS は Browse / Search から |
| ランキング表示 | Feed ではなく INDEX / Category Top の Browse Section で扱う |

---

## 7. Pages Not To Create Separately

以下は独立ページとして作成しない。理由を明記する。

| ページ候補 | 理由 |
|---|---|
| `/ranking` | INDEX / Category Top / Parts Browse Top の Browse Section（`data-query-preset="*-ranking"`）として吸収する。ランキングは「棚の並び順」であり独立ページが必要な情報量ではない（**注記: `*-ranking`プリセット定義はランキング全廃方針により#19キューで改訂対象**） |
| `/logs`（LOG Browse Top） | LOG 一覧・回遊は Feed で吸収する。LOG 専用 Browse Top は MVP では不要。必要なら Category Top の entity_type: log として対応可能 |
| `/makers`（Maker 一覧） | Library 内（`/library/makers/[makerId]`）で扱う。Maker 一覧は Library の補助コンテンツ |
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
| 5 | `myrig-feed-v3.html` | `/feed` | All Feed / Following Feed 2タブ構成。LOG カード `log-feed` variant を主役に。`activity_item` コンポーネント設計もここで行う |
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
| Master 識別 | `/library/rigs/[masterId]`, `/library/parts/[masterId]`, `/library/makers/[makerId]`。UUID or slug。**全エンティティで複数形に統一**（旧: `/library/rig/`, `/library/maker/` → 廃止） |
| Admin プレフィックス | `/admin/*`。認証 middleware で保護 |
| i18n | 日本語優先 MVP。将来 `/en/*` プレフィックスを追加可能な設計にする（Next.js i18n routing）（**注記: #24裁定（2026-07-23）で日英2言語公開はMVP時点からに変更 — mobile-feedback-ledger #24参照**） |

---

---

※ Breakpoint正典: desktop=1025px↑ / tablet=721-1024px / mobile=720px↓（詳細: `docs/design-rules.md` Breakpoint正典セクション参照）

*Page Role Matrix v1.3 — 作成: 2026-05-03 / 補正: 2026-05-12 (Session 86) — /saved廃止・/garage/favorites・/garage/pins 正式化 / GarageShell-List・GarageDetailShell 分離確定*
