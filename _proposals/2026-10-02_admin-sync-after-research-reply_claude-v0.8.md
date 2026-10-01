# Admin v0.8: Research 回答 #3 を受けた App 側の読みと次の手（Claude 主査案）

> 作成: 2026-10-02 JST / Claude（Cowork）／ 正典 revision: MYRIG-20261002-144 で追加
> 元: `_decisions/2026-10-02_db-inquiry-003-reply.md`（Research の回答・実測）／ 裁定原本 `_decisions/2026-10-01_admin-console-v1.md` §16
> 状態: **PROPOSAL**。§1 は事実の整理、§2〜§4 は App 側の案

---

## 1. 回答で確定したこと（事実）

| 項目 | 中身 |
|---|---|
| updated_at | 10 表は「どの列を変えても更新されるトリガーあり・NULL 0 件」。無いのは `rig_categories`（37 行）・`part_categories`（104 行）・VIEW の 3 つ |
| 行数 | part_masters **145,291** ／ master_external_links 80,001 ／ master_images 79,938 ／ master_publication 72,896 ／ rig_master_variants 3,366 ／ rig_masters 1,242 ／ manufacturers 863 ／ bodies 674 ／ master_aliases 276 ／ part_master_variants 75 |
| 主キー | 全表で確定（回答 B-2）。公開の判定は（entity_type, entity_id）の複合 |
| 全列 | 全表で確定（回答 B-3）。**写してはいけない列も確定**（B-5: 調査用の列・msrp_usd・evidence 系・notes など） |
| 公開の判定 | App の読み方で合っている（VIEW の結果をそのまま写す）。VIEW は master_publication と 1 対 1 |
| 複製の表の条件 | **Research に無い制約（外部キー・NOT NULL・UNIQUE・CHECK）を複製の表に足さない**。親の無い行がある（external_links 46・publication 121） |
| 一括更新 | 1 回の一括更新の全行が同じ updated_at になる（数万行が同じ値の例あり）。Research は一括更新を **1 トランザクション 10 分未満**で運用する |
| 画像 | 画像は Research も App もファイルを持たず、出所へのリンクで出す（ホットリンク）。`image_url` は複製の表にだけ持つ |
| 画像の許諾 | **R9 を満たす記録の場所は今は無い**。案 = 別の表 `master_image_rights`（メーカー単位・追記型）。止める操作は 3 点セット（画像 = denied ＋ hidden ／ 公開の判定 = denied ／ 止めた日と理由の記録） |
| Research 側の作業 | D1〜D6（NOT NULL 化・索引・カテゴリの updated_at・VIEW の updated_at・同期専用の役割・許諾の表）は**未適用**。週次ゲートでオーナー（イタヤ）の承認が要る |

---

## 2. §16（運営者 ID と同期）への反映（細かい調整）

| # | 変えるところ | 理由 |
|---|---|---|
| S-1 | **読み直しの幅を 10 分 → 15 分**にする | Research が「一括更新は 10 分未満」で運用するので、同じ 10 分では余裕が無い。重なって読んでも壊れない（主キーで上書き） |
| S-2 | **カテゴリ 2 表（141 行）は毎回全部写す** | 小さいので差分にしない。D3 を待たずに進められる |
| S-3 | **公開の判定は、D4 の適用までは毎回全部写す**（72,896 行）。D4 のあとは差分 | VIEW に「変わった時刻」が今は無い |
| S-4 | **最初の全件の取り込みは「一時の置き場 → 検証 → まとめて切り替え」で行う**（§16 の「大きくなったら」を最初から使う） | パーツだけで 14.5 万行。数万行が同じ時刻で変わる一括更新もある。1 つのまとまりで本番の表に入れるより安全 |
| S-5 | **複製の表には、Research に無い制約を足さない**。App の表（rigs など）から複製の表の主キーへの参照だけ張る | 回答 B-6。親の無い行があっても同期が止まらない。**App は「親が無い行」を表示のときに黙って飛ばす** |
| S-6 | 同期専用の役割は、**写してはいけない列を読めない**（列ごとの権限・Research の D5） | 回答 B-7。App に入ってはいけない値が、そもそも届かない |
| S-7 | 複製の表の列の型は text のまま受ける（permission_status・display_status・source_type に決まった値の制約が無い） | 回答 B-3 |

---

## 3. 新しく見つかった論点

### G31 🔴 購入先（Commerce）と Research の「外部リンク」が重なっている
- Research の `master_external_links`（80,001 行）には、**お店のリンクがすでに入っている**: `link_type` = retailer_search / retailer_product / distributor、`group_name` = mall / rc_specialty / distributor、`region` = jp / us / eu / asia / global、**`affiliate_enabled`**（真偽）。`master_publication` には **`monetization_ready`** もある
- 一方、v1.1 で決めた App の `commerce_offers` は「Master ごとの購入先の URL」を App が持つ形。**同じ「この製品はこのお店で買える」という事実を、Research と App の 2 か所で持つことになる**（二重の正本）
- **Claude 案（要裁定）**:
  - 「この製品がこのお店のこの URL にある」= **事実** → Research の `master_external_links` が正本（App は同期で読むだけ）
  - 「このお店と提携している・PR を出す・提携用の URL にする」= **App の商売の判断** → App の `commerce_merchants` が正本（お店ごとに、提携の状態と、URL に提携用の印を付ける決まりを持つ）
  - `commerce_offers` は**全件の URL を持つ表にしない**。Research のリンクでは足りない例外（個別の提携 URL・Research に無いお店）だけを持つ「上書きの表」にする
  - 表示の条件（v1.2 M3）は変えない: お店の提携が有効 かつ リンクが有効（Research の `display_status = active`）かつ リンクの確認が ok
