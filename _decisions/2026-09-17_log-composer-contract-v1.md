# 裁定: LOG Composer PC 契約 v1

作成: 2026-09-17 (JST) / 正典 **MYRIG-20260917-111** / イタヤ裁定
採用版: **`pc/myrig-log-composer-v1.html`**
schema: **`docs/schema/myrig_db_schema_v1_6.md` v1.6-r5**

> ⛔ Production DB 非接触。migration 未実行。既存行の意味推定変換なし。Research 所有 schema 未変更。
> `maintenance_logs` は **App 所有**テーブルなので 109 の Research↔App 境界契約には触れていない。

---

## 0. この文書の役割

**結果ではなく理由を残す。** 何を決めたかは CURRENT と schema に書いてある。
ここに書くのは「なぜそうしたか / 何を踏んだか」であり、次に触る人（AI 含む）が
**同じ改善案を理由を知らずに再提案しないため**の罠の地図である（CORE「記録するのは結論ではなく理由」）。

---

## 1. なぜ RIG / PARTS と同じ「章 Register」にしなかったか

RIG / PARTS Register（110 で CLOSE）は `01 基本情報 → Advanced → 02/03/04 → 下端 Action Bar` という
章立ての登録ページである。LOG にこれを持ち込まなかった。

**理由: 行為の性質が違う。**

| | 行為 |
|---|---|
| RIG / PARTS | **所有物をきちんと登録・管理する。** 腰を据えて入力する。章立ては摩擦ではなく道案内 |
| LOG | **走った → 気づいた → 写真を付けて残す。** SNS 投稿に近い。章立ては「フォームを入力しに来た」感を生む |

**「章を持たないこと」自体が LOG の Identity** と位置づけた。区切りは薄い separator 1 本だけで、
⛔ そこに見出しを置かない（見出しを置くと章になり、RIG / PARTS の 01〜04 と競合して
「章が 1 つだけある変な Register」に見える）。

ただし **外殻は共有する**。form chrome / ボタン / focus / autosave 表現 / 公開操作 / Action Bar は
Register Family の共有実装をそのまま使う。**家族感はここで担保し、内部の並べ方だけが違う。**

これは LOG Detail で既に採った裁定と同じ原則である
（`SoT_detail.css` の LOG reaction 行: 「外側の Detail System は共有し、LOG 内部だけ SNS 投稿文法にする」/ 2026-09-06）。
新しい文法を発明したのではなく、**既存の原則を Register 側へ適用した**。

---

## 2. なぜ modal-first か

LOG は Garage / RIG Detail / Feed を見ている最中に「そういえば書いておこう」で始まる。
ここで登録ページへ完全遷移すると、元の文脈が切れる。

**modal の価値は「背後が残ること」**。だから投稿後も Detail へ自動遷移しない（§9）。
自動遷移させると modal にした意味が半分消える。

**standalone は将来の課題（PENDING）。** `/register/log` へ直接アクセスした場合に
同じ Composer を単独ページとして出す形は、**Composer 本体を Single Source のまま**実現できる構造にしてある
（Action Bar の `.bar--inset` を外せば viewport 固定へ戻る）。今は実装しない。

---

## 3. なぜ D（X 型）/ E（Media 型）を不採用にしたか

第 2 ラウンドで実モックを作って比較した（`pc/myrig-log-composer-v1-compare.html?variant=d` / `=e`。履歴として残す）。

- **D（X 型）**: 本文 ＋ 下部ツールバー `[写真] [種別▾] [RIG] [日付] [＋]`。何も足さなければ本文と投稿ボタンだけ。
- **E（Media 型）**: D ＋ 写真を 1 枚でも入れると大きい 1 枚 ＋ サムネ列が本文の上へ出る。

**不採用の理由（イタヤ実機確認）:**

> 「軽いほど良い」ではなかった。D は確かに投稿しやすい。しかし **かなり X に近づき、
> 「RC の出来事を履歴として残している」という MyRIG LOG の意味が薄くなる。**
> E はさらに写真投稿寄りで、LOG＝写真投稿に見える。

加えて実装面で 2 つ分かった。

