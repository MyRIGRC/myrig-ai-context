# 裁定 — RIG / PARTS Field Contract 統合（Register ↔ Public Detail ↔ Owner Detail ↔ App DB）

裁定日: 2026-09-16 (JST)
裁定者: イタヤ
起票: Cowork（Field Contract 横断監査 `_state/FIELD_CONTRACT_AUDIT_20260916.md` → 裁定シート）
反映 revision: MYRIG-20260916-108
影響: App schema v1.6-r3 / RIG・PARTS Register / Public Detail / Owner Detail / privacy / LOG（entity_links）

---

## 0. 基本モデル — 4層

| 層 | 意味 | 代表 |
|---|---|---|
| 1. Master | 製品そのもの | `rig_masters` / Research `part_masters` |
| 2. User Entity | ユーザーが管理する RIG / PARTS の**個体・管理単位** | `rigs` / `parts` |
| 3. Relation / History | 実際に起きた装着・取り外しの**事実** | `rig_parts` |
| 4. Public / Owner data | 公開情報と本人管理情報 | 列ごとに公開／Owner-only を明示 |

既存 DB・既存画面を守ること自体を目的にしない。
一方で、**既存値を意味推定で変換しない・偽値で埋めない**。

---

## 1. PARTS は「製品」ではなく「ユーザーの管理単位」

同じ Master を参照する複数の PARTS を作成できる。

```
Master:      Reefs RC 422HD
User PARTS:  TF2用 / SCX10用 / スペア
```

物理 1 個 = 1 レコードとは限らない。タイヤ 4 本セットを 1 セットとして管理するなら 1 レコードでよい。

**`parts.nickname`（UI 表記「管理名」）** を追加する。役割は Owner が同一製品の管理単位を区別すること。
Public Detail の主タイトルは `product_name` のまま。**管理名を公開の製品名代わりにしない。**
公開したい用途・加工内容は `description` で扱う。

---

## 2. `condition` — 意味論を廃止

`new / used / modded` は
**「入手時の状態」「現在の状態」「加工の有無」という異なる軸**を 1 列に混ぜていた。

- Register から入力を外す
- Public PARTS Detail の「状態 新品/中古/加工済み」行を外す
- 新規作成時に `new` を自動設定しない（**DEFAULT 'new' を撤廃**）
- 列は DROP しない。既存データの意味推定変換もしない
- Production DB migration は今回行わない
- 加工内容は `description` / `images.caption` / LOG で表現する
- 将来 `condition` を再設計する余地は残す

---

## 3. 所有と装着を別の事実として扱う

**PARTS の所有 = `parts.ownership_state`**（MVP では最小限）

| 値 | UI |
|---|---|
| `owned` | 所有中 |
| `released` | 手放した |

売却 / 譲渡 / 処分の理由分類は MVP では持たない。

**⛔ active な `rig_parts` が無いことを「予備」と自動判定してはいけない。**
relation から言えるのはここまで:

| relation | 正しい表示 |
|---|---|
| active あり | ○○RIG に装着中 |
| active なし | **装着記録なし** |

「スペア」「予備」は**ユーザーの意図**であり relation から導出できない。
必要なら管理名（`nickname`）か `private_note` に書く。

---

## 4. `rig_parts` = 実際の装着履歴の SoT

- 「実際に装着した／していた事実」だけを持つ
- ⛔ `planned`（購入予定 / 取り付け予定）は入れない
- ⛔ Garage に無い RIG 名だけの relation は MVP では作らない
  （必要なら最小 RIG 登録へ誘導するか `private_note` へ）

**日付不明問題の修正 — 事実状態と日付を分離する**

`status`（`active` / `removed`）を追加。`installed_at` / `removed_at` はどちらも NULL 可。

```
status = removed
removed_at = NULL
→ 取り外し済みという事実は分かるが、日付は不明
```

再装着しても過去 relation を上書きしない。新しい行を足して履歴として残す。

**active relation の一意性を part_id 側でも保証する。**
PARTS が「ユーザー管理個体」である以上、1 つの PARTS が同時に複数 RIG へ active 装着されることはない。

⚠️ 送信機・バッテリー等の**複数 RIG で共有して使う機材**は装着 relation とは別概念。
今回 `rig_parts` へ混ぜない。受け皿は **HOLD**。

---

## 5. `build_details` と `rig_parts` の責務分離（今回確定）

判定基準は「**登録済み PARTS として存在する製品個体か否か**」。

| 入るもの | 例 | 保存先 |
|---|---|---|
| 製品個体と装着履歴 | Servo = Reefs 422HD / ESC = Hobbywing Fusion | **`rig_parts`**（唯一の SoT） |
| 設定値・加工・自由項目 | Drag Brake 80% / Shock Oil 35wt / Body Paint PS-5 | **`build_details`** |

同じ装着製品を両方へ二重保存しない。
⛔ manual 入力された製品名を安易に `build_details` へ重複保存しない。
旧 `build_details` キー（mechanics / suspension / exterior / electronics / battery）は廃止。
既存データの意味推定変換は行わない。

---

## 6. RIG の状態は 2 軸

**所有 — `rigs.ownership_status`（維持）**: `current` 現在所有 / `past` 過去所有 / `wishlist` 欲しい

**利用状況 — `rigs.usage_status`（新設・任意）**: `building` 製作中 / `active` 使用中 / `stored` 保管中 / NULL 未設定

`ownership_status='current'` のときだけ意味を持つ。

