# ASTRA 監査への回答 — MyRIG PC Library v5（revision MYRIG-20260924-118 / 未 commit）

- 回答: 2026-09-25 JST（Cowork / Claude）
- 元の監査: ASTRA「MUST FIX 8 / SHOULD FIX 7 / 本番移行注意 3」（指示文 = `_state/AUDIT_BRIEF_20260925_library-astra.md`）
- 結果: **MUST FIX 8 件・SHOULD FIX 6 件・移行注意 2 件を修正**。SHOULD 1 件（H1 契約の適用範囲）は裁定待ちの PENDING、移行注意 1 件（メーカーの N 台）は Research 申し送り
- 対応中に**別の誤マッチを 1 件検出して修正**（下記 X1）
- gate: `_state/library_check.py` **L01〜L53（L49 欠番 = PENDING）**、本検査 FAIL 0 / **selftest 52/52**
- 再現チェック: ASTRA の手順を自動化した 24 項目すべて PASS / 回帰 8 面 × 10 幅 = 80 状態 issue 0
- ⛔ Production DB 非接触。commit / push 未実行

---

## MUST FIX

| # | 指摘 | 修正 | 確認 | gate |
|---|---|---|---|---|
| M1 | 購入先 8 件が `href="#"` で同じ製品ページを開く | URL のある購入先だけ `<a rel="sponsored">`。fixture に URL が無い → 非リンク＋「未接続」表示と注記「購入先リンクは本番の購入先データから供給します（モックでは未接続）」。購入ブロック自体（D16）は残す | リンク 0 / 未接続 8 / 注記あり | L36 |
| M2 | 横断検索 → 種別タブで結果が消える（alias・全角・Variant・旧型番） | 照合を fixture の **`L.productMatch` 1 か所**に集約（alias → manufacturer id → maker_id / 製品名・型番・別名・Variant 型番）。横断検索もカタログも同じ関数 | たみや・PRO10128-03・AX90060・ｔａｍｉｙａ・Tamiya で「検索の件数 = 遷移先の件数」 | L37 |
| M3 | Variant 選択と ITEM・仕様・購入対象・URL が不一致 / 根拠なく先頭を選択済み表示 | `applyVariant()` に集約（ITEM 行 / 仕様 / chip / `data-buy-model`・`data-buy-variant` / `?variant=`）。既定選択 = master と同じ SKU の Variant、**無ければ未選択**。Variant で変わる値は Variant 側の `spec` に明示し、master の名前から Variant 固有の語を外した（例: `Hyrax 1.9" G8 …` → `Hyrax 1.9" …`、コンパウンドは Variant 側）。画像は代表 1 枚のまま（D3）なので、代表と違う Variant を選んだときはキャプションで「代表製品（ITEM …）」と明示 | predator 着地で ITEM・仕様・購入対象が一致 / G8 へ切替で URL・購入対象が追従 / 再読込で維持 / AXI03006 は未選択・master の ITEM | L38 |
| M4 | 根拠データなしで「対応／メーカー公表」を表示 | Detail 側の固定表 `COMPAT` を撤去。対応は **`compatOf()` が source（根拠）つきの値を返すときだけ**表示、出典ラベルもデータから。fixture に根拠つきの値は無い → 全製品で非表示。あわせて**仕様表の「対応」（fits = compatible_platforms 相当）も非表示**にした（同じ原則。製品名に含まれる「for SCX10 III」はメーカーの製品名なので残る） | VPS08670 で節・仕様表とも「対応」なし | L39 |
| M5 | 仕様ラベルを位置で結合し「サイズ：2S–3S」等 | PARTS 36 行の仕様を**キー付きオブジェクト**に書き換え、ラベルは `SPEC_LABEL` からキーで引く。位置ずれしていた 2 件（30120401・TRX4M-58）を正しいキーに | 30120401 = KV / セル / ESC 電流 | L40 |
| M6 | ① `eol=hide` で facet 件数が残る ② 10,050 件でページャ 419 個・9249px | ① `pristine()` に `eolHide` を追加 ② 到達上限 `CAP=10000`・番号は `1 … n-2 n-1 n n+1 n+2 … last` の省略表示・上限超過時は注記 | ① facet 件数なし ② 10,000+ / ボタン 6 個 / 最終 417 / はみ出し 0 | L41・L42 |
| M7 | 使用例 0 件で「登録すると、ここに表示されます」 | 「〜として登録できます。」に変更（PARTS は H-1、RIG も公開設定次第なので掲載を保証しない） | 文言確認 | L43 |
| M8 | メーカー索引 → 詳細 → 戻るで `q` が消える | 索引の各行リンクに `from`、詳細の「メーカー索引」リンクは `safeFrom` でメーカー索引の URL のときだけ受ける | `?q=たみや` → Tamiya → 戻る = `?q=たみや` | L44 |

