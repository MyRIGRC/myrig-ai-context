# Mockup State Matrix — モック実装状態の実測表

- Status: **OBSERVATION / NOT CANON。** 正典は `_AI/MyRIG_CURRENT.md`
- **使い方: 現在の実装状態を上書き更新する。** 履歴を積み上げない（経緯は CURRENT と `_decisions/`、
  ファイルの追跡状態は `git status` を正とする）。表示件数・余白・棚の順番のような UI 調整は書かない
- 初版: 2026-09-03 14:45 JST（交通整理 Audit）／ 更新: 2026-09-03 17:34 JST（Gate 2 CLOSE を反映）
- 正典: **MYRIG-20260903-046**（push 待ち。045 までは GitHub main と一致済み）／ モック: **`8f2c86b`**（push 待ち）
- 実測環境: ローカル静的サーバ + Playwright Chromium（PC 1440×900 / Mobile 390×844）、light・dark

---

## 0. 現在地（3行）

1. **Batch C は完了。Gate 2 = PASS / CLOSE。** RIG 詳細 PC の SYSTEM 整理は終わった（contract 31 PASS / 0 FAIL / 0 WARN）。
2. **`v15` = SYSTEM VERIFIED（実装の正本）。** Launcher・見比べ・サイト内リンク 33本とも v15。
   r8 と移行専用ツール（生成 / parity）は撤去済み（履歴は Git）。
3. **次は PARTS Detail（D）だが、ここから先は DESIGN レーン。** イタヤと実画面を見ながら始める。
   C に直接関係しない指摘は §4 に残し、PROPAGATION-SHELF / PROPAGATION-HEADER / E / F の別レーンへ送ってある。

### 交通整理 Audit（2026-09-03 初版）で挙げた 14件の処理

| 分類 | 件 | 状態 |
|---|---|---|
| BLOCKER | 4 | ✅ 045 で処理（044 push 手順 / Header 判定 / Launcher → r8 / Shelf を別バッチへ） |
| REGRESSION | 4 | Home ロゴ 2px → PROPAGATION-HEADER ／ Search 回帰の再実行 → 未 ／ Home 8/26・Mobile Home 8/30 の記録欠落 → 記録として保持（再設計しない） |
| CANON-STALE | 2 | ✅ 045 で是正（PC Feed 3タブ／ Batch B parity 証跡の但し書き） |
| SEPARATE-BATCH | 5 | E（Footer / SoT 乖離 / 孤児2件 / 404）・F（PC v8） |

---

## 1. リポジトリ現在地（checkpoint）

| リポジトリ | local HEAD | origin/main | working tree | 備考 |
|---|---|---|---|---|
| `myrig-ai-context` | 046 | `fcae4ed` 045（`ls-remote` ＋ cache-bust 付き raw の両方で一致を確認） | clean | 045 まで同期済み。046 は push 待ち |
| `myrig-mockup` | `8f2c86b`（Gate 2 CLOSE） | `45aec3f`（ローカル ref。private のため remote 未確認） | clean | **push 待ち。** author `MyRIGRC` 正常。Gate 2 の監査対象コードは `ec3398c` |

---

## 2. State Matrix（主表）

状態語彙: **LOCK** = VISUAL LOCK（イタヤ確定）/ **MIG** = MIGRATING / **VERIFIED** = SYSTEM VERIFIED / **WIP** = 未確定。
Launcher = `index.html`（`compare.html` の PAIRS も同一ファイルを指す）。

