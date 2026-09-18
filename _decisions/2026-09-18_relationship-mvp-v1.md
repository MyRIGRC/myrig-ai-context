# 裁定 — Relationship MVP v1（RIG / PARTS / LOG の関連付け）

**日付:** 2026-09-18 JST
**正典:** MYRIG-20260918-114（本裁定で採番）
**裁定者:** イタヤ（Mobile RIG Register の実機シミュレーションから発見 → 監査 → 裁定）
**schema:** `docs/schema/myrig_db_schema_v1_6.md` **v1.6-r6**
**⛔ Production DB 非接触。migration 未実行。物理 DELETE 禁止。Research 所有領域 不変。**

---

## 0. この裁定の射程

Mobile RIG Register（113）を「実際に自分で登録するつもりで」触った結果、
**RIG / PARTS / LOG が互いに孤立している**箇所が見つかった。
本裁定は **MVP 時点でこれが無いと不自然、という最小の関連付けだけ**を確定する。

⛔ **将来の完全な Relationship System を作る裁定ではない。**
装着履歴のタイムライン / LOG シリーズ / `parent_log` / 共有機材の複数 RIG 同時関連 /
自動関連推定 は **今回やらない**（§6）。ただし将来足せる骨格は壊さない。

---

## 1. 発見された穴（実測）

| # | 事実 | 出所 |
|---|---|---|
| F1 | RIG Register（PC v3.1 / Mobile v2）からパーツを足すと **必ず新しい `parts` 行を作る**。既存の自分の PARTS を選ぶ経路は **0 件** | `pc/myrig-register-rig-v3.1.html` `mapToSchema().parts_to_create` / grep「マイパーツ」PC・Mobile・PARTS 全 0 |
| F2 | 候補の出所は **Library Master か手入力の 2 経路のみ** | 同 `partCand()` |
| F3 | PARTS Register v1.1 の「搭載RIG」だけが **既存 Garage RIG を選ぶ**（`rig_parts` を張るだけ）。**唯一の「関係だけを張る」経路** | v1.1 `rigCand()` / `MY_RIGS` |
| F4 | Detail のリンク先は **すでに User Entity（PARTS Detail）が主**で、そこから製品情報 → Library へ降りる二層 | `myrig-rig-detail-v15.html` の使用パーツ 13 リンクが全て PARTS Detail 宛て |
| F5 | **LOG ↔ PARTS はデータも表示も両側とも無い**（111 §12 で v1 非搭載） | schema / LOG Detail v1:62 / PARTS Detail v1-open:152 |
| F6 | 親 entity の状態変化が `rig_parts` に何をするかの規則が **正典に無い** | schema 全文 |
| F7 | `parts` の説明文「1パーツ＝複数RIGに装着可能」が `idx_rig_parts_active_part`（同時 1 台）と**読み方が食い違う** | schema `parts` 冒頭 |
| F8 | RLS の `rig_parts` 行が **v1.6-r3 の `status` 化を反映していなかった**（「取り外しは `removed_at` の UPDATE」のまま） | schema RLS 節 |

**要点:** RIG↔PARTS は**データと表示が既に両側にある**。無いのは **RIG 側の「既存へ張る」入力経路**だけ。
LOG↔PARTS は**両側とも無い**。

---

## 2. 裁定（Q1〜Q6）

### Q1. LOG に PARTS だけを関連付けることを許可するか → **許可**

RIG なしで「このパーツの整備・テスト」という LOG は成立する（サーボ単体の分解整備、タイヤの加工など）。
**RIG のみ / PARTS のみ / 両方 / どちらも無し の 4 状態すべて許可する。**
→ `maintenance_log_parts` の SoT を `part_id` 側に置く根拠。`rig_id` 必須にしない。

### Q2. `maintenance_log_parts` に `rig_parts_id` を持つか → **持たない**

今回必要なのは **「この LOG がどの PARTS に関連するか」という関係だけ**。
過去の装着エピソードや「その LOG 当時どの RIG に付いていたか」まで MVP で追跡する意図はない。
**`log_id ↔ part_id` の単純 relation を SoT とする。**

🔴 **受け入れた副作用（承知のうえ）**: 将来 `rig_parts_id UUID NULL` を追加するのは容易だが、
**その時点で既存行を遡って埋めることはできない。** 自動推定は禁止で、
`rig_parts.installed_at` / `removed_at` は NULL 可のため日付からも復元できない。**過去分は恒久的に NULL。**
schema に明記済み（「後から埋められる」と誤解しないため）。

既存 `rig_parts` が内部的に装着履歴を保持していること自体は壊さない。
ただし **MVP の UI が主に扱うのは「今どの RIG に装着されているか」**。

### Q3. 非公開 PARTS の公開面での扱い → **出さない・数えない**

公開 RIG Detail の「使用パーツ」、公開 LOG Detail の「関連するパーツ」は
**`parts.is_public = true` のものだけ**を出し、**件数もそれで数える**。
⛔ 製品名だけ出す / 「非公開のパーツ N 点」と件数だけ出す、はどちらも**しない**（存在を漏らさない）。

