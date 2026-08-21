# Mobile Coverage Matrix v1 — MR-MOBILE-Phase0

**作成:** 2026-07-16 (JST) / Claude
**入力:** PC版39ページ（アーカイフ_.zip）/ r14 FINAL（files.zip・66files・md5照合済）/ page-role-matrix-v1.3 / auth-guard-spec-v1
**状態:** v1.2（2026-07-23 Coworkナレッジ監査 — **進捗欄と実績注記のみ更新**。treatment・裁定・集計はv1.1から不変）／v1.1 **確定**（2026-07-16 イタヤ承認済み。GPTクロスチェック裁定反映＋集計表記補正済み）。

---

## 0. 受領物照合（Phase 0-1/0-2）

| 資産 | 照合結果 |
|---|---|
| r14 FINAL zip | ✅ 66files一致。`アーカイフ_.zip`内 `mobile/0709 mobile/` とmd5同一 |
| PC版39ページ | ✅ ルートHTML 39本確認。SoT assets / docs 同梱 |
| v0.2 9ページ版ZIP | ✅ **受領・照合完了**（`myrig-mobile-claude-v0_2-flat__1_.zip`）。「9ページ版」＝ページ数（v0.2.9というバージョンではない）。2026-06-12作成・7/11精査スレッドで納品された旧系統資産。詳細は§0.5 |
| 旧5ページ版（APP-MR-MOCK-061） | 含有あり。ただしBottom Navに `/saved` を含む旧正典世代 → 照合対象外を推奨 |

---

## 0.5 v0.2 9ページ照合結果（Phase 0-2完了）

**全体判定:** shell系（mobile-shell.css/js）はr14の直系祖先（同一ヘッダー・r14がE13〜E26追記で発展）→ **shell回収不要、ページ骨格・部品表現のみ参考回収**。Bottom Navはホーム/探す/登録(中央)/Feed/ガレージで**現行確定構成と同一**（/savedなし・正典整合）。

| v0.2ページ | 対応Matrix行 | 扱い |
|---|---|---|
| home | #1（r14凍結） | **破棄**（r14で置換済み。rank-shelf等ランキング表現残存） |
| search-top | #2 /search（Phase 1-1） | **回収候補・最重要参考**（大型検索バー/カテゴリ探索ハブ） |
| search-result | #2 /search（Phase 1-1） | **回収候補**（固定UI: フィルタ/ソート/2列グリッド） |
| feed | #3 /feed（Phase 1-2） | 参考（構造のみ。カード表現はX型に刷新） |
| garage-top | #9 /garage（Phase 1-3） | 参考（通知集約#11は当時未反映） |
| rig-detail | #6（Phase 2-1） | 参考（カルーセル/sticky/アフィCTA） |
| browse-category | #4（Phase 2-3） | 参考（**ランキング部除外必須**） |
| public-garage | #17（Phase 4 skeleton） | 参考 |
| log-detail | #8（Phase 4 direct） | 参考 |

⚠️ 参考時の除外事項: ランキング表現（home / browse-categoryに残存・機能全廃済み）/ v0.2タイポYahoo!スケール（16px級・正典14px基準超過のまま未裁定）

---

## 1. Coverage Matrix（PC 39ページ全件）

treatment: **full mock** / **skeleton** / **direct**(Next.js直接実装) / **完了**
認証: auth-guard-spec-v1準拠（P1=redirect / P2=Login Required Modal / 公開=P3 mutateのみ）

### Browse / Relationship

| # | PCファイル | 正式URL | MVP | treatment | 再利用部品（主） | 認証 | 正典改訂 | 進捗 |
|---|---|---|---|---|---|---|---|---|
| 1 | myrig-home-v3.html | `/` | Must | **完了**（r14凍結） | Header / BottomNav / クイックアクセス / topics / 2列グリッド | 公開 | #9 #12 | 済 |
| 2 | myrig-search-v3.html | `/search` | Must | **full mock**（Phase 1-1） | 大型SearchField(E6) / filter-chip / seg-tabs / list-row / bottom-sheet | 公開 | — | **済**（P11-P12実装・契約v0.5・P14是正済み） |
| 3 | myrig-feed-v3.html | `/feed` | Should | **full mock**（Phase 1-2） | m-feed-card（X型: アバター行→本文→写真→アクション行） / seg-tabs（タブは**#28裁定で「おすすめ/フォロー中」に置換** — All/Followingは旧定義） | 公開 | — | **済**（P13〜P13b-r2で確定固定・#28イタヤ実機裁定済み） |
| 4 | myrig-browse-category-v3.html | `/category/[rigType]` | Must | **full mock**（Phase 2-3） | gcardグリッド / 棚 / filter。**/rigsと構造共用**（役割は別物） | 公開 | #8関連 | 未 |
| 5 | myrig-browse-parts-v3.html | `/parts` | Must | **skeleton** | Phase 2-3のカテゴリトップ構造流用。Parts Browse Top=探索ページのためdirectまで落とさない（GPT承認） | 公開 | — | 未 |

