# Research の現状確認（2026-10-04）— 2 つの報告と App 側の読み

> 保存: 2026-10-04 JST / Claude（App 側主査）／ 正典 revision: MYRIG-20261004-153
> 依頼: App 側 → RC Master Data Research（イタヤ経由・10-04 12:11）。報告は 2 つ: ① Claude の Research プロジェクト（主査）② GPT（Codex）の Research 担当。**本文は無編集**（Supabase の project ref だけ伏せた）
> どちらも Research DB には SELECT だけ。Production DB には非接触

## 0. App 側の読み（Claude）

### いちばん大事な事実
| 事実 | 出どころ |
|---|---|
| **Research DB は 500 MB の上限まで残り 25〜47 MB**（Supabase の数え方で 453.1 MiB = 475.1 MB）| ①A-1・②A-1 が一致 |
| **DB 全体のバックアップ（dump）が 1 つも無い**。あるのは 07-28 の CSV 18 表だけ（表の定義・関数・権限・VIEW は無い／07-28 以降は無い／戻せるかは未検証）| ①B・②B が一致 |
| 9/27 に作ったバックアップの手順（Mac Studio の `~/MyRIG_local/RUNS/MYRIG-CC-INFRA-RESEARCHDB-DATA-BACKUP-VERIFY-001/`）は**未実行・イタヤの実行待ち** | ①B-1 |
| `change_logs` が 193.7 MB で全体の 44%（736,292 行）| ①A-2・②A-2 |
| DB への書き込みは 08-23 が最後。いまは増えていない。**次の一括更新 1 回で 500 MB を超える**見込み（D2 の索引だけで 20〜25 MB）| ①A-5 |
| ローカルにしか無いもの: 取得した HTML・候補の台帳・**08-23 以降の成果物の全部**（DB 未反映）。GitHub（myrig-research）は 05-29 が最後で、手元は 235 コミット先行・未コミット 692 件 | ①C |
| Production DB の中身（05-24 の記録）: Axial 1 社ぶんの写し 2,444 行。Research レーンは使っていない | ①D |

### Claude（App 側）の訂正と反省
1. **152 の「停止から 90 日」は誤り。正しくは 1 年。** Supabase のコネクタの資料検索が古い版を返していた。公式ページ（2026-10-02 更新）を直接読んで確かめた: 「1-year window to restore」。Research の指摘が正しい
2. **App 側が作ったテスト用プロジェクトが 339 MB を使っている**（10-03 の実測で入れた作り物のパーツ 14.5 万行 × 2 表）。Research の指摘（利用制限の判定は組織の全プロジェクトの合計の恐れ）を受けて測った。**Research 453 ＋ Test 339 = 792 MiB**。合計で数えられるなら、すでに 500 MB を超えている。→ 作り物の 2 表を消すか、テスト用を止める（イタヤの判断待ち・§1）

### 食い違い・確かめること
- **Research DB を一時停止する計画（9/27）と、App が Research DB から差分を取りに行く設計（10/02・§16）がぶつかる** → 決める必要がある
- manufacturers の行数が 07-28 の CSV 931 → 今 863（−68）。物理 DELETE 禁止なのに減っている理由は未確認（②E-2。CSV 側の数え方の違いの可能性もある）
- Research 側の持ち越し: 旧 anon キーの無効化・anon が実行できる関数 5 本・RLS 無効 32 表 → **App の同期専用の役割（D5）を作る前に片付いているのが望ましい**
- ②（GPT）の実測は `transaction_read_only = off` のまま（SELECT だけだったと報告）。①（Claude）は on を確認

## 1. イタヤに決めてほしいこと（優先順）
| # | 内容 | Claude の推奨 |
|---|---|---|
| 1 | **バックアップをいま実行する**（Mac Studio で OWNER_RUNBOOK_001）| **最優先。ほかの何よりも先** |
| 2 | テスト用プロジェクトの扱い: 作り物の 2 表を消す ／ プロジェクトを止める | **両方**（消してから止める。実測の結果は正典に保存済み）|
| 3 | 容量: Free のまま（`change_logs` と `audit_backup` をローカルへ移して DB から外す）か、Pro にするか | バックアップのあとで決める。**書き込みを再開する前に必須** |
| 4 | Research DB の一時停止（9/27 の計画）を取りやめるか | App の同期を前提にするなら取りやめ |
| 5 | 次の週次ゲートの日 | 1〜3 のあと |

