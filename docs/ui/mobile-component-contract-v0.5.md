# Mobile 共通コンポーネント契約 v0.5 — #18検索入口再設計版（supersedes: v0.4）

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> いまモックアップ制作フェーズ。デザインとサービス概念を**議論しながら作る**段階なので、
> 本書は「今こうなっている」という出発点であって、議論の打ち切りではない。
> **「既存仕様と異なる」ことだけを理由に案を捨てないこと。**
> 差分を明示すればイタヤ裁定で変更できる。
>
> ただし **§3.2 の `.mobile-avatar-btn` 使用禁止、§4 の禁止語、§7 の静的ゲートは L1**
> （いずれも過去の事故の再発防止）。

**作成:** 2026-07-16 (JST) / Claude
**状態:** v0.5（2026-07-17。#18＝検索入口設計のイタヤ実機指摘とGPT裁定4点を反映。**v0.4は上書きせず版上げ**（既に実装・_INDEX登録済みのため）。_INDEX更新・v0.4の_archive退避はMR-MOBILE-P12実装バッチに含む。配布キューは#17裁定どおりナレッジ差し替えで代替）
**最終更新:** 2026-08-21
**根拠:** r14凍結Home実資産の直接棚卸し ＋ Coverage Matrix v1.1 ＋ auth-guard-spec-v1 ＋ SoT_tokens-v6.css ＋ SoT_card-components.js
**除外根拠:** app-shell-draft-v1は使用しない（/saved・Create=Header固定の旧設計）

---

## 1. 目的と適用範囲

Phase 1以降の全モバイルページが共有するShell・部品・tokenの**契約**（クラス名 / DOM構造 / JS API / 禁止事項）を固定する。

- **対象:** mobile-shell（現行v0.4）の8系統（§3）＋共通ページ部品（§4）＋カード契約（§5）＋JS契約（§6）
- **非対象:** Home専用表現 E12〜E26 → `.page-e3` adapterに残置。**Home視覚は凍結**、抽出でHomeの見た目を変えないことを回帰ゲート（§7）で担保
- **参照優先順位:** r14凍結Home → Coverage Matrix v1.1 → Page Role Matrix v1.5 → auth-guard-spec等現行仕様 → PC版39ページ → v0.2 9ページ版・app-shell-draft-v1（参考のみ）

---

## 2. Token契約

**3系統厳守:** `--color-*`（v6正典） / `--cat-*` / `--rig-status-*`

**`--cat-*` の値は Token Note v8 が正典**（docs/design/color-token-v8.md）:

```css
:root { --cat-rig:#FBFF00; --cat-rig-on:#151515;
        --cat-parts:#D92D20; --cat-parts-on:#ffffff;
        --cat-log:#2F5F8F;  --cat-log-on:#ffffff; }
[data-theme="dark"] { --cat-log:#3F709E; }
```

> ⚠️ 対文字色トークン `--cat-*-on` は必ずセットで使うこと（黄地に白文字はコントラスト1.08で読めない）。
> v7 の緑 `#66b900` / 紫 `#4e00de` / 橙 `#f88202` は失効。使わない。

**モバイル専用token:** mobile-tokens.cssで宣言済みの`--ms-*`は15種（下表）。別途、**mobile-shell.css内に未定義参照4種が実在**するため、Phase 0-5で新tokenを追加せず、canonical／既存tokenへ正規化する。

| 分類 | 宣言済みtoken（15種） |
|---|---|
| 背景・境界 | `--ms-bg` `--ms-bg-inset` `--ms-bg-subtle` `--ms-border` |
| テキスト | `--ms-text` `--ms-text-secondary` `--ms-text-tertiary` |
| 構造寸法 | `--ms-header-h` `--ms-nav-h` `--ms-gutter` `--ms-section-pad` `--ms-hdr-mb` `--ms-sep-mt` |
| デモ・幅 | `--ms-demo-bar-h` `--mobile-preview-width` |

**未定義参照の正規化（Phase 0-5必須作業・grep実測: 出現回数付き）:**

| 未定義参照 | 出現 | 正規化先（SoT_tokens-v6.cssにLight/Dark両方存在確認済み） |
|---|---|---|
| `--ms-surface` | 4 | `--color-surface` |
| `--ms-surface-hover` | 1 | `--color-surface-hover` |
| `--ms-border-subtle` | 4 | `--color-border-subtle`（必要なら`var(--color-border-subtle, var(--ms-border))`） |
| `--ms-bottomnav-h` | 1 | `--ms-nav-h` |