## SHOULD FIX

| 指摘 | 対応 | gate |
|---|---|---|
| Rail でカテゴリを変えると形態・駆動・並び・表示が消える | `catHref` が facet / 生産終了 / 並び / 表示を保持（子カテゴリとページだけリセット）。select と同じ遷移 | L45 |
| 操作後にフォーカスが BODY へ落ちる | 再描画前に操作中のコントロール（`data-k` / `#lc5Eol` / `data-sub` / 解除ボタン）を記録し、再描画後に戻す | L46 |
| 検索結果の更新通知がない | 横断検索 `#ls5Status`・メーカー検索 `#lm5Status` を `role="status" aria-live="polite"` で追加（件数 / 0 件） | L47 |
| 非表示の節へアンカーが残る | 着地点が非表示のアンカーは隠す。「別名・出典」は別名が無ければ出典注記（`#ld5Src`）へ向け「出典」に | L48 |
| **H1 契約の適用範囲（Product Detail 26px / L31 対象外）** | **修正せず PENDING P5**。勝手に補完しない。下記「判断が必要」参照 | （L49 欠番） |
| NO IMAGE の文字が 2.87:1 / 3.11:1 | opacity をやめ、faint → muted。light **4.93:1** / dark **5.62:1**（丸め前で確認） | L50 |
| `&theme=dark` 付きで LIGHT を押しても戻らない | テーマ操作時に URL の `?theme=` を外して操作を優先 | L51 |

## 本番移行の注意

| 指摘 | 対応 |
|---|---|
| publication 全体の適用は未検証 / `pub=null` で例外 | `canShowImage` を `pub=null` でも例外にせず非表示に（L52）。**publication 全体の受領境界の検査は mock では保証できない**ことを HANDOFF に明記（App 側で契約を新設しない） |
| `maker_id` が slug と同一 | fixture の manufacturer に **id（`mfr_001`…）/ slug（URL）/ name（表示）を別値**で持たせた。URL は slug、関係は id。`?maker=axial` → 4 件 / Tamiya 詳細 6 / 4 で不変（L52・L35 更新） |
| メーカーの「N 台」は単純合計 | **Research 申し送り**（本番は DISTINCT RIG 数。PARTS 側は H-1）。mock の表示は維持 |

## 対応中に検出した追加の不具合

- **X1 検索の正規化で `.` を消していた**: 「1.9」→「19」になり「1979 Ford F-150 …」に当たっていた（イタヤ懸念の「別語の誤マッチ」そのもの）。正規化から `.` `,` `/` を外した（空白・ハイフン・アンダースコア・中黒のみ）。`1.9` のパーツ 9 → 8 件に戻ることを確認。gate **L53**

## 判断が必要（イタヤ）

- **P5 H1 契約の適用範囲**: 今の契約（`.lib-pagehead` ＋ 32px）は Level-1 の 5 面（Top / 車種 / パーツ / メーカー索引 / 検索）＋ メーカー詳細。製品 Detail は「crumb → Stage → Identity の中の製品名（26px）」という別テンプレートで、page head ブロックを持たない。
  - 推奨: **製品 Detail は対象外と明記**（製品名は Stage 横の Identity 見出しで、ページ見出しではない。32px にすると Stage と並んだときに製品名が支配的になりすぎる）
  - 決まれば L49 として gate 化する

