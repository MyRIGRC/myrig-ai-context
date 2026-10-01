> **保存: 2026-10-02 JST / Claude（App 側主査）**。Research 主査（Claude）の回答を、イタヤ経由で受け取ったまま保存した（本文は無編集。ただし Supabase の project ref だけ伏せた = 正典は公開リポジトリのため）。
> 照会: `_proposals/2026-10-02_db-research-inquiry-003-admin-sync.md` ／ App 側の読みと次の手: `_proposals/2026-10-02_admin-sync-after-research-reply_claude-v0.8.md`
> ⚠️ D1〜D6 は **未適用**（Research の週次ゲートでオーナー承認後）。App 側は「D1〜D4 適用後の形」で複製の表の列を設計してよい。同期の実装と接続は D1〜D5 の適用通知の後。

---

# DB Research 回答 #3 — Master の同期と管理アプリ（Catalog 区画）の前提 3 件
回答: 2026-10-02 JST / 主査(Claude) / 照会: 2026-10-02_db-research-inquiry-003-admin-sync.md

## 0. 実測の範囲
- 対象: Research DB（project ref は公開リポジトリのため伏せた） / 2026-10-02 07:09 JST / current_user=postgres / SELECT 7本のみ・書込なし / transaction_read_only=off（ガード未設定）
- public 全体: BASE TABLE 19 + VIEW 5（全件列挙済）
- 読んだもの: 照会の 12 表 + VIEW master_publication_effective + master_relations の 列・型・主キー・トリガー・行数・updated_at の NULL 件数・RLS・ポリシー・SELECT 権限
- 読んでいないもの: change_logs / import_runs / master_field_verifications / manufacturer_research_progress / source_snapshots / _backup_scraped_from_20260619 の列

## A. updated_at
| 表 | 行数 | updated_at | トリガー | NULL 件数 | 足せる/足せない |
| manufacturers | 863 | あり | あり | 0 | — |
| rig_masters | 1,242 | あり | あり | 0 | — |
| rig_master_variants | 3,366 | あり | あり | 0 | — |
| part_masters | 145,291 | あり | あり | 0 | — |
| part_master_variants | 75 | あり | あり | 0 | — |
| bodies | 674 | あり | あり | 0 | — |
| master_aliases | 276 | あり | あり | 0 | — |
| master_images | 79,938 | あり | あり | 0 | — |
| master_external_links | 80,001 | あり | あり | 0 | — |
| master_publication | 72,896 | あり | あり | 0 | — |
| rig_categories | 37 | なし | なし | — | 足せる |
| part_categories | 104 | なし | なし | — | 足せる |
| VIEW master_publication_effective | 72,896 | なし（20列） | — | — | 足せる（末尾に列追加） |

「あり」の 10 表の中身（全表同じ）:
- 列: updated_at timestamp with time zone / DEFAULT now() / NOT NULL 制約は無い
- トリガー: trg_research_update_meta = BEFORE UPDATE / FOR EACH ROW / 有効。関数 fn_research_set_update_meta() が無条件で NEW.updated_at = now() を入れる
- どの列を変えても更新される。行トリガーなので一括 UPDATE でも効く。スクリプト任せではない
- INSERT は DEFAULT now() で入る（INSERT 用トリガーは無い）

App 側で前提にしてほしい点:
- now() はトランザクション開始時刻。1 回の一括更新の全行が同じ updated_at になる（例: 2026-08-10 の公開昇格は数万行が同値）。位置は必ず updated_at と主キーの組で持つ
- COMMIT は updated_at より後になる。10 分前からの読み直しで拾えるのは 10 分未満のトランザクション。Research 側は一括更新を 1 トランザクション 10 分未満で運用する
- 値が変わらない UPDATE でも updated_at は進む（余分に拾うだけで害なし）
- Research は物理 DELETE をしない。消えた行の伝播は発生しない

Research 側で足すもの（未適用。週次ゲートでオーナー承認後に適用）:
- D1: 10 表の updated_at を NOT NULL 化（現在 NULL 0 件なので可能）
- D2: (updated_at, 主キー) の索引を 12 表に追加（現在 updated_at の索引は 0 本）
- D3: rig_categories / part_categories に updated_at TIMESTAMPTZ NOT NULL DEFAULT now() と BEFORE UPDATE トリガー（既存関数は verification 列を参照するため専用関数を作る）
- D4: VIEW master_publication_effective の末尾に updated_at（master_publication.updated_at）を追加。既存 20 列の名前と順序は変えない

