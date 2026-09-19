# 裁定 — 1 PARTS = 同時 1 RIG をやめる（Multi-RIG Relation v1）

- 日付: 2026-09-19 JST（生成: Cowork `ZoneInfo("Asia/Tokyo")`）
- revision: MYRIG-20260919-115
- 前提正典: MYRIG-20260918-114 / `_decisions/2026-09-18_relationship-mvp-v1.md` / schema v1.6-r6
- 裁定者: イタヤ（実機確認）
- ⛔ Production DB 非接触。migration 未実行。物理 DELETE 禁止。Research 所有領域 不変。

---

## 1. 発端

114 の Relationship MVP を実機で触った結果、**管理が細かすぎた**。

ほかの RIG に装着中の PARTS を選ぶと、必ずこの確認が出ていた。

```
このパーツは「白いSCX10」に装着中です。
［このRIGへ移す］［同じ製品をもう1個登録する］［キャンセル］
```

これはユーザーに、

- これは物理的に同じ個体なのか
- 本当に移動したのか
- もう 1 個所有しているのか

まで判断させている。**MyRIG がやりたいことに対して過剰。**

## 2. 裁定

> **MyRIG は厳密な物品在庫管理ではなく、
> 「ユーザーがどの RIG でどの PARTS を使っているか」を記録できればよい。**

したがって:

- **1 PARTS は複数 RIG と同時に関係を持ってよい。**
- マイパーツを選んだら、**確認を挟まず**そのまま `rig_parts` へ `active` を 1 行足す。
- ほかの RIG との relation が既にあっても **ブロックしない**。
- ⛔「移す」確認を出さない。⛔「同じ製品をもう1個登録する」へ誘導しない。

### 2-1. これは 114 からの方針変更である

114 の

- 「1 PARTS = 同時 1 RIG」
- 「別 RIG 装着中なら移す確認」（裁定 114 §3-3）

は **取り下げる**。114 が間違っていたのではなく、実機で触って前提の方が変わった。

### 2-2. UI だけ隠すのは不可

`idx_rig_parts_active_part` を残したまま確認 UI だけ外すと、
2 台目の INSERT が UNIQUE 違反で落ちる（黙って失敗するか、片方が消える）。
**mock / contract / schema の意味論をすべて揃える。**

## 3. schema への作用（v1.6-r7 / DDL 1 本の撤去）

```sql
-- ⛔ 撤去（2026-09-19）
-- CREATE UNIQUE INDEX idx_rig_parts_active_part
-- ON rig_parts(part_id) WHERE status = 'active';

-- 🔴 残す（唯一の一意制約）
CREATE UNIQUE INDEX idx_rig_parts_active_pair
ON rig_parts(rig_id, part_id)
WHERE status = 'active';
```

- 列の追加・削除・型変更は **無い**。索引 1 本を落とすだけ。
- 既存データの意味推定変換は **行わない**。いま `active` が 1 行だけの PARTS は、そのまま 1 行のまま。
- ⛔ 数量列 (`qty`) は作らない。「2 個持っている」は今回も表現しない。

### 3-1. 親 entity の状態変化 → `rig_parts` の伝播規則は **残す**

v1.6-r6 の伝播規則（RIG 論理削除 / RIG を手放す / PARTS を `released`）はそのまま。
ただし **根拠が変わる**。

| | 旧（r6） | 新（r7） |
|---|---|---|
| なぜ必要か | 索引のせいで **二度とどの RIG にも装着できなくなる**から | 手放した RIG に `active` が残り続けると **「いまこの RIG に付いている」が嘘になる**から |

⛔ 根拠が変わっただけで、規則そのものを緩めない。Q4 の一括確認も残す。

### 3-2. HOLD H-2 は解消

送信機・バッテリー等の共有機材は、**`rig_parts` に RIG ごとの `active` 行**として持てる。
H-2（複数 RIG 同時関連の置き場が無い）は、この裁定で自然に無くなった。

⛔ ただし「共有機材」という分類や専用テーブルは **作らない**。ただの relation として扱う。

### 3-3. HOLD H-1 は **変わらない**

`parts_master_id` は未接続のまま。この裁定は User Entity 同士の関係しか触らない。
「マイパーツから選ぶ」が MVP 唯一の重複防止経路である点も変わらない。

## 4. UI への作用

| 面 | 変更 |
|---|---|
| 共有 `SoT_relation-picker.js` | `confirmMove()` 削除。`mountedRigId`（単数）→ `mountedRigIds`（配列）。attach は即 `onPick` |
| 状態ラベル | `○○ に装着中` / **`○○ ほか N 台に装着中`** / `装着記録なし`。⛔「予備」「スペア」は従来どおり禁止 |
| Garage PARTS Detail（PC / Mobile） | 「装着RIGを変更」→ **「装着RIGを追加」**。⛔「〜に変更しました」と言わない（ほかの装着は外れないので嘘になる） |
| Mobile PARTS Register 02 搭載RIG | 1 行（変更で置き換え）→ **複数行 ＋「装着するRIGを追加」**。行ごとに × で外す |
| PC PARTS Register v1.1 02 搭載RIG | もともと複数行だった。**X-1（索引との食い違い）はこの裁定で解消**。手を入れない |
| RIG Register（PC / Mobile） | `movedFrom` / `_move_from_rig` / `moved_from_rig` を削除。足すだけ |

**唯一残る一意制約の分だけ**は UI でも守る:
すでに選んだ RIG は候補から外す（同じ RIG に同じ PARTS を 2 回足さない）。

## 5. ⛔ 今回やらないもの

- 過去装着タイムライン（114 のまま非搭載）
- 厳密な在庫・個体管理（数量・シリアル・個体識別）
- 「同じ製品をもう1個」を表現する仕組み
- 既存データの意味推定変換

## 6. 反証確認

| 疑い | 確認 |
|---|---|
| UI だけ直して索引が残っていないか | schema v1.6-r7 で DDL を撤去。mock のコメントからも「1 台だけ」を削除（grep 実測 0） |
| 同じ RIG に 2 回足せてしまわないか | picker / Mobile PARTS Register の両方で `exclude` を掛け、gate で「候補が 1 件減る」ことを実測 |
| 「移しました」の文言が残っていないか | mock 全体で grep 0。gate（relation_check B2）でも body 文字列を見て落とす |
| Q4 の一括確認まで消していないか | `garage_check` / `relation_check` B3 が PASS のまま。根拠の文章だけ書き換えた |
| 単数 `mountedRigId` の読み残しが無いか | grep 0。`mountedRigIds()` を picker から export して他面も配列で読む |
| 公開面の表示が壊れていないか | `mobile_garage_detail_check` M9（Public Detail baseline）PASS |