| Page | Visual Canon | Launcher → 実ファイル | Header | Footer | Shelf | Shared CSS/JS（主） | page-local override | migration | 確定後の drift 可能性 | parity |
|---|---|---|---|---|---|---|---|---|---|---|
| **PC Home** | LOCK（8/20） | 確定 → `pc/myrig-home-v3.html` ✔ | cx 共有 **＋ page-local header 系 17ルール**（`.app-logo--image` !important 等）→ 実測: タグライン「RC GARAGE」が他6面より約2px右ずれ | F00 コピー（8面同一） | inline 51ルール + inline JS（`SoT_shelf.js` の昇格元。**DEPRECATED 表記済み**） | app-shell / catalog-v6 / browse-sidebar(旧+v5) / footer / card-components / browse-sidenav / browse-directory | inline CSS 2,769行（`:root` v8 上書き / header 系 17 / `!important` 825） | Header 済（残置 17 → PROPAGATION-HEADER）/ 棚 → PROPAGATION-SHELF | **あり**: 8/26 density pass + `explore-strip` 新設（裁定記録なし）/ 8/30 Sidebar V5（036/037 裁定あり）/ 9/2 B | B: CURRENT記載のみ（証跡ファイル未検出）。今回ヘッダー帯実測: Home基準（ただし Home 自身がロゴ部で他6面と不一致） |
| **PC Search** | LOCK（8/25 CLOSE） | 確定 → `pc/myrig-search-v3.html` ✔ | cx 共有（page-local は `.app-search__suggest` 系 6ルール＝検索サジェスト機能） | F00 コピー | — | app-shell / catalog-v6 / filter-sidebar / footer / card-components / category-taxonomy | inline CSS 806行（v8 上書き / header 系 6） | Header済 | **あり（内部）**: 8/30 `category-taxonomy.js` 導入・scat 語彙 slug 化（CLOSE後）。回帰101項目の再実行記録 未検出 | ヘッダー帯 diff vs Home = ロゴ部 429px（Home 側起因）＋ nav ハイライト |
| **PC Feed** | LOCK（8/30 Launcher）| 確定 → `pc/myrig-feed-v3.html` ✔ | cx 共有（page-local 1: guest 時の avatar） | F00 コピー **＋ page-local `display:none`**（閲覧アプリ型・コメントあり） | — | app-shell / catalog-v6 / filter-sidebar / footer / card-components / feed-demo-data | inline CSS 782行（v8 上書き / footer 2ルール） | Header済 | 低（B のみ）。**正典側が古い**: CURRENT「B-1 PC Feed #28 未適用」「07-30 Override 実装待ち」だが実装済み（おすすめ/新着/フォロー中 3タブ） | diff vs Home = ロゴ部 429px ＋ nav ハイライト |
| **PC Browse RIG Root** | LOCK（8/31 039） | 確定 → `pc/myrig-browse-rigs-v3.html` ✔ | cx 共有 | F00 コピー | inline 24ルール + arrow JS（**DEPRECATED 表記済み**） | app-shell / catalog-v6 / browse-sidebar 旧+v5 / browse-shell / footer / card-components / browse-* js ×5 | inline CSS 848行（v8 上書き） | Header済 / 棚 → PROPAGATION-SHELF | 低（B のみ −73行） | diff vs Home = ロゴ部 429px のみ（Browse 3面は互いに一致） |
| **PC Browse Category** | LOCK（8/31） | 確定 → `pc/myrig-browse-category-v3.html` ✔ | cx 共有（page-local 0） | F00 コピー | inline 27ルール（うち20は Home と同一）+ arrow JS | 同上 + category-taxonomy | inline CSS 577行 | 同上 | 低（B のみ） | 同上 |
| **PC Browse Parts Root / Motor・ESC** | LOCK（8/31） | 確定 → `pc/myrig-browse-parts-v3.html`（`?category=motor-esc`）✔ | cx 共有（page-local 0） | F00 コピー | inline 24ルール + arrow JS | 同上 + parts-category-demo | inline CSS 845行 | 同上 | 低（B のみ）。`?category=motor-esc` で `src="null"` 404 1件 | 同上 |
| **PC RIG Detail v15** | r8 の見た目のまま（r8 は撤去。デザイン履歴は Git） | **確定 → `pc/myrig-rig-detail-v15.html`**（青＝PC版のみ。サイト内リンク 33本も v15） | 共有 cx の `.cx--quiet` を選ぶだけ（定義は Header SoT） | F00 コピー（共有化は E） | `dt-shelf*` ＋ 共有 `SoT_shelf.js`（`[data-shelf]`） | app-shell / catalog-v6 / footer / card-components ＋ `SoT_detail.css` / `SoT_detail-rail.css` / `SoT_detail-components.js` / `SoT_shelf.js` | **inline `<style>` 0 / インライン `<script>` 0 / inline style 属性 0 / 版番号クラス 0** | **SYSTEM VERIFIED（Gate 2 CLOSE）** | — | contract 31 PASS / 0 FAIL / 0 WARN |
| PC RIG Detail v6 | WIP（要確認・Launcher 未掲載） | — | **create-soft（Legacy・唯一の残存面）** | F00 コピー | — | app-shell / catalog-v6 / footer | inline CSS 66行・`:root` なし → **v7 色（`--cat-rig #66b900`）** | 参照 0（Mobile 3面のコメントに由来の記述が残るのみ） | — | — |
| **MO Home** | LOCK（8/20） | 確定 → `index-e-roomclip.html` ✔ | mobile-shell-header（shell 共有） | なし（bottom nav） | — | css/sot ×3 / mobile-tokens / mobile-shell / mobile-shell.js / browse-directory | inline CSS 75行（v8 `:root` 重複宣言） | 対象外（Mobile shell） | **あり**: 8/30 `mycat-digest` / `mycat-more` / `mycat-encounter` 追加（既定 hidden・裁定記録なし） | — |
| **MO Search top / results** | LOCK（8/25 CLOSE） | 確定 → `search.html` / `search-results.html?q=TRX-4` ✔ | mobile-shell-header--search | なし | — | 同上 + category-taxonomy | inline CSS 234 / 218行 | 対象外 | **あり（内部）**: 8/30 catsheet を taxonomy 生成へ・slug 化（CLOSE後） | — |
| **MO Feed** | LOCK | 確定 → `feed.html` ✔ | mobile-shell-header | なし | — | 同上 + feed-demo-data | inline CSS 28行 | 対象外 | 8/30 +124/−53（Feed確定前後。判別不能） | — |
| **MO Browse 4面** | LOCK（8/31） | 確定 → `browse-rigs / -category / -parts(+?category)` ✔ | なし（`bp-*` ヒーロー） | なし | `bp-shelf`（mobile-shell.css 共有） | 同上 + browse-directory / category-featured / parts-category-demo | inline CSS 0 / 0 / 55行 | 対象外 | **なし**（8/31 以降変更 0） | 8/31 チェッカー 214/201 FAIL 0（記録） |
| MO RIG Detail | WIP | 要確認 → `rig-detail.html` | `rd-subhdr` | なし | `rd-shelf` | mobile-shell | inline CSS 284行 | 未着手 | — | — |