1. **D は種別の「選び忘れ」が構造的に起きる。** 種別がツールバーの中に既定値で座るため、
   触らずに投稿すると走行ログが整備に溜まる。「軽い」と「種別を確実に取る」は両立しにくい。
   （※ §5 で種別を任意化したのでこの問題自体は消えたが、**D を採る理由にはならなかった**）
2. **E は Composer と LOG Detail の見え方が最も離れる。** Detail は「本文の後・同じ高さで 3 枚」（067 CLOSE）。
   E のヒーローを採ると Detail 再設計がセットになる。MVP でそこまで広げる価値は無いと判断した。

**⛔ D / E は Exploration として残す。再オープンしない。** 比較モックは削除していない。

---

## 4. なぜ A2 を採ったか

A（第 1 ラウンド基準案）は、開いた瞬間に **RIG / 実施日 / 種別 / 写真** が見える。
これは摩擦ではなく **投稿の質を作る枠**として働く。「そういえば一昨日だった」「これは走行だ」
「写真も付けよう」が自然に起きる。MyRIG が欲しいのは投稿量ではなく
**あとで自分で見返して価値がある RC 履歴**なので、この程度の構造は良い摩擦である。

一方 A には「きれいな管理フォーム」感が残っていた。原因は**下半分の常設欄**
（RIG の大きいカード / 日付 input / タイトル input）。

**A2 = A の上半分 ＋ D の下半分。**

- RIG と実施日は最上段の**小さな context row**（押してその場で変更できる）へ。
  日付は LOG で重要（「今日の投稿だけど作業したのは一昨日」は普通に起きる）ので、
  ⛔ D のようにツールバーへ畳まず常時見える位置に置いた。
- タイトルと場所は `＋` で開く（空のまま離れたら畳む）。
- 写真 3 枠は A のまま残した。**このアプリでは「写真を残せる」ことが開いた瞬間に分かるのが強い。**
  ⛔ D の `[写真]` 小ボタンまで畳む必要は無かった。

---

## 5. なぜ `log_type` を任意にしたか

**きっかけはイタヤの指摘:** 整備でもカスタムでも走行でも、自分にとってのメモでもない投稿がある。
「このパーツ欲しいな」「来週ここを確認する」「新しいボディどうしよう」——
`memo` に押し込むことはできるが、それは **「分類不能＝メモ」** になってしまい美しくない。

**裁定: 4 値は残したまま、選択自体を任意にする。未選択 = NULL を正式な状態にする。**

| 状態 | 意味 |
|---|---|
| `NULL` | **分類していない** |
| `memo` | **ユーザーが「これはメモとして残す」と意味を与えた** |

**この 2 つは別物**なので両方あってよい。「購入検討中」をメモだと思う人は memo を選べるし、
特に分類したくなければ選ばなくてよい。

CORE の **「公式根拠 > HOLD > 空欄/unknown > 推測 > 偽値」** にも合う。
整備でないものを DEFAULT の `maintenance` として保存するより、
分類していないなら NULL のほうが正しい。

**既存 `maintenance` 行は推測で NULL へ変換しない。** DEFAULT で入った値と本人が選んだ値を
区別する手段が無いため、変換したら偽情報を作ることになる。

---

## 6. なぜ `other` を復活させないか

**`other` は v1.2 で `setup` とともに廃止済みの slug**（schema v1.6 変更履歴）。
つまり「分類不能の受け皿」は**一度作って捨てられている**。

議論の途中で「5 値目に『その他』を足せばいい」に戻るのは、
CORE が名指しで警戒している失敗そのもの——
**「過去に失敗した理由を知らずに、同じ改善案をもう一度提案すること」**。

なお NULL と `other` は別の設計である。`other` は「その他という分類をした」ことになり、
結局 `memo` と同じ「分類不能の押し込み先」問題を再生産する。NULL は「分類していない」。
**だから NULL を選び、`other` は復活させない。**

⛔ `setup` / `other` は将来 5 値化する裁定が出ても再利用せず、別 slug を使う。

---

## 7. なぜ場所だけ残し、時間 / 路面 / 天候を Composer から外したか

現行 schema は `location` / `duration_minutes` / `surface` / `weather` を直列で持っている。
これを **全 LOG 共通の入力文法として扱うのをやめた。**

