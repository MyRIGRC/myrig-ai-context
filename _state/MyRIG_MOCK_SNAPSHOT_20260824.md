<!-- Status: OBSERVATION / NOT CANON -->
<!-- Observed: 2026-08-24 -->

# MyRIG モック現況スナップショット（観測記録 / 2026-08-24）

> **Status: OBSERVATION / NOT CANON**
> **Observed: 2026-08-24**
>
> この文書は 2026-08-24 時点のモック実測スナップショット。**確定仕様ではない。**
>
> 2026-08-25 に Search は大幅修正・CLOSE 済みのため、
> **Searchに関する観測値・不具合・ページ状態は一部陳腐化している。**
>
> Searchの現在仕様は以下を優先する:
> 1. `_AI/MyRIG_CURRENT.md`
> 2. `docs/search/search-page-plan-v2.md`
> 3. `_decisions/2026-08-25_search-closure-v1.md`
>
> 本書は **Web文法・全体配線監査の入力資料**として使用し、
> 現在状態を断定する際は**対象実装を再確認すること**。
>
> 正本参照位置はここ1箇所。`myrig-mockup/docs/` へ複製しない。

---

**調査日:** 2026-08-24
**対象:** `MyRIGRC/myrig-mockup`（`App/MOKUP/myrig_pc_Ver3`）
**調査時のCURRENT:** `MYRIG-20260824-030`
**性質:** READ ONLY 調査。修正・commit・push・正典更新は一切行っていない
**除外:** `_archive/` `_backup/` `_compare/`（A/B/C/D比較の旧実験）`board/`

> **この調査の前提**
> ランチャー / VERSIONS.md / CURRENT の記述は手掛かりとしてのみ使い、
> すべて実ファイルまたはブラウザ実操作で検証した。
> 「見つからない」と「存在しない」は区別し、確認できなかったものは**判定不能**と記す。

---

## 0. 要約（先に結論）

1. **モックの実ページ数は PC 39 / Mobile 41。** CURRENT の「Mobile 約55ページ」は**ランチャーのカード数**（状態違いを別カウント）であり、実ページ数ではない
2. **PCモックはサービスとして繋がっていない。** 40ページ中29ページが行き止まり。原因はヘッダーナビが29ページで `href="#"`。Mobileは全ページにボトムナビがあり行き止まりゼロ
3. **検索結果から詳細ページへ行けない**（PC/Mobile とも）。Feedも同様
4. **絞り込みが実際に動いているのは、今日データ駆動化した検索だけ。** Library・Feed・ガレージは PC/Mobile とも見た目のみ。**ただしその検索も、単一種別ソート・ページャ・結果→詳細は未接続**（§12）
5. **`SoT_` を名乗るCSSがMobile用とPC用に分岐し、片方だけ修正されている**
6. 共通JS `mobile-shell.js` がグローバルに掴むセレクタと、ページ固有実装が**6種で衝突しうる**（うち `[data-chipgroup]` は13ページ）
7. **リンク切れ（`.html`参照）はゼロ。** 検出された「切れ」は全て実装時ルート（`/register/rig` 等）の意図的プレースホルダ

---

## 1. 現行ページインベントリ

### 1-1. 実体の集計

| | CURRENT記載 | 実測 | 備考 |
|---|---|---|---|
| PC | 39ページ | **39ページ（+孤立1）** | 一致。`pc/preview.html` が孤立 |
| Mobile | 約55ページ | **41ページ** | 「55」はランチャーのカード数 |

**ランチャーのカード総数 = 55枚。** `?guest=1` `?empty=1` `?owner=1` などの状態違いを1枚ずつカードにしているため、ページ数と一致しない。

### 1-2. ファイル分布

```
直下          43 HTML  → Mobile 41ページ + index.html(ランチャー) + compare.html(見比べツール)
pc/           40 HTML  → PC 39ページ + preview.html(孤立)
pc/assets/sot-templates   7  → テンプレート（ページではない）
pc/assets/catalogs        7  → 部品カタログ（ページではない）
_compare       5  → 検索トップA/B/C/D比較の旧実験
board/         1  → sidebar-plan
```

### 1-3. 機能グループ（ランチャー15グループ / 55カード）

| グループ | カード数 | Mobile | PC |
|---|---|---|---|
| ホーム | 1 | index-e-roomclip | myrig-home-v3 |
| 検索 | 3 | search / search-results | myrig-search-v3（1枚で兼務） |
| Feed | 3 | feed | myrig-feed-v3 |
| ブラウズ | 2 | browse-category / browse-parts | myrig-browse-* |
| RIG詳細 | 2 | rig-detail | myrig-rig-detail-v6 |
| パーツ詳細 | 3 | parts-detail | myrig-parts-detail-v6 |
| ログ詳細 | 3 | log-detail | myrig-log-detail-v6 |
| 公開ガレージ | 6 | user-garage 系4 | myrig-public-garage-* |
| マイガレージ | 9 | garage 系7 | myrig-garage-* |
| RIG詳細（オーナー編集） | 1 | garage-rig-detail | myrig-garage-rig-detail-v6 |
| MyRIG CATALOG | 7 | library 系7 | myrig-library-* |
| 認証・オンボーディング | 4 | auth / onboarding / welcome-tour / error-states | 対応4 |
| 設定・通知 | 2 | settings / notifications | 対応2 |
| 登録・投稿 | 3 | register-rig / parts / log | 対応3 |
| 情報・法務・サポート | 4 | help / legal / about / support-us | support-legal が2枚兼務 |
| 認証・状態デモ | 2 | index-e-roomclip の状態違い | — |

### 1-4. 動作状態の分布

