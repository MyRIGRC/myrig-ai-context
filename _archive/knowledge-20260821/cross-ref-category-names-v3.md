# カテゴリ名 突き合わせ表 v3 — DB正典 ↔ MyRIG画面表示

更新: 2026-08-20 (JST) / size_class矛盾解消（DB Research rev2回答）反映
元: `cross-ref-category-names-v2.md`

---

## 更新差分（v2→v3）

| 軸 | v2時点 | v3（確定） |
|---|---|---|
| `size_class` | Research正典未定義 | **確定済み**（7/30主査裁定・13値）。正典文書への収載はv1.5で実施予定（引用先誤りの訂正含む） |
| `mini-micro`（build_tags, deprecated） | 置換先未定で宙に浮いていた | 置換先=`size_class`に確定。**個々データの移行は件数READ待ちで継続HOLD** |
| Research未定義軸の分類 | 5軸を一括りに「未定義」としていた | 再分類：`event_tags`のみ**Research管轄・未確定**／`log_type` `surface` `weather`は**App管轄（元から確定していたものの誤分類）** |
| `rig_type=drone-fpv` 表示 | 表記ゆれ（中点/スラッシュ無し/スラッシュ有り混在） | 正は **「ドローン / FPV」**（前後スペース＋スラッシュ）に統一。slugは`drone-fpv`のまま変更なし |

---

## その他の固定値（App側で表示に使っているもの）— 確定版

| 軸 | 値の数 | 状態 | 値 |
|---|---|---|---|
| `rig_type` | 5 | 確定 | RCカー / ミニ四駆（予定） / **ドローン / FPV** / RC飛行機 / RCボート |
| `size_class` | 13 | **確定（7/30裁定）** | `1/5` `1/6` `1/7` `1/8` `1/10` `1/12` `1/14` `1/16` `1/18` `1/24` `1/27` `mini-z`（表示Mini-Z） `other`（表示その他） |
| `log_type` | 4 | 確定（App管轄） | 整備 / 走行 / カスタム / メモ |
| `surface` | 10 | 確定（App管轄） | 岩 / 土 / 芝 / アスファルト / カーペット / 砂 / 雪 / 屋内コース / 屋外コース / その他 |
| `weather` | 6 | 確定（App管轄） | 晴れ / 曇り / 雨 / 雪 / 屋内 / その他 |
| `event_tags` | 12（4件のみ判明・残8件復元中） | **未確定（Research管轄）** | RECON G6 / ヒルクライム / 耐久 / デモリション ほか |
| `power_source` | 7 | 確定・UI非表示 | （データ層のみ） |
| `build_tags` | 5（うち1件deprecated） | 確定 | コンペ / ロックレーサー / スケール / トレイル / ミニマイクロ(deprecated→size_class) |

part_categories親14・rig_categories24は前版（v2）のまま変更なし（`cross-ref-category-names-v2.md`参照）。

---

## 残っているオープン事項

1. `event_tags` 残8件の復元（Research側作業、待ちのみ）
2. `mini-micro`件数READ後の移行判断（Research側作業、待ちのみ）
3. App Schema v1.6の実行タスク（P23バッチで実行中）

## 反映済み・クローズ

- drone-fpv表示統一
- size_class 13値の確定
- 未定義軸の管轄分離

---

## 関連

- `cross-ref-category-names-v2.md`（part_categories親14・rig_categories24の詳細一覧はこちらが正）
- `db-schema-answers-v1.md`（2026-07-30 主査裁定・size_class根拠）
- `myrig_vocab_export_20260820_rev2.csv/json`（Research側エクスポート）