### Detail

| # | PCファイル | 正式URL | MVP | treatment | 再利用部品（主） | 認証 | 正典改訂 | 進捗 |
|---|---|---|---|---|---|---|---|---|
| 6 | myrig-rig-detail-v6.html | `/rig/[rigId]` | Must | **full mock**（Phase 2-1） | カルーセル+dots / sticky縮小 / アフィCTA(E4) / ImageLightbox | 公開 | — | 未 |
| 7 | myrig-parts-detail-v6.html | `/parts/[partId]` | Must | **full mock**（Phase 2-2） | Phase 2-1一式流用 | 公開 | — | 未 |
| 8 | myrig-log-detail-v6.html | `/log/[logId]` | Must | **direct**（Phase 4） | ImageLightbox / m-card-log | 公開 | — | 未 |

### Garage（Own）

| # | PCファイル | 正式URL | MVP | treatment | 再利用部品（主） | 認証 | 正典改訂 | 進捗 |
|---|---|---|---|---|---|---|---|---|
| 9 | myrig-garage-top-v6.html | `/garage` | Must | **full mock**（Phase 1-3） | アバター / 通知集約 / quick-actions(E5) / お気に入り・ピン導線 | **P1** | #11 | **モック済**（P15/P15b/P16。現状版 vs `?view=compact` のOwn View実機裁定待ち — 台帳#32） |
| 10 | myrig-garage-rigs-v6.html | `/garage/rigs` | Must | direct | Phase 1-3のリスト表現＋m-card-rigで確認済みにできる | P1 | — | 未 |
| 11 | myrig-garage-parts-v6.html | `/garage/parts` | Must | direct | 同上 | P1 | — | 未 |
| 12 | myrig-garage-logs-v6.html | `/garage/logs` | Must | direct | 同上 | P1 | — | 未 |
| 13 | myrig-garage-favorites-v6.html | `/garage/favorites` | Must | direct | Phase 1-3で集約導線として表現 | P1 | #11 | 未 |
| 14 | myrig-garage-pins-v6.html | `/garage/pins` | Must | direct | 同上 | P1 | #11 | 未 |
| 15 | myrig-garage-rig-detail-v6.html | `/garage/rigs/[rigId]` | Must | **skeleton（代表1画面）** | **GarageDetailShell**（page-role-matrix v1.3 §GarageShell分離）のモバイル配置確認: 戻り導線/保存状態/管理タブ(MANAGE等)/公開設定/危険操作。フォーム部はPhase 3-1流用 | P1 | — | 未 |
| 16 | myrig-garage-parts-detail-v6.html | `/garage/parts/[partId]` | Must | direct | #15 RIG編集skeletonからの派生＋Phase 3-2流用 | P1 | — | 未 |

### Public Garage

| # | PCファイル | 正式URL | MVP | treatment | 再利用部品（主） | 認証 | 正典改訂 | 進捗 |
|---|---|---|---|---|---|---|---|---|
| 17 | myrig-public-garage-v3.html | `/user/[username]` | Must | **skeleton**（Phase 4） | GarageShell共用ビュー切替 / Followボタン | 公開 | — | 未（**注記:** P16裁定で推奨版採用の場合、カバー画像・自己紹介・フォロー数はPublic側に残す責務分離が確定 — 台帳#32④） |
| 18 | myrig-public-garage-rigs-v3.html | 同上（RIGタブ） | Must | included state | #17単一ページ内のタブ状態（正典: 別ページではなくビュー切替） | 公開 | — | 未 |
| 19 | myrig-public-garage-parts-v3.html | 同上（PARTSタブ） | Must | included state | 同上 | 公開 | — | 未 |
| 20 | myrig-public-garage-logs-v3.html | 同上（LOGタブ） | Must | included state | 同上 | 公開 | — | 未 |

### Library