インラインJS量と `addEventListener` 数による分類（実挙動は §6 で別途検証）。

| | JS駆動 | 一部のみ | 静的 |
|---|---|---|---|
| Mobile 41 | 22 | 13 | 6 |
| PC 39 | 15 | 5 | **19** |

**PCは約半数が静的。** 特に以下はインラインJSゼロ。

- `pc/myrig-auth-v1.html` — JSゼロ かつ 外部JS読み込みもゼロ（app-shellすら無い）
- `pc/myrig-garage-rigs-v6` / `garage-parts-v6` / `garage-logs-v6` — 一覧ページだがJSゼロ
- `pc/myrig-library-v3` / `myrig-library-maker-detail-v3` — JSゼロ
- Mobile `library.html` / `library-maker-detail.html` — インラインJS 39バイト（実質ゼロ）

### 1-5. 孤立・重複

| 項目 | 内容 |
|---|---|
| `pc/preview.html` | 159KB。`myrig-home-v3.html` と **841行差**。ランチャー・compare のどちらからも未参照。**孤立** |

---

## 2. PC / Mobile 対応表

**全Mobileページに対応PCページが存在する。** ただし以下は1つのPCページが複数のMobileページを兼務。

| PCページ | 兼務しているMobile | 評価 |
|---|---|---|
| `myrig-search-v3.html` | `search.html` / `search-results.html` | **意図的**（2026-08-24裁定「PCに検索トップを置かない」） |
| `myrig-support-legal-report-pc-v0.1.html` | `help.html` / `legal.html` | 要確認。PC側で1ページに統合されている |

逆（1つのMobileが複数PCへ）は**なし**。

---

## 3. サービス遷移グラフ

### 3-1. 全体像

```
【Mobile】全ページにボトムナビ（37/41）＝どこからでも4拠点へ戻れる

        ┌─────────────────────────────┐
        ↓                                     │
   index-e-roomclip ──→ browse-category/parts │
        │             ──→ library ──→ library-rigs/parts/makers
        │                              └→ *-master-detail
        ↓
     search ──→ search-results ──→ ✕ 詳細へ行けない（href="#"）
        └──→ user-garage
        ↓
      feed ──→ ✕ 詳細へ行けない（href="#"）
        ↓
     garage ──→ garage-rig-detail
        └──→ garage-rigs/parts/logs/favorites/pins（相互リンクあり）
        └──→ user-garage ──→ garage-rig-detail ← ⚠ 誤結線の疑い

【PC】ヘッダーナビが29ページで href="#" ＝ページ間が繋がっていない

   ランチャー ──→ 各ページ（1枚ずつ開くだけ）
                    └ 例外: library クラスタのみ相互リンクあり
                      library ⇄ library-rigs/parts/makers ⇄ master-detail
```

### 3-2. 連結性の実測

| | Mobile 41 | PC 40 |
|---|---|---|
| 行き止まり（他ページへ出て行かない） | **0** | **29** |
| 他ページからリンクされない | 2 | **26** |
| グローバルナビが実リンク | 37ページ | **1ページ** |

**PCヘッダーナビ（`nav.app-nav`）の実体**

| 内容 | ページ数 |
|---|---|
| `('#','#','#')` — 全て仮リンク | **29** |
| navそのものが無い | 8 |
| `('/','/feed','/library')` — 実装時ルート | 2 |
| 実相対リンク | **1**（`myrig-about-v0.1.html` のみ） |

**Mobileボトムナビ（`nav.mobile-bottom-nav`）**

| 内容 | ページ数 |
|---|---|
| 4ファイルへの実リンク | **37** |
| navなし | 4（auth / onboarding / welcome-tour / error-states） |

被リンク数上位（Mobile）: `index-e-roomclip` 40 / `search` 38 / `garage` 38 / `feed` 38

### 3-3. 検出した問題

| # | 種別 | 内容 |
|---|---|---|
| B-1 | **行き止まり** | **検索結果 → 詳細ページへ行けない。** PC/Mobile とも結果カードが `href="#"`（PC検索は `#` が112箇所）。検索して結果を見た先が無い |
| B-2 | 行き止まり | **Feed → 詳細ページへ行けない。** 同上 |
| B-3 | **PC/Mobileで意味が違う** | PCはページ間が繋がっておらず「1枚ずつ開く画集」、Mobileは「歩けるサービス」。同じモックだが体験の性質が別物 |
| B-4 | **誤結線の疑い** | `user-garage.html`（他人の公開ガレージ）のRIGカードが `garage-rig-detail.html`（**自分のRIG編集画面**）を指す。コード上は `P22 B1-7: 既定の遷移先は Own RIG 詳細` と意図的に見えるが、公開ビューの前提と矛盾 |
| B-5 | ナビ欠落 | PC `settings` / `notifications` にヘッダーナビが無い。モーダル系（log-composer / register）と同じ扱いになっている |
| B-6 | 仮リンク多数 | `href="#"` が PC home 156 / preview 152 / browse-category 142 / browse-parts 124 / search 112 |

### 3-4. リンク切れ

**`.html` 参照のリンク切れはゼロ。**

検出された「実在しない参照」は全て以下のいずれかで、**意図的**と判断。

- 実装時ルート: `/register/rig` `/register/part` `/register/log` `/legal/terms` `/legal/privacy` `/legal/tokushoho` `/user/{name}` `/library/*` `/external/*` `/login` `/signup` 等
- JS文字列連結の誤検出: `' + page + '` `' + q.href + '` 等（検出手法側の限界）

---

## 4. CSS / JS 依存マップ

### 4-1. 共有アセット