規則: 値はr14実測値を正としてそのまま移送。**新token追加・既存値変更はGPTクロスチェック対象**。ページ個別`<style>`でのtoken上書き禁止。`--mobile-preview-width`はPC Live Server=390px / `@media(max-width:500px)`で100% — 全ページ共通CSSで維持。

**実装注記（Phase 0-5必読・2026-07-16訂正済み）:** Homeは`SoT_tokens-v6.css`を直接linkしていないが、**`SoT_component-catalog-v6.css`冒頭の`@import url('./SoT_tokens-v6.css')`経由で読込済み**であり、canonical `--color-*`は現mock環境でも**解決済み**（実測: `--color-surface:#fff`＝`--ms-bg:#fff`同値 / `--color-surface-hover:#f6f8fa`＝`--ms-bg-subtle:#f6f8fa`同値 / `--color-border-subtle:#e8ecf0`≠`--ms-border:#d0d7de`）。正規化は**fallback付き参照形式**で行う: `var(--color-border-subtle, var(--ms-border))` / `var(--color-surface, var(--ms-bg))` / `var(--color-surface-hover, var(--ms-bg-subtle))`。fallbackは**canonical未ロード時の単独利用に備えた防御的指定**として残す。正規化により§C系の境界線色はcanonical値（#e8ecf0）へ変わるが、未定義参照4種の所在はすべてHome非使用セレクタ（spec-row / build-item / comment-row / search-hub-bar / quick-action / a2hs）のため回帰対象外。**SoT_tokens-v6.cssの直接link追加は、重複読込と読込順の複雑化を避けるため禁止**。

---

## 3. Shellコンポーネント契約

### 3.1 Header
- **DOM:** `header.mobile-shell-header#mobileHeader[role=banner]` > `.mobile-shell-header__inner` > ①`.mobile-logo.mobile-logo--sm`（`__img--light/--dark` 2枚出し分け＋`__sub`「RC GARAGE」）②`.hdr-search`（`__t--full` / `__t--short`＝379px以下短縮文言）
- **確定事項（裁定済み・再提案禁止）:** 通知アイコン・アバターは**置かない**。検索窓文言は「**RIG・パーツ・LOGを検索**」
  - 🔴 **2026-08-24 改訂**: 旧文言は「RIG・パーツ・LOG・製品情報を検索」。標準検索の対象から製品情報を外したため
    （SEARCH-UPDATE-001 / `_decisions/2026-08-24_search-community-scope-v1.md`）。
    「再提案禁止」の対象は**通知アイコン・アバターを置かないこと**であって、検索窓文言は対象範囲の変更に追随する
- **状態:** 通常 / 未ログイン＝**「新規登録」ピル**を`__inner`末尾に表示（タップ48px・注入はmobile-shell.js）。遷移先: `/signup?next=<encoded・safeNext通し>`【#13・v0.4改訂】
- **#13成立条件（契約化）:** ①`/signup`のファーストビュー内に「登録済みの方はログイン」を表示し`/login?next=<同一next>`へ遷移 ②signup→login間で`next`を落とさない ③新規登録後はauth仕様どおりonboarding経由（`next`はsessionに退避）で完了後に`next`へ復帰（auth-guard-spec L173準拠）