**理由は DB 負荷ではなく意味論。** nullable 列が 4 本あること自体は問題ではない。危ないのは
**「すべての LOG には場所・天候・路面・時間という概念がある」という前提を schema に埋め込むこと**である。

MyRIG の `rig_type` は将来 5 系統（rc-car / mini4wd / drone-fpv / rc-airplane / rc-boat）。
Drone なら飛行時間・高度・風、ボートなら水面状態・航行時間。
全部を `maintenance_logs` へ列追加していったら、かなり嫌なテーブルになる。

**個別に外した理由:**

| 列 | 外した理由 |
|---|---|
| `duration_minutes` | 「滞在時間 / 実走時間 / 作業時間 / バッテリー単位」で**意味が曖昧**。さらに サーキットの「ベストラップ 18.243 秒」とは**全く別概念**で、同じ列に押し込むと後で分離できない |
| `surface` | 自由テキスト 1 欄では**比較軸として弱い** |
| `weather` | 単独値より本文中の状況説明のほうが有用。かつ **「実施日 ＋ 場所」から後で自動取得できる唯一の項目**。今ユーザーに手入力させると、自動化したときに手入力値と衝突してどちらが正かの裁定が要る |

**`location` だけ残した理由:** サーキット / RC フィールド / 河川敷 / ショップ / イベント会場 と、
クローラーでは実際に意味がある。汎用性も高い。

**⛔ 列は DROP しない。既存データも変更しない。** やったのは
**「Composer v1 から新規入力させない」ことだけ**。「DB にあるから UI へ出す」はしない。

### `location` を `log_type` に依存させなかった理由

検討の途中で「走行を選んだときだけ走行情報を出す」案が出たが、**採らなかった。**

1. **§5 で種別を任意にしたのと噛み合わない。** 「河原に行ったけど走らせずに写真だけ撮った」
   （種別なし ＋ 場所）が入力できなくなる。種別を選ばない自由を与えた直後に、
   種別を選ばないと出てこない欄を作ることになる。
2. **`rig_type` 依存にはできない。** `maintenance_logs` に `rig_type` 列は無く、`rigs` を辿るしかないが
   **`rig_id` は NULLABLE** なので RIG 未指定の LOG では判定できない。
   さらに **MVP の Register は `rc-car` 固定**（108 §8）なので、今書いても常に true の死んだ分岐になる。

**他 RIG タイプ固有のフィールドは各カテゴリの設計時に決める。今は先行定義しない。**
将来の受け皿は `activity_data JSONB`（`rigs.spec_data` に前例がある）か別テーブル。
逆に今 drone 用の列を足すと **物理 DELETE 禁止**なので消せなくなる。

---

## 8. なぜ写真に Cover / caption を持たせないか

`photoMax('log') = 3` は **063（2026-09-06 イタヤ裁定）で既決**（RIG 7 / PARTS 5 / LOG 3）。
111 では共有 JS の暫定値 7 をこれに追随させただけで、新規裁定ではない。

**Cover を持たせない理由:** LOG Detail（067 CLOSE）の `.dt-photos` は本文の後に 3 枚を
**対等に**並べる。cover は無い。Register 側だけ Cover を作ると、登録時と公開時で意味が変わる。

→ `hasCover('log') = false` を共有 JS の契約にした。`photoSlots()` / `photoHint()` は
cover 前提（`max-1`、「カバー 1 枚 ＋ 追加 N 枚」）なので **LOG に使うと例外を投げる**ようにしてある。
黙って壊れるより止まるほうがよい。

**caption を持たせない理由:** LOG Detail に caption の表示枠が無い（`myrig-fotonote` 実測 0 件）。
持たせると **CLOSE 済みの Detail を再オープンする必要が出る**。
⛔ `images.caption` 列自体は禁止しない。MVP の LOG UI で入力・表示しないだけ。

**crop しない理由（F-2）:** Register の `.ph-slot` / `.ph-cell__i` は `aspect-ratio:3/2` ＋ `cover` で
**crop する**が、LOG Detail の `.dt-photos > img` は `height` 固定 ＋ `width:auto` で **crop しない**。
RIG / PARTS は Detail 側も 3:2 なので矛盾しなかったが、**LOG だけ WYSIWYG が崩れる**。
トリミング位置を決める UI が無いまま crop すると、投稿者が見たものと違う絵が公開される。
→ Composer 側も crop しない（高さを揃え、幅は元比率）。

