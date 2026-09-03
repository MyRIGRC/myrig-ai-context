# 引き継ぎ 2026-09-03 — RIG Detail 確定（r8）と共有化バッチ B/I-1 完了、次は C

> **新スレッド冒頭で読ませたうえで、§1 の revision 突き合わせ（cache-bust 付き）を必ずやらせる。**
> 本書は正典ではない。「いまどこにいるか」は `_AI/MyRIG_CURRENT.md` NOW節が正本。
> 本書に書くのは **判断・約束・踏んだ罠** だけ。ファイルの追跡状態は `git status` を正とする。

---

## 0. 読む順番

1. 本書 §1（起動）→ §6（踏んだ罠）
2. `_AI/MyRIG_CORE.md`（「共有UI Single Source 原則」節が 2026-09-02 に追加された）
3. `_AI/MyRIG_CURRENT.md` NOW節（044: r8 確定・A/B CLOSE・C 着手・DECISION 5件・C 計画）
4. `_decisions/2026-09-02_shared-ui-single-source-v1.md`（共有化の裁定原本・棚卸し実測）
5. 確定版 `myrig-mockup/pc/myrig-rig-detail-v14r5-complete-r8.html`（デザインを再検討しない）

---

## 1. 起動時にやること【必須】

```
revision.txt              → MYRIG-20260903-044
_AI/MyRIG_CURRENT.md 冒頭 → revision: MYRIG-20260903-044
```

**両方に `?cb=<一意値>` を付けて取得する。** 2026-08-31 に、素の URL では **2ファイルとも `001`（最初期値）で一致してしまい**、突き合わせでは検出できない事象が実際に起きた（CORE「raw取得には毎回一意のクエリを付ける」L1）。
Cowork なら `~/Desktop/MyRIG/myrig-ai-context` を直読みし、`git fetch` → `git status -sb` で origin と揃っていることを確認。

回答冒頭に `CURRENT: MYRIG-20260903-044` を明示する。

---

## 2. 現在地（2026-09-03 13:55 JST）

| | |
|---|---|
| 正典 | `bb8e704`（044）。**本書作成時点で push 待ちの可能性あり** → `git status -sb` で確認 |
| モック | `45aec3f`（C-0 checkpoint）push 済み・デプロイ済み |
| RIG Detail PC | **`pc/myrig-rig-detail-v14r5-complete-r8.html` をイタヤ確定** |
| 次のバッチ | **C: Detail 部品箱新設 ＋ r8 → `v15` 載せ替え** |

### レーン

| | 状態 |
|---|---|
| 検索 / Browse（PC＋Mobile） | ✅ CLOSE（再オープンしない） |
| Home 実画面レビュー | ⚫ 失効（040。Home は 8/20 に確定済みだった） |
| **A: RIG Detail 確定** | ✅ **CLOSE（r8）** |
| **B: Header Single Source 化** | ✅ **CLOSE（`9aacdc8`）** |
| **I-1: アイコン意味修正** | ✅ 完了（`45aec3f`） |
| **C: 部品箱 ＋ 載せ替え** | 🔵 **次はここ** |
| D: PARTS / LOG Detail | ⚪ C 完了後。共有部品で新規作成。コピー禁止 |
| E: Footer / Garage `pit-*` / 未使用資産の掃除 | ⚪ 独立 |
| F: PC v8 トークン横断 | ⚪ 独立。正典と実装状態の不一致（6面は page-local で v8 済み、23面は v7）を推測で解消しない |

---

## 3. このスレッドで決めたこと（時系列）

### 040（8/31）
- Home 実画面レビューを失効。次レーンを詳細3面へ
- raw 取得の cache-buster を L1 化（§1）