---

## 報告 ①（Claude の Research プロジェクト・主査）

# DB Research 回答 — 現状確認（2026-10-04）
回答: 2026-10-04 JST / 主査(Claude) / 照会: 現状確認の依頼（App 側主査 → RC Master Data Research / 2026-10-04）

## 0. 実測の範囲
- Research DB (（project ref は伏せた）): 2026-10-04 12:14〜12:24 JST / current_user=postgres / 読み取り専用トランザクション（transaction_read_only=on を毎回確認）/ SELECT 9 本・書込なし（ほか 2 本は構文エラーで不実行）
- Mac Studio のローカル: ~/MyRIG_local（全 25 ファイル）と ~/Desktop/MyRIG（222,123 ファイル・19 GB）を件数・容量・更新日で集計。書込なし
- Google Drive: マイドライブ/MyRIG_Handoff の 1〜2 階層目のフォルダ名だけ
- Supabase 公式資料 3 ページ（2026-10-02 更新版）
- 読んでいないもの: Production DB（非接触）/ Test (r12 probe) / MacBook Pro のローカル / Drive の各フォルダの中身と容量 / Supabase の画面（Disk IO の残量・停止日）/ Time Machine 等 / 表の中の空き領域（測る拡張が未導入）/ audit_backup_20260528 の正確な行数（推定値のみ）

## A. Research DB のサイズと余裕
1. サイズ
- postgres DB 単体: 459,992,211 bytes = 438.68 MB → 500 MB まで残り 61.32 MB
- Supabase 公式の数え方（全 DB の合計。template0/1 を含む）: 475,107,505 bytes = 453.10 MB → 残り 46.90 MB
- 上は 1 MB=1,048,576 bytes で計算。1 MB=1,000,000 bytes なら 475.1 MB・残り 24.9 MB。Supabase がどちらで判定するかは未確認
- スキーマ別: public 402.45 MB（19 表）/ audit_backup_20260528 22.40 MB（31 表）/ pg_catalog 12.16 MB / auth 1.43 MB / storage 0.41 MB
- WAL 320 MB は 500 MB の対象外（ディスク側）

2. 表ごとの上位 15（MB。本体 / 索引 / 合計 / 行数）
| 表 | 本体 | 索引 | 合計 | 行数 |
| public.change_logs | 125.12 | 68.53 | 193.73 | 736,292 |
| public.part_masters | 71.38 | 10.10 | 81.58 | 145,291 |
| public.master_images | 49.94 | 10.86 | 60.84 | 79,938 |
| public.master_external_links | 23.84 | 7.91 | 31.79 | 80,001 |
| public.master_publication | 22.73 | 6.94 | 29.71 | 72,896 |
| audit_backup_20260528.part_masters | 19.00 | 0 | 19.05 | 推定 37,080 |
| public.rig_master_variants | 1.59 | 0.25 | 1.88 | 3,366 |
| audit_backup_20260528.rig_master_variants | 1.34 | 0 | 1.38 | 推定 3,366 |
| public.rig_masters | 0.68 | 0.11 | 0.83 | 1,242 |
| public._backup_scraped_from_20260619 | 0.62 | 0 | 0.66 | 3,532 |
| audit_backup_20260528.rig_masters | 0.52 | 0 | 0.55 | 推定 1,173 |
| public.manufacturers | 0.23 | 0.11 | 0.38 | 863 |
| public.bodies | 0.24 | 0.05 | 0.33 | 674 |
| audit_backup_20260528.master_external_links_pre016f4p2 | 0.26 | 0 | 0.29 | 推定 873 |
| audit_backup_20260528.bodies | 0.25 | 0 | 0.26 | 推定 674 |
- 圏外: import_runs 0.23 MB（318 行）/ source_snapshots 0.04 MB（0 行）
- change_logs だけで全体の 44%