```
Mobile用
  css/mobile-shell.css        188KB   ← 巨大
  css/mobile-tokens.css         6KB
  css/sot/SoT_app-shell.css    12KB
  css/sot/SoT_component-catalog-v6.css  31KB
  css/sot/SoT_footer.css        6KB
  css/sot/SoT_tokens-v6.css     3KB
  js/mobile-shell.js           33KB   ← 全41ページが読む
  js/modern-screenshot.umd.js  28KB   ← compare.html 専用

PC用
  pc/assets/css/SoT_app-shell.css          17KB
  pc/assets/css/SoT_component-catalog-v6.css 31KB
  pc/assets/css/SoT_browse-sidebar.css     19KB
  pc/assets/css/SoT_public-garage-sidebar.css 13KB
  pc/assets/css/SoT_filter-sidebar.css     10KB
  pc/assets/css/SoT_garage-sidebar.css     10KB
  pc/assets/css/SoT_library-header.css      7KB
  pc/assets/css/SoT_footer.css              6KB
  pc/assets/css/SoT_garage-drawer.css       3KB
  pc/assets/css/SoT_tokens-v6.css           3KB
  pc/assets/css/SoT_skeleton.css            2KB
  pc/assets/js/SoT_card-components.js      80KB   ← 最大
  pc/assets/js/SoT_register-shell-components.js 21KB
  pc/assets/js/SoT_app-shell.js             6KB
  pc/assets/js/SoT_browse-sidebar.js        2KB
```

### 4-2. 被参照数

| CSS | 数 | | JS | 数 |
|---|---|---|---|---|
| SoT_app-shell.css | 73 | | mobile-shell.js | 41 |
| SoT_footer.css | 72 | | SoT_app-shell.js | 32 |
| SoT_component-catalog-v6.css | 62 | | SoT_card-components.js | 23 |
| mobile-tokens.css | 41 | | SoT_browse-sidebar.js | 4 |
| mobile-shell.css | 41 | | SoT_register-shell-components.js | 2 |
| SoT_library-header.css | 7 | | | |
| SoT_garage-sidebar.css | 6 | | | |
| SoT_filter-sidebar.css | 5 | | | |
| SoT_tokens-v6.css | **5** | | | |

### 4-3. 依存パターンの分裂

| | パターン数 |
|---|---|
| Mobile 41ページ | **1種類**（全ページ同一：mobile-shell.js + CSS 5本） |
| PC 39ページ | **14種類** |

**PCでCSS/JSを一切読まないページ（完全自己完結）**

- `pc/myrig-notifications-pc-v0.1.1.html`
- `pc/myrig-settings-pc-v0.2.5.html`
- `pc/myrig-support-legal-report-pc-v0.1.html`

### 4-4. 同名だが別実装（SoTの分裂）

`css/sot/`（Mobile）と `pc/assets/css/`（PC）に同名ファイルがあり、内容が乖離している。

| ファイル | 状態 |
|---|---|
| `SoT_app-shell.css` | **乖離**（460行 vs 549行 / 差分103行） |
| `SoT_component-catalog-v6.css` | **乖離**（差分4行） |
| `SoT_footer.css` | 同一 |
| `SoT_tokens-v6.css` | 同一 |

`SoT_component-catalog-v6.css` の差分内容:

```css
/* Mobile側だけに存在 */
/* P22-B5: モバイル配布コピーのみの調整。タッチ端末では :hover がタップ後に張り付くため
   ホバー可能環境に限定する。PC側のSoT本体にも同じ処置が要る（#19 PC改訂キューへ起票）。 */
@media (hover: hover) and (pointer: fine){ a:hover{text-decoration:underline} }

/* PC側 */
a:hover{text-decoration:underline}
```

**「PC側にも同じ処置が要る」とコメントに書かれたまま未対応。** SoT（Source of Truth）を名乗るファイルが2つに分かれ、片方だけ直っている状態。

### 4-5. トークン定義の重複

| ファイル | `:root` | `--cat-rig` 定義 |
|---|---|---|
| `pc/assets/css/SoT_app-shell.css` | 1 | **3箇所** |
| `css/sot/SoT_app-shell.css` | 1 | **3箇所** |
| `pc/assets/css/SoT_tokens-v6.css` | 1 | 2箇所 |
| `css/mobile-tokens.css` | 2 | 0 |

`@import` は**使われていない**。`SoT_tokens-v6.css` を読むのは5ページのみだが、同じトークンが `SoT_app-shell.css` にも定義されている。
→ **どのトークン定義が効いているかが、ファイルを見ただけでは決まらない。**

### 4-6. 共通JSとページ固有実装の衝突マップ

`mobile-shell.js` がグローバルに掴むセレクタ（全41ページに適用される）。

```
[data-chipgroup]                  ← 単一選択を強制（クリックで兄弟の is-active を全消し）
.filter-chip
.seg-tab / .seg-tabs
.bottom-sheet
.action-bar__btn[data-act]
.feed-card__stat[data-like]
.create-item
.build-group__hd
.mobile-bottom-nav__item[data-nav]
```

このうち**ページ側でも同じものを操作している箇所**（＝今日踏んだ事故と同型）。

| 共通JSが握る | ページ側でも操作 | ページ数 |
|---|---|---|
| `[data-chipgroup]` | about / auth / error-states / help / legal / notifications / onboarding / register-log / register-parts / register-rig / settings / support-us / welcome-tour | **13** |
| `.filter-chip` | garage-favorites / garage-logs / garage-parts / garage-pins / garage-rigs / search-results / user-garage-logs / user-garage-parts / user-garage-rigs | **9** |
| `.action-bar__btn` | garage-parts-detail / log-detail / parts-detail / rig-detail | 4 |
| `.seg-tab` | garage / user-garage | 2 |
| `.bottom-sheet` | search-results | 1 |
| `.feed-card__stat` | feed | 1 |