**代表画像:** 新規 LOG 画像の `is_primary` は代表指定に使わない（全て false）。
代表が要る面（カード等）は **`sort_order` 最小**の画像を使う。
つまり「1 枚目」はあるが「Cover」ではない。⛔ 既存データを意味推定で変換しない。

---

## 9. なぜ `title` を任意にしたか

**現行 schema は `title NOT NULL` / `body NULLABLE` で、実 UI と完全に逆だった。**

- LOG Detail v1（**067 CLOSE**）は「タイトルは任意。**無くても穴が空かないことが仕様**」と実装コメントに書き、
  検証用 fixture `?title=none` まで持っている
- Composer v0.3.9 も本文必須 / タイトル任意
- `docs/ui/pc-mobile-spec-inheritance-v1.1.md`（074）が **「PC 現物が正」** と宣言している

→ **schema を直す**ほうを選んだ。UI を直すと CLOSE 済みの公開面を再オープンすることになる。

さらに、`title NOT NULL` を維持すると未入力時に**本文冒頭から見出しを自動生成するしかない**。
これは偽値であり、`purchased_period` で「偽の日付を作らない」と決めたのと同じ理由で禁じ手。

**⛔ 本文冒頭から偽 title を生成しない。⛔ 空文字 title を見出しとして描画しない。**
見出しが無い LOG は **h3 ごと出さず excerpt を繰り上げる**。

### `body` を `NOT NULL DEFAULT ''` にした理由

保存意味論が「作成＝**非公開で実体作成** → autosave → 明示的に公開」なので、
**実体作成の瞬間は body が空**になる。素の `NOT NULL` は初回 autosave で必ず違反する。

→ 空文字を許し、**公開時だけ非空を DB が担保**する:
`CHECK (is_public = false OR char_length(btrim(body)) >= 1)`

**投稿ゲートの 10 文字は App の UX 値**であり、⛔ DB 契約値として固定しない。
「5 文字でもメモなら十分」に変える可能性があり、そのたび migration になるため。
DB が守るのは「公開 LOG なのに本文が完全に空」を作らないことだけ。

**draft 実体作成時は `is_public=false` を明示して INSERT する。**
`is_public` の DEFAULT true に draft 生成を依存させない。

---

## 10. なぜ投稿後に自動遷移しないか

modal の価値は「背後の Garage / RIG Detail / Feed が残ること」（§2）。
投稿後に LOG Detail へ全面遷移させると、その価値が消える。

**裁定: modal を閉じ、元ページと scroll 位置を維持し、toast「投稿しました　見る」。**
「見る」を押した人だけ Detail へ行く。

---

## 11. なぜ種別の選択状態にカテゴリ色を使わないか

イタヤから「選んだら LOG のトークンカラーにしたい」という要望があったが、**採らなかった。**

`design-nogo-list.md` の **NG-7 職域表**:

| 用途 | 色 |
|---|---|
| 種別の識別 | RIG / PARTS / **LOG のみ** |
| **現在地・選択** | **淡いピル＋太字。塗りで反転させない** |

NG-2 は「押して切り替えるチップの**小さなドット**」を**可**としているが、
その条件は **「種別を言うだけ」**である（原文の例は RIG 黄 / PARTS 赤）。

**LOG Composer では 4 つとも LOG。** 選択済みにだけ青ドットを付けたら、
そのドットが言っているのは種別ではなく**選択状態**であり、NG-7 の職域に反する。
ベタ塗りを小さいドットに縮めても意味は変わらない。

加えて、Composer は Header に `[LOG]` の青チップが常時出ている。
**画面上のすべてが既に LOG なので、選択済みチップを青くしても新しい情報が増えない。**

→ **Composer 内でカテゴリ色を持つのは Header の `[LOG]` チップ 1 箇所だけ。**

---

## 12. 今回 v1 に載せなかったもの（理由つき）

### 関連パーツ（`maintenance_log_parts`）

旧 Composer v0.3.9 は「関連パーツ」chip を持っていたが、**保存先が無い**。

実測すると**両側に表示面が無い**:

