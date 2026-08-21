# MyRIG Token Correction Note — v7 / Card Catalog

**版管理メモ（v7統合 2026-06-13）:**
v6 は5つの異なる内容を持つファイルが "v6" を名乗る版管理破綻状態だった（3,656B / 7,442B / 7,527B / 8,036B / 10,990B の5版）。本 v7 は最新・最大の 10,990B 版（MOKUP/myrig_pc_Ver3/docs/ / mtime 2026-05-29 / MR-MOCK-057 Token Canonical Decision 含む）を正典として確定したもの。旧 "v6" 5版は全て `_doc_archive/superseded/2026-06-13/` に退避済。内容は前版 v6（10,990B版）と完全同一。

## 目的

このファイルは、Card Catalog v7.2.x 作業中に発生したトークン混在ミスを記録し、今後の修正・Claude Code作業で同じ誤りを防ぐための注意書きである。

## 結論

V2モック時代のカラートークンは参照禁止。

現在のUI判断では、以下のv6系ファイルを正典として扱う。

- `SoT_component-catalog-v6.css`
- `SoT_component-catalog-v6.html`
- `SoT_typo-check-v6.html`
- `SoT_garage-catalog-v6.html`
- `myrig-rig-detail-v6.html`
- `myrig-parts-detail-v6.html`
- `myrig-log-detail-v6.html`

Card CatalogはカードWeb Componentsの比較用。  
カード内部仕様は `SoT_card-components.js` を正とする。

## 今回のミス

Claude / GPT側で、過去のV2モック由来と思われるカラートークンや、別途作成されたDesign Tokens v1.0の値を混在させた。

特に以下が問題だった。

```css
--cat-rig: #FBFF00;
--cat-parts: #F05400;
--cat-log: #0084D6;
```

これを現在のv6 UIにそのまま適用した結果、RIGバッジが強い黄色になり、v6モックの見た目と乖離した。

## 現在参照すべきv6トークン

現時点では、v6モックで実際に使われている以下のトークンを優先する。

```css
/* Light */
--color-bg: #ffffff;
--color-bg-subtle: #f6f8fa;
--color-bg-inset: #eff1f3;
--color-surface: #ffffff;
--color-surface-hover: #f6f8fa;
--color-border: #d1d9e0;
--color-border-subtle: #e8ecf0;
--color-text: #1f2328;
--color-text-secondary: #656d76;
--color-text-tertiary: #8b949e;
--color-accent: #0969da;
--color-link: #0969da;
--color-chip-bg: #e4e8ec;

--cat-rig: #66b900;
--cat-parts: #4e00de;
--cat-log: #f88202;
```

```css
/* Dark */
--color-bg: #0a0d13;
--color-bg-subtle: #111318;
--color-bg-inset: #070a0f;
--color-surface: #111318;
--color-surface-hover: #1c2128;
--color-border: #272c35;
--color-border-subtle: #1a1f27;
--color-text: #e6edf3;
--color-text-secondary: #8b949e;
--color-text-tertiary: #6e7681;
--color-accent: #0969da;
--color-link: #58a6ff;
--color-chip-bg: #21262d;

--cat-rig: #7acc1e;
--cat-parts: #7c3aed;
--cat-log: #ffa033;
```


## 幅系トークン正典

**幅系トークン（`--content-max-width` 等）は `docs/content-width-policy-v2.md` を正典とする。**（MR-MOCK-028 改訂 / 2026-05-18）

v1（`docs/content-width-policy-v1.md`）は履歴目的で残置されているが、現行正典は **v2** であり、4 分類（discovery-fluid / contained-shell / reading / detail-layout）で整理されている。v1 の「Y 案 中央寄せ / 1440px 制約」は U-NEXT 型探索体験と矛盾するため撤回された。

カラートークン・カテゴリバッジについては以下を参照。

## v6バッジ正典

`SoT_component-catalog-v6.css` の `.cat-badge` を正とする。カード内の `badge-rig` / `badge-parts` / `badge-log` もこの見た目に寄せる。

