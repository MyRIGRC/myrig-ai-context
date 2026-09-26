# ASTRA 監査への回答 — MyRIG Mobile Library v5（revision MYRIG-20260924-118 / mock 未 commit）

- 回答: 2026-09-26 10:09 JST（Cowork / Claude）
- 監査指示: `_state/AUDIT_BRIEF_20260925_mobile-library-astra.md` / 裁定: `_decisions/2026-09-25_mobile-library-v1.md`
- ASTRA 結果: **MUST 1 / SHOULD 2**（PC の退行は 64 状態比較で検出なし）→ 以下すべて対応済み

## MUST

| 指摘 | 原因 | 修正 | 確認 | gate |
|---|---|---|---|---|
| メーカー索引で `tamiya` と打ち → 詳細 → 「メーカー索引へ戻る」で `tamiy` になる（貼り付けは空になる） | `render()` が行リンクの `from=`（`fromHere()` = 現在の URL）を作った**後**に `history.replaceState` で URL を検索語へ更新していた。1 文字前の URL がリンクに入る | `render()` の先頭で URL を更新してから行を描く。**PC の `myrig-library-makers-v5.html` にも同じ不具合があったので同じ 1 か所を修正（限定再 OPEN。ASTRA 推奨どおり）** | 入力 / 貼り付け / 消去 → 往復で検索語が完全一致（PC / Mobile）| **L57**（PC）/ **M15**（Mobile）＋ ブラウザ検査 **B1** |

## SHOULD

| 指摘 | 修正 | 確認 | gate |
|---|---|---|---|
| 長い空白なしの検索語（36 字 × 2）で 375px が横にはみ出す（車種 / パーツ / 横断検索） | 条件ピル・空状態・型番一致・ヒントに `overflow-wrap:anywhere` ＋ 幅制限（Mobile CSS） | 3 面 × 375px で はみ出し 0 | **M16** ＋ **B2** |
| 保存テーマなしで `?theme=dark` を開くと light（車種 / パーツ / 横断検索 / メーカー索引） | 面が状態 URL を書き換えた瞬間に `theme` が落ち、後から読む mobile-shell が light に戻していた → fixture に `L.stateUrl(u)`（`?theme=` を**live で**運ぶ。テーマボタンが消した後は再付与しない = PC S7 / L51 と両立）。catalog5 の url / catHref、メーカー索引、横断検索がこれを使う。mobile-library-shell は `?theme=` を先に反映（ちらつき防止）| 4 面 × PC / Mobile で dark 維持・URL に theme 残存 | **L58** / **M15** ＋ **B3** |

## 意見への対応

| ASTRA 意見 | 対応 |
|---|---|
| M4（メーカー select を置かない）に賛成。PC も別バッチで | そのまま。PC のメーカー select は **PENDING（別バッチ）** として裁定に残す。「1,000 社で破綻」は実測していないので裁定の表現を「select では実用にならない見込み」に弱める |
| P-M2 車種も製品行でよい | **確定**（裁定 M2 の ⚠️ を解消） |
| Top の構成に賛成 | Top = 骨格確定のまま。新着 4 行は mock tuning |
| P-M4 Not Found にも H1 | `notFoundHTML`（fixture 1 か所）の見出しを `<h1 class="lib-notfound__t">` に（文字は `<b>` 15px → **h1 17px**）。PC / Mobile とも Not Found 面が H1 1 つになる（PC は h1 0 → 1 の変化 = 限定再 OPEN に含める）| **M17** |
| D18 の Mobile 差分（26px → 22px）を裁定に明記 | 裁定 **M6** として追記（製品 Detail の H1 = 製品名は同じ契約。Mobile は 22px） | **M17** |

## gate 化（ASTRA 提案 4 点 → `_state/library_browser_check.py` 新設・Playwright）
B1 メーカー名 入力 / 貼り付け / 消去 → 往復で一致 / B2 長い検索語ではみ出し 0 / B3 `?theme=dark` 維持 / B4 Variant 着地（57991 / PRO10128-03 / 曖昧 PRO10128）/ B5 もっと見る → 戻り の件数・位置。PC ＋ Mobile を同じ script で見る。
playwright の無い環境（Cowork の device VM）は SKIP を報告して exit 0。実行は Playwright のある環境（Cowork の作業環境 / ASTRA / Codex）で。

## 結果
- 静的 gate: PC L01〜L58 FAIL 0 / selftest 60/60。Mobile M01〜M17 FAIL 0 / selftest 16/16
- ブラウザ検査 B1〜B5: FAIL 0（PC ＋ Mobile）
- 回帰: Mobile 12 URL × 3 幅 × light / dark ＋ PC 10 URL × 4 幅 × light / dark = **152 状態 issue 0**（Not Found 含め全面 H1 = 1）
- PC 限定再 OPEN の内容（3 点・いずれも 1 か所）: ①メーカー索引の URL 更新順（L57） ②状態 URL に theme を運ぶ（L58） ③Not Found の H1（notFoundHTML）
- Mobile 裁定ファイルが GitHub main で 404 なのは未 push のため（mock と一緒に push する）