- LOG Detail v1（067 CLOSE）: 「⛔ `maintenance_log_parts` が無いので『このLOGで使ったパーツ』の節は作らない」と明記
- PARTS Detail v1-open: RELATED の「入れないもの」に **「装着RIGのLOGを『このパーツの関連LOG』と呼ぶこと」** が明示

→ 保存しても公開面に一切出ない **死蔵入力**になる。表示契約の無い entity 参照を先に作らない（CORE）。
製品への言及は `tags` と本文が担う。**⛔ tags から `part_id` を機械的に同定しない**（tags は文字列であって `part_id` ではない）。

将来 `maintenance_log_parts` を足すときは **PARTS Detail 側の表示契約とセットで**裁定する。片側だけ先に作らない。

### 外部リンク（`entity_links`）

`entity_links.entity_type` は `('rig','part','log')` を既に許容し、RIG Register v3.1 に動作する入力部品もある。
欠けているのは **LOG Detail の表示枠だけ**（RIG / PARTS は `dt-rail-links widget="external-links"` を持つが LOG は 0 件）。

同じ理由で v1 非搭載。ただし需要は明確（走行ログに走行動画）なので、
**Composer 入力 ＋ LOG Detail 表示を同一バッチで開く**こととして PENDING に置く。⛔ 片側だけ実装しない。

**⛔ 本文を linkify しない。** 実測で linkify / autolink の実装は 0 件で、本文中の URL はプレーンテキストのまま出る。
linkify を実装すると、`entity_links.moderation_status`（個人サイトは `pending` → 運営確認）を
**本文経由で迂回できる経路**ができる。現状は塞がっているので、開けないことを明文化する。

---

## 13. 横断追随（111 で実施）

`log_type` / `title` が NULL を取れるようになったことへの追随。**デザインは変えていない。semantic guard だけ。**

| 面 | 変更 |
|---|---|
| `SoT_card-components.js` `variant='list'` | `tag \|\| badgeText \|\| 'LOG'` → **偽 fallback `'LOG'` を撤去**。title NULL で h3 ごと出さず excerpt を繰り上げ |
| 同 `variant='sm-meta'` | 同上 |
| 同 `variant='pc'` | title NULL で h3 ごと出さず excerpt を繰り上げ（`logMetaHtml()` は元から `if(type)` ガードがあり無改修で正しかった） |
| 同 Feed（`renderFeedLight`） | 空 `data-log-type` 属性と空 `<span class="pc-feed-card__type">` を出さない |
| `pc/myrig-log-detail-v1.html` ＋ `js/detail-state-demo.js` | `?type=none` を追加。**種別と、そのためだけの separator を両方落とす**。breadcrumb も「ログ」に。⛔ `[LOG]` チップを種別値の代わりに出さない |
| `docs/search/search-page-plan-v2.md` | LOG の検索対象に **`body` を追加**。title NULL でも本文から検索できる。⛔ body を title として表示しない |

**フィルタ**: 「指定なし」専用フィルタは MVP で作らない。
契約は「**すべて**」に NULL LOG を含む / 各値はその値だけ。NULL だけを絞る UI は作らない。

### 廃止値 `setup` の追随漏れを発見・是正

CURRENT には 2026-08-22 の記録として
**「廃止値 setup の撤去 … これで log_type 絞り込み UI は全ページ 4 値に揃った」** とあったが、
**実体は揃っていなかった。**

| ファイル | 残っていたもの |
|---|---|
| `pc/myrig-public-garage-logs-v3.html` | フィルタチップ `data-value="セットアップ"` ＋ カード `data-type="セットアップ"` |
| `user-garage-logs.html`（Mobile） | フィルタチップ `data-value="セットアップ"` |

111 で撤去した（カードの `data-type` は v1.2 で `setup` が移行した先である `custom`＝「カスタム」へ是正）。
**⚠️ 過去の記録は削除していない。** 後発裁定として「追随漏れを発見・是正」と CURRENT に併記する。

※ 本文・タイトル中の「セットアップを見直した」等の普通の日本語、および
`tags="セットアップ,Tires"`（ユーザー自由入力のタグ）は `log_type` ではないので**対象外**。残置。

---

## 14. 共有部品への昇格（111）

⛔ **LOG 専用のコピーを恒久管理しない**（CORE / 共有 UI Single Source）。