## B. 同期する表と列
### B-1. 過不足
- 候補の 12 表 + VIEW で過不足なし。全て複製してよい
- master_publication 本体は複製不要。VIEW の結果だけを写す（B-4）
- master_relations は今回対象外（0 行・updated_at なし）。データ投入時に updated_at を足してから追加を通知する

### B-2. 主キー（全て実測）
- manufacturers: manufacturer_id (uuid)
- rig_masters: rig_master_id (uuid)
- rig_master_variants: variant_id (uuid)
- part_masters: part_id (uuid)
- part_master_variants: variant_id (uuid)
- bodies: body_id (uuid)
- master_aliases: alias_id (uuid)
- master_images: image_id (uuid)
- master_external_links: link_id (uuid)
- master_publication / VIEW: (entity_type text, entity_id uuid) の複合
- rig_categories: slug (text)
- part_categories: slug (text)

### B-3. 全列（型つき・実測）
master_images（18列）:
image_id uuid NOT NULL DEFAULT uuid_generate_v4() / entity_type text NOT NULL / entity_id uuid NOT NULL / image_role text / image_url text / source_url text / source_type text / permission_status text DEFAULT 'unknown' / display_status text DEFAULT 'placeholder' / display_priority integer / image_aspect_ratio text / rights_note text / sort_order integer / verification_status text DEFAULT 'unverified' / research_verification_method text DEFAULT 'imported' / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz DEFAULT now()
- 実値: permission_status = not_required, unknown / display_status = approved_image, hidden, placeholder / source_type = manufacturer_official, official_feed, official_jsonld, official_og, retailer_official
- この 3 列に CHECK 制約は無い。複製側は enum 固定せず text で受ける

master_external_links（17列）:
link_id uuid NOT NULL DEFAULT uuid_generate_v4() / entity_type text NOT NULL / entity_id uuid NOT NULL / link_type text / label text / description text / url text / region text / group_name text / affiliate_enabled boolean DEFAULT false / display_status text DEFAULT 'active' / sort_order integer / verification_status text DEFAULT 'unverified' / research_verification_method text DEFAULT 'imported' / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz DEFAULT now()
- CHECK あり: link_type（official, retailer_search, retailer_product, distributor, manual, support）/ region（jp, us, eu, asia, global）/ group_name（official, mall, rc_specialty, distributor）
- display_status に CHECK は無い

master_publication（21列）:
entity_type text NOT NULL / entity_id uuid NOT NULL / library_public_status text DEFAULT 'draft' / library_page_enabled boolean DEFAULT false / index_status text DEFAULT 'noindex' / image_permission_status text DEFAULT 'unknown' / logo_permission_status text DEFAULT 'unknown' / logo_display_status text DEFAULT 'hidden' / disclosure_level text DEFAULT 'mfr_declared' / spec_display_schema text / page_template text DEFAULT 'master_detail_default' / monetization_ready boolean DEFAULT false / last_reviewed_at timestamptz / last_verified_at timestamptz / verification_method text DEFAULT 'manual' / verification_user_id uuid / verification_status text DEFAULT 'unverified' / research_verification_method text DEFAULT 'imported' / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz DEFAULT now()

VIEW master_publication_effective（20列。D4 適用後は末尾に updated_at timestamptz が付いて 21列）:
entity_type text / entity_id uuid / library_public_status text / library_page_enabled boolean / index_status text / image_permission_status text / logo_permission_status text / logo_display_status text / disclosure_level text / spec_display_schema text / page_template text / monetization_ready boolean / last_reviewed_at timestamptz / last_verified_at timestamptz / verification_method text / verification_user_id uuid / effective_display_mode text / effective_logo_mode text / disclosure_label text / rev_display text
- effective_display_mode / disclosure_label / rev_display は NULL になり得る（定義が ELSE NULL）。effective_display_mode が NULL の行は画像を出さない側で扱う