⚠️ これは **v1.6-r3 の RLS 原則「非公開 entity の情報を relation 経由で公開面へ漏らさない」の適用**であって、
新しい原則ではない。r6 で `maintenance_log_parts` にも同じ規則を明記した。

🔴 **帰結（承知のうえ）**: 同じ RIG でも **Owner と第三者で「使用パーツ」の件数が変わる。**
現物の `section__count` は静的値（v15 / v1-open / garage v7 で実測）だが、
**実装では「閲覧者に見える PARTS だけ」を対象に計算する。**

### Q4. RIG を手放す（`ownership_status='past'`）とき → **一度だけ一括確認**

装着中 PARTS がある場合、**1 回だけ**まとめて聞く。

- **パーツも一緒に手放した** → 各 `parts.ownership_state='released'` ＋ `rig_parts` を `removed`
- **パーツは手元に残した** → `rig_parts` を `removed` のみ（**既定**）

⛔ 過去装着履歴をユーザーに細かく管理させる UI にはしない。

### Q5. 手放した PARTS を picker に出すか → **出さない**

通常の「マイパーツから選ぶ」picker は **`ownership_state='owned'` のみ**を候補にする。
「手放したパーツも表示」のような導線は将来必要になったら追加する。

⚠️ **既に張られた relation は `released` になっても消えない**（表示は続く。picker に出ないだけ）。
→ 手放す前に張った LOG 関連は残る。手放した**後に**新規で張ることはできない。

### Q6. 1 LOG の関連 PARTS 上限 → **UI 暫定 10 件。DB 制約にしない**

`entity_links`（最大 10）と同じ考え方。**DB の恒久制約にはしない。**

---

## 3. 追加で確定したもの（Q1〜Q6 に含まれないが今回決めた）

### 3-1. 親 entity の状態変化 → `rig_parts` の伝播規則（schema v1.6-r6）

**`idx_rig_parts_active_part` があるため、`active` を残したまま親を削除 / 手放しすると、
その PARTS は二度とどの RIG にも装着できなくなる。** 規則を 4 行で明文化した（schema 参照）。
**DDL 変更は無い。App の操作規則。**

### 3-2. パーツ追加の 3 経路と順序

RIG Register / Garage RIG Detail の「パーツを追加」は **3 経路**にする。

1. **マイパーツから選ぶ** — 既存 PARTS を `rig_parts` で関連付ける（**新しい `parts` 行を作らない**）
2. **製品から探す** — Library Master → 新規 PARTS ＋ `rig_parts`
3. **手入力する** — Master なし PARTS ＋ `rig_parts`

⚠️ **1 番目に置く根拠**: `parts_master_id` は **HOLD H-1 で未接続**のため、
**Master 一致による重複検出が構造上できない。** MVP で使える重複防止は **1 番の経路そのもの**。

### 3-3. 「別 RIG に装着中」の PARTS を選んだときの分岐（必須）

`idx_rig_parts_active_part` がある以上、**黙って失敗も黙って付け替えも事故**。
**索引の帰結であって任意機能ではない。**

```
このパーツは「SCX10 III」に装着中です。
［このRIGへ移す］［同じ製品をもう1個登録する］［キャンセル］
```

- **移す** = 旧 `rig_parts` を `status='removed'`（`removed_at` は **NULL**。日付を捏造しない）＋ 新 `active` を **1 操作で**書く
- **もう1個登録する** = 「製品から探す」経路へ渡して**新規 PARTS** を作る（管理名で区別）

### 3-4. 共有 Relation Picker（CORE 共有UI Single Source L1）

`pc/assets/js/SoT_relation-picker.js` を新設し、**5 面が同じ 1 本を読む**。
RIG Register（PC / Mobile）/ PARTS Register / Garage RIG Detail / Garage PARTS Detail / LOG Composer。

⚠️ **実測**: mock に `MY_PARTS` 相当が**存在しなかった**（grep 0）。
`MY_RIGS` は `pc/myrig-register-parts-v1.1.html` に**ローカル定義**されていた。
→ **両方を共有へ引き上げる**（L1 違反を 1 つ解消する副産物）。

状態ラベルは **3 値のみ**: `○○ に装着中` / `装着記録なし` / （`released` は候補に出ない）。
⛔「予備」「スペア」と言わない（relation から導出できない。Field Contract §3 のまま）。
色は**中立**。カテゴリ色を使わない（NG-7）。

### 3-5. 「装着 RIG の LOG」を「このパーツの LOG」と見せない（111 維持）

PARTS Detail の「このパーツに関連するLOG」は **`maintenance_log_parts` の行だけ**。
⛔ 装着している RIG の LOG を混ぜない。⛔ `tags` から `part_id` を同定しない。

---

## 4. 111 §12「v1 非搭載」の再 OPEN について

111 §12 が `maintenance_log_parts` を載せなかった理由は
**「両側に表示面が無い → 死蔵入力になる」**であり、
**「将来足すときは PARTS Detail 側の表示契約とセットで裁定する。片側だけ先に作らない」**と条件が書かれていた。

