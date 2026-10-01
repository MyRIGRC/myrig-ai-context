# DB Research 照会 #3-3 — 管理アプリ（Catalog 区画）が Research DB に書く範囲

**起票: 2026-10-02 (JST) / 起票元: MyRIG_App 管理画面の設計 / 照会者: イタヤ（App 側主査 Claude 経由）**
回答 #3-2 §2「Catalog 区画が書く表・列・操作の一覧を App 側から照会してください」への返答。

## 0. 前提
- 書くのは**運営者（イタヤ）が管理アプリで操作したときだけ**。自動の処理は Research に書かない（同期は読むだけ）
- 物理 DELETE はしない。Research の規約どおり
- 役割は **2 段階に分けて**お願いしたい。第 1 段は公開の条件（R9・画像を止める）に要る最小限。第 2 段は Catalog 区画（MVP の後）の本体で、設計が固まってからあらためて照会する

## 1. 第 1 段（公開の前に要る・最小限）— 役割の案 `catalog_rights_writer`
| 表 | 操作 | 列 | 何のため |
|---|---|---|---|
| master_images | UPDATE | permission_status / display_status | 画像を止める・再開する（3 点セットの 1） |
| master_publication | UPDATE | image_permission_status / logo_permission_status / logo_display_status | 公開の判定を止める・再開する（3 点セットの 2） |
| master_image_rights（D6） | INSERT だけ（追記型） | 全列 | 許諾の確認・止めた日と理由の記録（3 点セットの 3・R9） |
| 上の 3 表 ＋ manufacturers | SELECT | — | 対象を探して確かめる |

- **3 点セットを 1 つの関数にまとめてもらえると安全です**（例: メーカー ID・決定・理由・運営者 ID を渡すと、3 つの書き込みを 1 つのまとまりで行う）。管理アプリは関数を呼ぶだけにし、表へ直接 UPDATE する権限を持たない形が望ましい。関数にするかどうかは Research の判断にお任せします
- 役割ができるまでは、回答 #3-2 のとおりオーナーが SQL Editor で実行します

## 2. 第 2 段（MVP の後・Catalog 区画の本体）— いまは予告だけ
設計が固まったら照会します。想定している範囲（確定ではありません）:
- Master の追加と修正: manufacturers / rig_masters / rig_master_variants / part_masters / part_master_variants / bodies
- 別名（表記の揺れ）: master_aliases の追加
- 外部リンク: master_external_links の追加と display_status の変更（**affiliate_enabled には触りません**）
- 公開の設定: master_publication
- 調査の依頼: App で集めた候補（Master に無いもの・表記の揺れ・誤りの報告・0 件の検索語）を受け取る表。**Research 側に新しい表が要ります**（名前と列は Research の正本で）

## 3. 教えてほしいこと
1. **「誰が書いたか」の渡し方**: App の運営者の恒久 ID は `operator_id`（UUID・App の `admin_operators`）です。Research の `change_logs` や `verification_user_id` に、この ID をどう残すのが正しいですか（接続ごとに設定値で渡す／関数の引数で渡す など）
2. 第 1 段の役割と関数を、D1〜D7 と同じ週次ゲートに載せられますか（D8 として）
3. 第 2 段で、Research 側が「管理アプリから直接書かせたくない」表や列はありますか（取り込みの手順を必ず通すべきもの など）。先に分かれば、Catalog 区画の設計をそれに合わせます
4. **購入先のリンクの整備**（別件の相談）: 回答 #3-2 で、取扱店のリンクは retailer_product 1,896 行（全部パーツ・品質未確認）だけで、RIG 向けとモール（Amazon・楽天など）は 0 行と分かりました。App は公開時に「Library の詳細」と「RIG のベースモデル」に購入先を出す裁定です（提携が有効なお店だけ・PR つき）。**RIG（rig_masters / rig_master_variants）向けの取扱店リンクを Research で整備する計画は立てられますか**。優先度と時期はオーナーが決めます。整備されるまで、App は Research にリンクがある製品にだけ購入先を出します（無ければ出しません）