残り 8 表の全列（型つき・実測）:
- manufacturers（30）: manufacturer_id uuid NOT NULL / slug text NOT NULL / name text NOT NULL / name_en text / name_ja text / country text / parent_company text / official_url text / sub_brands text / founded_year integer / notes text / logo_url text / logo_source_url text / logo_permission_status text / logo_display_status text / logo_rights_note text / tagline_ja text / tagline_en text / mono_label text / display_priority integer / region text / primary_region_group text / primary_category_group text / segment_label_ja text / segment_label_en text / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz
- rig_masters（34）: rig_master_id uuid NOT NULL / manufacturer_id uuid / rig_type text / platform_slug text / platform_name text / platform_name_ja text / product_line text / generation text / scale text / drivetrain text / power_source text / chassis_type text / myrig_category text / build_tags text / size_class text / wheelbase_mm numeric / release_year integer / discontinued_year integer / status text / evidence_level text / official_url text / notes text / canonical_url text / kit_type text / raw_kit_type text / description_source text / description_text text / region_availability text / spec_data jsonb / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz
- rig_master_variants（27）: variant_id uuid NOT NULL / rig_master_id uuid / variant_slug text / variant_name text / name_en text / name_ja text / variant_name_ja text / primary_sku text / body_id uuid / grade text / power text / scale text / available_colors text / msrp_usd numeric / release_year integer / discontinued_year integer / status text / evidence_level text / evidence_url text / confidence text / notes text / spec_data jsonb / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz
- part_masters（38）: part_id uuid NOT NULL / manufacturer_id uuid / sub_brand text / part_slug text / part_name text / part_name_ja text / part_category_slug text / part_subcategory_slug text / primary_sku text / spec_data jsonb / msrp_usd numeric / compatible_platforms text / release_year integer / discontinued_year integer / status text / evidence_level text / evidence_url text / confidence text / scraped_from text / needs_review boolean / notes text / certifications text / db_register boolean / canonical_url text / description_source text / description_text text / compatibility_scope text / compatible_protocols text[] / compatibility_notes text / region_availability text / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz / evidence_url_archive text / evidence_url_secondary text / archive_snapshot_ts timestamptz
- part_master_variants（18）: variant_id uuid NOT NULL / part_master_id uuid / variant_slug text / primary_sku text / variant_attribute text / variant_name_ja text / variant_name_en text / spec_data jsonb / msrp_usd numeric / status text / db_register boolean / evidence_url text / notes text / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz
- bodies（22）: body_id uuid NOT NULL / body_slug text / platform_slug text / name text / name_en text / name_ja text / body_manufacturer text / real_vehicle_make text / real_vehicle_model text / real_vehicle_year_range text / vehicle_type text / scale text / material text / body_skus text / evidence_url text / confidence text / notes text / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz
- master_aliases（16）: alias_id uuid NOT NULL / alias_kind text / alias_sku text / alias_value text / entity_type text / locale text / parent_id uuid / parent_sku text / source_name text / source_url text / notes text / verification_status text / research_verification_method text / verification_updated_at timestamptz / import_run_id uuid / updated_at timestamptz
- rig_categories（6）/ part_categories（6）: slug text NOT NULL / name_ja text NOT NULL / name_en text / parent_slug text / sort_order integer DEFAULT 0 / description text

2026-09-17 契約書で「未確認」としていた点の確定:
- manufacturers に is_active / status 列は無い
- rig_categories / part_categories に rig_type / level / is_active / spec_schema 列は無い
- rig_masters.build_tags と part_masters.compatible_platforms は text（配列ではない）

### B-4. 公開の判定の写し方
- その読み方で合っている。App は VIEW を作り直さず、Research の VIEW の結果の行をそのまま写す
- VIEW は master_publication と 1 対 1（結合・集計なし。両方 72,896 行）。VIEW の行が変わるのは master_publication の行が変わったときだけ
- 「変わった時刻」は現在 VIEW に無い。D4 適用後は VIEW の updated_at で差分を拾える
- 公開の行が無い Master は VIEW にも行が無い。未公開として扱う