3. 直近で大きく増えた取り込み（8 月。import_runs 37 本・対象 311,873 行）
- 08-07〜08-10 IMAGES-2026-W1〜W5: master_images に 67,063 行
- 08-10 PUBLISH-2026-W1: 一括更新 131,006 行
- 08-05 GATE7-G7 15,289 行 / 08-08 T3-MISSING-2026 12,189 行 / 08-23 RUN-391 11,920 行（最後の書込）
- change_logs は 8 月だけで 442,441 行（全体の 60%）。5 月 49,297 / 6 月 236,540 / 7 月 8,014
- サイズの履歴は残っていない。行数の比で割ると 8 月分は約 190 MB（推定）

4. 読み取り専用か: なっていない（default_transaction_read_only=off）

5. 500 MB にいつ届くか
- 08-23 以降は書込 0。このままなら増えない
- 書込を再開すると届く。日付は不明（次の週次ゲートの中身で決まる）
- 目安（推定）: D2 の索引 12 本で 20〜25 MB。一括更新は変更 1 項目につき change_logs が 1 行（索引込み約 260 bytes）増える。8 月と同じ規模の作業 1 回で超える

## B. バックアップ
1. 全体の書き出し（dump）: 取っていない
- 確認した範囲（Mac Studio の上記 2 フォルダと Drive の名前検索）に pg_dump 形式のファイルは 0 件
- 9/27 に作ったスクリプトと手順書（~/MyRIG_local/RUNS/MYRIG-CC-INFRA-RESEARCHDB-DATA-BACKUP-VERIFY-001/）は未実行。dump/ と manifest/ は空。RUN_STATUS は「オーナーの実行待ち」
- 代わりになるもの: 2026-07-28 16:16 の CSV 書き出し 18 表（Research/_handoff/DB-329_FULL_EXPORT/・154 MB）。表のデータだけで、行数の照合は今回していない
2. 頻度と実行者: 定期のものは無い。上の CSV は監査用の 1 回きり
3. 戻せることの確認: したことがない
4. いま DB が失われた場合
- 戻せる見込み（未検証）: 07-28 時点の 18 表（CSV）＋ 8 月の投入 SQL（GATE7〜24 と RUN-391 がローカルに残っている）を順に流し直す
- 戻せない・作り直しになるもの: 表の定義一式（制約・トリガー・関数・RLS・役割と権限・VIEW 5 本。まとまった書き出しが無い）/ 07-28 以降の change_logs 約 44 万行 / audit_backup_20260528 の 31 表
- Supabase 側: Free はバックアップをダウンロードできない

## C. ローカルのデータ
1. Mac Studio（~/Desktop/MyRIG/Research: 14 GB・176,094 ファイル）
- 取得した HTML 42,490 件・6.7 GB / TSV 8,105 件・1.1 GB / JSON 2,057 件・617 MB / CSV 297 件・199 MB / JSONL 126 件・135 MB / xlsx 390 件・80 MB / SQL 682 件・50 MB
- 場所: _archive 6.2 GB / _staging 3.0 GB / .git 2.1 GB / _apps 1.8 GB / _handoff 540 MB
- 08-24 以降に更新されたファイルは 9 件だけ。作業は MacBook Pro と Drive に移っている
- Drive（MyRIG_Handoff/outbox/MyRIG_MBP）: 1 階層目にフォルダ 107・ファイル 4（目視で集計）。作業 RUN 001〜072 と、夜間収集 RUN-MBP-20260823〜20261003。中身と容量は未読
- MacBook Pro のローカル: 今回は届かない。記録では RUN-381〜390 の原本が /Users/pb/Desktop/ にある
2. 正本: Master は Supabase の Research DB
- ローカル・Drive にしか無いもの: 取得した HTML、候補の台帳、08-23 以降の成果物の全部（DB 未反映）
- DB にしか無いもの: B-4 の「戻せないもの」と、07-28 以降の各表の最新状態
3. ローカルのバックアップ
- Time Machine・外付け: 不明
- GitHub（MyRIGRC/myrig-research）: 最後に届いているのは 2026-05-29 のコミット。手元は 235 コミット先行、未コミットの変更 692 件。HTML などの大きいファイルは対象外の設定

