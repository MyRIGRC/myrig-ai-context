# カテゴリ名 突き合わせ表 v4 — DB正典 ↔ MyRIG画面表示

更新: 2026-08-21 (JST) — docs精査で size_class / event_tags / log_type を実DB確認結果に合わせて改訂
前回更新: 2026-08-20 / パーツ子カテゴリ命名基準・event_tags進捗を反映
元: `cross-ref-category-names-v3.md`

---

## 更新差分（v2→v3 時点の記録）

> ⚠️ **2026-08-21 監査: 本表は v3 策定時点の記録であり、`size_class` と `event_tags` は
> その後の実DB確認で覆っている。**現在の状態は下の「その他の固定値」表を見ること。

| 軸 | v2時点 | v3時点の記録 | 2026-08-21 実DB確認後 |
|---|---|---|---|
| `size_class` | Research正典未定義 | 確定済み（7/30主査裁定・13値） | ⚠️ **HOLD。**13値の出典は db-schema-answers-v1 §2 に実在するが、実データは18パターンでenum運用されていない |
| `mini-micro`（build_tags, deprecated） | 置換先未定で宙に浮いていた | 置換先=`size_class`に確定 | `size_class` 自体がHOLDのため継続HOLD |
| Research未定義軸の分類 | 5軸を一括りに「未定義」 | `event_tags`のみResearch管轄・未確定／`log_type` `surface` `weather`はApp管轄 | ⚠️ `event_tags` は**列が物理的に存在せず**owner未確定。✅ `log_type` は**4値で決着**（2026-08-22・下表参照） |
| `rig_type=drone-fpv` 表示 | 表記ゆれ | 正は「ドローン / FPV」に統一。slugは`drone-fpv` | 変更なし（有効） |

---

## その他の固定値（App側で表示に使っているもの）— 確定版

| 軸 | 値の数 | 状態 | 値 |
|---|---|---|---|
| `rig_type` | 5 | 確定 | RCカー / ミニ四駆（予定） / **ドローン / FPV** / RC飛行機 / RCボート |
| `size_class` | **不明（実データ18パターン）** | ⚠️ **HOLD（2026-08-21 実DB確認で「13値確定」の裏付けが崩れた）** | 旧記載の13値: `1/5` `1/6` `1/7` `1/8` `1/10` `1/12` `1/14` `1/16` `1/18` `1/24` `1/27` `mini-z` `other`<br>⚠️ **13値そのものの出典は存在する** — `db-schema-answers-v1.md`（2026-07-30 主査裁定・App側の写し）§2 に固定値集合として明記。**HOLDの主因は実装実態との乖離**:<br>`_decisions/2026-08-21_db-inquiry-002-realdata.md` J-2: **実データは18パターン**（`NULL` 639件が最多 / `1/10` 341 / `1/8` 62 / `1/12` 50 … `M-chassis` 2 / `mini` 1 等の自由記述が混在）で、**13値enumとして運用されていない**。<br>副次的な不確かさ: J-1 の記録どおり **DB Research PJ が保持する7/30裁定書の原本は未確認**（App側の写しのみ）で、**`MyRIG_Category_Structure_v1.4`（6/16改訂・7/30より前）は TEXT 自由記述として定義**している。<br>App側提案は「実データ主導（18パターンを土台に再確定）へ切替」。イタヤ裁定待ち |
| `log_type` | **4** | ✅ **4値が正**（2026-08-21 モック照合で決着） | 整備 `maintenance` / 走行 `run` / カスタム `custom` / メモ `memo`<br>**「5値論争」は誤診だった。**5値目の実装値は `setting` ではなく **`setup`** で、これは schema v1.2 で廃止済みの値（`pc/myrig-log-composer-modal-v0.3.9.html:1043`）。同じモック内の `pc/myrig-feed-v3.html:725` が「以前ここにあった『セットアップ』は廃止された値」と注記している。**`setting` という値はモックにもDBにも存在しない**（文書上にしかない語）。<br>実装は登録が5値・閲覧/検索/絞り込みが4値で内部分裂しており、**登録フォームから `setup` を撤去すれば解消**（裁定不要のバグ）。詳細は `_audit/canon-vs-mockup-20260821.md` |
| `surface` | 10 | 確定（App管轄） | 岩 / 土 / 芝 / アスファルト / カーペット / 砂 / 雪 / 屋内コース / 屋外コース / その他 |
| `weather` | 6 | 確定（App管轄） | 晴れ / 曇り / 雨 / 雪 / 屋内 / その他 |
| `event_tags` | **不明（列が物理的に存在しない）** | ⚠️ **HOLD・owner未確定** | 2026-08-21 実DB確認（`_decisions/2026-08-21_db-inquiry-002-realdata.md` I-3）: **`event_tags` という列名はDB全体のどのテーブルにも存在しない（0件）。** Category v1.4「確定値12種」も本表の旧記載「4件判明・残8件復元中」も、**どちらも未実装の机上記述**と判明。値の議論より先に owner（App実装かResearch管轄か）の確認が必要 |
| `power_source` | 7 | 確定・UI非表示 | （データ層のみ） |
| `build_tags` | 5（うち1件deprecated） | 確定 | コンペ / ロックレーサー / スケール / トレイル / ミニマイクロ(deprecated→size_class) |