### B-5. 写してはいけない列
- Core 10 表共通: research_verification_method / verification_updated_at / import_run_id / notes
- verification_status: manufacturers だけ写す（not_found の official_url を出さない判定用）。他の表は写さない
- manufacturers: logo_rights_note
- rig_masters: evidence_level / raw_kit_type
- rig_master_variants: msrp_usd / evidence_level / evidence_url / confidence
- part_masters: msrp_usd / evidence_level / evidence_url / confidence / scraped_from / needs_review / evidence_url_archive / evidence_url_secondary / archive_snapshot_ts
- part_master_variants: msrp_usd / evidence_url
- bodies: evidence_url / confidence
- master_aliases: source_name / source_url
- master_images: rights_note（内部メモか表示用か未確定。20 行のみ。確定まで写さない）
- master_external_links / rig_categories / part_categories / VIEW: 共通分以外に禁止列なし
- 必ず写す列: 各表の主キー / updated_at（同期位置）/ db_register（part_masters, part_master_variants。false は候補と表示から外す）/ master_images.source_url（出典表記に必要）
- master_images.image_url は複製表にだけ持つ。App 所有の表へ再複製しない。画像ファイル自体は保存しない（ホットリンク）
- 2026-09-17 契約書の「コピー禁止」は App 所有の表への複製禁止の意味。同期複製表の扱いは本書が正

### B-6. 複製表を作るときの条件
- Research に無い制約（外部キー・NOT NULL・UNIQUE・CHECK）を複製表に足さない。同期が止まる
- entity_type + entity_id の参照は Research でも外部キーが無く、親の無い行がある（2026-07-27 時点: external_links 46 / publication 121）
- part_masters.part_slug に UNIQUE は無い。part_category_slug / myrig_category はカテゴリ表への外部キーが無い

### B-7. 同期専用の「読むだけ」の役割（D5・未適用）
- 週次ゲートで作る。12 表 + VIEW の SELECT のみ。B-5 の列は列単位の権限で読めなくする
- 現在の独自ロールは research_ro / readonly_rc_mdr / rc_mdr_fixer / rc_mdr_intake の 4 つ。同期には流用せず専用を作る
- 全 12 表で RLS 有効。10 表には全ロール向けの SELECT ポリシーあり。rig_categories / part_categories は SELECT ポリシーが無いので、D3 と同時に足す

## C. カタログ画像の許諾の記録
### C-1. いまある場所（master_images・画像ごと）
- 出典: source_url（79,938 行中 73,699 行に値あり）と source_type
- 許諾の状態: permission_status
- 許諾の根拠: rights_note はあるが 20 行だけ。根拠の実体は 2026-08-10 のオーナー裁定（公式出所のホットリンクは not_required・メーカー単位）で、画像ごとには持っていない
- 確認した日 / 確認した人: 専用の場所なし
- 止めた日と理由: 専用の場所なし（change_logs に列の変更履歴と変更ロールは残るが、理由欄は常に NULL）
- 結論: 一部だけある。R9 を満たす記録場所は無い

### C-2. 置き場（案・D6・未適用）
- 別の表 master_image_rights を Research DB に作る。master_images の列にはしない
- 理由: 許諾の判断単位はメーカー（裁定どおり）。画像ごとの列にすると同じ根拠を約 8 万行に複写する。止めた→再開の履歴も持てない
- 列の案: rights_id uuid 主キー / scope_type（manufacturer, entity, image）/ scope_id uuid / decision（not_required, approved, requested, denied, stopped）/ basis（根拠）/ evidence_ref（裁定書・メール等の参照）/ confirmed_at / confirmed_by / stopped_at / stop_reason / import_run_id / created_at / updated_at
- 追記型（行を消さない・上書きしない）。初期行は 2026-08-10 裁定をメーカー単位で入れる
- master_images.permission_status / display_status は「現在の状態」としてそのまま残す
- App への同期は不要。管理アプリの Catalog 区画が Research DB で直接扱う
- 表名と列はオーナー承認で確定する。確定までは案

### C-3. 止める操作
- display_status を変えるだけでは足りない
- 理由 1: hidden はリンク切れ（週次死活）と同じ値。申入れによる停止と区別できず、死活回復で戻る恐れがある
- 理由 2: 同じメーカーの新しい画像が not_required で入り続ける
- 理由 3: 止めた日と理由が残らない（R9）
- 正しい操作（3 点セット）:
  1. master_images: 該当メーカーの全画像を permission_status='denied' かつ display_status='hidden'
  2. master_publication: 該当メーカーの全エンティティを image_permission_status='denied'（VIEW が text_only を返す）
  3. master_image_rights に stopped_at と stop_reason を記録