```css
.cat-badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 52px;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: .06em;
  text-transform: uppercase;
  padding: 2px 0;
  border-radius: 4px;
  color: #fff;
}
.cat-badge--rig { background: var(--cat-rig); color: #fff; }
.cat-badge--parts { background: var(--cat-parts); }
.cat-badge--log { background: var(--cat-log); }
```

注意：`RIG` バッジは v6最新版では白文字。過去の一部タイポ確認サンプルに残る黒文字指定は採用しない。

## タイポグラフィ正典

`SoT_typo-check-v6.html` の13項目を正とする。

- page section title: 32px / 800 / Barlow Condensed / .04em / uppercase
- section title: 20px / 800 / Barlow Condensed / .02em / uppercase、日本語は18px / Noto Sans JP
- panel title: 17px / 800 / Barlow Condensed / .02em / uppercase、日本語は15px / Noto Sans JP
- page h1: clamp(26px, 3.2vw, 38px) / 800 / -.04em / mixed
- pit hero name: 32px / 800 / -.045em / white
- pit sub name: 16px / 800 / -.025em / white
- card title md: 16px / 800 / -.025em
- card title standard: 15px / 800 / -.025em
- card title sm: 14px / 800 / -.02em
- maker large: 20px / 800 / Barlow Condensed、日本語は18px
- maker md: 12px / 700 / Barlow Condensed / .06em / uppercase / secondary
- body / excerpt: 14px / 450 / line-height 1.7
- meta / date / count: 12px / 500 / tertiary（PTB日付のみ14px）
- spec value: 14px / 700 / Inter / text

## 今後の禁止事項

- V2モック時代のトークンを参照しない
- 過去のDesign Tokens値を現在のv6 UIに無断適用しない
- `--cat-rig` / `--cat-parts` / `--cat-log` を勝手に置換しない
- カードバッジの色をトークン変更で解決しようとしない
- Card Catalog側CSSでカード内部を再現・上書きしない

## 今後の修正方針

### カード本体を直す場合

`SoT_card-components.js` を修正する。

例：

- list variant の余白
- lc variant の画像フレーム
- md / sm / sm-meta のカード内部
- badge / footer / meta / title の構造

### カードの並びを直す場合

Card Catalogまたは各ページ側のコンテナCSSを修正する。

例：

- 3列グリッド
- list表示幅
- lc表示幅
- related表示エリア
- Favorites / Pins の初期表示

## 注意

`MyRIG_Design_Tokens_v1.0.md` は存在するが、v6系ファイルとカテゴリ色が矛盾する。

そのため、カテゴリ色については勝手に統合・補正しない。  
矛盾がある場合は、必ず「v6を正にするか、Design Tokens v1.0を正にするか」を確認してから作業する。

---

## Status Badge 正典

> 追記: 2026-05-03 — Category Badge との色分離を確定。

Status Badge は Category Badge とは別物として扱う。

### Category Badge

- RIG / PARTS / LOG
- コンテンツ種別表示
- `--cat-rig` / `--cat-parts` / `--cat-log` を使用

### Status Badge

- Garage / Pit Table / Owner context 用
- RIG / PARTS / LOG のカテゴリ色を流用しない
- 状態ごとに専用色を使用する

```css
.gc__status--building { background: #3b82f6; color: #fff; } /* ビルド中 */
.gc__status--setting  { background: #f59e0b; color: #fff; } /* セッティング中 */
.gc__status--active   { background: #22c55e; color: #fff; } /* 運用中 */
.gc__status--stored   { background: #6b7280; color: #fff; } /* 保管中 */
.gc__status--archived { background: #374151; color: #fff; } /* アーカイブ */
```

### 禁止事項

- Status Badge に `--cat-rig` / `--cat-parts` / `--cat-log` を使用しない
- Category Badge と Status Badge を同じclass設計にしない
- `.tag-badge` を Status Badge 代わりに流用しない
- Category Badge、Status Badge、`.tag-badge` を相互流用しない

### 旧クラス名禁止（root版 Token Note より吸収・2026-05-03）

以下の旧クラス名は廃止済み。新規実装・現行 SoT では一切使用しない。

| 旧クラス名 | 代替 |
|---|---|
| `.gs--a` | `.gc__status--active` |
| `.gs--b` | `.gc__status--building` |
| `.gs--s` | `.gc__status--stored` |
| `.gs--r` | `.gc__status--archived` |