**2026-08-24 の実例**: `search-results.html` のコンテンツタイプを複数選択化した際、`[data-chipgroup]` の単一選択ハンドラが兄弟の `is-active` を全消しして機能しなかった。
→ 同じ構造が**13ページで再発しうる**。特に `.filter-chip` を持つガレージ系5ページは、複数選択化した瞬間に同じ問題を踏む。

---

## 5. 共通UI部品マップ

| 部品 | クラス | 使用面 | 備考 |
|---|---|---|---|
| カードコンポーネント | `myrig-rig-card` / `myrig-part-card` / `myrig-log-card` | PC（Web Components） | `SoT_card-components.js` 80KB。`variant="browse"` 等 |
| カード（Mobile） | `.gcard` / `.sr-row` / `.m-list-row` | Mobile | `.m-card-rig` 等は互換併記のみで実スタイルなし |
| Libraryカード | `.lib-result-card` / `.pcard` | PC / Mobile | 検索のLibrary補助導線でも流用 |
| 種別バッジ | `.cat-badge--*` / `.gcard__type--*` / `--cat-rig/parts/log` | 全面 | v8トークン（RIG=黄 / PARTS=赤 / LOG=青） |
| チップ | `.filter-chip` / `.fs-chip` / `.type-chip` | 全面 | **3系統が併存** |
| チェック項目 | `.fs-check-item` | PCサイドバー | 89個がHTML直書きだった（検索は2026-08-24にデータ駆動化） |
| ボトムシート | `.bottom-sheet` | Mobile | Dialog Controller管理 |
| ヘッダー | `.app-header`（PC） / `.mobile-shell-header`（Mobile） | 各面 | |
| ナビ | `.app-nav`（PC） / `.mobile-bottom-nav`（Mobile） | 各面 | PCは29ページで `href="#"` |
| フッター | `SoT_footer.css` | 72ページ | 唯一Mobile/PCで同一のSoT |

---

## 6. 「見た目だけ機能」一覧

ブラウザで実際にクリックし、**表示件数と中身が変わるか**を測定した結果。

### 分類1: 正常に機能

| ページ | 機能 |
|---|---|
| `pc/myrig-search-v3.html` | Facet絞り込み / 関連順・新着順 / URL同期（**2026-08-24 実装**）。⚠️ ページャと単一種別ソートは**未接続**（§12-A / §12-D） |
| `search-results.html` | Facet絞り込み / URL同期。⚠️ 単一種別ソートは**未接続**（§12-D） |
| `rig-detail.html` ほか詳細系 | いいね / お気に入り（DOM変化を確認） |
| `feed.html` | いいね |

### 分類4: 見た目だけで機能していない（実測）

| ページ | 操作 | 実測 |
|---|---|---|
| `garage-rigs.html` | 並び替えチップ「追加順」 | 7→7件 中身同一 |
| `garage-parts.html` | 同 | 24→24件 中身同一 |
| `garage-logs.html` | 「古い順」 | 24→24件 中身同一 |
| `garage-favorites.html` | 「追加が古い順」 | 24→24件 中身同一 |
| `garage-pins.html` | 「追加が古い順」 | 14→14件 中身同一 |
| `user-garage-rigs.html` | 「更新日（古い順）」 | 7→7件 中身同一 |
| `user-garage-parts.html` | 「追加順」 | 24→24件 中身同一 |
| `user-garage-logs.html` | 「投稿日（古い順）」 | 24→24件 中身同一 |
| `feed.html` | タブ「フォロー中」 | 4→4件 中身同一 |
| `pc/myrig-library-rigs-v3.html` | メーカー「Traxxas 167」 | 12→12件 中身同一 |
| `pc/myrig-library-parts-v3.html` | メーカー「RC4WD 430」 | 12→12件 中身同一 |
| `pc/myrig-feed-v3.html` | 「RIGに紐付いている」 | 14→14件 中身同一 |
| `pc/myrig-garage-rigs-v6.html` | サイドバー「RIG 7」 | 6→6件 中身同一 |
| `pc/myrig-garage-logs-v6.html` | 同 | 6→6件 中身同一 |

### 分類5: 判定不能

| ページ | 理由 |
|---|---|
| `pc/myrig-public-garage-rigs-v3.html` | 想定セレクタで操作対象を発見できず |
| `pc/myrig-browse-category-v3.html` | 同上 |
| `pc/myrig-browse-parts-v3.html` | 同上 |

> **これらは「機能していない」ではない。** 探し方が合わなかっただけの可能性がある。

### この節の解釈

**このモックで絞り込み・並び替えが実際に機能しているのは、2026-08-24 に作った検索だけ。**
Library・Feed・ガレージは PC/Mobile とも見た目のみで、`is-active` クラスが付くだけ。

これは「バグが多い」のではなく、**モックがそういう作られ方をしてきた**ということ。
見た目とレイアウトを確認する目的なら妥当な作り方であり、それ自体は問題ではない。

**注意すべきなのは、CURRENT がそれを「実装済み」と読める書き方をしていた点。**
モックの作り方の問題ではなく、記述の問題。

---

## 7. 全体を見て初めて判明した構造上の問題

