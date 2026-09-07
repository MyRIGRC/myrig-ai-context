# DECISION — PC Core Convergence Audit の裁定と是正（2026-09-07）

- 日付: 2026-09-07 JST
- revision: MYRIG-20260907-072
- 監査レポート: `myrig-mockup` `_state/CORE_CONVERGENCE_AUDIT_20260907.md`（生データ `_state/core_audit/`）
- 対象: PC Core 9面（Home / Browse RIG / PARTS / Category / Search / Feed / RIG Detail / PARTS Detail / LOG Detail）
- モック commit: `75fc806` → `9086794`（7コミット）

---

## 0. 監査の結論（実測）

**壊れは少なく、ズレは「共有化の取りこぼし」と「トークンの未整理」に集中していた。**
Detail 3面は同じ shell で寸法が完全一致、コメント単位は4面で完全一致、entity カードは LOG / RIG で同じ文法。
「LOG 系・コメント系だけ文字が小さく弱く見えるか」は**見えない**（12px 未満の割合は LOG Detail 18.6% で最も低い側）。

分類: BUG 5 / DRIFT 5 / INTENTIONAL 11 / TUNING 6。

---

## 1. 裁定（5件）

| # | 論点 | 裁定 |
|---|---|---|
| 1 | Feed 本命案（068 CLOSE）が `?flow=inline` の fixture のままで既定は旧状態 | ✅ **今、既定へ昇格する。** fixture / demo 分岐を恒久実装として残さない。旧 Feed 挙動との二重管理は禁止 |
| 2 | Contrast: light `--color-text-tertiary` 3.08:1 / dark `--color-accent` 3.64:1（AA 未満） | ✅ **修正する。「passing する最小視覚差」**。色案を増やさない。tertiary が text 以外（罫線・装飾）にも使われているなら **token 全体を暗くせず text token を分離**。dark accent は同色相の明度調整のみ。🔴 **PC カテゴリ色 v8 rollout と混ぜない**（別バッチ） |
| 3 | 「いいね済み」が3色（rail 青 / LOG inline `--cat-parts` 赤 / Feed #e5484d） | ✅ **統一する。ただし `--color-reaction-on` ではなく like 専用 `--color-like-on`。** favorite / pin は別 semantic state として現行色を維持。🔴 **`--cat-parts` を like 色へ流用しない**（カテゴリ識別色と操作状態色は別職域）。形・背景・配置は variant ごとでよい。**pressed の意味色だけ共通** |
| 4 | discovery 面の entity カード `href="#"`（Core 内 257件） | ✅ **Core 対象面で一括配線する。** canonical route は `/rig/[rigId]` / `/parts/[partId]` / `/log/[logId]`。🔴 **Garage 等の未監査面へ範囲を広げない** |
| 5 | Feed カードが3実装（PC page-local / Mobile page-local / 未使用の共有 `feed` variant） | ✅ **1 と同時に Shared Source 化。** 🔴 **古い variant へ現行 UI を合わせず、採用された PC Feed を正として共有側を更新**。死んだ静的14枚と旧定義を撤去 |

### 裁定なしで直してよいもの

A-1 Feed の `<main>` / `<h1>`、A-2 Browse stage の複数 h1、A-3 header active の `aria-current="page"`、
B-4 md5 一致の Shelf CSS の Shared Source 化、B-2 Feed 面内会話の ⋯ / 通報（同じ `<myrig-comment>` の compact variant として有効化）。

### 🔴 直さないもの

**B-5「Feed 右レーン radius 14 vs Detail 9」は直さない。**
Feed と Detail は役割が異なり、右レーンの見た目まで機械的に揃える根拠がない（071 の原則に逆行する）。
**INTENTIONAL DIFFERENCE 寄りとして保留。** 実画面で違和感が出たときに再検討する。

pin は PENDING 維持。TUNING 6件は再オープンしない。

---

## 2. 是正結果