---

## ASTRA への再監査依頼（貼り付け用）

```
MyRIG PC Library v5 — 再監査依頼（revision MYRIG-20260924-118 / 未 commit）

前回の MUST FIX 8 / SHOULD FIX 7 / 移行注意 3 への対応を入れました。
回答書: _state/AUDIT_RESPONSE_20260925_library-astra.md
gate: _state/library_check.py L01〜L53（L49 欠番 = PENDING）/ 本検査 FAIL 0 / selftest 52/52

お願いしたいこと
1. 前回の再現手順（M1〜M8・SHOULD）をそのまま再実行し、解消を確認してください
2. 修正で新たに入った挙動の妥当性
   - Variant: 既定は master と同じ SKU の Variant、無ければ未選択 / Variant で変わる値は Variant 側の spec /
     代表と違う Variant 選択時の画像キャプション
   - 仕様表の「対応」（fits = compatible_platforms 相当）を非表示にした判断
   - 購入先の「未接続」表示（D16 の購入導線を弱めていないか）
   - manufacturer の id / slug / name 分離（URL = slug・関係 = id）
   - 検索の正規化から . , / を外した判断（X1）
3. 修正による退行（特に検索・カタログ・Detail の戻り状態）

対象外は前回と同じ（Mobile / v3 / _archive / Register・Community・Garage / 106 面 Front-wide / PENDING P1〜P5 / Research HOLD）。
P5（H1 契約の適用範囲）は裁定待ちなので、実装ではなく論点の妥当性だけ見てください。

⛔ ファイルを直接書き換えない。⛔ Production DB 非接触。⛔ commit / push しない。
報告形式は前回と同じ（MUST / SHOULD / 意見 / gate 化可否）。
```


---

# 第 2 回（再監査）への回答 — 2026-09-25 20:14 JST

ASTRA 再監査: MUST 1 / SHOULD 2。前回 M1〜M8 の再現事象は解消済みと確認された。

| 指摘 | 修正 | 確認 | gate |
|---|---|---|---|
| **MUST** Variant 型番で検索 → 通常カード → 代表（G8）へ着地。型番一致の行だけ Predator | fixture に `matchedVariant(p,q)`（完全一致優先。部分一致は 1 つの Variant だけに当たり代表の Item No. に当たらないときだけ。曖昧なら代表）。`cardHTML5` / `rowHTML5` が `opts.variant` を受けて**遷移先に `&variant=`・ITEM 表示を Variant に**。横断検索の通常カード、カタログのグリッド / リストの両方で渡す | `PRO10128-03` / `10128-03` → 通常カード → Predator・ITEM PRO10128-03・購入対象 PRO10128-03。カタログ grid / list も同じ。曖昧な `PRO10128` は G8 のまま | **L54**（＋ L24 の形を更新）|
| SHOULD 並び・表示を操作してからカテゴリ移動すると戻る | 並び・表示の操作後に `renderRail()` も実行（Rail のリンクを作り直す） | 並び → 表示 → Rail の順で操作して `sort=name&view=list` 保持。形態・駆動つきでも保持 | **L45** を操作順の検査に拡張（`lc5Sort` / `lc5View` のハンドラが Rail を更新するか）|
| SHOULD HANDOFF の J と K が矛盾（URL = id / slug）| J 節を**失効扱い**にし K を正とする注記。関係付けの原則部分は有効と明記 | — | — |

**同時に見つけて直したもの**: 型番一致の行が `E(p.maker)` を読んでいた（id 移行で製品から表示名フィールドを外した取り残し。メーカー名が空で出ていた）→ `L.makerName(p)`。**L55**（製品の表示名フィールドを読まない）を追加し全面で検出するようにした。