| # | 問題 | なぜ単体では気づけなかったか |
|---|---|---|
| S-1 | **PCモックがサービスとして繋がっていない**（29/40が行き止まり、ナビが `href="#"`） | 1ページずつ見ると完成度が高く見える。遷移グラフを作って初めて分かる |
| S-2 | **検索結果から詳細へ行けない** | 検索ページ内だけを見ていた。今日の議論は全て「結果に何を出すか」で、「出した先」を見ていなかった |
| S-3 | **絞り込みが動いていないのは検索固有ではなく全面的** | PC検索を直したとき「PC固有の問題」と報告した。他ページを測って初めて全面的と分かった |
| S-4 | **SoTが2つに分裂し片方だけ修正されている** | ファイル名が同じなので、片方を見れば足りると錯覚する |
| S-5 | **共通JSの単一選択ハンドラが13ページに潜在** | 今日1ページで踏んだ。横断で数えて初めて再発範囲が見える |
| S-6 | **トークンが複数ファイルで重複定義、`@import` なし** | ページ単位では動くので気づけない |
| S-7 | **CURRENT のページ数がカード数と混同されていた** | 数字を信じて作業していた |
| S-8 | **`pc/preview.html` が孤立**（home と841行差） | ランチャーにもcompareにも出てこないため存在に気づかない |

---

## 8. 検索設計へ影響する発見

| # | 発見 | 検索設計への影響 |
|---|---|---|
| 8-1 | **検索結果 → 詳細への遷移が無い** | 「検索結果に何を出すか」は詰めたが、**出した先が無い**。Community検索の価値は詳細へ辿れて初めて成立する。第2周ではなく、実装時の必須要件 |
| 8-2 | 絞り込み非機能は全面的だった | 「PC検索だけ壊れている」という私の報告は不正確だった。検索は**唯一直っている面**であり、他が追随していない |
| 8-3 | `.filter-chip` 系9ページが同じ衝突リスクを持つ | ガレージ一覧を複数選択化すると、検索で踏んだ `[data-chipgroup]` 事故を再発する。**検索で確立した規約を他面へ渡す価値がある** |
| 8-4 | PCサイドバーの語彙とカード属性が不一致だった（`ロッククローラー` vs `Rock Crawler`） | Library等の他サイドバーでも同じ可能性。**表示ラベルと値の分離**は検索だけの問題ではない |
| 8-5 | PC/Mobile でダミーデータが別物 | 「同じ条件＝同じ結果」は**意味論の一致**としてしか検証できない。数値一致は原理的に不可能。契約の書き方に注意が要る |
| 8-6 | Library補助導線の `.lib-result-card` は Library本体と共通 | 検索側で見た目を変えると Library にも波及しうる。今回は検索側でスコープを絞ったが、恒久的には要注意 |

---

## 9. Next.js 実装前に直すべきもの

**判断基準:** 後から直すコストが高い / 知らずに実装すると破綻する / 複数箇所へ波及する

| # | 項目 | 理由 |
|---|---|---|
| N-1 | **SoT CSS の分裂を解消** | 「SoT」を名乗るファイルが2つあり片方だけ修正されている。実装時にどちらを正とするか決められない。`SoT_component-catalog-v6.css` の hover 差分は明示的に「PC側にも要処置」と起票済みのまま放置 |
| N-2 | **トークンの定義箇所を1つにする** | `--cat-*` が `SoT_app-shell.css` と `SoT_tokens-v6.css` に重複。`@import` なし。Design Token は L1（恒久ルール）扱いなのに、実体が多重定義 |
| N-3 | **検索結果 → 詳細の結線** | 検索の価値が成立しない。URL設計（詳細ページのIDパラメータ）とセットで決める必要がある |
| N-4 | **表示ラベルと値の分離** | 語彙不一致（日本語ラベル vs 英語値）は実装時に必ず踏む。Facet定義に `label` と `value` を分けて持つ規約を先に決める |
| N-5 | **共通ハンドラの適用範囲を明示** | `mobile-shell.js` がグローバルに13ページ分の `[data-chipgroup]` を単一選択強制している。React化時に「なぜこれが単一選択なのか」が失われる |
| N-6 | **公開ガレージ → オーナー編集画面の結線** | 権限モデルに関わる。RLS/認証ガードの設計と矛盾しないか確認が要る |

---

## 10. モック第2周で見ればよいもの

**判断基準:** 全ページが揃ってから通しで触った方が判断材料が増えるもの

| # | 項目 |
|---|---|
| M-1 | PCヘッダーナビの `href="#"` を実リンクにするか（モックとして必要か、実装時で足りるか） |
| M-2 | 絞り込み・並び替えを他ページでも動かすか（見た目確認が目的なら不要かもしれない） |
| M-3 | 検索シートの高さ・通知位置・チップ配置・適用ボタン・余白・文言（2026-08-24 に凍結しないと決定済み） |
| M-4 | `pc/preview.html` の扱い（削除 / `_archive` へ退避 / 復活） |
| M-5 | PC `settings` / `notifications` にヘッダーナビを付けるか |
| M-6 | Home → 検索 → 絞り込み → 詳細 → 戻る → Garage/Feed の**通し操作**での違和感 |
| M-7 | Mobileで絞り込み中に結果がリアルタイム更新されないことの是非 |
| M-8 | 「ビルダー」文言の再検討（実データの作例比率を見てから） |
| M-9 | 人気検索ワード / PR枠の発見棚化 |
| M-10 | ユーザー一覧の最終配置 |

---

## 11. 【別項目】検索基盤の現行実装・契約7点の扱い

### 判定：**いま正典化するのは見送る。**

理由は品質ではなく**時期**。モックはまだ未完成で、検索結果→詳細（§8-1）という
基幹の穴が空いたままの状態を「確定仕様」として固めると、後で正典を書き直すことになる。
正典は「2人が独立に判断して食い違ったら壊れるもの」を入れる場所であり、
まだ動いている土台を入れる場所ではない。

