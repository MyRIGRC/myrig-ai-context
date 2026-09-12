# 裁定記録: Web Fundamentals Recovery Batch 1 — 093 CLOSE 失効と「操作できるか」Gate（2026-09-12）

- 裁定: GPT（監査結果の裁定）／実装・検査: Cowork（Claude）
- モック: `myrig-mockup` `17f4210`／正典: CURRENT 094・Region Behavior Matrix v1.2
- 種別: 失効（093 CLOSE / M nav sticky 契約）＋ 新契約（static 統一・Overlay 層）＋ 新 Gate（hit-test）

## なぜ 093 の CLOSE は失効したか

093 は「W 静止画 pixel 一致・DOM/Tab 順・G20・WM7 550 PASS・Mac 14 本 4,089 PASS」で Garage M を閉じた。
独立反証レビュー（cloud 34 面 × 95 幅 ＋ Mac 内蔵ブラウザ）で、**同じ帯でクリックできない**ことが分かった。

| | 何を踏んだか |
|---|---|
| R-01 | `SoT_app-shell.css` §3.4 は ≤1024px で `.drawer-overlay{display:block}` にする（open/close の opacity transition のため）。base は `display:none; opacity:0` で `pointer-events` 未指定。結果、**閉じていても透明な全画面 fixed（z 65）が content の上に残り**、Header（z 80）以外の全 pointer 操作を奪っていた。Garage 変更起因ではなく既存 Shell 欠陥。JS（`initDrawer`）は close 後 `style.display=''` に戻す設計で、CSS 側が block を強制していた |
| R-03 | M の Nav を `position:sticky` にしたが、**Chrome では grid item の sticky 拘束矩形が grid area ではなく grid container**。row 3 の Nav が row 4（全幅の defer）まで降りて RECENT ACTIVITY / LATEST LOGS の見出しとサムネイルを遮蔽した。W で案A（sticky）を落とした理由と同系統の現象を M に残していた |
| R-04 | 「Public は従来挙動（sticky + flex）を維持」として PNF の上書きを Own だけに限定した。結果、≤720 で aside（Profile＋Nav 約 520px）が sticky のまま全幅化し、scroll 中に本文と LATEST LOGS を覆っていた。CURRENT 093 の契約表「PNF = static」と Matrix §5 の記述も矛盾していた |

**共通の失敗**: 「見える」「DOM にある」「位置が正しい」「Tab 順が正しい」を「操作できる」と誤認した。
4,089 PASS のうち M 帯を assert していたのは WM7（550）だけで、それも幾何・DOM 順のみ。hit-test は 1 本も無かった。

## 裁定

1. **M の Nav は sticky → static。** sticky は主要機能ではなく利便性。現 Grid では拘束が defer まで伸び、
   W でも同系統で sticky 案を不採用済み。wrapper を足して sticky を守るより static が単純・予測可能・回帰リスクが低い。
   モック段階で複雑性を増やさない。契約: **W static / M static / PC Narrow Fallback static**。
2. **PC Narrow Fallback は static・1 列を Own / Public 共通契約**とする。Public だけ従来 sticky を維持する例外は廃止。
3. **Overlay 層の成立条件**: 閉状態は content の pointer 操作を遮らない（閉 `pointer-events:none` / 開 `auto`）。
   z-index 等の具体値は正典化しない。
4. **hit-test を Gate に必須化**（`_state/hit_test_check.py`）。「透明 overlay 等が前面にいれば FAIL」を直接検知する。
5. **selftest は故障を 1 種ずつ注入**し、どの Gate がどの故障を検知したかを対応表で出す（同時注入の FAIL 総数は和集合であり検知力の証明にならない）。
6. **Recovery Batch 1 が PASS しても Garage M / Web Fundamentals は再 CLOSE しない。** Global Shell が M で閉じていない
   （hamburger は Home / Browse 4 面でしか drawer を開かず、`.app-nav` が M で消える = R-02）。次は Recovery Batch 2（Common Drawer）。

## 実装で踏んだ罠（次に触る人へ）

- **Home / preview の page-local 複製を消すと drawer が壊れる**: 両面の inline `<style>` は共有 CSS より後に読まれ、
  `.home-dir.home-dir--framed{position:sticky!important}`（同特異度 0,2,0）が §3.4 の `position:fixed!important` に後勝ちする。
  以前は複製ブロックがさらに後ろで fixed を再適用していたので隠れていた。対処は共有側 §3.4 の drawer 規則を
  `body .home-dir.home-dir--framed`（0,2,1）にして順序に依存しないこと。§3.3（≥1025）は上げない — Home の W レールが
  sticky なのは page-local の仕様のまま。
- **ラスタライズ差は幾何差ではない**: sticky 層を解除すると M / PNF のスクショに数千 px の差が出るが、全要素の小数 px rect は
  0 差。判定は幾何（DOM rect）で行い、pixel は W のロック幅（1440 / 1000 等）だけで見る。
- **cloud で 14 本を流すには Chromium の proxy を外す**: 環境の HTTPS_PROXY を経由すると外部 font / 画像への接続が
  タイムアウトまで待つ。`--proxy-server=direct://` ＋ `--host-resolver-rules="MAP * ~NOTFOUND, EXCLUDE 127.0.0.1"` で即失敗させる。
  image_integrity / mobile_feed / mobile_detail / mobile_garage_detail の FAIL は cloud 環境依存（Mac が正）。
- **`hit_test_check` の偽陽性を潰した条件**: 閉じた drawer 内（画面外）／閉じたアコーディオン内（祖先 overflow で不可視）／
  開いている dialog の背後（塞がれているのが正しい）は対象にしない。sticky Header の下に中心が来る場合は Header 下端＋4px で判定。
- **settings の `.cover-change-btn`** は後続 `.profile-lower`（relative / margin-top:-32px）に下 2/3 を覆われていた。
  hit-test Gate が拾った既存欠陥で、`z-index:1` の最小修正。同種（描画順で隠れる操作点）は今後も Gate が拾う。