### 3.1b Header Search Overlay【#18・v0.5新設】
- **性質:** Dialog（sheet/modal）ではなく**検索UI**。`sheets{}`・`closeAll()`の管理対象外（close処理は自前）。旧searchSheetの復活ではない — **searchSheet系の語の再利用禁止**（静的ゲート継続）
- **命名（固定）:** JS `openHeaderSearch` / DOM `.header-search-overlay` / `#headerSearchInput` / `#headerSearchSuggestions`
- **DOM:** ヘッダー検索窓は装飾リンクではなく**実input**とする: `form[role=search] > input#headerSearchInput[type=search]`。候補パネル `#headerSearchSuggestions`
- **ARIA（条件付き）:** `role=combobox`／`role=listbox`／`role=option`を名乗るのは、矢印キー候補移動・Enter選択・`aria-activedescendant`同期まで**フル実装する場合のみ**。それ以外（モック段階を含む）は通常の検索フォーム＋リンク一覧として扱う（不完全なcombobox宣言はアクセシビリティ上むしろ有害）
- **履歴制御:** open時＝Overlay未開放時のみ専用marker付き`pushState`（focusのたびに重複pushしない）。popstate＝Overlayを閉じるのみ（`history.back()`を再実行しない）。×／Escape／外側タップ＝markerありなら`history.back()`・なければ直接close。close時は`input.blur()`し、focusイベントによる即時再オープンを防止
- **挙動:** タップ＝**入力欄そのものへのネイティブfocus**（タップイベント内で同期的にfocus。overlay展開アニメーションはfocusの後 — iOSキーボード表示のため。ただしキーボード自動表示は引き続き保証しない）。ロゴは一時縮小または非表示可。ページ本体は背後に残す
- **表示内容:** 空欄時＝最近の検索＋「検索履歴を消す」のみ（**「注目の検索キーワード」等のトレンド表示は置かない** — ランキング全廃方針・search-page-plan承認事項と整合）。入力後＝候補グループ（メーカー / RIG・車種 / パーツ / ユーザー）＋「“○○”の検索結果をすべて見る」
- **確定・終了:** 入力確定時のみ`/search?q=...`へ遷移。close（×／Escape／外側タップ／Back）で**Homeの元の状態へ完全復帰・スクロール位置維持**
- **開放中:** 背景`#mobileViewport`をscroll lock。**BottomNavは明示的に非表示**（sheet系の「判定停止のみ」とは別扱い — キーボード文脈のため）。close時に元の表示状態へ復元
- **使用:** Browse系全ページ。Detail・登録・編集系は3.9 SubHeader variantに切替

### 3.2 BottomNav
- **DOM:** `.mobile-bottom-nav` > `.mobile-bottom-nav__item[data-nav]`×5（`__pill` / `__label`付き）。中央=`__item--center#createTrigger`（aria-haspopup=dialog）。ガレージタブのアバター=**`.mobile-bottom-nav__avatar`**（23px円形・`is-active`時currentColorリング — r14実装確認済み）
- **禁止:** `.mobile-avatar-btn`は**使用禁止**（旧Header/UserMenu系クラス。Headerへのアバター復活誤実装を防ぐため、v0.3抽出対象からも除外）
- **構成（確定）:** ホーム / 探す / 登録(中央) / Feed / ガレージ。`/saved`復活禁止
- **挙動:** 下スクロールで自動非表示（threshold 10px / top buffer 24px）。**dialog/sheet開放中は自動hide/show判定を停止（明示的な非表示はしない・close時に通常判定へ復帰 — §3.7要件7）**。タップ領域53px実測維持
- **状態:** `is-active` / 未ログイン時もナビ表示（ガード発火は§3.8）

### 3.3 SafeArea
- r14 E2実装を移送。`env(safe-area-inset-*)`をHeader上端・BottomNav下端・bottom-sheet下端に適用。PWA standalone前提で全画面内遷移を維持

### 3.4 PageContainer
- **DOM:** `.mobile-viewport#mobileViewport`（スクロールコンテナ・**windowではない**）> `main.mobile-page-content#mobilePageContent`
- Playwright検証・stickyはこのIDを対象（scrollBehavior='auto'注入 / sticky検証時`.mobile-page-content`に`flex:none`）

### 3.5 SearchField【#18・v0.5再改訂 — 検索3導線の確定】
- **3本整理（確定）:** ①Header検索＝**その場で検索開始**（3.1b Overlay・遷移しない） ②BottomNav「探す」＝`/search`へ移動（**自動focusなし** — カテゴリやメーカーから探す動線を妨げない） ③検索確定＝`/search?q=...`へ移動
- **`focus=1`方式は廃止**【v0.4から変更】。関連コード・パラメータ処理を除去
- **/searchのヘッダー＝SearchHeader variant** `.mobile-shell-header--search`: 通常の「ロゴ＋小型検索窓」ヘッダーを出さず、**横幅いっぱいの検索入力欄**＋クリア/検索ボタンで構成。leading領域は**検索トップ＝ロゴまたは空（戻る矢印なし — BottomNav主要タブを下層ページに見せない）／検索結果＝戻る矢印（fallback=`/search`。Homeではない）**。**ページ内に大型検索欄を別途置かない**（検索窓2つ問題の解消 — 大型欄をヘッダーへ統合する。近づけるだけでは不可）。/searchトップの下部構成（種別タブ・メーカー・車種カテゴリ・最近の検索・フィルター/結果）は
**search-page-plan v2 が正**（docs/search/search-page-plan-v2.md）。
  - 🔴 **2026-08-24 改訂**: 「種別タブの無限スクロールは #26 裁定」は失効。
    **種別タブという独立状態を廃止**し、種別は検索結果ページ内のフィルター状態になった。
    Mobileの検索結果は「すべて（RIG/パーツ/LOG混在）」の1状態＋無限スクロール。
    詳細は `_decisions/2026-08-24_search-community-scope-v1.md`