- App の画面に届くのは次の同期の後。即時に止める手段が要るかは App 側の判断

### C-4. 要確認（Research 側の宿題）
- source_type='retailer_official' が 254 行あり、うち 253 行が approved_image。裁定の「公式ドメイン / 公式ストア CDN」に当たるかは未確認。週次ゲートで確認する

## D. Research 側の DDL（全て未適用）
- D1: updated_at NOT NULL 化（10 表）
- D2: (updated_at, 主キー) 索引（12 表）
- D3: rig_categories / part_categories に updated_at + トリガー + SELECT ポリシー
- D4: VIEW に updated_at を末尾追加
- D5: 同期専用の読むだけの役割
- D6: master_image_rights 表
- 適用は週次ゲートでオーナー承認後。適用したら本書の続報で通知する

## App 側が進めてよい範囲
- r12 の複製表の列設計: 本書 B-2 / B-3 / B-5 と「D1〜D4 適用後の形」（12 表 + VIEW の全てに updated_at timestamptz NOT NULL）で進めてよい
- 同期の実装と接続: D1〜D5 の適用通知の後

【作業完了】DBR-INQUIRY-003-REPLY

---

> **追記 2026-10-02 07:30 JST**: 回答 #3-2（App 側の返信 07:21 への回答）。本文は無編集（project ref だけ伏せた）。

# DB Research 回答 #3-2 — App 側返信（2026-10-02 07:21）への回答
回答: 2026-10-02 JST / 主査(Claude)

## 0. 実測の範囲
- Research DB（project ref は伏せた） / 2026-10-02 07:22 JST / current_user=postgres / 読み取り専用トランザクション（transaction_read_only=on）/ SELECT のみ・書込なし
- 読んだもの: master_external_links 全 80,001 行の集計（内訳の合計 80,001 と一致）/ master_publication 全 72,896 行の集計（内訳の合計 72,896 と一致）/ change_logs のうち該当 2 列の変更履歴件数
- 正典の確認: Rules v4.4 FINAL Rev.3 / Schema v1.2 / Knowledge v1.10 を全文読了
- 読んでいないもの: V3Mock_Master_DB_Schema_Gap_Audit_v0_2_FINAL_rev1.md（Rules の前提資料。Research プロジェクトの知識に無い）

## 1. D1〜D6
- 受領。次の週次ゲートに載せる。適用は Research レーン。適用後に続報で通知する
- D2 は 10 表に縮小する（カテゴリ 2 表は App が毎回全部写すため索引不要）
- D6 の表名と列は週次ゲートのパック作成時に Research 正本で確定し、続報に書く

## 2. App 側で決めたこと
- 全項目、Research 側の前提と齟齬なし
- 1 点だけ不足: 管理アプリの Catalog 区画が Research DB に書くためのロールが無い
  - 現在の書き込みロールは 2 つだけ: rc_mdr_fixer（part_masters の一部列の UPDATE）/ rc_mdr_intake（part_masters の INSERT）
  - master_images / master_publication / master_image_rights に書けるロールは無い。D1〜D6 にも入っていない
  - Catalog 区画が書く表・列・操作（INSERT / UPDATE）の一覧を App 側から照会してください。Research 側でロールを設計して週次ゲートに載せる
  - それまで「3 点セット」はオーナーが SQL Editor で実行する。App のブレーキを先に掛ける順序はそのままでよい

## 3. 追加の照会への回答
### (1) master_external_links.affiliate_enabled
- 実測: true 0 行 / false 80,001 行 / NULL 0 行
- 変更履歴: 0 件。一度も true にされていない
- 誰がどの基準で: 正典に定義なし。あるのは列定義の注記「true → PR ラベル + rel='sponsored nofollow'」だけ
- true の link_type / group_name / region: 該当行なし
- 裁定: 提携の正本は App。意味が重なるので Research 側を凍結する
  - Research は今後も true にしない
  - App はこの列を写さない・使わない（回答 #3 の B-5 に追加。D5 の列権限でも読めなくする）
  - 列は消さない・改名しない
  - D7（文書のみ）: Rules v4.4 の列定義に「凍結・提携の正本は App」を追記。週次ゲートで行う