pageerror: 上記 20 ロード（PC 9 / Mobile 9 / Launcher / compare）すべて **0**。

---

## 3. Shared Component State

| Component | SoT（物理） | 適用済み consumer | 未適用 / 逸脱 | 二重管理 |
|---|---|---|---|---|
| Header cx | `pc/assets/css/SoT_app-shell.css`（cx 25 + app-nav 5）/ `SoT_app-shell.js` `initCreateMenu()` | pc/*.html 44 中 31 面に cx markup。確定7面は inline cx 0 | **✅ 解消: r8 の `.cx__btn` 6ルールは `.cx--quiet` variant として Header SoT へ移設済み** / **Home: header 系 page-local 17ルール（`.app-logo--image` !important 等）→ ロゴ・タグラインが他6面と約2px不一致** / Search: サジェスト 6ルール（機能） / v6: create-soft（DEPRECATED 明記済み） / `v14r3-rail-tuning-patch`: 同型上書き（孤児） | `css/sot/SoT_app-shell.css`（460行）と **179行乖離**（8/29 の 103行から拡大）。Mobile 42面が読むが `.app-header` markup 0面 |
| Footer F00 | `SoT_footer.css`（261行・PC/mobile 一致）＋ `sot-templates/SoT_footer.html`（include 機構なし） | 33面が markup コピー。確定6面 + v6 + preview = **同一（md5 一致）** | **v15 は独自版**（r8 由来。差分: aria-label / SVG 簡略化）。テンプレ自体も8面版と微差。Feed は page-local `display:none` | markup が 33面複製（Batch E スコープ） |
| Shelf（挙動） | **`pc/assets/js/SoT_shelf.js`（2026-09-03 新設）** | v15（`[data-shelf]` / step=viewport） | Home ＋ Browse PC 3面は page-local JS のまま（**DEPRECATED 表記済み**） | PROPAGATION-SHELF で解消 |
| Shelf（見た目） | base `.shelf` 5ルール（catalog-v6。Detail 用ではない） | Home / Browse 3面（page-local 51 / 27 / 24 / 24 ルール） | v15 は `dt-shelf*`（`SoT_detail.css`） | **2系統。統合は Gate 4 で裁定** |
| Detail Gallery / Comment / Rail | **`SoT_detail.css` / `SoT_detail-rail.css` / `SoT_detail-components.js`（新設）** | v15 | PARTS / LOG Detail は未着手（D） | Comment / Modal は catalog-v6 の同名部品と2系統 → Gate 4 |
| Color token | `SoT_tokens-v6.css` = **v7**（`--cat-rig #66b900`）。Mobile は `css/mobile-shell.css` = v8 | Mobile 全面 v8 | PC 確定7面 + r8 は page-local `:root` で v8 上書き（既知 PENDING → F）。v6 は v7 のまま | `pc/assets/css` ⇄ `css/sot`: tokens 0 / footer 0 / catalog-v6 4行 / app-shell 179行 |
| Launcher 状態語彙 | `index.html` 5値（確定 / PC版のみ / モバイルのみ / 要確認 / 未着手） | — | LOCK / MIG / VERIFIED の区別を持たない（「確定」1段） | `compare.html` PAIRS が別定義（手動同期） |

---

## 4. 未処理の指摘（open issues）

凡例: **[事実]** = 実測 / **[推測]** = 根拠つき推定 / **[未確認]** = 調べたが判定不能
※ 解決済みの項目は残さない（経緯は CURRENT と Git 履歴）。

| # | レーン | 内容 | 状態 |
|---|---|---|---|
| 1 | PROPAGATION-HEADER | **[事実]** PC Home に header 系 page-local 17ルール（`.app-logo--image` !important 等）。Batch B の除去対象（cx / app-nav / ダーク地）に `.app-logo` が入っておらず、ロゴとタグライン「RC GARAGE」が他6面と**約2px不一致**。B の出口条件が面内 before→after だったため面間差が素通りした | 未着手。Gate 2 の後 |
| 2 | PROPAGATION-SHELF | **[事実]** 棚の page-local 複製が Home 51 / Browse-cat 27 / Browse-rigs 24 / Browse-parts 24 ルール ＋ 各面の arrow JS。挙動の後継 `SoT_shelf.js` は用意済み・4面とも DEPRECATED 表記済み | 未着手。Gate 2 の後、4面それぞれ parity |
| 3 | Regression | **[事実]** Search は 8/25 CLOSE 後に `category-taxonomy.js` で `scat` を slug 化した（内部語彙の変更）。`_state/search_regression.py` は旧語彙を前提にしており **13項目目で停止する**（2026-09-03 実走。そこまでは 12 PASS / 0 FAIL）。スクリプト側を slug へ追随させて 101項目を再実行するまで CLOSE の証跡は不完全 | 未実施 |
| 4 | 記録のみ | **[事実]** PC Home は 8/20 確定後に `explore-strip`（markup 5・CSS）新設と density pass（8/26）が入っており、裁定・HANDOFF に記録が無い。**[推測]** 当時の「イタヤがライブを見て指摘 → 実装」運用で入った。**[未確認]** 承認の有無。MO Home も 8/30 に `mycat-digest` 系（既定 hidden）を追加、記録なし | **Home は再設計しない。** 記録欠落として残す |
| 5 | E | **[事実]** Footer markup が 33面に複製。v15 版（r8 由来）は他8面と不一致（aria-label / SVG 簡略化）。`SoT_footer.css` は共有だが markup は include 機構なし | Batch E |
| 6 | E | **[事実]** `SoT_app-shell.css` が `pc/assets/css` と `css/sot` で **179行乖離**（8/29 の 103行から拡大。B は PC 側だけ更新した） | Batch E |
| 7 | E | **[事実]** 参照 0 の孤児2件: `pc/myrig-rig-detail-gpt-zero-concept.html`（1.9MB）/ `pc/myrig-rig-detail-v14r3-rail-tuning-patch.html`（v14r2 用の patch 断片・base も r8 も撤去済み）。**[未確認]** 意図的な残置か | Batch E |
| 8 | E | **[事実]** 404 3件: PC Home の画像1（`:` を含むファイル名・既知）/ `browse-parts?category=motor-esc` の `src="null"` / MO browse-rigs の `2304_mickeyrock_3.webp` 欠落 | Batch E |
| 9 | F | **[事実]** PC の v8 適用が7面のみ（page-local `:root`）、共有 `SoT_tokens-v6.css` は v7 のまま。正典 `color-token-v8.md` は「PC未適用・部分適用禁止」 | Batch F |
| 10 | Gate 4 | **[事実]** Detail の 棚 / Comment / Modal が `dt-*`（`SoT_detail.css`）と catalog-v6 の同名部品の **2系統**。catalog 側は Detail 用ではなく（`.shelf__rail` は `gap:16px` と `--page-side-padding`）、名前を寄せると確定済みの見た目が動く | PARTS Detail 着手前に裁定 |
| 12 | ✅ 解消 | 044 DECISION の `data-widget` / `data-section` は Gate 2 再監査の裁定で **C の最後に markup へ降ろした**（右レーン9 / RELATED 5。`data-order` は持たない）。識別子は CURRENT 参照 | 済 |
| 11 | 記録のみ | **[事実]** Batch B「6面 pixel parity 0px」の証跡ファイルはリポジトリ内に無い（CURRENT 本文の記載のみ）。面内 before→after であって面**間**の一致は測っていない | 再撮影しない。#1 で解消する範囲だけ扱う |

---

## 5. Impact List（共有部品を触るときの影響範囲）

| 変更 | 影響 consumer | 分類 |
|---|---|---|
| `SoT_detail.css` / `-rail.css` の変更 | RIG Detail v15（将来 PARTS / LOG Detail） | 詳細3面の横断変更。Gate 4 / 5 |
| `SoT_detail.css` の Detail cx variant を変える | 詳細面のみ。**共有 cx（31面）には波及しない**（詳細面だけが本ファイルを読む） | Detail 内の DESIGN 変更 |
| 共有 `.cx__btn`（`SoT_app-shell.css`）を変える | cx markup 31面。確定7面を含む | **横断デザイン変更 → 別裁定** |
| `SoT_shelf.js` の挙動を変える | v15（＋ PROPAGATION-SHELF 後に Home / Browse 3面） | 内部変更 → 全 consumer で parity |
| Home の `.app-logo` 上書き 17ルールを除去 | Home のみ。約2px 動く | LOCK 面の見た目が動く → parity ではなくイタヤ確認 |
| catalog-v6 の `.shelf` / `.cmt-modal` を変える | Home / Browse / 各面（Detail は `dt-*` なので影響なし） | 横断変更 → 別裁定 |
| Footer 共有化 | 33面 | Batch E |

---

## 6. 状態語彙の現在割当

| 状態 | 該当 |
|---|---|
| VISUAL LOCK ＋ SYSTEM VERIFIED | Home PC/MO・Search PC/MO・Feed PC/MO・Browse PC 4 / MO 4（Header B の parity を VERIFIED と読む場合。証跡の但し書きは §4-11） |
| **VISUAL LOCK のみ** | **RIG Detail PC r8** |
| **MIGRATING** | **RIG Detail PC v15**（parity 54 PASS / 0 FAIL・Gate 2 待ち） |
| WIP | RIG Detail MO / PARTS Detail / LOG Detail ほか Launcher 橙 45枚 |

Launcher の集計（実測）: 確定 12 / PC版のみ 1 / 要確認 45。

---

## 7. 検証環境（再実行のしかた）

```
# 詳細面（v15・将来の PARTS / LOG）を変更したら必ず通す
LD_LIBRARY_PATH=$HOME/.local/lib python3 _state/detail_contract_check.py
LD_LIBRARY_PATH=$HOME/.local/lib python3 _state/detail_contract_check.py --selftest
# ページを足すとき: 第2引数以降に相対パスを並べる
LD_LIBRARY_PATH=$HOME/.local/lib python3 _state/detail_contract_check.py pc/myrig-parts-detail-vX.html
```

移行専用だった `build_detail_system.py` / `detail_parity.py` / `parity_out/` は
Gate 2 CLOSE で r8 ごと撤去した。当時の実行方法とログを見るなら `ec3398c^`。

- Playwright は `pip install playwright --break-system-packages` → `python3 -m playwright install chromium`。
  `libXdamage` が無いので `apt-get download libxdamage1` → `dpkg-deb -x` → `~/.local/lib` を `LD_LIBRARY_PATH` に置く
- **`/tmp` はセッション内でも消えることがある。** 作業ファイルは `~/` か mount 先に置く
- parity の PNG は `.gitignore` 済み（1枚で数MB）。判定結果は `_state/parity_out/parity_result.json` と `parity_log.txt`
- **判定は「同一ツリー2回撮影のノイズ床」と比べる。** 写真の縁のラスタライズ差が毎回数十px出る