- searchSheet系の語は引き続き参照0件（静的ゲート）
- **実機ゲート:** iPhone Safari実機でOverlayタップ→キーボード挙動確認（デプロイ後・イタヤ確認項目）

### 3.6 CreateActionSheet（登録ハブ）
- **DOM:** `.sheet-overlay#sheetOverlay` + `.bottom-sheet#createSheet[role=dialog][aria-modal]`（`__grip/__hdr/__ttl/__x` + `.create-list > .create-item(.create-circle/.create-sep)`）
- **項目:** RIG / パーツ / LOG → `/register/rig|part|log`。ページ遷移ではなくシート（確定）
- **未ログイン:** 項目タップで**先にCreateActionSheetをclose → LoginRequiredModal（context='register'）をopen**（単一開放原則 — §3.7）
- カテゴリシート（`.bottom-sheet--cat#catSheet`）も同一契約に従う

### 3.7 LoginRequiredModal（新規）＋ 共通Dialog Controller
- **方針（裁定済み）:** 独立ページモック不要。本契約＋状態サンプル1画面（`?login-modal=1`）のみ
- **DOM:** `.login-required-modal[role=dialog][aria-modal=true][aria-labelledby][aria-describedby]`。overlayは`.sheet-overlay`共用
- **仕様出典:** auth-guard-spec-v1 §2.2/§3（文言・「ログイン」CTA→`/login?next=<encoded元URL>`・「新規登録」CTA→`/signup?next=<encoded元URL>`・close）。**CTAは実リンク**（モックでも実href。`data-next`はデバッグ用併記可）
- **外装（新デザイン判断を発生させない）:** 既存Bottom Sheet流用 — `class="bottom-sheet login-required-modal"`とし、`__grip / __hdr / __ttl / __x`を再利用。新規CSSは`.login-required-modal__actions`（CTA配置）等の最小追加のみ
- **`openLoginRequiredModal(nextUrl, context)`の入力条件【v0.4: context追加】:** `nextUrl`は**同一オリジンの相対パスのみ**受け付ける。`safeNext`準拠の二段フォールバック（auth-guard-spec-v1 §4）: 空 / 不正値 / 外部URL / `//`始まり → **`/`**、`/login`・`/signup`で始まるループ → **`/garage`**
- **`context`【#14・v0.4】:** semantic contextはauth-guard-spec既存の**8種を維持**（`'default' | 'register' | 'follow' | 'like' | 'favorite' | 'pin' | 'comment' | 'notifications'`）。**表示文言のみ5グループに解決**（呼び出し側を変えずに将来文言を細分化できる設計）:

| context | 本文（タイトル「ログインが必要です」・CTA構成は現行維持） |
|---|---|
| `register` | ログインすると、RIG・パーツ・LOGを登録して、自分のガレージを管理できます。 |
| `like` / `favorite` / `pin` / `comment` | ログインすると、気になる投稿を保存したり、いいねやコメントを残したりできます。 |
| `follow` | ログインすると、気になるガレージをフォローして更新を追えます。 |
| `notifications` | ログインすると、いいね・コメント・フォローなどの通知を確認できます。 |
| `default`（省略時） | ログインすると、ガレージ管理やお気に入りなど、MyRIGの主な機能が使えます。 |
- **Dialog Controller統合要件（Shell実装責務・mobile-shell.js v0.3）:**
  1. `sheets{}`レジストリにModalも登録し、`closeAll()`の対象に含める
  2. **同時に開けるdialog/sheetは常に1つ**（open時に他を全close）
  3. overlayの表示状態と`aria-hidden`を同期
  4. open時に`#mobileViewport`をscroll lock、close時に解除
  5. initial focus設定 / focus trap / close後にトリガー要素へfocus復帰
  6. Escape / overlay外側クリック / ×ボタンを共通close処理に統一
  7. **dialog/sheet開放中はスクロール連動の自動hide/show判定を停止する（明示的な非表示は行わない — r14実測挙動を維持）。close時は通常判定へ復帰。** 開放判定セレクタは`.bottom-sheet.is-open, .login-required-modal.is-open`【v0.4: `.search-sheet.is-open`を削除】