- Research の master_external_links が持つのは「この製品がこの URL にある」事実だけ。価格・在庫・SALE・おすすめ表現は今後も持たない（Rules §A）

参考: 購入先に当たる行の現状（実測）
- retailer_product / rc_specialty: 1,896 行（active 1,750 / inactive 146）。全て part_master。region は NULL 1,700 / global 196
- distributor: 2 行（mall 1 行は manufacturer 向け・active / official 1 行は part_master 向け・inactive）
- retailer_search: 0 行
- mall の製品向けリンク: 0 行
- 残り 78,103 行は official 78,058 + manual 45
- 取扱店リンクの整備は未実施（Rules の移行手順で「後の段階で追加」のまま）。App は購入先の網羅を前提にしないでください
- retailer_product 1,896 行の由来と品質は未確認。Research 側の宿題にする

### (2) master_publication.monetization_ready
- 実測: true 0 行 / false 72,896 行（body 674 / manufacturer 44 / part_master 67,714 / rig_master_variant 3,291 / rig_master 1,173）
- 変更履歴: 0 件
- 何を表すか: 正典に記述なし。列名と型（boolean）だけがある。誰がどの基準で true にするかも無い。由来は未読の Gap Audit 文書の可能性がある（未確認）
- 裁定: App は使わない。「購入先を出してよい」の条件にしない
  - 理由 1: 基準が未定義
  - 理由 2: 全行 false。条件にすると購入先が 1 件も出ない
  - 理由 3: 収益・提携の判断は App が正本という分担と重なる
- Research は今後も値を入れない（凍結）
- VIEW の列としては残る（列名と順序を変えない条件のため）。複製表に写ってよいが判定に使わない
- 購入先の表示に使う Research 側の値は master_external_links.display_status='active' と親エンティティの実在だけ

## 4. Research 側の宿題
- C-4: source_type='retailer_official' の 254 行の確認（週次ゲート）
- 実測のガード: 今回から読み取り専用トランザクションで実行（on を確認）。postgres ロールの使用はスキーマ実測の規約どおり
- 追加 1: retailer_product 1,896 行の由来と品質の確認（週次ゲート）
- 追加 2: master_images.source_type の語彙が 3 か所で不一致。Rules §G-10 は manufacturer_official / manufacturer_press / public_domain、Knowledge は manufacturer_official / retailer_official、実値は 5 種（official_feed / official_jsonld / official_og を含む）。正典の是正を週次ゲートに載せる。App は text で受ける（回答 #3 のとおり）

## 5. 週次ゲートに載せるもの（全て未適用）
- D1: updated_at NOT NULL 化（10 表）
- D2: (updated_at, 主キー) 索引（10 表）
- D3: rig_categories / part_categories に updated_at + トリガー + SELECT ポリシー
- D4: VIEW master_publication_effective の末尾に updated_at
- D5: 同期専用の読むだけの役割（B-5 の列と affiliate_enabled は列権限で読めなくする）
- D6: master_image_rights
- D7: Rules への追記（affiliate_enabled と monetization_ready の凍結・文書のみ）

【作業完了】DBR-INQUIRY-003-REPLY-2

---

> **追記 2026-10-02 07:36 JST**: 回答 #3-3（Catalog 区画が Research DB に書く範囲）。本文は無編集（project ref だけ伏せた）。

# DB Research 回答 #3-3 — 管理アプリ（Catalog 区画）が Research DB に書く範囲
回答: 2026-10-02 JST / 主査(Claude) / 照会: 2026-10-02_db-research-inquiry-003-3-catalog-write-scope.md

## 0. 実測の範囲
- Research DB（project ref は伏せた） / 2026-10-02 07:33 JST / current_user=postgres / 読み取り専用トランザクション（transaction_read_only=on）/ SELECT のみ・書込なし
- 読んだもの: change_logs と import_runs の全列・制約 / change_logs の書き手別件数 / master_publication.verification_user_id の非 NULL 件数 / master_images の entity_type 別件数（合計 79,938 と一致）/ ロゴ状態の分布（master_publication 72,896 行・manufacturers 863 行）/ retailer_product 1,896 行のホスト別件数（合計 1,896 と一致）/ RIG 向けリンクの種別別件数 / rig_master_variants の SKU 有無
- 読んでいないもの: 各リンク先ページの中身（製品との一致は未確認）