part_categories親14・rig_categories24は前版（v2）のまま変更なし
（`cross-ref-category-names-v2.md` 参照。⚠️ただし同ファイルは**本repo内に存在しない** — 下の「関連」参照）。

---

## パーツ子カテゴリ90件（level=2）— 命名基準確定（2026-08-20）

App側UIには現状未表示。将来表示する際の命名基準として、Research側で以下が確定済み。

1. 抽象語（「システム」「電子」等）単独使用を避け、実体語を用いる
2. 類義語併記は正式名＋補足語まで（3語以上の並記はしない）
3. 目安8文字以内

**90件全件がこの基準を満たすかの個別点検は未実施**（次回週次ゲート、優先度は親14 SQL反映の次）。
子カテゴリをApp側UIに表示する計画ができた時点で、Researchへ「点検の先出し」を依頼すること
（連絡すれば優先度を上げてもらえる）。

---

## 残っているオープン事項

1. **`size_class` の値集合の再確定** — 13値の出典は db-schema-answers-v1 §2（7/30主査裁定の写し）に実在するが、
   実データは18パターンでenum運用されていない（実DB確認J-2）。
   実データ18パターンを土台に再確定する案をイタヤ裁定待ち
2. **`event_tags` の owner 確認** — 列が物理的に存在しない（実DB確認I-3）。
   App未実装機能の先行記述なのか、Research管轄の未構築なのかを先に決める
3. ~~`log_type` 4値/5値の裁定~~ → **クローズ（2026-08-21）。4値が正**。5値目は廃止済み `setup` の
   残骸だった。残るのはモック側の撤去作業のみ（composer `:1043`・`:1299`、`register-log.html:381`）
4. `mini-micro`件数READ後の移行判断（`size_class` 確定が前提のため上記1に従属）
5. パーツ子カテゴリ90件の個別点検（Research側作業、待ちのみ）。
   ⚠️ ただし実DB確認E-3で `part_categories` は**0行（空）**と判明。点検以前にデータ投入が未了

## 反映済み・クローズ

- drone-fpv表示統一
- 未定義軸の管轄分離（※`event_tags` の管轄は上記2で再確認中）
- パーツ子カテゴリ90件の命名基準（点検自体は上記オープン事項5として継続）

（2026-08-21 監査: 旧「反映済み」にあった「size_class 13値の確定」は**クローズ扱いを撤回**し、
オープン事項1へ差し戻した）

---

## 関連

- `cross-ref-category-names-v2.md`（part_categories親14・rig_categories24の詳細一覧はこちらが正）
  ⚠️ **2026-08-21 監査: 本repo内に存在しない**（`_archive` にも v3 のみ）。
  親14・子90・RIG24 の詳細一覧を参照する必要が生じた場合は、所在の確認が先
- `db-schema-answers-v1.md`（2026-07-30 主査裁定・**size_class 13値の出典**。App側の写し）
- **`_decisions/2026-08-21_db-inquiry-002-realdata.md`（実DB確認。size_class / event_tags /
  spec_data / part_categories の実体はこちらが最新の一次記録）**
- `myrig_vocab_export_20260820_rev2.csv/json`（Research側エクスポート）
- `parts-category-naming-questions-v1.md`（Q5・今回で回答クローズ）