- 上記1〜7は既存createSheet / catSheet / Search Filter Sheetにも同一適用。**ページ新設のbottom-sheetも必ず`sheets{}`に登録し同一Controller管理とする**【v0.4】。**Header Search Overlay（3.1b）はDialogではないため管理対象外**（close・scroll lock・BottomNav制御は自前実装）【v0.5】。Homeの視覚に影響しないJS挙動追加に限る

### 3.8 認証ガード境界（P1 / P2 / P3 — middleware責務の明記）
- **P1（本契約対象の一般ユーザー向けモバイル領域）:** matcher = `/garage/:path*` / `/settings/:path*`。未ログイン→`/login?next=<encoded>`。**`/admin/:path*`の管理者ガード、Maintenance / Suspended等の全体ガードは本契約の対象外**とし、既存仕様を維持する
- **P2（ページ/interaction層）:** `/notifications` / `/register/:path*` は**P1 matcherから除外**。page層でsession確認し、未ログインなら元ページ維持のままLoginRequiredModal表示（auth-guard-spec §2.2「元ページを維持したままModalを開く」準拠）
- **P3（公開ページのmutate時のみ）:** matcher対象外。Follow/Like/Favorite/Pin/Comment等の操作時にModal表示、closeで元ページ残留
- モックでは`?guest=1`でP2/P3挙動をデモ（§6の状態定義参照）

### 3.9 SubHeader（Detail・登録・編集系 — Headerのvariant）
- 別Header新設ではなく**`.mobile-shell-header`のvariant**として定義（r14 §Cに`__back`/`__pagettl`要素が実在 — 実資産準拠）:
  `header.mobile-shell-header.mobile-shell-header--sub > .mobile-shell-header__inner > .mobile-shell-header__back + .mobile-shell-header__pagettl + .mobile-header-actions`
- **対象:** Detail系＋登録・編集系。Browse系Header（3.1）と排他使用
- **戻る:** PWA standalone前提。`history.back()`不能時（直リンク流入等）の**fallback URL必須**・ページ毎に定義。例: RIG Detail→`/rigs`（**URL改訂候補#8の発効後**。発効前は`/`をfallbackとする）

### 3.10 共通幅・余白
- `--ms-gutter` / `--ms-section-pad` / `--ms-hdr-mb` / `--ms-sep-mt`のみで組む。ページ側の直値margin/padding新設は最小化

---

## 4. 共通ページ部品契約（実クラス表 — 全クラスr14 CSS実在確認済み）

| 部品 | 実クラス | 主用途 |
|---|---|---|
| Filter Chip Bar | `.filter-bar` / `.filter-bar__scroll` / `.filter-chip` | Feed / Search / Category（sticky横スクロール） |
| Segment Tabs | `.seg-tabs` / `.seg-tab` | Garage / Public Garage / Library内タブ |
| Detail Hero | `.detail-hero` | RIG Detail フルブリードカルーセル |
| Owner Row | `.owner-row` | Detail: 投稿者＋フォロー |
| Action Bar | `.action-bar` | ♥いいね・★お気に入り・📌ピン留め・共有（用語固定） |
| Spec Table | `.spec-table` | 基本スペック |
| Build Details | `.build-group` / `.build-item` | アコーディオン |
| Body Text | `.m-bodytext` / `.m-readmore` | 説明文 / LOG本文 |
| Buy / Info | `.buy-links` / `.buy-link` / `.buy-note` | アフィリエイトCTA |
| Comments | `.comment-list` / `.comment-row` / `.comment-input` | コメント |
| Feed Full Card | `.feed-card` | Feed本体（表現はPhase 1-2でX型に刷新） |
| Activity Row | `.activity-row` | Feed内の薄い行 |
| Empty State | `.m-empty` | 空状態（empty-state-spec準拠） |

規則: 子要素クラスは実CSSに従いそのまま移送（本表は契約ルート）。視覚詳細は各Phaseのページ実装で確定。禁止語: マスター / あとで見る / ウォッチリスト。ランキング表現は全部品で禁止。
補助コンポーネント `myrig-comment` / `myrig-ext-item` / `myrig-fotonote` / `myrig-recommend` は**カードではなく本§4系の部品**として扱う（React化時に対応）。

