# カテゴリ名 突き合わせ表 v4 — DB正典 ↔ MyRIG画面表示

> **拘束力: L2（現在の確定仕様・より良い案の提案歓迎）**
>
> 「既存仕様と異なる」ことだけを理由に案を捨てない。
> 差分を明示すればイタヤ裁定で変更できる。
>
> ただし **⚠️ HOLD と記した軸を「確定」として扱わないこと**は L1（逸脱禁止）。

更新: 2026-08-22 (JST) / 元: `cross-ref-category-names-v3.md`

App 側 UI に出す固定値の一覧。列定義そのものの正本は
`myrig_db_schema_v1_6.md`（App所有領域）/ `db-schema-answers-v1.md`（Research所有領域）。

---

## 固定値一覧

| 軸 | 値の数 | 状態 | 値 |
|---|---|---|---|
| `rig_type` | 5 | 確定 | RCカー / ミニ四駆（予定） / **ドローン / FPV** / RC飛行機 / RCボート |
| `size_class` | 未確定 | ⚠️ **HOLD** | 実データは18パターンで enum 運用されていない。`db-schema-answers-v1` §2 記載の13値（`1/5` `1/6` `1/7` `1/8` `1/10` `1/12` `1/14` `1/16` `1/18` `1/24` `1/27` `mini-z` `other`）は**確定値として扱わない**。イタヤ裁定待ち |
| `log_type` | 4 | 確定 | 整備 `maintenance` / 走行 `run` / カスタム `custom` / メモ `memo` |
| `surface` | 10 | 確定（App管轄） | 岩 / 土 / 芝 / アスファルト / カーペット / 砂 / 雪 / 屋内コース / 屋外コース / その他 |
| `weather` | 6 | 確定（App管轄） | 晴れ / 曇り / 雨 / 雪 / 屋内 / その他 |
| `event_tags` | 未確定 | ⚠️ **HOLD・owner未確定** | 列が DB に物理的に存在しない。値の議論より owner（App未実装機能か / Research管轄の未構築か）の確認が先 |
| `power_source` | 7 | 確定・UI非表示 | データ層のみ。値は `db-schema-answers-v1` §2 |
| `build_tags` | 5（うち1件deprecated） | 確定 | コンペ / ロックレーサー / スケール / トレイル / ミニマイクロ(deprecated→`size_class`) |

- `rig_type` の表示は「ドローン / FPV」に統一。slug は `drone-fpv`。
- **`log_type` を将来5値化する裁定が出ても、`setup` は廃止済み slug なので再利用しない。**
- `mini-micro`（build_tags, deprecated）の置換先は `size_class`。`size_class` が HOLD のため移行判断も保留。

part_categories 親14・rig_categories 24 は v2 のまま変更なし。
詳細一覧の参照先は `cross-ref-category-names-v2.md`（⚠️ 同ファイルは本 repo 内に存在しない。
参照が必要になった時点で所在確認が先）。

---

## パーツ子カテゴリ90件（level=2）— 命名基準

App 側 UI には現状未表示。将来表示する際の命名基準として Research 側で確定済み。

1. 抽象語（「システム」「電子」等）単独使用を避け、実体語を用いる
2. 類義語併記は正式名＋補足語まで（3語以上の並記はしない）
3. 目安8文字以内

90件全件がこの基準を満たすかの個別点検は未実施。
子カテゴリを App 側 UI に表示する計画ができた時点で、Research へ「点検の先出し」を依頼すること。

---

## 未確定の事項

1. **`size_class` の値集合** — enum 未確定（上表）。イタヤ裁定待ち
2. **`event_tags` の owner** — 列が存在しない（上表）。owner 確認が先
3. **`mini-micro` の移行判断** — 上記1に従属
4. **パーツ子カテゴリ90件の個別点検** — Research 側作業。ただし `part_categories` テーブルは実DB上0行（空）で、
   点検以前にデータ投入が未了

経緯・一次記録は `_AI/MyRIG_CURRENT.md`（HOLD索引）と
`_decisions/2026-08-21_db-inquiry-002-realdata.md`（実DB確認）にある。

---

## 関連

- `cross-ref-category-names-v2.md`（親14・子90・RIG24 の詳細一覧。⚠️本 repo 内に存在しない）
- `db-schema-answers-v1.md`（Research 所有領域の正本）
- `_decisions/2026-08-21_db-inquiry-002-realdata.md`（実DB確認の一次記録）
- `myrig_vocab_export_20260820_rev2.csv/json`（Research 側エクスポート）
- `parts-category-naming-questions-v1.md`