**代わりに、以下を本ナレッジの記録として残す。** 実装フェーズへ移る段階で、
このセクションを一次資料として正典化を再検討する。

### 11-1. 契約7点の成立状況（2026-08-24 GPT監査を受けて訂正）

> ⚠️ **本節は当初「7/7成立」と書いていたが、誤りだった。**
> 私の検証は**各面の中だけ**で閉じており、
> ①URLの値が結果に効いているか ②両面で同じURLが同じ意味になるか
> を見ていなかった。GPT監査の指摘を実ファイル・ブラウザ実測で確認し、下表へ訂正する。
> 検証の詳細は §12。

| # | 契約 | 面内 | 横断 | 実測 |
|---|---|---|---|---|
| 1 | 検索状態の一元管理 | ○ | ○ | DOMから状態を読まない構造。ただし1オブジェクトではなく複数変数 |
| 2 | URLから完全復元 | **×** | **×** | `page` / `per` が**カードに効かない**（§12-A）。type slugが両面不一致（§12-B） |
| 3 | Facet定義駆動 | ○ | **△** | 両面とも定義駆動だが、**定義集合が違う** |
| 4 | 同軸OR・異軸AND | ○ | ○ | PC実測 12→15（OR）/ 15→4（AND） |
| 5 | Facetごとの適用Scope | ○ | **×** | Mobileに `scale` / `pcond` があり PC に無い |
| 6 | UIと検索ロジックの分離 | ○ | △ | 両面ともページ内JS。共通Search Serviceは無い（モック段階なので許容） |
| 7 | 件数はデータ由来 | ○ | ○ | 静的値を全廃 |

**面内では4/7、横断では3/7。** GPT独立監査の判定（#2 / #3 / #5 が未完）と一致した。

骨格の考え方（Community 3種限定 / scope と facet の責務分離 / 同軸OR・異軸AND / 不適用Facetの実解除 / LOG makerのRIG派生 / URLを復元源にする）は保持する価値が高い。**壊れているのは骨格ではなく配線。**

### 11-2. 正典化する際に加えるべき2点（記録）

**追記A: 表示ラベルと値の分離**

調査で、PCサイドバーの表示ラベル（`ロッククローラー`）とカード属性値（`Rock Crawler`）が一致せず、素直に繋ぐと全件0になることが判明した。今回は選択肢をデータから生成して回避したが、**実装時は表示名と値が別物になるのが普通**（多言語・表記ゆれ・IDとの対応）。

> Facetは `value`（照合に使う値）と `label`（画面に出す表記）を分けて持つ。
> 画面の表記文字列で照合しない。

契約3（定義駆動）の一部にあたる。書かないと、次に実装する人が同じ地雷を踏む。

**追記B: 派生Facetの原則（2026-08-24 裁定済み・正典未記載）**

2026-08-24 に裁定した以下が、まだ正典に入っていない。

> LOGの manufacturer は物理属性ではなく、linked RIG からの検索用派生属性。
> 未紐付けLOGは NULL で、メーカー絞り込みに一致しない。
> 一般化: **信頼できる正本リレーションから確実に導出できる値だけ**、検索用ドキュメントへ派生属性として持てる。推測補完は禁止。

これは「別の実装者が `maintenance_logs` に `manufacturer_id` 列を足す」判断を防ぐためのもの。
**7点の中では、これだけは早めに正典化する価値がある**（スキーマ設計に直結し、後から直すコストが高いため）。

### 11-3. 表現の修正が必要な1点

**契約5「PC/Mobileで同じ条件なら同じ結果になる」は、このままだと検証不能。**

PC と Mobile はダミーデータが別物（PC=カードのDOM属性 / Mobile=JS配列）なので、**件数は原理的に一致しない**（実測: RIG単独で PC 21件 / Mobile 18件）。

> 修正案:
> 「PC/Mobileで**同じ検索意味論**（Scope解釈・OR/AND・適用範囲・解除規則・URL語彙）を共有する。
> 件数の一致は求めない（モックのデータソースが別のため）。実装時は同一のSearch Serviceを使うことで自動的に担保される。」

### 11-4. いずれにせよ書けないもの

| 項目 | 理由 |
|---|---|
| 検索結果 → 詳細の遷移仕様 | **まだ存在しない**（B-1）。URL設計と一緒に決める必要があり、いま書くと推測になる |
| Facetの `selectionMode` / `operator` フィールド | 現在は全て multi + OR で足りており、値の入らないフィールドを先に定義しても検証できない |
| Search Service / Query Builder の層構造 | モックにバックエンドが無い。今書くと実装時に書き直す |

### 11-5. まとめ

> **2026-08-24 訂正:** 保留の理由を「時期尚早」から
> **「実体が契約を満たしていないから」**へ変更した（§12-J）。
> 閉じる条件は、§12-J のP0を直したうえで**同一URL文字列を両面へ与える反証テストを通すこと**。

| | いま | 実装フェーズ移行時 |
|---|---|---|
| 契約7点 | **本ナレッジに記録**（正典化しない）。面内4/7・横断3/7 | P0修正＋反証テスト後に再検討 |
| 派生Facetの原則 | **正典化を推奨**（スキーマ直結・後戻りコスト大） | — |
| value / label 分離 | 本ナレッジに記録 | 契約3に統合 |
| 契約5の表現 | 「件数一致は求めない」で記録 | 同上 |
| 検索結果→詳細 | **未実装として明記** | URL設計とセットで決める |
| UI細部（シート高さ・通知位置・チップ配置・文言・カードサイズ） | **記録もしない**（2026-08-24方針） | 第2周で再評価（§10） |

---

## 12. GPT独立監査との照合（2026-08-24 実施）