---

## 5. カード契約（互換併記方式・正典variant基準）

- 命名: `.m-card-rig` / `.m-card-part` / `.m-card-log` / `.m-feed-card` / `.m-list-row`（CSSクラス方式・Web Components不使用 — Next.js SSR互換）
- 移行期は互換併記: `.gcard, .m-card-rig { … }`。React移行時に正式名へ収束
- **SoT対応表（正典variant基準 — SoT_card-components.js L27の旧互換宣言・委譲実装を確認済み）:**

| mobile契約名 | SoT正典対応 |
|---|---|
| `.m-card-rig` | `myrig-rig-card` ＋ 用途別variant（sm/md/lg/xl/browse系） |
| `.m-card-part` | `myrig-part-card` ＋ 用途別variant |
| `.m-card-log` | `myrig-log-card` ＋ 用途別variant |
| `.m-feed-card` | `myrig-log-card variant="feed"`（旧myrig-log-feedの委譲先 — 実装確認済み） |
| `.m-list-row` | `myrig-rig-card` / `myrig-part-card` / `myrig-log-card` の `variant="list"` |

- **旧互換5種は新契約の対応先にしない:** `myrig-gc` / `myrig-rig-lc` / `myrig-log-pc` / `myrig-log-cp` / `myrig-log-feed`（SoT内で「旧互換」明記・log系はvariant委譲のみのラッパー）
- カード内部仕様の正典は`SoT_card-components.js`。**カードAPI（クラス構造・modifier）の変更はGPTクロスチェック対象**

---

## 6. JS契約（mobile-shell.js v0.5）

- 名前空間API（v0.3で公開・**v0.5現行は下記6関数**）:

```js
window.MyRIGMobileShell = {
  openBottomSheet,        // (key, label)
  openHeaderSearch,       // §3.1b Overlay起動（closeAll対象外・close自前）【v0.5】
  openLoginRequiredModal, // (nextUrl, context) — §3.7。contextは8種union、文言5グループ解決
  closeAll,
  toast,                  // (msg)
  toggleTheme
};  // v0.4でopenSearchSheet削除（語の再利用禁止）・v0.5でopenHeaderSearch追加。Next.js移植時はTS union型＋文言resolverへ置換
```

- グローバル直生やし禁止（名前空間のみ）。**モック専用API**であり、Next.js移植時はReact側controller（Context/Zustand等）へ置換する前提を明記
- **IDフック:** `#mobileViewport` / `#mobileHeader` / `#sheetOverlay` / `#stateInd`
- **挙動:** Escape→closeAll / ナビ自動隠し（§3.2） / テーマ切替（`data-theme=dark`・Light基調）
- **guest状態の位置づけ:** `?guest=1`は**未ログイン判定そのものではなくモック用デバッグ状態**。実装対応: モック=`?guest=1` / Next.js=Supabase session。**新規登録ピルはモックでも実リンク** `/signup?next=<encoded現URL>`（safeNext通し・`data-next`併記可）【v0.4: §3.1と統一】
- guestピル注入はmobile-shell.jsに実装済み（v0.3で移管完了）
- **デバッグパラメータ:** `?debug=1` / `?pwa=1` / `?guest=1` / `?round=1` / `?login-modal=1`（`&ctx=<context>`で文言グループのデモ切替可【v0.4】）

---

## 7. 変更管理・回帰ゲート

1. 本契約の変更（token追加 / Shell構造 / カードAPI / 認証挙動）= **GPTクロスチェック対象**。ページ内部の視覚調整は対象外
2. Home（r14）は視覚凍結。抽出はコード移動と§2正規化・§3.7 JS挙動追加のみで、**Homeの算出スタイル（computed value）を変えない**
   > ⚠️ 既存の v0.4 / v0.5 baseline は **v8カラー適用前**のもので、pixel diff は必ず非0になる。
   > 回帰判定は **v8適用後の baseline を新規作成**して行い、旧baselineとの比較で不合格判定を出さない。