**P5 の文言**: ASTRA の意見どおり「製品 Detail の H1 は別の表示契約」と定義する形に更新（裁定待ちのまま）。

- gate: L01〜L55（L49 欠番）本検査 FAIL 0 / **selftest 55/55**
- 再監査の操作順チェック 8/8 PASS / 前回分の再現 24/24 PASS / 回帰 8 面 × 10 幅 = 80 状態 issue 0

---

# 第 3 回（最終確認）依頼 — 2026-09-25 20:35 JST

再監査後にイタヤ裁定で入った変更（裁定 `_decisions/2026-09-24_library-shell-hybrid-v1.md`）。

| 裁定 | 内容 | gate |
|---|---|---|
| **D18**（P5 解消）| 製品 Detail の H1 は別の表示契約。製品名が H1、表示は `.ld5-id__name` 26px。ページ見出し型の `.lib-h1` 32px は Detail 以外 | **L49**（欠番を実装）|
| **D19** | 購入 CTA（購入先を見る）を青に。`--lib-buy` = `--color-accent-fill` #0969da（Community / Garage の `--dt-action` と同色）。中立レイヤーの唯一の例外で、購入以外に使わない | **L56** |
| **D20** | リフォームしやすい土台（トークン / 部品 1 か所 / name・id・slug 分離 / gate）。Next.js では版番号を名前に持ち込まず役割名に。CORE 候補（実装変更なし）| — |

- gate: L01〜L56 本検査 FAIL 0 / **selftest 57/57**
- 購入ボタン コントラスト light / dark 5.19:1 / 回帰 8 面 × 10 幅 = 80 状態 issue 0

---

# 第 3 回（最終確認）への回答 — 2026-09-25 20:44 JST

ASTRA 最終確認: 前回指摘は解消を確認。追加で MUST 1 / SHOULD 1。

| 指摘 | 修正 | 確認 | gate |
|---|---|---|---|
| **MUST** Variant カードの仕様が代表のまま（`57991` → ITEM `57991 · XB（完成品）`・仕様 `… · Kit`）| fixture に `cardSpec(p,v)` を追加し、`cardHTML5` / `rowHTML5` の仕様を 1 か所に。Variant を受けたら Variant の `spec`（車種 = 形態 / スケール、パーツ = `partSpecEntries(p,v)`）を優先。旧 `specValues` は撤去 | `57991`: 横断検索・車種カタログ grid / list すべて ITEM・仕様とも XB（完成品）、Detail「形態 XB（完成品）」・購入対象 57991 と一致。`58691` は Kit のまま。`PRO10128-03` / 曖昧 `PRO10128` も前回どおり | **L54** 拡張（`cardSpec` が Variant の spec を見る / カード・行が `cardSpec(p,v)` を使い `p.kit` を直接読まない）＋ selftest **L54c** |
| SHOULD D18 の記述と実表示の差（720px 以下は 26px / メーカー詳細は Y=136）| 実表示を正として D18 を明確化: 721px 以上 32px・720px 以下 26px、Level-1 5 面は H1 Y=100、メーカー詳細はパンくずの下で Y=136（1440px） | 製品 Detail の別契約は変更なし | 位置は実行時の値のため回帰チェックで確認（静的 gate には入れない）|

**D20 への意見**（CORE には共有責務・識別境界・変更時の検証原則だけ、px 値・クラス名・gate 番号は実装と裁定記録へ）: 採用候補として CORE 昇格時に反映する（CORE 変更はイタヤ / GPT 判断）。

- gate: L01〜L56 本検査 FAIL 0 / **selftest 58/58**

---

# 結果 — 2026-09-25 20:54 JST

ASTRA 最終確認 **OK**（MUST / SHOULD 残件なし・追加修正の提案なし）。本検査 FAIL 0 / selftest 58/58 / 8 面 × 10 幅 × light・dark = 160 状態 issue 0。PC Library v5 の監査を CLOSE。