| 分類 | 監査時 | 是正後 |
|---|---|---|
| **BUG** | 5 | **0** |
| **DRIFT** | 5 | **0 ＋ 保留 1（B-5）** |
| INTENTIONAL | 11 | 11 ＋ B-5 |
| TUNING | 6 | 6（後回し） |

### 新しい Shared Source（正本の所在）

| | 正本 | 消費面 |
|---|---|---|
| Feed カード markup | `SoT_card-components.js` `<myrig-log-card variant="feed">`（**light DOM**。他 variant は shadow のまま） | Feed |
| Feed カード style | `pc/assets/css/SoT_feed-card.css` | Feed |
| コメント単位（⋯ / 通報）style | `pc/assets/css/SoT_comments.css`（SoT_detail.css から移動） | Detail 3面 / Feed |
| コメント挙動（⋯ 委譲・面内会話・投稿・件数同期） | `pc/assets/js/SoT_comments.js` | Detail 3面 / Feed |
| Shelf style | `pc/assets/css/SoT_shelf.css` | Browse RIG / PARTS |
| 意味トークン | `--color-like-on`（like pressed）/ `--color-decor-tertiary`（装飾用・旧 tertiary 値） | 全面 |

### トークン値（`SoT_tokens-v6.css`）

| token | light | dark |
|---|---|---|
| `--color-text-tertiary` | #8b949e（3.08）→ **#6a737e（4.81 / subtle 4.52）** | #6e7681（3.77）→ **#7a838e（4.50）** |
| `--color-decor-tertiary`（新設・装飾用） | #8b949e（旧値のまま） | #6e7681（旧値のまま） |
| `--color-accent` | 変更なし | #0969da（3.33）→ **#1b80f6（4.50）** |
| `--color-like-on`（新設） | **#e12f35（4.51）** | **#e64c50（4.54）** |

すべて同色相・同彩度で明度のみ。比率は白 / subtle（light）、`#161b22` surface（dark）基準。

### 検証

- **既定 Feed は旧 `?flow=inline` と画素差 0**（1440 light / 1280 dark / スクロール 700・1600）
- Detail 3面・Home・Browse: 構造変更（a11y / 配線 / Shelf 共有化）は**画素差 0**
- トークン変更: `token_change_proof.py`（旧ツリー vs 新ツリー＋旧トークン注入）で **9面すべて ★ 0** → 差分はトークン値だけ
- like token: 差分は like ボタン領域のみ（LOG 297px / RIG 512px / Feed 0）
- 実クリック: Home の RIG / PARTS / LOG カードが正しい Detail に着地。nested interactive 0
- `entity_actions_check` 36/36 ／ `launcher_link_check` 174/0 ／ `search_regression` 101/101 ／ `detail_contract_check` 51/0 ／
  `footer_single_source_check` 4/0 ／ `shelf_propagation_check` Browse 2面 8/0 ／ 9面 pageerror 0

### Core 対象外で触れたもの（明示）

- `myrig-garage-logs-v6.html`: 共有 `feed` variant の実体が変わったため、旧 shadow 実装を残す代わりに既存の `pc` variant へ属性を写した（6枚）。**Garage バッチで詰める**
- `--color-decor-tertiary` への付け替えは active な `pc/` 全体 45件（見た目不変。絞ると対象外の面の罫線が暗くなるため）
- `about` / `auth-onboarding` は page-local で token を再定義しており今回の contrast 修正が効かない（記録のみ）

---

## 3. 記録（実装者が決めていないこと）

- LOG inline の **favorite pressed は `--cat-parts`（赤）のまま**、rail の favorite pressed は青。「favorite / pin は現行維持」の裁定どおり触っていないが、
  **カテゴリ色を操作状態色に使っている箇所として残る**（次の論点候補）
- `pc-feed-card__*` の class 名は画素不変を優先して改名していない。Next.js 移行時に `feed-card__*` へ
- Mobile `feed.html` の page-local `.feed-card` は Phase 4 で同じ共有 variant に寄せる