3. **回帰ゲート（Phase 0-5直後・Phase 1-1着手前）— 二段階判定:**
   - 前提条件: 同一Chromium版 / 同一DPR / フォント読込完了 / 画像decode完了 / transition・animation停止後に撮影（viewport: 360×800 / 390×844 / 430×932、ライト・ダーク）
   - **第一基準: pixel diff 0**
   - 文字輪郭のみに限定された差分の場合のみ再判定: diff pixel ratio ≤ 0.0005 / bounding box ±0.5 CSS px以内 / Header・BottomNav・主要余白・カード幅・色のcomputed value完全一致
   - 文字輪郭外の差分・折返し変更・1px以上の位置差 = **不合格**
4. 検証共通項: 横はみ出し0 / SafeArea / Header・BottomNav追従 / タップ48px
5. **静的ゲート【v0.5改訂】:** アクティブ資産（_archive・変更履歴除く）で `searchSheet` / `openSearchSheet` / `.search-sheet` / `#searchSheet` / **`focus=1`** の参照0件
6. **機能ゲートB【v0.5全面置換 — v0.4版（focus=1遷移）は失効・変更履歴参照】:** Header Search＝その場でOverlay開始（遷移なし） / BottomNav「探す」→`/search`（自動focusなし） / 検索確定→`/search?q=<encoded>`（モック対応はP12の読替表） / 最近の検索がOverlayと`/search`で同一キー共有 / catSheet従来どおり開閉
7. **【v0.4】Dialog回帰ゲートC:** searchSheet除去後もCreateActionSheet / catSheet / LoginRequiredModalで単一開放・Escape・overlay close・focus trap・focus復帰・scroll lock・BottomNav自動hide停止と復帰を再確認
8. **【v0.4】認証導線ゲートD:** ピル→`/signup?next=` / signup→loginでnext維持 / 各context文言 / Login・Signup CTA / safeNext外部URL拒否 /（onboarding後next復帰は本実装時）
9. **【v0.4・実施済み】視覚回帰の判定分離E:** P11バッチで適用完了（v0.4 baseline作成済み）。現行バッチはE'（§7-10）を適用する
10. **【v0.5】Overlayゲート分離E':** ①Overlayを閉じた通常Home＝旧Home（v0.4 baseline）と**pixel diff 0**（ヘッダー検索窓のa→input化を含めて視覚同一であること） ②検索窓タップ後＝**v0.5 baseline新規作成** ③close後＝元のHomeへ完全復帰・**スクロール位置維持** ④Back／Escape／外側タップで正常に閉じる ⑤Overlay中のBottomNav明示非表示と復元

---

## 8. 変更履歴

### rev.1変更履歴（GPT監査7点への対応 — 全件r14実資産で裏取り済み）

| # | 指摘 | 対応 | 裏取り |
|---|---|---|---|
| 1 | 未定義token 4種 | §2に正規化表を追加（新token追加禁止） | grep実測: surface×4 / surface-hover×1 / border-subtle×4 / bottomnav-h×1。canonical側の存在もSoT_tokens-v6で確認 |
| 2 | カード対応の旧互換混入 | §5をvariant基準に全面修正・旧5種を対応先から除外 | SoT L27旧互換宣言＋log-feed→`variant="feed"`委譲実装＋`v==='list'`分岐の存在を確認 |
| 3 | Modal のDialog Controller未統合 | §3.7に統合要件7項を契約化 | 現行anySheetOpenセレクタが`.bottom-sheet/.search-sheet`のみであることを実測 |
| 4 | P1/P2 middleware境界 | §3.8新設 | auth-guard-spec: /notifications・/register=P2、P2は「元ページ維持のままModal」明記を確認 |
| 5 | 実クラス名相違 | §3.2を`.mobile-bottom-nav__avatar`に修正＋`.mobile-avatar-btn`使用禁止化 / §3.9をHeader variant化 | HTML実物・CSS L2844で`__avatar`確認、`__back`/`__pagettl`実在確認 |
| 6 | §4の実クラス化 | 13部品×実クラス表に置換 | 全クラスgrepで存在確認（filter-bar__scroll含む） |
| 7 | JS API・guest位置づけ | §6を名前空間API＋モック/実装切り分けに修正 | IIFE・windowエクスポート無しを実測 |
| — | 回帰ゲート許容誤差 | §7を二段階判定に更新（GPT裁定採用） | — |