### RIG Detail の設計判断（8/31〜9/3、v7 → v9 → 案A/B/C → v12 → v14 系 → r8）
- **7:3・1560px Detail キャンバス・1本のグリッド**（Hero と本文を別グリッドにすると列境界が跳ぶ／行またぎは高さが配分されて画像が巨大化する。どちらも実画面で踏んだ）
- **Identity は全幅。Base Model が主、Build Name は副（約80%）で任意。** 長い製品名を右30%へ押し込まない。案B（Builder を左バイラインへ）は実画面で否定された → **右レーンにビルダーが「ドンと」要る**（SNS 性）
- Build Tag（Trail / Scale）は廃止。分類は MyRIG（カテゴリ）、意味付けはユーザータグ
- 表示ラベルは日本語（メーカー／プラットフォーム／駆動方式／入手時期／状態…）
- **Dual Path**（製品情報を見る＝Library／購入先を見る＝SHOP）は本文とサイドバーの両方に置いてよい。**Library は必須経由地点ではない。** 購入 URL は HTML に書かず `data-buy-*` を共通ロジックが受ける（入口は複数、データ管理は1つ）
- **Discovery 棚は本文と別文法**。理由行（「Vanquish VS4-10 Pro を装着中」等）は文法の必須要素。カテゴリ色は種別識別専用で棚の識別に使わない
- 「この1台の見どころ」（自動生成テキスト）は **撤回**。代わりに「このRIGのフィード」＝実データを新着順に流すだけ。RECENT ACTIVITY はそこへ吸収
- **操作色:** ティール → 撤回（第4の意味色に見える）。青 `#0969da`（v6 の follow 色）→ 撤回。**黒 `#1F2328` ベタ＝購入CTA、状態はニュートラル階調＋アイコン line/fill＋weight**、共有はブランド色。絵文字は廃止し SVG 2形
- **ピン留め件数は公開しない**（pins は L1 で完全非公開。本人の状態のみ）
- **ベースモデル枠は中立面**。購入先＋ショップ群だけを Commerce 領域として囲み「PR／アフィリエイトリンクを含みます」を明示。カード全体を暖色にしない
- **画鋲の正典 = `_ic_pin_push`**（Home のカードの塗りアウトライン）

### 044 で DECISION にしたもの
- Gallery Cover 1 ＋ Sub 8（最大9）／ Photo Notes は Sub 最大8
- 右レーンは現在形を標準プリセット。**Widget Stack 化できる境界を維持**（`data-widget`。順序は DOM 順が唯一の正本。`data-order` は持たない）
- コメントは Detail 共通 Conversation（ページ内は件数＋数件プレビュー、全会話は Modal、⋯→通報。backend は未確定）
- RELATED は「近い関連＝Detail 幅 → 広い発見＝フルワイド」の二段階。棚の内容・順番・件数は固定しない（`data-section`、order 番号なし）

### 共有化の原則（041 / CORE L1）
- 横断 UI は **2面目へ展開する段階で共有部品へ昇格**。昇格後は page-local に markup / style / behavior / a11y state を複製しない
- 探索中の page-local 試作は許容。実装方式（Web Component / React）は正典で固定しない
- 恒久二重管理は禁止。移行期の並存は `DEPRECATED <日付> / successor / remove after` 明記のみ
- **旧コードは Git 履歴を退避先とし `_archive` へ複製しない**（CORE「物理DELETE禁止」は DB恒久ルール節のもので、Git 管理コードには適用しない — GPT 指摘で解釈が割れ、裁定原本に理由を記録）

---

## 4. 次にやること: Batch C（承認済み計画）

```
C-1  共有4ファイル新設
     SoT_detail.css        Grid / Identity / Gallery拡張(nav・count・9枚) / Section Shell / Comment Conversation / Related Zone
     SoT_detail-rail.css   Rail Section Shell / Builder / Entity Actions / Share / Library Bridge(Commerce+PR) / Feed・Shelf List / AD slot
     SoT_detail-components.js   Gallery / Entity Actions 状態遷移 / Comment Modal＋⋯ / Follow
     SoT_shelf.js          Home の inline 棚JS（62行）を昇格。r8 と Home の2面で使う
C-2  myrig-rig-detail-v15.html（r8 と同一内容で共有版を読む）
C-3  parity: r8 vs v15 スクリーンショット差分 0px（light/dark）/ Action・Gallery・Comment・Shelf・Follow の挙動一致 / pageerror 0 / 中括弧収支不変
C-4  Home を SoT_shelf.js へ切替（pixel parity 0px 条件）
C-5  反証: v15 の inline に部品定義が1つも残っていないことを機械確認
C-6  r8 を active tree から除去（DEPRECATED / successor: v15 / remove after: PASS）・報告・停止
```

**既存を拡張し、新部品を作らない:** Gallery は既存 `.gallery`、Comment は既存 `.cmt-modal` `.comment`、棚は既存 `.shelf` ＋ Home の棚 JS。r8 は同じ動きを `r8-shelf-*` / `r7-cmt-modal` で page-local 再実装しているので、そこを寄せる。

**C-1 に入る前に決めること（イタヤ）:** r8 には `.app-header .cx__btn` の page-local 上書きが **8箇所**ある（ヘッダー「投稿する」を静かにする調整）。共有側の cx を r8 の見え方に更新する（全面に効く）か、Detail 専用として `SoT_detail.css` に置くか。