## D. 「MyRIG Production DB」
1. 用途: 公開用の DB として 2026-05-23 に作成。Research レーンは使っていない（憲章で接触禁止）。記録で確認できた接触は 2026-05-24 の試験投入まで
2. 中身（2026-05-24 の報告書。現在の中身は未確認）: Axial 1 社ぶんの写し 2,444 行（manufacturers 1 / bodies 52 / rig_masters 28 / rig_master_variants 94 / part_masters 661 / master_aliases 92 / master_publication 836 / master_external_links 680）。Research の進捗表では 258 社すべて「未同期」
3. Research への影響: 無い

## E. Research の進み具合
1. 段階と直近 1 か月
- DB への書込は 2026-08-23 が最後（書込停止を継続中）
- 9/5〜9/29: MacBook Pro で主要メーカーの根拠集め（RUN-020〜072。フォルダ名からの把握）。成果物は Drive、DB 未反映
- 夜間の自動収集は 10-03 分まで出力あり
- 9/17 App 向け RIG / PARTS の受け渡し仕様 / 9/27 Disk IO 警告への対応（バックアップ手順の作成まで）/ 10/02 照会 #3・#3-2・#3-3 に回答
2. 行数: 回答 #3 の 12 表はすべて変化なし。ほかは change_logs 736,292 / import_runs 318 / manufacturer_research_progress 258 / master_relations 0 / master_field_verifications 0 / source_snapshots 0
3. 次の週次ゲート: 日付は未定
- 載せるもの: D1〜D8 / RIG 向け取扱店リンクの議題 / 宿題 3 件（retailer_product 1,896 行・retailer_official 254 行・source_type の語彙）
- 9/27 からの持ち越し: 旧 anon キーの無効化 / anon が実行できる関数 5 本の確認 / RLS 無効 32 表 / 取り込み時の重い照合クエリの改修
4. 止まっていること・判断待ち
- バックアップの実行（9/29 以降の予定が未実行）
- バックアップ後に Research DB を一時停止する計画（9/27）と、App が Research DB から差分を取る設計（10/02）がぶつかっている
- import_runs に「実行中」のまま 3 件・「失敗」1 件（6 月と 8 月。台帳の整理が要る）

## F. Research から見た心配ごと
- 検証済みのバックアップが無いまま、Free にはバックアップ機能も無い
- 残り 46.9 MB。D2 と一括更新を流す前に、容量の方針が要る。物理 DELETE 禁止のため、減らす手段は「change_logs と audit_backup をローカルへ移して外す（規則の例外）」か「Pro」
- 公式資料では、利用制限（402）の判定は組織内の全プロジェクトの DB サイズの合計・請求期間の日次平均。Research 453 MB に Test (r12 probe) が足される。Test のサイズは App 側で測ってください
- 動かせるのは 2 つまで。Research と Test が動いている間、Production は戻せない
- 訂正: 停止したプロジェクトを画面から戻せる期間は、現行の公式資料では 1 年（90 日は旧記載）
- Free は 7 日間の利用が少ないと自動で停止の対象になる（約 1 週間前に警告メール）
- Disk IO: 全件の読み出し（バックアップ・App の初回同期）は読み書き枠を大きく使う。初回同期は小分けにしてください
- 旧 anon キー宛ての外部アクセス（9/27 時点で 1 日 279 件・全て 404）は未対処

## イタヤに決めてほしいこと
- バックアップをいつ実行するか（Mac Studio で OWNER_RUNBOOK_001。最優先。Research の推奨は次のどの作業よりも先）
- Free のまま続けるか、Pro にするか（公式の例で月 $25〜）。Free のままなら、change_logs と audit_backup をローカルへ移して DB から外すことを認めるか
- Research DB の一時停止（9/27 の計画）を取りやめるか。App の同期を前提にするなら取りやめになる
- Test (r12 probe) をいつ止めるか（App 側）
- 次の週次ゲートの日