**v0.3確定時の追記（GPT条件付き承認3点＋Claude実装注記1点・再監査不要扱い）:**
1. §3.8 P1適用範囲を「本契約対象の一般ユーザー向けモバイル領域」に限定（/admin・Maintenance・Suspended系は対象外）
2. §3.5 Header Search=`#searchSheet`起動 / BottomNav探す=`/search`遷移の役割分離＋§6 APIに`openSearchSheet`追加＋searchSheetのdialog化
3. §3.7 `nextUrl`のsafeNext入力条件＋§3.9 `/rigs`はURL改訂発効後（発効前fallback=`/`）
4. §2 実装注記: fallback付き参照形式で正規化・SoT_tokens-v6.cssの直接link追加禁止

**P05事前検証での訂正（2026-07-16・GPT指摘6点反映・再監査不要）:**
1. §2実装注記を訂正 — canonicalは`@import`経由で**解決済み**（「未ロード」は誤認）。fallbackは防御的指定として維持
2. §3.7 CTA・§6 guestピルを実リンク化（`/login?next=` / `/signup?next=`・safeNext通し）
3. catSheetのインライン制御（openCat/closeCat）もDialog Controllerへ正式移管（実行指示書T2/T3に反映）
4. §3.7 Modal外装=既存`.bottom-sheet`流用＋`.login-required-modal__actions`最小CSSのみ
5. §3.7要件7のBottomNav挙動を「開放中は自動hide/show判定停止・明示非表示なし（r14実測維持）・close時復帰」に統一（GPT案の「明示非表示」は現行実装と不一致のため実測準拠へ修正）
6. バックアップの上書き防止（実行指示書に反映）

**v0.5変更履歴（2026-07-17・#18＝検索入口再設計。イタヤ実機指摘→GPT裁定4点反映）:**
1. §3.1b新設 — Header Search Overlay（Dialogではなく検索UI・combobox/listbox ARIA・同期focus→展開アニメの順序契約・sheets{}対象外・searchSheet語の再利用禁止）
2. §3.5再改訂 — 検索3導線確定（Header=その場開始 / 探すタブ=/search移動・focusなし / 確定=/search?q=）。`focus=1`廃止。/search=SearchHeader variant `--search`（検索窓2つ問題の解消: 大型欄をヘッダーへ統合）
3. §6 — `openHeaderSearch`追加（6関数・closeAll対象外）
4. §7-10 — Overlayゲート分離E'（閉状態diff 0 / タップ後v0.5 baseline / 完全復帰・スクロール位置維持）
5. Claude監査修正 — Overlay空欄時の「注目の検索キーワード」は**不採用**（ランキング・トレンド全廃方針とsearch-page-plan承認事項に抵触するため。GPT提案から除外）
6. 版管理 — v0.4は上書きせず版上げ（実装・_INDEX登録済みのため）。P12で_INDEX更新（supersedes: v0.4）・v0.4退避
7. P12事前監査反映（GPT指摘4点＋Claude追加）— §7の旧focus=1ゲート失効処理・§6版表記/6関数化・ARIA条件化（フル実装時のみcombobox）・履歴制御仕様・SearchHeaderのleading裁定（トップ=戻るなし／結果=戻る・fallback=/search）・静的ゲートにfocus=1追加

**v0.4変更履歴（2026-07-17・#13/#14/#15 GPT修正後承認の全条件反映）:**
1. §3.5全面改訂 — 検索導線一本化・searchSheet廃止・`.hdr-search`実リンク化・focus=1（キーボード自動表示は保証しない/パラメータ除去/実機iPhoneゲート）
2. §3.1 — ピル「新規登録」化＋#13成立条件3点（signup内ログイン導線・next不落・onboarding経由復帰）
3. §3.7/§6 — `openSearchSheet`削除・context引数追加（**8種union維持・文言5グループ解決** — 3種への集約はGPT不承認につき撤回）・「主な機能」表現
4. §7 — 静的ゲート/機能B/Dialog C/認証D/視覚回帰判定分離E（expected diff＋v0.4 baseline）を追加
5. 版管理 — v0.3をsupersededとし_archive退避・`App/_docs/_INDEX.md`更新・配布キュー記録（Charter運用）

**P05実行後の正誤訂正（2026-07-16・erratum #1）:** §3.7 safeNextのループ時フォールバックを`/`と記載していたが、auth-guard-spec-v1 §4の正は二段構え（不正値→`/` / login・signupループ→`/garage`）。契約を正典に合わせて訂正。実装はMR-MOBILE-P05-FIX1（1行修正）で追従。Claude Codeの「契約優先で実行・逸脱として報告」の判断は指示書どおりで正しい。