| # | PCファイル | 正式URL | MVP | treatment | 再利用部品（主） | 認証 | 正典改訂 | 進捗 |
|---|---|---|---|---|---|---|---|---|
| 21 | myrig-library-v3.html | `/library` | Must | **skeleton（Library family代表1本）** | 同一成果物内で2状態確認: ①Top入口ハブ構成 ②一覧の検索/フィルター/2-CTAカード構成 | 公開 | — | 未 |
| 22 | myrig-library-rigs-v3.html | `/library/rigs` | Must | direct | #21 skeleton一覧状態からの派生 | 公開 | — | 未 |
| 23 | myrig-library-parts-v3.html | `/library/parts` | Must | direct | 同上 | 公開 | — | 未 |
| 24 | myrig-library-makers-v3.html | `/library/makers` | Must | direct | 同上 | 公開 | — | 未 |
| 25 | myrig-library-rig-master-detail-v3.html | `/library/rigs/[masterSlug]` | Must | **skeleton**（Phase 4・製品詳細テンプレ） | アフィCTA / スペック表 / User Examples | 公開 | — | 未 |
| 26 | myrig-library-parts-master-detail-v3.html | `/library/parts/[masterSlug]` | Must | **skeleton**（Phase 4・同テンプレ共用） | 同上 | 公開 | — | 未 |
| 27 | myrig-library-maker-detail-v3.html | `/library/makers/[makerSlug]` | Later | **direct**（Later） | — | 公開 | — | 未 |

### Utility / Account / Info

| # | PCファイル | 正式URL | MVP | treatment | 再利用部品（主） | 認証 | 正典改訂 | 進捗 |
|---|---|---|---|---|---|---|---|---|
| 28 | myrig-register-rig-v2.9.5.html | `/register/rig` | Must | **full mock**（Phase 3-1） | 10アコーディオン / sticky CTA / キーボード / 下書き / 離脱警告 | **P2** | — | 未 |
| 29 | myrig-register-parts-v0.1.10.html | `/register/part` | Must | **full mock**（Phase 3-2） | Phase 3-1一式流用 | P2 | — | 未 |
| 30 | myrig-log-composer-modal-v0.3.9.html | `/register/log` | Must | **full mock**（Phase 3-3） | 500字/3枚 / bottom-sheet / 画像グリッド | P2 | — | 未 |
| 31 | myrig-settings-pc-v0.2.5.html | `/settings` | Should | **direct**（Phase 4） | — | **P1** | — | 未 |
| 32 | myrig-notifications-pc-v0.1.1.html | `/notifications` | Should | **direct**（Phase 4） | ガレージ配下導線（#11） | P2 | #11 | 未 |
| 33 | myrig-auth-v1.html | `/login` `/signup` | Must | **direct**（Phase 4）＋遷移図・状態一覧先行 | LoginRequiredModal文言はauth-guard-spec §3 | 公開 | — | 未 |
| 34 | myrig-auth-onboarding-pc-v0.2.html | `/onboarding` | Should | **direct**（Phase 4）＋遷移図先行 | — | 未規定（auth-guard-spec対象外・遷移図で確定） | — | 未 |
| 35 | myrig-welcome-tour-v0.1.html | PROPOSED / SoT改訂待ち | Should | **direct**（Phase 4）＋遷移図先行 | — | 未規定 | **起票要** | 未 |
| 36 | myrig-about-v0.1.html | PROPOSED `/about` / SoT改訂待ち | Should | **direct**（Phase 4） | — | 公開 | **起票要** | 未 |
| 37 | myrig-support-us-v0.1.html | PROPOSED `/support` / SoT改訂待ち | Should | **direct**（Phase 4） | — | 公開 | **起票要** | 未 |
| 38 | myrig-support-legal-report-pc-v0.1.html | PROPOSED / SoT改訂待ち | Should | **direct**（Phase 4） | 通報モーダルはP3連動 | 公開 | **起票要** | 未 |
| 39 | myrig-error-states-v0.1.html | 共通エラー画面（URLなし） | Must | **direct**（Phase 4） | empty-state-spec-v1 | — | — | 未 |

### 補助行（PCファイルなし・モバイル固有）

| ID | 対象 | treatment | 備考 |
|---|---|---|---|
| A | `/rigs`（RIG総合ブラウズ・新設） | **full mock**（Phase 2-3で#4と構造共用実装） | 正典外 → 改訂候補#8起票済。役割定義は#4と別物（曖昧統合禁止） |
| B | CreateActionSheet（登録ハブ） | **Phase 1-4**（ページではなくシート） | RIG/パーツ/LOG → `/register/*`。未ログイン=P2 Modal。→ **✅実装済み**（P05 shell抽出・契約§3.6・Dialog Controller管理下） |
| C | LoginRequiredModal | **共通Shell新規・独立ページモック不要** | 契約＋状態サンプルのみで実装可（文言/CTA/close/focus trap/scroll lock/next検証はauth-guard-spec＋dialog-interaction-specで確定済み）。→ **✅実装済み**（P05新規・P11で#14文言5グループ化） |
| D | SubCategory（`/category/[rigType]/[categorySlug]` / `/parts/category/[slug]`） | direct | Category Top派生。PCモック自体なし。Should優先度 |