| 部品 | 内容 |
|---|---|
| `<myrig-register-header variant="modal">` | **111 で共有部品へ追加。** modal の中に置く Register Header。ロゴと「戻る」を持たず、閉じるボタンを持ち `myrig:register-close` を dispatch する。RIG / PARTS は `variant` を渡さないので**従来どおり無変更** |
| `save-status="saved-draft"` | **111 で共有語彙へ追加。**「下書き保存済み」。「保存済み」だけだと公開済みと読める |
| `.bar.bar--inset` | Action Bar の container variant。`.bar` は viewport 固定なので modal 内では画面下端に貼り付く。**同じ Action Bar を container 下端に置くための修飾子**。standalone 化のときはこの修飾子を外すだけで戻る |
| `photoMax('log')=3` / `hasCover('log')=false` | 写真枚数と Cover 文法の Single Source |

### 保存状態の文言を RIG / PARTS へ波及させなかった理由

LOG は「下書き保存済み」が適切。しかし **RIG / PARTS は公開後も同じ画面で autosave する**ため、
`is_public` の状態によって「下書き」が偽になる。**意味が同じではない。**

→ 共有語彙に `saved-draft` を**足す**にとどめ、RIG / PARTS の文言は変えていない。
揃えるかどうかは意味の確認が先（PENDING）。

---

## 15. 失効させたもの / Exploration として残すもの

⛔ **削除していない。履歴として残す。**

| 対象 | 扱い |
|---|---|
| `pc/myrig-log-composer-modal-v0.3.9.html` | **失効。** `_archive/20260917_log-composer-close/` へ mv（`_archive` は `.gitignore` / `.vercelignore` 対象。ディスク上のみ保全） |
| `pc/myrig-log-composer-v1-compare.html`（A / A2 / B / C / D / E） | **Exploration として active tree に残す。** ただし通常導線からは到達させない（`compare.html` / 各面の導線は正式版へ更新済み） |
| D（X 型）/ E（Media 型） | **不採用。§3 の理由。再オープンしない** |
| B（Write First）/ C（Garage Journal） | 第 1 ラウンドで役目終了。URL でのみ到達可 |
| 「LOG にも 01〜04 章 / Advanced を持たせる」 | **失効。** §1 の理由 |
| 「種別の選択済みを LOG 青にする」 | **不採用。** §11 の理由（NG-7） |
| 「走行を選んだときだけ走行情報を出す」 | **不採用。** §7 の理由（種別任意化と噛み合わない / `rig_type` 依存にできない） |
| 「`log_type` に 5 値目『その他』を足す」 | **不採用。** §6 の理由（`other` は廃止済み slug） |


---

## 16. 追記: LOG Detail Compatibility Gate と C-1 / C-2 裁定（2026-09-17 / 正典 112）

111 の Composer / データ契約と既存 LOG Detail（067 CLOSE）を E2E で突き合わせた。
**⛔ Detail の再設計ではない。111 契約への整合修正。** デザイン骨格・情報階層・写真文法は変更していない。

判定: **PASS 9 / MINOR_FIX 2 / DECISION_REQUIRED 2**。DECISION_REQUIRED はイタヤ裁定を得て実装した。

### C-1: title=NULL のときの H1

**背景（自己訂正）**: 111 バッチで「`?title=none` → h1 0」と報告したのは**計測ミス**だった。
`.dt-log-title` だけを数えており、合成 h1 は `class="sr-only"` なのでカウント外だった。
**合成 sr-only h1 は実在していた。**

- 実装は `js/detail-state-demo.js`（宣言と生成が 120 行離れていたため見落とした）
- 生成元は `.dt-log-meta` のテキスト ＋ `.dt-identity__author-name`
- 入ったのは `dbe37c7`（2026-09-07）＝ **111 より前**
- **canon 根拠 0 件。** `_AI` / `_decisions` / `docs` を全文 grep しても sr-only / h1 / 見出しレベルの
  裁定は存在せず、根拠はコードコメント「a11y / SEO のため必要」だけだった

**裁定（イタヤ）**: 合成を**廃止**する。111 の「⛔ ユーザーが入力していない title を偽生成しない」を優先。
ただし**ページ構造上の h1 は維持**し、title=NULL のときだけ固定の構造見出しを置く。