## 1. 前提（照会 §0）
- 了解。自動処理は書かない・物理 DELETE なし・役割は 2 段階

## 2. 第 1 段（D8）
### 2-1. 結論
- D8 として D1〜D7 と同じ週次ゲートに載せる。順序は D6（master_image_rights）の後
- 関数方式にする。catalog_rights_writer は関数を呼ぶだけで、表への UPDATE / INSERT 権限は持たない
- 役割ができるまではオーナーが SQL Editor で実行（照会どおり）

### 2-2. D8 の中身（未適用。細部は週次ゲートのパックで確定し続報に書く）
- ロール catalog_rights_writer: 関数の実行権限と SELECT だけ
- SELECT の範囲: D5（同期専用の読むだけの役割）と同じ範囲 ＋ master_image_rights。照会の 4 表だけでは「このメーカーの画像」を辿れない（master_images は entity_id しか持たず、メーカーへは part_masters / rig_masters 経由）ため
- 関数 1: 止める・再開する（3 点セットを 1 つのトランザクションで実行）。引数は 対象の種類（manufacturer / image）・対象 ID・決定・理由・operator_id
- 関数 2: 許諾の確認を記録するだけ（master_image_rights への追記）。引数は 対象の種類・対象 ID・決定・根拠・参照・operator_id
- 関数 1 には「件数だけ返す確認モード」を付ける（対象の画像数と公開判定の行数を返し、何も書かない）。管理アプリは実行前にこれを表示する

### 2-3. 照会の案から変える点
- ロゴ（logo_permission_status / logo_display_status）は第 1 段から外す
  - master_publication は全 72,896 行が unknown / hidden
  - manufacturers は全 863 行が unknown / hidden で、logo_url は 0 件
  - 止める対象が無い。ロゴの状態は master_publication と manufacturers の両方に列があり、正本の整理が先。ロゴを使い始めるときに追加する
- 対象の種類に image（画像 ID 指定）を足す
  - master_images の内訳: part_master 79,455 / rig_master_variant 189 / body 177 / rig_master 117
  - bodies には manufacturer_id 列が無い（body_manufacturer は text）。body の画像 177 行はメーカー単位では辿れない
- 再開で戻すのは「その停止で止めた行」だけ。リンク切れなどで元から hidden の行は再開しても戻さない

## 3. 教えてほしいこと への回答
### 3-1. 「誰が書いたか」の渡し方
- 関数の引数で渡す（operator_id uuid）。接続ごとの設定値は使わない
  - 理由: 設定値方式は既存の監査トリガーの改修が要る。接続の使い回しで付け忘れ・取り違えも起きる
- 残し方（既存の仕組みを使う。トリガーは変えない）:
  1. 関数が import_runs に 1 行作る。run_type='manual_edit' / executed_by に operator_id / notes に理由
  2. 変更する行の import_run_id にその ID を入れる。既存トリガーが change_logs.import_run_id に自動で記録する。行の元の import_run_id は change_logs に旧値として残る
  3. master_image_rights の確認者・停止者の列に operator_id を入れる（uuid 列にする）
- change_logs.changed_by には DB のロール名が入る（現在の実値は postgres と rc_mdr_fixer の 2 種）。運営者は import_run_id から import_runs.executed_by を引いて特定する
- verification_user_id は公開面の確認者の列（現在は全行 NULL）。第 1 段では使わない。第 2 段で運営者が公開面の確認をしたときに operator_id を入れる
- Research DB には運営者の表が無い。operator_id が誰かの対応は App が正本

### 3-2. 週次ゲートに載せられるか
- 載せられる（D8）。§2 のとおり