**照合相手:** `MyRIG_mock_full_audit_GPT_20260824.md`（GPTがZIPを静的解析）
**照合方法:** GPTの指摘を鵜呑みにせず、**実ファイルのgrepとブラウザ実操作で再検証した**

### 12-0. 結論

**GPTの指摘は8件すべて正しかった。** 私の調査が見落としていた。

見落としの構造は明確で、私は「**各面の中で動くか**」しか見ておらず、
「**URLの値が実際に結果へ効いているか**」と
「**両面で同じURLが同じ意味になるか**」を検証していなかった。

一方、GPTはブラウザ自動操作ができない環境だったため、
静的に「JSがある」ページの**実挙動**は取れていない。**両者は補完関係にある。**

---

### 12-A. 【重大・私の誤報】PCページャは結果を分割していない

GPT指摘 F-4。**ブラウザ実測で確認した。**

```
?q=a&per=30          カード54枚  ページャ表示  現在地=1  「54件中 1〜30件」  先頭「80年代ハイラックス」
?q=a&per=30&page=2   カード54枚  ページャ表示  現在地=2  「54件中 31〜54件」 先頭「80年代ハイラックス」
「2」ボタンを実クリック → カード54枚 URL=?q=a&per=30&page=2  先頭 変化なし
```

`paintPager()` は総数・現在地・ボタン・URLを更新するが、
`paintBlend()` は `list.forEach(...)` で**全件描画**しており `per`/`page` の `slice` が無い。

**私はCheckpoint Bで「URL同期 ✅」と報告した。** URLのラウンドトリップ（書き出し→復元）は
確かに通っていたが、**その値が結果に効いているかを見ていなかった。**
UI状態だけを見るテストは、このバグを通してしまう。

副次的に判明: 表示件数の選択肢は `30 / 60 / 90` のみ。`?per=20` のような
範囲外の値は `select.value = ''` になり、**無言で無効化される**（既定値へも戻らない）。

### 12-B. 【重大・私の誤報】LOGのtype slugがPC/Mobileで違う

GPT指摘 F-1。**ソースとブラウザ両方で確認した。**

```js
search-results.html:737       var COMMUNITY = ['rig', 'parts', 'log'];    // Mobile
pc/myrig-search-v3.html:2717  var COMMUNITY = ['rig', 'parts', 'logs'];  // PC
```

同じURL文字列を両面へ与えた実測:

| URL | PC | Mobile |
|---|---|---|
| `?type=log` | **全種別へ落ちる** | LOG |
| `?type=logs` | LOG | **全種別へ落ちる** |
| `?type=rig,log` | **RIGのみ**（LOG消失） | RIG+LOG |
| `?type=rig,logs` | RIG+LOG | **RIGのみ**（LOG消失） |

**片側で条件が黙って消える。** エラーも警告も出ない。

さらにPC内部にも不整合がある。

```js
2531: var COMMUNITY_ORDER = ['rig', 'parts', 'log'];    // ← log
2717: var COMMUNITY       = ['rig', 'parts', 'logs'];   // ← logs
2453: var CHIP_TO_TYPE = { rig:'rig', parts:'parts', logs:'log' };
2535: var TYPE_TO_CHIP = { rig:'rig', parts:'parts', log:'logs' };
```

内部で `log` と `logs` を相互変換して辻褄を合わせている。**動くが、脆い。**

> **私が §11-3 で「件数一致は求めない、意味論が一致していればよい」と書いた判断は、
> ここでは免罪符にならない。** 意味論以前に、**URL文字列という契約そのものが一致していない。**

推奨（GPT案に同意）: URL canonical slug を `log` に一本化し、PC内部の `logs` は
DOM互換のための内部名として閉じ込める。

### 12-C. 【私の誤報】Facet集合がPC/Mobileで違う

GPT指摘 F-2。確認した。

| | maker | scat | scale | pcat | pcond | ltype |
|---|---|---|---|---|---|---|
| Mobile `AXES` | ○ | ○ | **○**(HOLD) | ○ | **○** | ○ |
| PC `FACETS` | ○ | ○ | — | ○ | — | ○ |

`?type=rig&scale=...` はMobileでは効き、PCでは**無視されURLからも消える**。

契約5を「○」と報告したのは誤り。私は各面で「不適合Facetが解除されるか」だけを見ており、
**そもそも軸の集合が違う**ことを見ていなかった。

### 12-D. 【私の見落とし】単一種別ソートが動いていない

GPT指摘 F-3。**ブラウザ実測。**

| 種別 | 選択肢 | 実測 |
|---|---|---|
| RIG | relevance / newest / **likes** | newest ○動く / **likes ★変化なし** |
| PARTS | relevance / **installs** / newest | **installs ★変化なし** / **newest ★変化なし** |
| LOG | relevance / newest / **likes** | newest ○動く / **likes ★変化なし** |

`paintBlend()` が `sortKey === 'newest'` のときしか並び替えないため。
**PARTSは newest すら効かない**（GPTの指摘より1件多い。PARTSに日付データが無いため）。

§6で「検索は正常に機能」と書いたのは**言い過ぎ**だった。正しくは
「Facet絞り込みと関連順/新着順（PARTS除く）は機能。単一種別ソートとページャは未接続」。

### 12-E. 【私の見落とし】旧scopeの残存