- 旧クラス名が古いファイル（`_archive/session78/` 等）内に残っていても、現行 SoT・新規実装では参照しない
- Status Badge クラスは必ず `.gc__status--{slug}` 形式を使う（SoT_card-components.js 内は `.gs--{slug}` 形式だが、これは Shadow DOM 専用のため別管理）

---

## Card Catalog v7.2.3 修正方針

`Sot_MyRIG_Card_Catalog_v7.2.3.html` は以下を満たすこと。

- v6のLight/Darkトークンを使う
- ヘッダーに「v6 aligned」であることを明記する
- 外側のvariantラベルはv6 `.cat-badge` 準拠にする
- カード内部のバッジは `SoT_card-components.js` 側で直す
- Card Catalog側CSSでカード内部を上書きしない
- `#FBFF00` / `#F05400` / `#0084D6` をカードカタログの正規トークンとして使わない

---

## MR-MOCK-057 Token Canonical Decision（2026-05-29 追記）

> 詳細は `docs/token-canonical-decision-MR-MOCK-057.md`。本セクションは結論の要約。
> 前提: `docs/strict-audit-response-MR-MOCK-056.md`（極厳監査レポート回答）。

### 結論

**canonical token は3系統**とする。

1. **`--color-*`** — base palette（bg / surface / text / border / accent / warning / danger 等）。Light / Dark 両方で定義（`:root` と `[data-theme="dark"]`）。
2. **`--cat-*`** — category color（`--cat-rig` / `--cat-parts` / `--cat-log`）。カテゴリ色として維持。
3. **`--rig-status-*`** — RIG status color（building / setting / active / stored / archived 等）。status badge の元色として維持。

- **廃止対象**は `--text` / `--bg` / `--surface` 等の短縮 token。
- register / composer 系の修正は **単純renameではない**。local `:root` の Light 固定 token 定義を削除し、SoT（`SoT_component-catalog-v6.css`）から Light / Dark を継承させる。

### canonical mapping 表（短縮 → canonical）

| 廃止対象（短縮） | canonical |
|---|---|
| `--bg` / `--bg-subtle` / `--bg-inset` | `--color-bg` / `--color-bg-subtle` / `--color-bg-inset` |
| `--surface` / `--surface-hover` | `--color-surface` / `--color-surface-hover` |
| `--border` / `--border-subtle` | `--color-border` / `--color-border-subtle` |
| `--text` / `--text-2` / `--text-3` | `--color-text` / `--color-text-secondary` / `--color-text-tertiary` |
| `--accent` | `--color-accent` |
| `--chip-bg` | `--color-chip-bg` |
| `--rig` | `--cat-rig` |

注意: `--cat-*` / `--rig-status-*` は canonical なので **rename 対象ではない**。

### 追加予定 semantic token（SoT 未定義）

- `--color-warning` / `--color-danger` を Light / Dark で **新設予定**（CSS 追加は MR-MOCK-058）。
  - Light: `--color-warning: #b45309;` / `--color-danger: #cf222e;`
  - Dark: `--color-warning: #d29922;` / `--color-danger: #f85149;`

### global SoT に即昇格しない token

- `--rig-soft` / `--rig-border` は **即 global 昇格しない**。local 保持＋dark 上書き、または component token 化を MR-MOCK-058 で判断。未定義のまま削除・rename しない。

### badge contrast

- badge contrast は `--cat-*` / `--rig-status-*` の **変更ではなく派生 token で解決**する（MR-MOCK-060）。
- 候補: `--badge-rig-bg` / `--badge-log-bg` / `--badge-status-setting-bg` / `--badge-status-active-bg` / `--badge-status-building-bg`。
- `--cat-parts` は contrast 合格のため派生不要。

### 禁止事項（再掲・追加）

- --cat-rig 自体を暗くしない（`--cat-log` も同様）。
- --cat-* を --color-* に誤統合しない。
- --rig-status-* を --color-* に誤統合しない。
- `--text` を `--color-text` に置換するだけで終わらせない（local `:root` の Light 固定定義削除まで必須）。