### 3-3. 第 2 段で直接書かせたくない表・列
原則: 第 2 段も全て関数経由。表への直接権限は付けない。そのうえで、次は関数でも「取り込みの手順」を必ず通す。
- 主キー全部: 再採番しない
- 来歴の列（import_run_id / verification_status / research_verification_method / verification_updated_at / updated_at）: 関数とトリガーだけが書く
- Master の新規追加（manufacturers / rig_masters / rig_master_variants / part_masters / part_master_variants / bodies）: import_runs の親行 → 重複チェック → メーカー帰属の確認、の順を必ず通す。part_masters.part_slug に UNIQUE が無く、二重登録は物理 DELETE 禁止のため消せない
- manufacturer_id の付け替え（帰属の変更）と メーカーの統合: オーナー判断。管理アプリから直接は不可
- manufacturers.official_url: ブランド確認の手順が必須。直接は不可
- compatible_platforms: 公式根拠が必須。根拠 URL つきの専用手順だけ
- status の discontinued: 公式に明示がある場合だけ
- spec_data: キーの定義が未確定。確定まで不可
- msrp_usd: 触らない
- master_aliases: 「確実に同一物」の基準を通す。alias_kind は 11 値の CHECK あり。import_runs の親行が先に要る
- master_external_links: official の URL は一覧ページ・小売ドメイン不可。価格・在庫・SALE は書かない
- master_relations: App の集計（使われている RIG など）を書き戻さない
- master_publication: public 化と index_status の index 化は週次ゲートの判断。直接は不可
- change_logs / import_runs / source_snapshots / master_field_verifications: 直接書かない
- 調査の依頼を受け取る表: Research 側に新設する。Master の表とは分け、追記だけ。Master への昇格は Research の取り込み手順で行う。名前と列は第 2 段の照会時に確定する

### 3-4. 購入先のリンクの整備
計画は立てられる。週次ゲートの議題に載せる。優先度と時期はオーナーが決める。

RIG 側の現状（実測）:
- rig_master: official 887 行（active 793 / inactive 94）/ manual 45 行（全て inactive）
- rig_master_variant: official 87 行（active 85 / inactive 2）
- 取扱店リンク（retailer_product / retailer_search / distributor）: 0 行
- rig_master_variants は 3,366 行。SKU ありは 3,257 行

Research 側の方針:
- 単位は rig_master_variants（SKU 単位）。SKU の完全一致だけ採用する。名前の一致ではリンクを張らない
- 価格・在庫・SALE は取らない
- 対象のお店は Research が選ばない。App の「提携が有効なお店」の一覧（店名・ドメイン・地域）をください。計画の入力にする
- 収集は日次の自走で候補置き場まで。DB への投入は週次ゲート
- モールを単品ページで持つか検索結果ページ（retailer_search）で持つかは計画時に決める

既存の retailer_product 1,896 行について:
- ホスト別（active）: www.thextraspeed.com 795 / www.amainhobbies.com 468 / www.vajjexrc.com 222 / www.hobbyrecreationproducts.com 46 / alshobbies.co.uk 45 / shop.robitronic.com 36 / www.rc-modell-shop.de 34 / www.towerhobbies.com 24 / rcaddict.com 23 / www.rakuseimodel.co.jp 11 / beachrc.com 8 / www.bigsquidrc.com 8 / www.rcmart.com 7 / www.wl-toys.com 6 / gpmodels.co.za 6 / super-rc.co.jp 5 / www.redcatracing.com 2 / www.enginediy.com 1 / www.fmshobby.com 1 / www.ebay.com 1 / funyat.com 1（計 1,750）
- ホスト別（inactive）: www.amainhobbies.com 119 / 60years.associatedelectrics.com 27（計 146）
- メーカー自社とみられるサイト・メディア・ebay が混じっている。取扱店リンクとしては未整理
- App へのお願い: この 1,896 行は、提携が有効なお店のドメインに一致する行だけを対象にし、Research の確認が済むまで購入先に出さないでください。確認は週次ゲートの宿題（回答 #3-2 §4 追加 1）

## 4. 第 2 段の予告（照会 §2）
- 受領。確定ではない前提で、設計が固まったらあらためて照会してください

## 5. 週次ゲートに載せるもの（全て未適用）
- D1〜D7: 回答 #3-2 §5 のとおり
- D8: catalog_rights_writer ロール ＋ 関数 2 本（D6 の後）
- 議題: RIG 向け取扱店リンクの整備（優先度と時期）
- 宿題: retailer_product 1,896 行の確認 / source_type='retailer_official' 254 行の確認 / source_type の語彙の是正

【作業完了】DBR-INQUIRY-003-3-REPLY