今回は **LOG Detail「関連するパーツ」と PARTS Detail「このパーツに関連するLOG」を同時に作る**ので、
**111 が明示した条件を満たす再 OPEN**。113 との矛盾ではなく、111 が予告した経路。
⛔ 111 の「装着 RIG の LOG を、このパーツの LOG として見せない」は**維持**（§3-5）。

---

## 5. 反証確認

| ケース | 結論 |
|---|---|
| 非公開 PARTS | 公開面に出さない・数えない（Q3）。Owner 面は全部出す |
| 手放した PARTS | picker に出ない（Q5）。既存 relation は残り表示も続く |
| 未装着 PARTS | 両 picker で選べる。「装着記録なし」と表示（⛔「予備」と言わない） |
| 別 RIG に装着中 | 必ず確認（§3-3）。移すと旧 `removed` ＋ 新 `active` |
| LOG に RIG だけ | 許可（現行どおり） |
| LOG に PARTS だけ | 許可（Q1） |
| RIG も PARTS も無い LOG | 許可（111 のまま） |
| 関連 PARTS 複数 | M:N。同一 LOG に同一 PARTS は UNIQUE で弾く。UI 上限 10（Q6） |
| RIG 論理削除 | 事前に `active` を `removed`。⛔ 行を消さない（§3-1） |
| PARTS 論理削除 / `released` | `active` を `removed`。`maintenance_log_parts` は残し、表示で隠す |
| LOG 論理削除 | `maintenance_log_parts` は残し、表示で隠す |
| 他人の PARTS | picker に出ない ＋ App が `log.user_id = part.user_id` を保証 |
| HOLD H-1（Master 未接続） | 本設計は `parts_master_id` に**一切依存しない**（User Entity 同士の関係のみ）。H-1 のまま実装できる |
| HOLD H-2（共有機材） | 触らない。ただし §3-3 の確認があるので**黙って壊れる経路は無くなる** |

**破綻は見つからなかった。**

---

## 6. 今回やらないもの（将来の骨格は壊さない）

- PARTS の過去装着タイムラインをユーザー向けに詳細表示
- LOG 時点の装着エピソード保存（＝ `rig_parts_id`。Q2）
- LOG シリーズ / ツリー化 / `parent_log_id`
- 共有機材（送信機・バッテリー等）の複数 RIG 同時関連（**HOLD H-2 のまま**）
- 高度な関連 LOG 推薦 / 自動推定による関連付け
- PARTS の数量列（セット物は **1 PARTS = 1 管理単位**のまま）
- Custom PARTS → Master 昇格の規則（**H-1 が解けるまで文面のみ**）

**壊さない骨格**: `rig_parts` の履歴行（再装着は新行）/ `maintenance_log_parts` に後から列を足せる形 /
`tags` を関連の代用にしない / Master 参照と User Entity の分離。

---

## 7. VISUAL LOCK の扱い（裁定）

Public Detail 3 面 ＋ Garage Owner Detail 2 面に pixel diff 0 の回帰ゲートがかかっている。
**節を 1 つ足せば pixel diff は必ず動く**（壊れたのではなく意図した追加）。

**手順を固定する:**

1. canon 114 起票・commit
2. mock 実装
3. 対象 5 面の diff と表示を確認
4. **意図した差分だけであることを確認してから**新 baseline を採用
5. 以後は新 baseline に対して pixel diff 0

⛔ **旧 baseline は削除しない。** Relationship MVP 適用前の履歴として残す。
**触らない面（Home / Feed / Library / Search / Browse / Garage Top / Public Garage）は
既存 baseline に対して pixel diff 0 を維持する。**

---

## 8. 影響範囲（実装レーンへの申し送り）

### 新規
`pc/assets/js/SoT_relation-picker.js` / `pc/assets/css/SoT_relation-picker.css` / `_state/relation_check.py`

### 変更（PC 8 面）
RIG Register v3.1（110 再 OPEN・経路追加のみ）/ PARTS Register v1.1（110 再 OPEN・共有化と §3-3）/
LOG Composer v1（111・112 再 OPEN・1 節追加）/ LOG Detail v1（節追加）/ PARTS Detail v1-open（節追加）/
Garage RIG Detail v7（stub 結線 ＋ Q4 確認）/ Garage PARTS Detail v7（stub 結線 ＋ 節追加）

### 変更（Mobile 5 面）
RIG Register v2（113・picker のみ）/ log-detail / parts-detail / garage-rig-detail / garage-parts-detail

### 設計に織り込むだけ
Mobile PARTS Register（未制作。処遇表に共有 picker を最初から書く）/
Mobile LOG Register `register-log.html`（111 前の実体。**今回は触らない**）

### 変更しないと確認済み（実測）
Browse / Library / Search / Feed / Public Garage / Own Garage 一覧 /
`SoT_card-components.js` / `SoT_garage-list.js` — **relation 表示を持たない**

### 再 OPEN しないもの
PC RIG / PARTS Register の章立て・写真・保存意味論 / LOG Composer の種別・本文・写真・投稿後動線 /
Mobile RIG Register の flow・chrome・Advanced トリガー（113 の裁定）
