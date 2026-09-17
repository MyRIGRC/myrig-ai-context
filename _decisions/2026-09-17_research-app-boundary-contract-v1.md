# 裁定 — Research ↔ App 境界契約（RIG / PARTS）

裁定日: 2026-09-17 (JST)
裁定者: イタヤ
起票: Cowork（`_state/E2E_CONTRACT_AUDIT_20260917.md` の BLOCKER 6 件 ＋ 不整合 4 件）
入力: Research 主査 `RIG_PARTS_CONTRACT_EXPORT_20260917`（Research 所有領域の正本）
反映 revision: MYRIG-20260917-109 / App schema **v1.6-r4**
前提: 108（`_decisions/2026-09-16_field-contract-rig-parts-v1.md`）は有効。本書はその**一段外側**を閉じる。

---

## 0. この裁定の射程

108 が閉じたのは **App 内**（Register ↔ Public Detail ↔ Owner Detail ↔ App DB）。
本書が閉じるのは **Research Master ↔ App** の境界。

> **「UI に入力欄が無い＝不整合」ではない。**
> 誰がその値を書くのか（write authority）まで決めるのが 109 の仕事。

⛔ Production DB へは接触しない。migration も実行しない。Research schema も変更しない。
⛔ cross_ref を推測で生成しない。SCHEMA_TRUTH 未確認値を確定しない。

---

## 1. `build_tags` の同名別義を解消

| | Research | App |
|---|---|---|
| 列 | `rig_masters.build_tags` | 旧 `rigs.build_tags` |
| 意味 | **その製品がどんな種類の RIG か**（Master 固定の分類軸） | **ユーザーが自分の RIG に付ける公開タグ** |
| 編集 | ユーザー編集**不可**・Master から自動継承 | ユーザーが Register で入力（最大 6 個） |

同じ名前・同じ TEXT[]・意味が逆だった。Next.js で素直にマッピングすると混ざる。

**裁定**
- Research 側の列名は**変更しない**。
- App user entity 側を **`rigs.user_build_tags`** へ改名する。
- **UI 表示名「ビルドタグ」は維持**してよい。
- ⛔ Master の `build_tags` を `user_build_tags` へ**自動コピーしない**。両者は別 provenance。
- Production DB migration は今回行わない。

---

## 2. FK 境界 — 参照先を Research 実 PK へ

App user entity 側の列名（`rigs.rig_master_id` / `rigs.manufacturer_id`）は維持してよいが、
**参照先の PK 名**を Research 同期 Master の実物に合わせる。

| | 誤（v1.6-r3 まで） | 正（v1.6-r4） |
|---|---|---|
| RIG マスター | `rig_masters.id` | **`rig_masters.rig_master_id`** |
| メーカー | `manufacturers.id` | **`manufacturers.manufacturer_id`** |
| PARTS マスター | `parts_masters.id` | **`part_masters.part_id`**（⛔ 接続は HOLD。§5） |

⛔ Research 同期 Master の PK を**再採番しない・改名しない**。

---

## 3. Category 境界 — UUID をやめて slug 参照へ

Research 物理の PK は `rig_categories.slug` / `part_categories.slug`（親子は `parent_slug` 自己参照）。
v1.6-r3 までの App `category_id UUID → *_categories.id` は Research 物理と一致しておらず、
**Category v1.4 §DB設計の DDL（`id` / `parent_id`）が App 向け設計図だった**ことが起点（EXPORT §7-5）。

**裁定**
- `rigs.category_id UUID` → **`rigs.rig_category_slug TEXT`**（FK → `rig_categories.slug`）
- `parts.category_id UUID` → **`parts.part_category_slug`** ＋ **`parts.part_subcategory_slug`**
  （Research は親 `part_category_slug` / 子 `part_subcategory_slug` の 2 列構成）
- ⛔ **slug を偽 UUID へ変換しない。**
- **親カテゴリは `*_categories.parent_slug` から導出**し、ユーザー entity へ重複保存しない。

---

## 4. RIG Variant を保存する

Research は `rig_master_id`（車種）と `rig_master_variants.variant_id`（実際の SKU）を分けている。
App `rigs` には variant の受け皿が無く、**Public RIG Detail は「バリエーション」を実際に表示していた**
（`myrig-rig-detail-v15.html:194` = "Mojave II Body Set"）。同じ SCX10 でも RTR / Kit / ボディ違いが落ちる。

**裁定**
- **`rigs.rig_master_variant_id UUID NULL`** を新設。参照先は同期済み `rig_master_variants.variant_id`。
- Master だけ選択 → NULL / Variant を選択 → variant ID を保持 / Custom RIG → NULL。
- ⛔ `base_model TEXT`（載せ替え用）で代用しない。
- Public Detail の「バリエーション」はこの参照から取る。
- ⛔ **存在しない Variant を推測生成しない。** 現行 Resolver mock は Master 単位（`rm_*`）しか返さないので、
  **Register の Variant 選択導線は未実装のまま**とし、受け皿だけ先に確定する（下記 HOLD H-9）。

---

## 5. PARTS Master ID は HOLD 継続

Research の唯一の source ID は **`part_masters.part_id`（UUID PK）**。`id` ではない。
App `parts_masters` は Rules §B-2（API リソース名は `part_masters`）から逸脱した同名別義で、
列対応も cross_ref 文書も未作成。

**裁定: HOLD H-1 を維持。`parts_master_id` へ今つながない。**