- ⛔「整備中」は一時的なイベントなので状態にしない（LOG で扱う）
- ⛔「アーカイブ」も利用状況へ混ぜない
- Public Detail では曖昧な「状態」1 行にまとめず、**所有: 現在所有 / 利用状況: 製作中** のように意味を分ける

---

## 7. 購入 / 入手情報

RIG / PARTS とも任意で持つ（入手時期・入手価格・入手先）。
**RIG の価格は「ベース車両／キットを入手した価格」**であり、総製作費ではない。

**価格・入手先は Owner-only。Public Detail へ出さない。** 入手時期は公開可。

既存の `purchase_price` / `purchase_store` / `currency_code` を利用する。

**日付精度 — 偽値を入れない最小設計**
既存の他領域に日付精度のパターンは無かったため、新規に最小設計を定義する。

```
purchased_period TEXT NULL
CHECK (purchased_period ~ '^[0-9]{4}(-[0-9]{2}(-[0-9]{2})?)?$')
```

`YYYY` / `YYYY-MM` / `YYYY-MM-DD` を**ユーザーが入れた粒度のまま**保持する。辞書順＝時系列順。
`purchased_at DATE` は**非推奨**（DROP しない）。⛔ 年月入力に架空の 1 日を補完しない。

---

## 8. `rig_type`

MVP の Register では **`rc-car` 固定**。Selector は表示しない。
App schema の将来 5 タイプ対応は維持する。
これはサービス上の大分類であり、**パーツ互換性を意味しない**。
`compatible_platforms` には触らない（Research 正本が上流）。

---

## 9. 公開文 / Owner-only 情報

| 列 | 区分 | 意味 |
|---|---|---|
| `parts.description` | 公開 | パーツ紹介文 |
| `parts.nickname` | **Owner-only** | 管理名 |
| `parts.private_note` | **Owner-only** | メモ |
| `rigs.description` | 公開 | 紹介文 |
| `rigs.tagline` | 公開 | キャッチコピー |
| `rigs.build_tags` | 公開 | ビルドタグ |
| `rigs.private_note` | **Owner-only** | メモ |
| `images.caption` | 公開 | フォトノート |
| `images.alt` | — | `caption` とは別概念。**今回の裁定対象にしない** |
| `*.purchase_price` / `*.purchase_store` | **Owner-only** | 価格・入手先 |

⛔ `tagline` / `build_tags` / `private_note` を `build_details` へ押し込まない。

**既存 `parts.description` が「ユーザーメモ」として運用された実データがある場合、
勝手に公開紹介文へ変換しない。本番データの自動変換は今回行わない。**

---

## 10. 公開範囲の契約

公開 PARTS が非公開 RIG と relation を持っていても、公開面へ次を漏らさない。

- 非公開 RIG の名前
- 非公開 RIG の画像
- 非公開 RIG へのリンク
- **relation 経由で非公開 RIG を推測できる表示**（件数の実数表示を含む）

同様に、非公開 entity の情報を relation 経由で公開面へ漏らさない。

---

## 11. `entity_links`（新設）

RIG / PARTS / LOG のユーザー関連リンクを共通 Single Source へ切り出す。
列定義は `docs/schema/myrig_db_schema_v1_6.md` Domain 4 を正とする。

`moderation_status`: `not_required` / `pending` / `approved` / `rejected`
- SNS 等（ドメイン照合で本人性が担保できる）→ `not_required`
- 個人サイト → `pending` → `approved` / `rejected`
- **個人サイト URL を変更したら `approved` を引き継がず `pending` へ戻す**

Master 公式リンクは Research 側 `master_external_links` が正本。⛔ `entity_links` へコピーしない。
Public Detail では**ユーザーリンク + Master 公式リンクを同じ視覚ブロックへ合成してよい**（データ源は分離）。

既存 `rigs.external_links` は `entity_links` への移行対象。**恒久二重管理は禁止。**
Production DB migration は今回行わない。

---

## 12. `planned`

MVP では**構造化保存しない**。対象: 購入予定 / 取り付け予定 / Garage に無い RIG 名だけの relation。
必要なら `private_note` へ書ける。
**「以前使用」は実際の履歴なので `rig_parts` へ残す**（日付不明でも `status='removed'` の事実だけ保持できる）。

---

## 13. HOLD（今回は決めない）

| # | 内容 | 理由 |
|---|---|---|
| H-1 | **PARTS の Master ID を App FK へ接続すること** | CORE 上、Research = `part_masters`（単数）/ App 文書 = `parts_masters`（複数）で**同名別義**。cross_ref 写像表が未作成。Register が実際に持っているのは Resolver の `kind:'part_master'`（＝Research 側の語彙）で、値も mock slug（`pm_*`）であり App の `parts_masters.id` UUID ではない。**名前だけ schema に合わせて ID を刺さない。** Register は `parts_master_id` を出さず、出所付きの `master_ref` として保持する |
| H-2 | 複数 RIG 共有機材（送信機・バッテリー等）の受け皿 | `idx_rig_parts_active_part` と衝突するため `rig_parts` へ混ぜられない |
| H-3 | `rigs.external_links` → `entity_links` のデータ移行手順 | Production DB への migration は別裁定 |
| H-4 | `build_details` 旧キーの新キーへの寄せ方 | 意味推定での自動変換は禁止 |
| H-5 | `images.alt` の追加要否 | `caption` とは別概念。今回の対象外 |
| H-6 | `condition` の将来設計 | 意味論は廃止済み。再設計の余地は残す |
| H-7 | Owner Detail「まとめて編集」の実体 | Register を開くのか独自フォームかが未定義。UI 契約であってデータ契約ではない |