- 確かめること（Research へ）: `affiliate_enabled` と `monetization_ready` は誰がどの基準で立てているか。App の提携の状態と意味が重ならないか

### G32 カタログ画像を「すぐ止める」手段
- Research の正しい止め方（3 点セット）が App に届くのは**次の同期のあと**。メーカーから「使わないで」と言われたときは「すぐ」止める必要がある（対応集 S13）
- **Claude 案**: App 側に「表示を止めるだけ」の小さな表を持つ（メーカー・製品・画像のどれか単位で「画像を出さない」）。管理アプリの Catalog 区画で止めると、**① App のこの表にすぐ書く（すぐ効く）② Research に 3 点セットを書く（正本）** を続けて行う。同期で Research の denied が届いたら、App 側の一時の止めは外してよい
- 二重の正本にしないための決まり: **App 側は「一時のブレーキ」だけ。許諾の判断と記録の正本は Research**

### G33 retailer_official の画像 254 行（Research 側の宿題）
- 「公式の出所」に当たるか未確認（回答 C-4）。Research の週次ゲートで確認。**確認が済むまで、この 254 行の画像を公開面に出すかは R9 の確認事項**に入れる

### G34 Research の DB の読み取り専用のガード
- 回答 §0 に「実測は postgres の役割・`transaction_read_only` は off（Schema §12′ のガード未充足）」とある。**Research 側の運用の話**なので App は決めないが、イタヤに共有する（書き込める役割で調べている状態）

---

## 4. イタヤに決めてほしいこと（推奨つき）

| # | 問い | Claude の推奨 |
|---|---|---|
| Q-R1 | Research の D1〜D5（updated_at の NOT NULL 化・索引・カテゴリの updated_at・VIEW の updated_at・同期専用の役割）を、次の週次ゲートに載せて承認する | **はい**（App の同期の前提） |
| Q-R2 | Research の D6（画像の許諾の表 `master_image_rights`・メーカー単位・追記型）を週次ゲートに載せる | **はい**（R9 を満たす唯一の置き場） |
| Q-R3 | §2 の調整（読み直し 15 分・カテゴリは毎回全部・最初は一時の置き場から切り替え・複製の表に制約を足さない） | **はい** |
| Q-R4 | G31: 購入先の URL の事実は Research の外部リンクを正本にし、App は提携と PR だけを持つ（`commerce_offers` は例外の上書きだけ） | **はい**。ただし先に Research へ `affiliate_enabled` と `monetization_ready` の意味を確かめる |
| Q-R5 | G32: カタログ画像をすぐ止めるための、App 側の一時のブレーキを持つ | **はい** |

---

## 5. 回答 #3-2 を受けた追記（146）

| 分かったこと | App 側の扱い |
|---|---|
| `affiliate_enabled` は全 80,001 行が false・一度も使われていない。Research は**凍結**（今後も true にしない） | **App は写さない・使わない**（B-5 に追加）。提携の正本は App で確定 |
| `monetization_ready` は全行 false・意味の定義が無い。Research は凍結 | **App は判定に使わない**。VIEW の列として写ってよいが読まない |
| 購入先に当たるリンクは **retailer_product 1,896 行だけ**（全部パーツ向け・品質未確認）。**RIG 向け 0・モール（Amazon・楽天など）0**。取扱店リンクの整備は未実施 | 🔴 **G35（新）**: Q-K5（公開時に Library の詳細と RIG のベースモデルに購入先を出す）は、**今のままだと出せるリンクがほぼ無い**。RIG はリンク 0、パーツは H-1 で接続できない |
| Catalog 区画が Research に書くための役割が無い | 照会 #3-3 で書く範囲を返した（第 1 段 = 画像を止める 3 点セットと許諾の記録／第 2 段 = Catalog の本体は後で） |
| `master_images.source_type` の語彙が正典の 3 か所で不一致 | Research の宿題。App は text で受ける |

### G35 の案（Claude）
- G31 の分け方（URL の事実 = Research）は変えない。**App に URL を全件持ち直すと、二重の正本に戻る**
- 公開時は「Research にリンクがある製品 かつ お店の提携が有効」のときだけ出す。無ければ出さない（#34「迷ったら出さない」）
- RIG 向けの取扱店リンクの整備を、Research に計画してもらう（照会 #3-3 の 4）。優先度と時期はイタヤが決める
- 提携用の URL が「お店の決まりで機械的に作れない」場合（リンクごとに発行が要るお店）は、App の `commerce_offers` に **Research のリンク（link_id）を指して提携用の URL だけ**を持つ（事実の URL は持たない）