**集計:** full mock成果物9本（対象URL 10件。`/category/[rigType]`と`/rigs`はPhase 2-3の同一成果物内で別状態として制作）＋Home完了1本 / skeleton 6本（/parts・Public Garage・Library製品詳細テンプレ×2・Garage RIG編集代表・Library family代表） / included state 3件（Public Garageタブ） / direct 残り全部

---

## 2. mobile-shell v0.3 抽出範囲（Phase 0-5・引き継ぎ書確定リスト ↔ r14実資産対応）

| v0.3抽出対象 | r14での所在 | 処置 |
|---|---|---|
| Header | mobile-shell.css §A（小ロゴ+RC GARAGE+検索窓+?guest=1ピル） | 抽出 |
| BottomNav | §A＋E1（48px当たり判定） | 抽出 |
| SafeArea | E2 | 抽出 |
| PageContainer | `.mobile-page-content` / `#mobileViewport` / `--mobile-preview-width`＋@media(max-width:500px) | 抽出 |
| SearchField | ヘッダー検索窓＋E6/E6b（探す用大型はPhase 1-1で拡張） | 抽出 |
| CreateActionSheet | mobile-shell.js `openBottomSheet('create')`＋bottom-sheet CSS | 抽出 |
| LoginRequiredModal | **なし → 新規**（auth-guard-spec準拠） | 新規 |
| 共通幅・余白token | mobile-tokens.css `--ms-*`一式 | 抽出 |
| **含めない** | E12〜E26のHome専用表現（topics/ストリーム/Yahoo型/RoomClip型部品） | `.page-e3` adapterとして残置（Home視覚凍結） |

カード: 互換クラス併記方式（`.gcard, .m-card-rig { … }`）。SoT_card-components.js（`アーカイフ_.zip`内 `assets/js/` に現物確認済）との対応表を抽出作業の先頭で作成。

**⚠️ app-shell-draft-v1はShell抽出の根拠に使用しない**（Bottom Navに/saved・Create=Header固定の旧設計 — 2026-05-03作成、現行確定と矛盾。grep検証済み）。

**参照優先順位（Shell契約・抽出作業で固定）:**
1. r14凍結Home → 2. 本Coverage Matrix → 3. Page Role Matrix v1.3 → 4. auth-guard-spec等の現行仕様 → 5. PC版39ページ → 6. v0.2 9ページ版・app-shell-draft-v1（参考資料のみ）

**回帰ゲート（必須）:** Phase 0-5のShell抽出直後、Phase 1-1着手前に **r14 Homeのbefore/after回帰検証**を実施（Playwright 3 viewport スクショ差分＋主要要素bounding box照合、共通CSS変更によるHome破壊がないことを確認）。

---

## 3. イタヤ最終承認（1点）

**GPTクロスチェック裁定反映後の最終形（本Matrix v1.1）を一括承認するか。**
G1 /parts=skeleton ✅ / G2 garage一覧5件=direct ✅ / G3 RIG編集=skeleton代表・PARTS編集=direct派生 / G4 Public Garage=1ページ＋3タブ状態 / G5 Library family skeleton1本＋派生3件direct・SubCategory=direct

承認後の進行順（回帰ゲート込み・確定）:
1. Matrix v1.1確定 → 2. Phase 0-4 共通コンポーネント契約ドラフト → 3. GPTクロスチェック → 4. Phase 0-5 mobile-shell v0.3最小抽出 → 5. **r14回帰ゲート** → 6. Phase 1-1 探す構成案

（クローズ済: v0.2 9ページ版＝受領・照合完了（§0.5）。旧5ページ版APP-MR-MOCK-061・app-shell-draft-v1は旧世代のため根拠外）

---

## 4. v1.2変更履歴（2026-07-23 Coworkナレッジ監査）

- 進捗欄のみ実績反映: #2 /search=済（P11-P12・契約v0.5・P14是正） / #3 /feed=済（P13〜P13b-r2・#28確定） / #9 /garage=モック済（P15/P15b/P16・Own View裁定待ち） / 補助行B・C=実装済み（P05〜）
- #3の部品欄・#17備考に#28/#32裁定の注記追加
- **treatment・優先度・集計・承認記録（§3）はv1.1から一切変更なし**

---
*本ドキュメントは台帳（起票のみ）。正典改訂候補のWRITEは配布キュー反映まで行わない。*