| 状態 | h1 |
|---|---|
| title あり | visible `.dt-log-title` が h1。**sr-only は足さない** |
| title NULL | visible title なし。`<h1 class="sr-only">ログ詳細</h1>` **のみ** |

⛔ body からも、`log_type` / `logged_at` / `author` からも title を作らない。
⛔ 文言を状態から組み立てない（固定文字列 1 つ）。これは**ユーザー投稿の title ではなく、
ページ種別を示す構造見出し**である。

### C-2: location だけの Log facts

**背景**: 111 以降、新規 LOG が structured facts として持てるのは `location` だけになる（§7）。
その状態を実測すると、2 カラムの `spec-grid` に 1 項目だけが入り、
**右半分が空いて下罫線も半分で途切れる**（見出し「ログ情報」＋ 1 行で高さ 90px）。
壊れてはいないが、**111 以降の新規 LOG の通常形**としては採用しない、というのが裁定。

**裁定（イタヤ）**: 表示位置は変えず（actions の下・comments の上のまま）、形だけ 2 つに分ける。

| 条件 | 表示 |
|---|---|
| `location` だけ（`surface` / `weather` / `duration_minutes` 全 NULL） | 「ログ情報」section を作らず **compact metadata row 1 本**（見出しなし・フル幅） |
| legacy が 1 つでも非 NULL | **従来の「ログ情報」section を維持**。`location` もその中へ入れる |
| 全 NULL | 何も出さない（既存どおり） |

- ⛔ `location` を上部 context へ移す再設計はしない
- ⛔ map icon / link 化 / GPS / 地図 / place entity を作らない。`location TEXT` をそのまま出す
- ⛔ LOG 専用の新 component を作らない。既存の中立語彙
  （`.section--flat` / `.spec-grid--quiet` / `.spec-item` / `.spec-key` / `.spec-value`）＋
  1 列化 modifier **`.spec-grid--single`** だけで組む
- ⛔ 既定の `.spec-grid` / `.spec-grid--quiet` は触らない（consumer は RIG v15 / PARTS v1-open ほか。両面 VISUAL LOCK）
- ⛔ **既存 DB 値は捨てない。** legacy 3 項目は Composer v1 で入力させないだけ

実測: 見出しなし・1 列フル幅 850px・**右空白 0**・高さ 128 → **55px**。

### MINOR_FIX（新裁定不要・111 で既決だった追随漏れ）

`pc/myrig-log-detail-v1.html` 冒頭の失効説明を是正した。⛔ 削除せず「**旧裁定 / 111 で失効**」と明示して残した。

| 失効していた記述 | 現行契約 |
|---|---|
| 「WIP。まだ VISUAL LOCK していない」 | 067 で CLOSE 済み |
| 「`title` を将来 optional にするかは別途検討」 | 111 で `title NULLABLE` 確定 |
| 「`surface` / `weather` は composer 側で入力できない**既知の問題**」 | 111 で**意図的な非搭載**が確定。⛔ "問題" ではない |
| 旧 composer の入力順（本文 → 画像 → タイトル） | 111 の正式版の順序へ |
| 「CURRENT 063 のモック追随は別バッチ」 | 111 で共有 JS 追随済み |

### Gate で確認した対応表（Composer → schema → Detail）

`rig_id` / `logged_at` / `log_type` / `title` / `body` / `photos` / `location` /
`duration_minutes` / `surface` / `weather` / `tags` / `is_public` の 12 フィールドについて
「Composer で入力できるか / DB に存在するか / Detail でどう表示するか / NULL 時 / legacy」を実測した。

**「Composer に無い＝Detail から消す」はしていない。**
`duration_minutes` / `surface` / `weather` は表示ロジックを一切触っておらず、既存値があれば従来どおり出る。

### 反証確認で潰した誤検出

820px / 420px で LOG Detail だけ横溢すると測れたが、**検証環境に `img/` を置いていなかったため**
`naturalWidth=0` になり `SoT_detail-components.js` の縮小計算が効かなかっただけだった。
実画像を入れて再測すると 3 面 × 3 幅すべて横溢なし。**報告前に取り下げた。**
同様に dark の横溢も 280ms 時点の計測タイミングによる flake で、650ms 待つと 4/4 で解消した。