| GPT指摘 | 実測 | 内容 |
|---|---|---|
| F-6 | **PC 31/39・Mobile 4/41** | ヘッダーplaceholderが旧文言 `RIG・パーツ・LOG・製品情報を検索` のまま。Mobile該当: feed / garage / index-e-roomclip / user-garage |
| B-4 | 確認 | `?type=user` がruntimeに残存。`user-garage.html:1043`（history無しの戻り先）と `index.html:805`（ランチャー「到達経路: 検索のユーザー結果」カード） |
| F-5 | 確認 | `mobile-shell.js:395` のヘッダー検索サジェストに「ユーザー」グループが残り、全候補が `search-results.html?q=` へ送られる。**41画面共有なので影響が広い** |

私は検索**ページ本体**のscopeだけを更新し、**全画面共有のヘッダー**が追従していないことを見ていなかった。

### 12-F. 【私の見落とし】共通shellのroute drift

GPT指摘 G-1。確認した。

```js
js/mobile-shell.js:80-84
'<a href="garage.html" class="user-menu-item">マイガレージ</a>'
'<a href="#" data-href="/settings/profile" ...>プロフィール編集</a>'
'<a href="#" data-href="/settings" ...>設定</a>'      ← settings.html は実在する
```

`settings.html` `about.html` `help.html` `legal.html` はすべて実在するのに、
共通メニューは `data-href` のまま「次Phaseで実装します」とtoastする。
**ページを作った後、共通shellのプレースホルダを実リンクへ昇格していない。** 41画面へ波及。

### 12-G. 【私の見落とし】版管理のズレ

GPT指摘 A-3。確認した。

| | 値 |
|---|---|
| `VERSION.txt` | `P22-C27 / 2026-08-19` |
| `VERSIONS.md:5` | 現行の正を `P22-C27` と記述 |
| `index.html:7`（ランチャー） | **`P22-C34`** |

**台帳が実体より7版古い。** 「VERSION / VERSIONS / launcher を同期する」という
運用ルール自体が守られていない。

---

### 12-H. 私だけが持っている発見（GPT未検出）

ブラウザ実操作が必要だったもの、および横断diffが必要だったもの。

| # | 発見 | GPT側の状況 |
|---|---|---|
| 1 | **「見た目だけ機能」14件を実クリックで確定**（件数・中身の前後比較） | 静的解析のため一部を推定で列挙。判定粒度は本調査が細かい |
| 2 | **`SoT_` CSSがMobile用/PC用に分裂**（`app-shell` 460行 vs 549行、`component-catalog` に hover差分）。片方だけ修正され、コメントに「PC側にも要処置」と書かれたまま放置 | 未検出 |
| 3 | **`mobile-shell.js` のグローバルセレクタが6種でページ固有実装と衝突**（`[data-chipgroup]` 13ページ / `.filter-chip` 9ページ） | C-1で「1本の古い前提が41画面へ伝播」と方向は指摘。**具体的な衝突箇所と件数は未特定** |
| 4 | **トークンの多重定義**（`--cat-rig` が `SoT_app-shell.css` に3箇所、`SoT_tokens-v6.css` に2箇所。`@import` なし） | 未検出 |
| 5 | **`pc/preview.html` が孤立**（159KB、homeと841行差、ランチャー・compareのどちらからも未参照） | 補助物として列挙のみ。孤立の指摘なし |
| 6 | **`user-garage` → `garage-rig-detail`（オーナー編集）の誤結線疑い** | 未検出 |

### 12-I. 数字が食い違った箇所

いずれも**定義差**であり、事実の矛盾ではない。

| 項目 | 本調査 | GPT | 原因 |
|---|---|---|---|
| PCの依存パターン数 | 14 | 16 | 粒度の差（Google Fonts等を数えるか）。**未解消・影響小** |
| PC outbound 0 の画面数 | 26（到達不能）/ 29（行き止まり） | 28 | 母数（`preview.html` を含むか）と「outbound」の定義 |
| リンクを1本も持たないMobile画面 | 4（ボトムナビ無し） | 1（`welcome-tour` のみ） | 「nav」で数えるか「あらゆる `.html` リンク」で数えるか |
| Mobileページ数 | 41 | 41 | **一致** |
| PCページ数 | 39 | 39 | **一致** |
| compare.html PAIRS | — | 42組・欠落なし | 本調査は網羅性を数えていない。**GPT側を採用** |

---

### 12-J. 照合後の統合結論

**検索を「一度閉じる」前に配線を直す必要がある。** GPTの優先順位に同意する。

| 優先 | 項目 | 出典 | 検証 |
|---|---|---|---|
| P0 | URL type slug を `log` に統一 | GPT F-1 | 実測済 §12-B |
| P0 | PC/Mobile の Facet集合を揃える（`scale` はHOLD維持、`pcond` は入れるか両面から外す） | GPT F-2 | 実測済 §12-C |
| P0 | PC の `page`/`per` を結果カードへ効かせる | GPT F-4 | 実測済 §12-A |
| P0 | 単一種別ソートを動かす、またはUIから外す（PARTSは newest も未接続） | GPT F-3 | 実測済 §12-D |
| P0 | 検索結果カード → 詳細への結線 | 両者一致 | §8-1 / GPT B-3 |
| P1 | ヘッダー検索サジェストの「ユーザー」を分離／撤去 | GPT F-5 | 実測済 §12-E |
| P1 | `?type=user` の旧導線を撤去 | GPT B-4 | 実測済 §12-E |
| P1 | placeholder旧文言 35画面 | GPT F-6 | 実測済 §12-E |
| P1 | Library補助導線を Library実画面へ結線 | GPT B-3 | 確認済（`href="#"`） |

**正典化の判定は両者一致で「保留」。** ただし理由は当初の私の説明（時期尚早）ではなく、
**実体が契約を満たしていないから**へ訂正する。

閉じる条件は GPT の提案どおり:
**上記P0を直したうえで、同一のURL文字列を両面へ与える反証テストを通すこと。**