⛔ 境界キーに使ってはいけないもの:
`part_slug`（UNIQUE なし・台帳と 51.5% 不一致）/ `primary_sku`（NULL・`-`・`N/A`・他社 SKU 接頭辞・JAN 混在）/
`part_name` / `canonical_url` / `evidence_url` / `scraped_from` / `master_aliases.alias_value` / `alias_sku` /
`manufacturers.slug` / `platform_slug` / `variant_slug` / これらの組合せハッシュ / App 生成 UUID。

Register は引き続き `pending.master_ref`（出所付き参照）まで。

---

## 6. PARTS 型番の write authority

同じ UI ラベル「型番」でも、経路で**書き込み主体が変わる**。

| 経路 | 権威 | Register |
|---|---|---|
| Master / Variant 紐付き | Research `primary_sku`（Variant があれば `part_master_variants.primary_sku` 優先） | **read-only**（server-derived。ユーザー上書き不可） |
| Custom PARTS | ユーザー | 自由入力 |

---

## 7. Master 継承値を client payload へ詰めない

**原則**

```
client  → master identity（master ID / variant ID）だけを送る
server  → 同期 Master を参照して FK と cache を生成する
```

server-derived として扱う値:
`manufacturer_id` / `manufacturer_name_cache` / `rig_category_slug` / `part_category_slug` /
`part_subcategory_slug` / `platform` / `product_line` / Master 紐付き時の `part_number` /
`scale` / `size_class` / `power_source` / `drivetrain` / `status` ほか Master 継承値。

⛔ Register に hidden input を増殖させない。
⚠️ App schema が CACHE 可として持つ列でも、**ユーザー入力値ではなく server-derived** であることを明記する。
Custom entity だけは App 所有値として別経路を持つ。

---

## 8. `db_register`

`part_masters.db_register = false` は **Resolver / Picker の候補から除外**する（確定）。
Research の物理 DELETE 代替であり、⛔ App 側で物理 DELETE しない。

---

## 9. publication — 公開面と Picker を別契約にする

**確定（公開面）**
Public Detail / Library / Search / 公式画像 / 公式説明 / 公式外部リンクは
**VIEW `master_publication_effective` を唯一の公開判定源**とする。
⛔ App 側で `effective_*` を再計算・固定化しない。
公式画像は `display_status='approved_image'` **かつ** `effective_display_mode='image_enabled'` のときだけ。
公式リンクは `master_external_links`（`display_status='active'`）。

**確定（Picker）— publication gate は Register Picker にかけない**

理由:
1. `master_publication` 行が無い part が **64%**（EXPORT §4・2026-07-27 実測）。
   publication を候補条件にすると Register が機能しない。
2. Research 資料の publication は「**公開表示可否**の唯一の判定源」であり、
   **登録内部検索の資格（picker eligibility）と同義とは限らない**。
3. 自分の持ち物を記録することと、その Master を公開面に出すことは別の判断。

→ **picker eligibility** と **public display gate** を別契約として明文化する。
109 で固定するのは **`db_register=false` → Picker 除外** と **Public surface → publication gate 必須** の 2 点。
Picker にも publication gate が要ると判断が変わったら、その時点で本書を改訂する。

---

## 10. compatibility

- **`compatible_platforms`** — Research 正本。⛔ App ユーザー入力禁止・推測展開禁止・
  **部分文字列照合禁止**（`trx4m ⊃ trx-4` で 318 件の誤接続事故）。1 対多 token（`kraton` → 1/5・1/8・1/10）は
  解決せず据置。XREF（alias map）まで **HOLD**。
- **`compatible_types`** — Research `part_masters` に同名列は**存在しない**。
  App `parts.compatible_types` は **App 所有列**であり Research 継承値ではない。
  ⛔ Research 値として同期しない。所有の裁定（Research 新設 or App 所有）が出るまで、
  MVP の `rig_type` rc-car 固定と整合する既定値のまま触らない。

---

## 11. HOLD（109 でも決めなかった）

| # | 内容 | 解消先 |
|---|---|---|
| **H-1** | PARTS Master ID の App FK 接続 | Research 週次ゲート: `cross_ref_db_to_ui.md` / `cross_ref_ui_to_db.md` |
| **H-9**（新） | **Register の Variant 選択導線** | 受け皿（`rig_master_variant_id`）は 109 で確定。UI は Resolver が variant を返せるようになってから |
| **H-10**（新） | **RIG 側の cross_ref 文書** | Research 主査要請により PARTS と同時に作る |
| **H-11**（新） | **Picker への publication gate** | 現時点では不要と裁定（§9）。判断が変われば本書を改訂 |
| H-2 | 複数 RIG 共有機材（送信機・バッテリー等）の受け皿 | — |
| H-3 | `rigs.external_links` → `entity_links` のデータ移行 | 別裁定 |
| H-4 | `build_details` 旧キーの寄せ方 | ⛔ 意味推定での自動変換は禁止 |
| H-5 | `images.alt` | `caption` とは別概念 |
| H-6 | `condition` の将来設計 | — |
| H-7 | Owner Detail「まとめて編集」の実体 | UI 契約 |
| H-8 | RIG の設定値・加工メモ（`build_details`）の入力導線 | — |
| — | `part_categories.rig_type` / `spec_schema` / `manufacturers.is_active` / `size_class` 13 値 | Research SCHEMA_TRUTH 再実測・週次ゲート |