## ASTRA への再確認依頼（貼り付け用）

```
Mobile Library v5 の再確認をお願いします。

回答書: ~/Desktop/MyRIG/myrig-ai-context/_state/AUDIT_RESPONSE_20260926_mobile-library-astra.md
裁定: _decisions/2026-09-25_mobile-library-v1.md（M2 確定 / M4 表現 / M6 追記）

1. MUST の解消: メーカー索引で 入力 / 貼り付け / 消去 → 詳細 → 戻り で検索語が一致するか（Mobile と PC の両方。PC は限定再 OPEN で同じ修正）
2. SHOULD の解消: 375px の長い検索語 / 保存テーマなしの ?theme=dark（4 面）
3. PC の限定再 OPEN 3 点（メーカー索引の URL 更新順 / 状態 URL の theme / Not Found の H1）が、それ以外の PC の表示・URL を変えていないか
4. 新設 _state/library_browser_check.py（B1〜B5）が指摘を再現・検出できる形か
5. gate FAIL 0（L01〜L58 / M01〜M17）と selftest の確認

問題がなければ「Mobile Library 監査 OK」と明記してください。
⛔ ファイルを直接書き換えない ⛔ Production DB 非接触 ⛔ commit / push しない
```

---

# 再確認（第 2 回）— 2026-09-26 11:30 JST

ASTRA が利用制限のため、イタヤ指示「君の方で進めて」により **Claude の独立監査エージェント**（作成者と別コンテキスト・敵対的に欠陥を探す役）で再確認した。
監査は PC の CLOSE 時点（mock HEAD `e738d8e`）のスナップショットを base として **900 状態の表示比較 ＋ 操作 37 シナリオ × 2 幅 ＋ 変異注入 14 種**。

## 第 1 回の再確認で出た指摘 → 対応

| 指摘 | 内容 | 対応 | gate |
|---|---|---|---|
| **MUST M-1**（PC 退行） | `?theme=dark` → テーマボタン LIGHT → Rail / カード→戻る / メーカー行→戻る で dark に戻る。`stateUrl` が作った theme がリンク（catHref / from=）に焼き込まれていた | **?theme= は「その面だけの表示指定」**に確定: 自分の URL（replaceState）には残す（`stateUrl` / `withTheme`）、リンク（from= / catHref / `fromHere()`）には入れない | **L59** ＋ **B6** |
| SHOULD S-1 | Mobile ヘッダー「←」が from= を使わず一覧の状態が消える（file:// / 直リンク） | referrer が無いときは from=（`safeFrom`）を優先 | **B7** |
| SHOULD S-2 | `?theme=dark` が同じ面にしか残らない（dark → light → dark の往復） | M-1 の方針で統一（遷移先は保存値 → OS の通常規則）。往復は消えた | B6 |
| SHOULD S-3 | gate の穴（型番一致ボタンの variant / テーマボタン → 遷移） | B4b（型番一致ボタン）/ B6 を追加 | B4b / B6 |
| 意見 | Mobile のメーカー詳細の抜粋は M2 の対象か | **抜粋も製品行**（メーカー詳細 3 件・製品 Detail の関連製品。detail5 に `rows:true`。PC は既定でカード＝無変更） | **M07**（rows:true / 抜粋が rowHTML5）|
| 意見 | browser check の SKIP が「通った」に見える | SKIP は警告文 ＋ **exit 3** | — |

## 第 2 回の結果: **Mobile Library 監査 OK**（MUST 0）
- M-1 / S-1 / S-2 / S-3 はすべて再現せず。base にあった同種の潜在不具合（Top・メーカー索引の from= に theme が入る）も解消 = PC は base より良くなった
- 残った SHOULD 2 件も対応済み:
  - S-A: Mobile Detail のヘッダー「←」の読み上げ名が行き先と食い違う → detail5 がパンくずと同じ解決結果（from / backLabel）で `#mlBack` の href と aria-label を設定（**B7b**）
  - S-B: `rows:true` を外しても gate が落ちない → **M07** に追加 / docstring の exit コード修正
- PC 回帰（base 対比 900 状態）: 差分はすべて許容 4 点のいずれか。**許容リストを 4 点に更新**: ①メーカー索引の URL 更新順 ②自分の URL の ?theme= 維持 ③Not Found の H1（15px `<b>` → 17px h1） ④**リンク（from=）に ?theme= を入れない**（base の潜在不具合の解消）。それ以外の差分 0
- gate: PC L01〜L59 FAIL 0 / selftest 61/61。Mobile M01〜M17 FAIL 0 / selftest 17/17。ブラウザ B1〜B7 FAIL 0（PC ＋ Mobile）
- 監査スクリプト・結果: Cowork 作業環境の scratch（`astra2/`）。mock / canon は監査側で無変更