【作業完了】DBR-STATUS-CHECK-20261004-REPLY

---

## 報告 ②（GPT / Codex の Research 担当）

## 実測の範囲

2026-10-04 12:21〜12:29 JSTに、CodexのResearch担当として確認しました。対象はSupabaseのResearch DB `（project ref は伏せた）` とMacBook Pro上の `Desktop/MyRIG/Research`、そこにある2026-07-28付のCSV書き出しです。

DBでは表定義・行数表示・サイズ情報を読み、**SQLはSELECTを2回だけ実行しました。書き込みはしていません。** ただし、接続先で確認できた `transaction_read_only` と `default_transaction_read_only` はどちらも `off` でした。したがって「SELECTのみ」は守りましたが、依頼にあった「読み取り専用トランザクション」で実行したとは確認できません。この点を明記し、追加のDB照会は止めています。

Production、Mac Studioの `~/MyRIG_local`、Time Machineや外付け媒体は確認していません。過去の説明ではなく、今回読み取れた事実と、まだ確認できない点を分けて記します。

## A. Research DB のサイズと余裕

### 1. 全体サイズ

DBサイズは **459,992,211 bytes** でした。10進表記で約 **460.0 MB**、MiB表記で約 **438.7 MiB** です。

Supabaseの公式説明ではFreeプランはDBサイズが500 MBを超えると読み取り専用になります。ただし公式資料はこの測定値をどのバイト単位で比較するかまでは明示していません。500,000,000 bytesを基準にすれば残り約 **40.0 MB**、500 MiBを基準にすれば約 **61.3 MiB** です。限界に近い状態です。[Supabase: Database and Disk Size](https://supabase.com/docs/guides/platform/database-size)

### 2. 表サイズ上位

Postgresの表・索引サイズ関数で計測しました。単位はMiBです。`table`はTOAST等を含む表領域、`index`は索引、`total`は合計です。指定の小さい表も上位15件に追加してあります。

| 表 | table | index | total |
|---|---:|---:|---:|
| `public.change_logs` | 125.2 | 68.5 | **193.6** |
| `public.part_masters` | 71.5 | 10.1 | **81.6** |
| `public.master_images` | 50.0 | 10.9 | **60.8** |
| `public.master_external_links` | 23.9 | 7.9 | **31.8** |
| `public.master_publication` | 22.8 | 6.9 | **29.7** |
| `audit_backup_20260528.part_masters` | 19.1 | 0 | **19.1** |
| `public.rig_master_variants` | 1.6 | 0.3 | **1.9** |
| `audit_backup_20260528.rig_master_variants` | 1.4 | 0 | **1.4** |
| `public.rig_masters` | 0.7 | 0.1 | **0.8** |
| `public._backup_scraped_from_20260619` | 0.7 | 0 | **0.7** |
| `audit_backup_20260528.rig_masters` | 0.6 | 0 | **0.6** |
| `public.manufacturers` | 0.3 | 0.1 | **0.4** |
| `public.bodies` | 0.3 | 0.1 | **0.3** |
| `audit_backup_20260528.master_external_links_pre016f4p2` | 0.3 | 0 | **0.3** |
| `audit_backup_20260528.bodies` | 0.3 | 0 | **0.3** |
| `public.import_runs` | 0.1 | 0.1 | **0.2** |
| `public.source_snapshots` | 0.01 | 0.03 | **0.04** |

`change_logs`だけで全DBサイズのおよそ44%を占めます。`source_snapshots`の現在行数表示は0でした。

### 3. 直近の増加

`import_runs`で確認できた最後の記録は、**2026-08-23の `RUN-391-SQLPREP`、11,920行影響**です。9月4日以降の記録はありません。最大の `rows_affected` は8月10日の `PUBLISH-2026-W1` で **131,006行**でした。ただし、この数字からDBサイズの増加量を計算することはできません。

比較用の2026-07-28 CSV書き出しと今回の表一覧を比べると、表示上は `change_logs` が約44万行、`master_images` が約6.7万行、`part_masters` が約4.0万行増えています。一方、現在の表一覧の行数は全件走査の `COUNT(*)` ではなく、CSVとの比較も完全に同じ計測方法とは限らないため、差分は参考値です。

### 4. 読み取り専用状態

プロジェクト状態は `ACTIVE_HEALTHY`。今回のSQLセッションでは `default_transaction_read_only=off`、`transaction_read_only=off` でした。**今回送ったSQLはSELECTだけで、書き込みはしていません。** ただし、DBが読み取り専用モードに入っている状態ではありません。

### 5. 500 MB到達時期

**不明です。** 8月23日以降の取込記録はありませんが、過去のDBサイズ測定が同じ条件で揃っておらず、将来の増加ペースを推定できません。

## B. バックアップ

### 1〜3. 書き出し・頻度・復元確認

MacBook ProのResearch作業領域で確認できた最も明確なデータ書き出しは、`_handoff/DB-329_FULL_EXPORT.zip` です。

- 日付：**2026-07-28**
- 圧縮サイズ：約 **29 MB**
- ZIP内：18表のCSV、合計約 **161 MB**
- 形式：CSVのデータ書き出し。`pg_dump`形式のDB全体バックアップではありません。
- 自動／手動の頻度と実行者、復元テスト：**このファイルからは不明**。復元確認の記録も見つかりませんでした。

ワークスペース内には、このCSV以外の現行DB全体ダンプ（`.dump`、`.backup`等）は見つかりませんでした。9月27日時点でバックアップ検証用のインフラRUNはowner実行待ちでしたが、その後Mac Studioで実行されたかは、今回は確認していません。`~/MyRIG_local`には触れていません。

Supabase公式資料では、日次バックアップはPro以上が対象で、Free利用者には定期的なCLI書き出しと別保管を勧めています。また、FreeプロジェクトのバックアップはDashboardからダウンロードできない旨の説明もあります。[Supabase: Database Backups](https://supabase.com/docs/guides/platform/backups), [Production Checklist](https://supabase.com/docs/guides/deployment/going-into-prod)

### 4. 今失われた場合

7月28日のCSVに含まれるデータは、その時点の18表分まで復元素材になり得ます。ただし、表定義、関数、トリガー、権限、Storageの実ファイルなどを含むDB全体の復元手段ではありません。7月28日以降のDB状態を戻せる既知の完全バックアップや、復元可能性を検証した記録は**今回の確認範囲では見つかりませんでした**。インフラRUN成果物やMac Studio上にある可能性は未確認です。

## C. ローカルのデータ

### 1. 種類・場所・量

`/Users/itayahirotomo/Desktop/MyRIG/Research` は約 **14 GB** でした。主な内訳は `_archive` 約6.1 GB、`_staging` 約2.9 GB、`_apps` 約1.7 GB、`_handoff` 約539 MB、`_tools` 約82 MB、`_Master` 約29 MBです。

中にはステージング資料、TSV/CSV/JSON、収集・抽出済みページやWayback関連データ、アプリコード、過去RUNの成果物・アーカイブがあります。ファイル拡張子だけではHTMLすべてが研究証拠とは分類できず、正確な生データ件数は不明です。

### 2. 正本と差分

運用方針としては、**Research DBを最終正本、ローカルを日常の照合・作業層**とする意向が引き継がれています。一方、RUN-069〜072の私的ステージング候補や証拠資料はDBへ反映されておらず、RUN-072時点でDB照合待ちが1,241行ありました。ローカル側にDB未反映の研究成果があることは確かです。Mac Studioのローカル復元DBは確認していません。

### 3. ローカルのバックアップ

Time Machine、外付けディスク、別クラウドへのバックアップ状態は**不明**です。Google DriveのMyRIG_HandoffにRUN成果物が同期されていることは確認できますが、これはMac全体やResearchフォルダ全体のバックアップ確認にはなりません。

## D. 「MyRIG Production DB」について

Productionプロジェクトには接続も管理画面照会もしていません。依頼文の前提では停止中ですが、用途・DB内データ・停止日・復元期限は今回の独立確認では**不明**です。

ResearchレーンのRUN-069〜072はResearch DBまたはオフライン作業を対象としており、Productionには触れていません。したがって、**今回確認したResearch作業に停止の影響はありません**。他のアプリや運用への影響は判断できません。前回までのApp側調査では現行モックは静的サイトでSupabase接続コードなしとされていましたが、それだけでProduction DBの用途や内容は確定できません。

## E. Research の進み具合

### 1. 段階と直近1か月

現在は、候補の調査・証拠整理・private staging受け渡しの段階です。9月末のRUN-069〜072で、243件の契約検証済み候補を整理し、識別子54件の根拠を候補単位で補完しました。RUN-071では商品本文の確認不足を修正し、RUN-072ではtier C候補39件も再分類しました。RUN-072終了時点でも低優先のtier Cが558件、DB照合待ちが1,241行、owner判断待ちが残っています。これらのRUN報告ではDB接続0回でした。**今回がCodex側での初回DB実測です。**

### 2. 主な表の行数

以下はSupabaseの表一覧が返した数と、7月28日CSVの行数との差です。今回 `COUNT(*)` は実行していないため、現在数と差分は参考値です。

| 表 | 7/28 CSV | 今回の表一覧 | 表示差分 |
|---|---:|---:|---:|
| `change_logs` | 293,565 | 736,292 | +442,727 |
| `part_masters` | 105,019 | 145,291 | +40,272 |
| `master_images` | 12,875 | 79,938 | +67,063 |
| `master_external_links` | 72,187 | 80,001 | +7,814 |
| `master_publication` | 42,308 | 72,896 | +30,588 |
| `import_runs` | 279 | 318 | +39 |
| `manufacturers` | 931 | 863 | −68 |
| `rig_masters` | 1,242 | 1,242 | 0 |
| `rig_master_variants` | 3,366 | 3,366 | 0 |
| `bodies` | 674 | 674 | 0 |

### 3. 次の週次ゲート

**予定日は確認できませんでした。** RUN-072のWEEKLY_GATE_WAITには、候補243件のmanufacturer ID・RIG衝突・UUID・parts関係・公開行照合、カテゴリslug照合26件、RUN-056の生HTML25件、RLS Security HOLD、未投入分総台帳が記載されています。総台帳は未着手です。D1〜D8、RIG向け取扱店リンク、宿題3件の具体的な議題・日程は、今回読めた資料からは特定できませんでした。

### 4. 停止中・判断待ち

TFLの公式性、Yikong/HSPの調査方法、重複・バリエーション規則、カテゴリ範囲、WLToysの同一性、Absimaのブランド表示規則と保存ページの扱いが未決です。DB照合はインフラRUNのバックアップ検証とローカルDB利用が前提とされています。

## F. Research から見た心配ごと

FreeプランのDBサイズ上限は500 MBで、今回の実測値は上限にかなり近い状態でした。Freeプロジェクトは7日程度の低活動で自動Pause対象になり得ます。停止後にSupabase Studioから復帰できる期間は、現在の公式説明では**90日ではなく1年**です。[Project Pausing](https://supabase.com/docs/guides/platform/free-project-pausing)  
また、Freeプランのアクティブプロジェクト上限は2つで、Pause中のプロジェクトは数に含まれません。[Supabase Billing](https://supabase.com/docs/guides/platform/billing-on-supabase) 依頼文の「ResearchとTestが稼働、Productionが停止中」という前提が現在も正しければ、Free枠はすでに上限です。

今回の表一覧で `public._backup_scraped_from_20260619` は **3,532行、RLS無効**と確認されました。これは既知のSecurity HOLDです。RLSが無効でも実際にどのDBロールへ権限が付いているかは今回確認していません。既存記録ではanon権限なし、authenticatedは未確認でした。変更や権限確認は行っていません。

**イタヤに決めてほしいこと**

- 9月27日時点でowner実行待ちだったバックアップ検証を、その後実行したか。実行済みなら監査対象の成果物の場所。
- 500 MB上限に近いDBの今後の扱いと、次回の週次ゲート日程。
- D1〜D8・取扱店リンク・宿題3件の最新議題と、未決のowner判断項目の優先順位。