**C で触らないもの:** Footer（r8 に `<footer class="site-footer">` の page-local markup がある。共有化は **E**）／ Header（B 再オープンしない）／ v8 トークン（F）／ r8 のデザイン／ Browse・Search CLOSE 面。

---

## 5. Batch B（Header）で分かったこと

- cx（＋投稿する）CSS 25ルール・JS・app-nav 5ルール・ダーク地を `SoT_app-shell.css/js` へ移送。cx採用20面から inline を除去、旧 create-soft 23面を cx へ置換。テンプレも更新
- `--cat-*-on` に fallback（`#151515` / `#fff`）を付与（v8 未宣言の面で文字色が継承落ちするため）
- 出口: チェッカー 214/201 FAIL 0、56面 pageerror 0、**CLOSE/現在形6面 pixel parity 0px**、cx 7挙動同一
- 残置: `myrig-rig-detail-v6.html` の create-soft（母体保護。共有側に DEPRECATED 表記）

---

## 6. 🔴 踏んだ罠（次スレッドで再発させない）

1. **一括置換スクリプトが `@media` 内を二重出力してインライン CSS を壊した**（search で +123 行、フィルタサイドバーが畳まれた）。`re.finditer` で全体を舐めながら再帰する実装が原因。**逐次カーソル型パーサに書き直し、前後で中括弧収支が不変であることを assert**。pixel parity を出口条件にしていたから捕まった
2. **バックアップの除外パターンが部分一致**（`rig-detail-v6.html` が `garage-rig-detail-v6.html` にも一致）で1面が未バックアップ → Git HEAD から復旧。**除外は完全一致で書く**
3. **parity 測定は「同一ツリー2回撮影のノイズ」と「before→after」を分けて測る。** before ツリーを再構成するときは画像まで含めた全体コピーにする（画像欠損が差分に化ける）
4. **HTML/CSS を直しても JS が書き戻す**（v14r6 の絵文字）。部品は markup / style / behavior / a11y state をセットで扱う
5. **サンドボックスは共有フォルダ内のファイルを削除できない** → Cowork が `git commit` すると `index.lock` / `HEAD.lock` が残り、次の commit が誰からも通らない。**Cowork 側で commit したら、必ずイタヤに `rm .git/index.lock .git/HEAD.lock` を頼む**（`git status` でも `index.lock` が残ることがある）。`myrig-mockup` は private で push 不可（既知）
6. **他スレッドと作業ツリーを共有している。** 別スレッドがファイルを移動・削除した状態で作業を始めることがある（今回 14件が消えていた）。**着手前に `git status` と実体を確認し、消えていても勝手に復元しない**
7. `mockup` は `git add -A`。dirty が混在しているときに回すと全部一緒に commit される。**checkpoint の前に dirty の内訳を diff で確認**してから回す
8. 監査結果を正典へ確定記録する前に、**実体を再確認**する（別スレッドの棚卸しは別スナップショットを見ていることがある）

---

## 7. 環境メモ（Cowork サンドボックス）

- Playwright: `pip install playwright --break-system-packages` → `python3 -m playwright install chromium` は `libXdamage` 不足で失敗 → 8/31 HANDOFF §7 の apt-get download → dpkg-deb -x → `LD_LIBRARY_PATH` で回避（`/sessions/<id>/deps/ex`）。**`/tmp` はセッションをまたぐと消える**
- チェッカー: `_audit/browse_sidebar_v5_check.py` / `browse_contract_check.py` 各 約170秒。`timeout 170` を付ける
- parity: 1440×900、`networkidle` ＋ `animation/transition:none` 注入 ＋ 1.5s 待ち。ヘッダー 90px クリップと全画面の2種。同一ツリー2回のノイズは 0〜数px（feed のキャレット）
- 正典の commit はサンドボックスから可能（ロックが無いとき）。push はイタヤの `push` 関数（`~/.zshrc`。`mockup` は正典 push → モック add -A → commit → push → vercel）

---

## 8. 三者運用

- GPT は正典 READ 専用。WRITE は Cowork のみ
- GPT の案が来たら **同意なら同意、反論なら根拠**。今回 GPT が正しかったもの: 「Web Component を L1 に固定しない」「`_archive` へ複製しない」「Header は PARTS/LOG Detail 着手前」（順序は Cowork の反論を GPT が採用）
- イタヤの進め方: 実画面を直接見る（スクショ不要）。「探索中は自由、採用したら Single Source」。複数案を出すときは推奨を1つ明示。試行錯誤の段階で正典を盾にしない（ブレーキは トークン・フォント・色 / DB / 削除 / commit・push だけ）
