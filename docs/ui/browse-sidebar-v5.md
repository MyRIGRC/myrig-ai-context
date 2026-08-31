# Browse Sidebar V5 + Breadcrumb 正典

> **拘束力: L2（現在の確定仕様）**
>
> **作成:** 2026-08-29 (JST) / **確定:** 2026-08-30 (JST) — イタヤ裁定 BROWSE-CONTRACT-003
> **主査:** Cowork（Claude）
> **ベース:** `myrig-sidebar-gpt-proposal-v4-final.html`（V4・イタヤ採用）＋ V5差分 ＋ 8/30 確定分
> **実装:** `pc/assets/css/SoT_browse-sidebar-v5.css`（base `SoT_browse-sidebar.css` の**後**に読む）/ `js/browse-sidenav.js`
> **検証:** `_audit/browse_sidebar_v5_check.py`
> **対象:** Sidebar / Breadcrumb / RIG ROOT / WORLD階層 / Root current
> **非対象:** カード内部・棚のコンテンツ・カードvariant・棚レイアウト
> （`browse-display-contract-v1.md` の既存契約を維持し、V5に巻き込まない）
>
> 🔴 **2026-08-30 / revision 037: 036 の CLOSE を一部取り消して再オープン中。**
> 実画面レビューで、4軸のラベルが「何を基準に集まっているか」を説明していないことが
> 判明したため、**「4軸の意味・表記」と「Root current の見せ方」だけ**を再オープンした。
> **他の PC Browse デザイン（WORLD selector / Root entry 文法 / Breadcrumb /
> Directory / 表札 / 色 / 基準画面）は凍結したまま。**
> 裁定原本: `_decisions/2026-08-30_browse-axis-relation-view-v1.md`

**この文書の書き方の方針:** px値・余白量そのものを正典にしない。
**構造と意味**を記録し、寸法の合否判定は `_audit/browse_sidebar_v5_check.py` に持たせる。
数値を文書へ書き写すと、実装が動いたときに文書が黙って嘘になるため。

---

## 0. V5の核心 — HOMEはBrowse全体のRootではない

これまで「HOME」と呼んでいた現在のBrowse HOMEは、Browse全体の抽象的なRootではない。
**「RCカー / RC CARS」という1つの大きなWORLDのトップ**である。

```
Browse
├ RCカー / RC CARS               ← WORLD ROOT（現在のHOME）
│  ├ すべてのRCカー               ← RIG ROOT
│  │   ├ Rock Crawler            ← RIG CATEGORY
│  │   └ Buggy …
│  └ すべてのパーツ               ← PARTS ROOT
│      ├ モーター・ESC            ← PARTS CATEGORY
│      └ タイヤ・ホイール …
├ ミニ四駆 / MINI 4WD             ← 将来のWORLD（RCカーと同格）
├ ドローン / FPV
├ RC飛行機 / RC AIRCRAFT
└ RCボート / RC BOAT
```

**「RCカー」と「すべてのRCカー」は別階層。** 前者は WORLD、後者は RIG ROOT。

このV5差分は世界観を増やすための装飾ではなく、
**将来ミニ四駆・ドローン・飛行機・ボートが追加されても情報構造が壊れないための整理**である。

---

## 1. 「Browseトップ」という独立行は作らない

`BROWSE / トップ` のような独立Root entryは追加しない。
BrowseはApp Header上の大きな機能概念であり、Sidebar内の現在地として必要なのは
**現在表示しているWORLD**（現在は「RCカー」）である。

---

## 2. WORLD selector（HOME Sidebar 上部）🔴 2026-08-30 確定

HOME Sidebar の Directory より上に置く。
**「現在表示中のWORLDを掲示するカード」ではなく「WORLDを切り替えるセレクタ」**として見せる。

```
WORLD
┌────────────────────────┐
│ RCカー               ⌄ │   ← button[aria-haspopup="listbox"]
│ RC CARS                │
└────────────────────────┘
      ↓ クリック
┌────────────────────────┐   ← ul[role="listbox"] / overlay
│ ✓ RCカー    RC CARS     │
│   ミニ四駆   MINI 4WD  公開予定 │  ← disabled
│   ドローン / FPV        計画中 │  ← disabled
│   RC飛行機              計画中 │  ← disabled
│   RCボート              計画中 │  ← disabled
└────────────────────────┘
```

### 確定仕様

| 項目 | 確定 |
|---|---|
| 表示 | `RCカー` ＋ `RC CARS` ＋ **chevron**（切り替えられると分かること） |
| 閉状態の面 | 白〜ごく薄い無彩色 ＋ 細い無彩色 border |
| dropdown | **overlay（`position: absolute`）**。RIG以下のレイアウトを押し下げない |
| 開閉 | クリックで開閉 / 外側クリックで閉 / Escape で閉 |
| 将来WORLD | RCカー以外は **disabled**。選べるように見せない |
| ARIA | `aria-haspopup="listbox"` / `aria-expanded` / `role="listbox"` / `role="option"` |
| 実装スコープ | `.sb-wsel*` に閉じる。Sidebar本体 / Directory / Root entry / OTHER WORLDS に触れない |

### 🔴 禁止（再発防止のため必ず維持）

- **黒ベタ / 黒帯 / 反転文字 / 黒い selected chip**
- **WORLD と RIG をつなぐ縦棒・connector・stem・tree line**（余白だけで関係を示す）
- **「現在表示中」文言 / status dot**
- **カテゴリ色**（RIG黄 / PARTS赤 / LOG青）を WORLD ブロックに使うこと
- **dropdown で RIG 以下を押し下げること**
- **Sidebar 全体の CSS を書き換えて実現すること**

> **失効:** 旧 §2「CURRENT WORLD カード」（`.sb-world-card` / border 2px で current を表す /
> OTHER WORLDS と高さ・角丸を揃える / 「現在表示中」ラベル）は 2026-08-30 に**失効**。
> 関連CSS（`.sb-world-card` / `.sb-world--stem` / connector線）も実装から撤去済み。
> 「カードで現在地を掲示する」案は、WORLD が1つしか無い現状では
> 「切り替えられること」が伝わらないため採らない。

---

## 3. OTHER WORLDS

`ミニ四駆 / ドローン・FPV / RC飛行機 / RCボート` は **RCカーの子カテゴリではない**。
RCカーと同格の将来WORLDである。V4の独立カード文法を維持し、通常のDirectory rowへ戻さない。
現段階では非リンク（`pointer-events: none`）。将来その大ジャンル用ページが実装された時点でリンク化する。

> WORLD selector の dropdown と OTHER WORLDS カードは、同じ4件を別の役割で出している。
> selector は「切り替え操作」、OTHER WORLDS は「これから増える予告」。
> 重複に見える点は認識しているが、統合は行わない（PENDING 扱い・§17）。

---

## 4. Sidebar current の5状態 🔴 2026-08-30 確定

Sidebar には **WORLD selector / 4軸 / Directory** の3系統の現在地表示がある。
面ごとの正しい組み合わせは以下。

| 面 | WORLD selector | Directory current |
|---|---|---|
| RCカー HOME | `RCカー`（selector の選択値） | **なし**（RIG/PARTS内にcurrentを付けない） |
| RIG ROOT | — | **すべてのRCカー**（Root current） |
| Rock Crawler | — | ロッククローラー（子カテゴリ current） |
| PARTS ROOT | — | **すべてのパーツ**（Root current） |
| Motor・ESC | — | モーター・ESC（子カテゴリ current） |

5画面すべてで階層が直感的に異なって見えること。

### 反証（この5状態と同じくらい重要）

- **HOME で `すべてのRCカー` / `すべてのパーツ` を current にしない**
- **Rock Crawler / Motor・ESC で Root entry を current にしない**
  （Root current を足したときに、Root まで一緒に光らせない）

`_audit/browse_sidebar_v5_check.py` はこの反証側も機械判定する。

---

## 5. RIG ROOT を必ず用意する

PARTS ROOT「すべてのパーツ」が独立したBrowseページとして存在するなら、
RIG ROOT「すべてのRCカー」も独立ページとして存在させる。**両者は対称構造。**

| | 役割 |
|---|---|
| RIG ROOT | RCカー世界のRIG全体を見るトップ |
| PARTS ROOT | RCカー世界のPARTS全体を見るトップ |

実体: `pc/myrig-browse-rigs-v3.html`（2026-08-29 V5で新設）。

> 従来はPC HOMEがRIG ROOTを兼任していた。V5でこの兼任を解消した。

**RIG ROOT は Rock Crawler Category Top の別バージョンではない。**
本文は特定カテゴリに寄せず、OFF-ROAD / ON-ROAD / SCALE & SPECIAL が
最初の1〜2スクロールで混在して見えること。RIG棚にPARTSカードを混ぜない
（詳細は `browse-display-contract-v1.md` §11）。

---

## 6. Rootにもローカル主ナビを置く（本数はDomain内継承で決める）

**旧 `browse-display-contract-v1.md` §6.1「RIG Root / Parts Root はローカルタブを置かない」は失効。**

🔴 **2026-08-30 / revision 037: 4軸は「投稿種別の切替」ではなく
「現在のスコープを起点にした関係ビュー」である。**

🔴 **2026-08-31 / revision 039: 軸の本数は「Domain内のscope継承」で決める。**
裁定原本: `_decisions/2026-08-31_browse-domain-scope-inheritance-v1.md`

| Domain | 面 | スコープ | ローカルナビ |
|---|---|---|---|
| **RIG** | RIG ROOT（すべてのRCカー） | RCカーWORLDの全RIG | **トップ / RIG / パーツ / LOG** |
| **RIG** | RIG CATEGORY（Rock Crawler） | Rock CrawlerのRIG | **トップ / RIG / パーツ / LOG** |
| **PARTS** | PARTS ROOT（すべてのパーツ） | RCカーWORLDの全パーツ | **トップ / パーツ / LOG** |
| **PARTS** | PARTS CATEGORY（モーター・ESC） | モーター・ESCのパーツ | **トップ / パーツ / LOG** |

### 判断基準は2階建て【L1】

| 方向 | 基準 |
|---|---|
| **Domain内（Root → Category）** | 🔴 **継承する。** 需要判定を持ち込まない |
| **Domain間（RIG系 ↔ PARTS系）** | **閲覧需要の非対称性で決める。** 揃えない |

**Root → Category で変わるのは scope（母集団）だけ。見られるものの種類は変えない。**
子は親の部分集合なので、**子が親より閲覧できるものが多い構造を作らない**。

```
すべてのRCカー → パーツ  = 全RIGに現在装着されているパーツ
Rock Crawler  → パーツ  = Rock CrawlerのRIGに現在装着されているパーツ
```

Domain間の非対称は維持する。`すべてのパーツ → 装着RIG` `モーター・ESC → 装着RIG` は
主ナビにしない（装着RIGは発見棚で足りる）。

| | 性質 | 置き場所 |
|---|---|---|
| **LOG** | **閲覧モード**（その場所に関する活動を見る） | **全面のナビに常設** |
| **PARTS系 → RIG** | **発見コンテンツ**（眺めて気づく） | **棚** |

🔴 **「深さで軸を変えない」とは書かないこと【L1】**
HOME にローカルナビが無いことと矛盾する。HOME は Domain の外側（WORLD入口）であり、
継承の話が始まるのは Domain Root から。**正しい言い方は「同一Domain内では継承する」。**

🔴 **全面共通の「4軸」は復活させない【L1】**
RIG系4本 / PARTS系3本の非対称は意図的。entity の対称性で軸を決めない。

> **037 からの改訂（L1改訂）:** 037 は「面ごとに本数が変わる」を掲げ、
> RIG ROOT を `トップ / RIG / LOG` としていた。基準そのものではなく**適用範囲**が誤りで、
> Domain間の基準を Domain内の縦方向にも使った結果、
> **RIG ROOT だけが子より閲覧能力の低い親**になっていた。
> 2026-08-31 の PC/Mobile 4面同時比較で段差が可視化されたことが改訂根拠。
> 失効範囲の全一覧は裁定原本 §2。

> **039 で失効:** 「Root は構造上のHubなので軽くする」。
> Root も同じDomainのCategoryと同じ本数を持つ。役割差は scope と起点entityが担う。

🔴 **PARTS系の LOG軸は UI として採用するが、データ取得方式は PENDING。**
意味は「当該パーツ／パーツカテゴリについて記録されたLOG」。現行DBに part↔log の
直接関係が無いため、**`装着RIG → そのRIGの全LOG` を代替して「関連LOG」と偽装しない**（§6.1-c）。

**ラベルは素の `RIG` / `パーツ` / `LOG` のみ。ナビ側で関係を修飾しない。**
厳密な関係は押した先の棚見出し・サブ・表札が担う。

**WORLD ROOT（HOME）にはローカルナビを置かない。**

主従: **ナビcurrent ＞ Directory current**（§16）。

関係の定義・件数・`装着RIGのLOG` と呼ぶ理由は `browse-display-contract-v1.md` §6、
裁定原本は `_decisions/2026-08-30_browse-axis-relation-view-v1.md`。
押したときにどの棚が残るか（表示グループ）は §12 と
`_decisions/2026-08-30_browse-axis-display-groups-v1.md`（**有効**）。

### 🔴 ナビは「何を見るか」だけ。厳密な関係は押した先で説明する【L1】

**ラベルに定義を全部しゃべらせない。**

| 場所 | 役割 |
|---|---|
| 4軸ラベル | **何を見るか**だけを短く示す |
| 押した先の棚見出し・サブ | **どういう関係で集まっているか**を厳密に説明する |

- PARTS系で `LOG` を押したら → 見出し **`装着RIGのLOG`** ＋ サブ「このパーツを現在装着しているRIGに紐づくLOG」
- RIG系で `LOG` を押したら → 見出し **`RIGのLOG`** ＋ サブ「このスコープのRIGに紐づくLOG」
- `パーツのLOG` `関連LOG` は棚見出しでも使わない

> **失効:** `使用パーツ` / `装着RIG` / `RIGのLOG` / `装着RIGのLOG` をナビラベルに置く案は失効。
> 実画面で「仕様書としては正解、ナビとしては重い」と判断した
> （`装着RIGのLOG` は1語に「装着」「RIG」「LOG」の3概念が積まれている）。
> これらは**棚見出しへ移した**。
> あわせて「4軸はページによって出たり消えたりしない」も失効（§6 冒頭）。

---

## 7. 表札タイトル

カテゴリ色は使わず、**文字そのものの存在感**で見せる。

- 濃い無彩色 / 太い（900相当）/ letter-spacing はやや詰める
- **Sidebar内の利用可能幅を使い切る。** 2行になってよい
- 名称の長さに応じて3段階で大きさを変える（`data-len="short|medium|long"` を
  `js/browse-sidenav.js` が文字数から付与）

**名称が長い場合に小さい枠へ無理に押し込まない。**
重要なのは「ここが現在地の表札である」と一瞬で認識できること。

> Google Fonts から **weight 900 を実ロードすること。**
> 未ロードだとブラウザが合成太字を作り、他面と太さが揃わない。
> `font-synthesis-weight: none` を併用する。

---

## 8. Kicker と説明は脇役

```
RIG CATEGORY      ← 補助
Rock Crawler      ← 主役
岩場を走るRIGとビルドの世界  ← 補助
```

---

## 9. Root entry 文法 と Root current 🔴 2026-08-30 確定

`すべてのRCカー` / `すべてのパーツ` は通常の子カテゴリではない。

### 9-1. Root entry（V4から維持）

- 子カテゴリより1階層上
- **インデントなし**（子は1段内側）
- 少し高い / font-weight 強め
- 件数の前に細い縦区切り
- 下に区切り線

```
RIG
すべてのRCカー      | 3,241
──────────────────
OFF-ROAD
  ロッククローラー   1,204
```

### 9-2. Root current 🔴 2026-08-30 / revision 037 で確定（036 の専用デザインは失効）

RIG ROOT / PARTS ROOT では Root entry 自身が現在地である。
**その current は、子カテゴリ current と同じ表示文法にする。Root 専用のデザインを持たない。**

| current 時に揃えるもの（`ロッククローラー` / `モーター・ESC` と同一） |
|---|
| 文字サイズ / weight / 行高 / 左右 padding / 薄い角丸背景 / 左の細い neutral バー / 件数表示 |

**current 時に使わないもの**

- Root 専用の全面帯（横長のベタ面）
- 件数前の縦区切り
- 下罫線の濃色化

**非current 時の Root entry 文法は §9-1 のまま維持する。**

Root であることは、`RIG` / `PARTS` 見出しの直下という位置、Directory の上という配置、
ページ上部の表札で既に伝わる。**current だけ別デザインにする必要はない。**

> **失効（036）:** 「ごく薄い無彩色の面 ＋ 下罫線の色を一段濃く」「左の縦アクセントは付けない」
> 「Root entry の寸法を current でも変えない」は 2026-08-30 に失効。
> 実画面で、全面に地色が乗って**横長の帯**に見え、文字が左端に詰まり、
> **Root だけ別種類の current UI** に見えていた。
> 「階層が上だから current も強くする」が過剰だった。
>
> **実装上の注意:** `.myrig-sidebar` は `overflow: auto`。左バーを内余白の外へ
> 負オフセットで逃がす案はクリップされ、横スクロールも誘発するので成立しない。
> **current 時に内余白を取る**のが唯一の実装。

---

## 10. Breadcrumb — WORLD階層を反映する

| 面 | Breadcrumb |
|---|---|
| RCカー HOME | `Browse › RCカー` |
| RIG ROOT | `Browse › RCカー › RIG` |
| Rock Crawler | `Browse › RCカー › RIG › Rock Crawler` |
| PARTS ROOT | `Browse › RCカー › PARTS` |
| Motor・ESC | `Browse › RCカー › PARTS › モーター・ESC` |

**旧「HOMEにはBreadcrumbを出さない」は失効。** HOMEをBrowse全体Rootと考えていた時点の案であり、
HOMEは RCカー WORLD ROOT と整理されたため、HOMEにも `Browse › RCカー` を出す。

### 配置

**Sidebar内には置かない。App Header直下、メインコンテンツ側の最上部。**

```
App Header
────────────────
Browse › RCカー › PARTS › モーター・ESC
Hero / Page Title
Main Content
```

ヒーロー写真の中には原則入れない。背景写真で可読性が変わらず、全Browse面で位置を固定でき、
装飾ではなく構造情報として扱えるため。

### 視覚文法

- 小さく・無彩色・全Browse面で同じ高さ
- 祖先階層は二次色、current は少し濃く **リンクにしない**
- 区切りは薄い `›`
- **カテゴリ色は使用しない**
- Hover時のみ祖先リンクを少し濃くする

---

## 11. Breadcrumb と Sidebar の役割

| 要素 | 役割 |
|---|---|
| Breadcrumb | サイト全体の階層上の現在地 |
| Sidebar上部 | 現在WORLD / 現在カテゴリ |
| 4軸 | その場所で何を見るか |
| Directory | 別カテゴリへ移動 |

両方表示してよい。重複ではなく役割が違う。

---

## 12. 色の使用

RIG黄 / PARTS赤 / LOG青 を Sidebar の階層説明に反復使用しない。
用途は **登録CTA / カードの小さな種別バッジ / 必要な主要Action** に限定する。

- ライトモードで黄色を current マーカーや表札背景に使わない
- 黄色背景を使う場合、文字色は必ず黒系
- 登録CTAに「＋」を付けない

---

## 13. HOME Directory の順序

```
WORLD
[ RCカー / RC CARS  ⌄ ]      ← WORLD selector（§2）

RIG
すべてのRCカー                ← current にしない
  OFF-ROAD / ON-ROAD / SCALE & SPECIAL

PARTS
すべてのパーツ                ← current にしない
  モーター・ESC …

OTHER WORLDS
[ ミニ四駆 ][ ドローン / FPV ][ RC飛行機 ][ RCボート ]
```

---

## 14. Browse Category Sidebar の順序

```
戻る
現在地表札
4軸主ナビ
登録CTA
（必要ならフィルター）
区切り
Directory
OTHER WORLDS
```

Directory は HOME と**同じ component / data source を共有**する（`js/browse-sidenav.js`）。

> **失効:** 旧記述にあった Directory 直前のキャプション「他のカテゴリを探す」は
> **全Browse面から撤去済み**。直下に RIG / PARTS / OTHER WORLDS の Directory そのものが
> あるため、見出しがあると「検索へ行くのか」「別ページへ行くのか」が曖昧になる。
> チェッカーが存在しないことを検査する。

---

## 15. Directory の順序 — 現在ページ自身の種別が先

| 面 | 順序 |
|---|---|
| HOME | RIG → PARTS |
| RIG系Browse | RIG → PARTS |
| PARTS系Browse | **PARTS → RIG** |

Parts Root でも Motor・ESC でも、PARTS系では PARTS が先。
RIG側も3カテゴリ等で省略せず、HOMEと同じ全分類（RC24＋Parts14）を表示する。

---

## 16. current の強さ 🔴 2026-08-30 確定

```
4軸current  ≧  WORLD selector  ＞  Directory current（Root / 子で同じ）
```

| 種類 | 見せ方 |
|---|---|
| 4軸current | Sidebar内で最も強い（薄グレー面＋境界補強＋太字） |
| WORLD selector | 「今いる世界」を示す操作要素。無彩色・chevronで切替可能と伝える |
| Directory current | より弱い（薄い角丸面＋短い無彩色の左バー）。**Root も子も同じ**（§9-2） |

> 🔴 **2026-08-30 / revision 037: Root current を別段として持たない。**
> 旧「Root current は最も弱い面＋下罫線を濃く」は失効。

**強弱は絶対輝度ではなく Sidebar 地色との相対差で判定する。**
ダークモードでは「地色より少し暗い」方向で同じ序列を作る。
絶対値で見ると、ダークで黒ベタと誤判定する。

**黄色や赤で current を表現しない。**

---

## 17. 触らないもの / 別タスク

カード内部 / 各棚のコンテンツ / カードvariant / 棚レイアウトは
`browse-display-contract-v1.md` の既存契約を維持し、Sidebar修正に巻き込まない。

以下は本文書の対象外。PC Browse V5 の CLOSE には含めない。

| 項目 | 状態 |
|---|---|
| 1024px以下の共通 Drawer | **PENDING** |
| 4軸 visible 判定の実装分散 | **PENDING**（Mobile Browse着手時に共通化検討） |
| `part` / `parts` の語彙2系統 | **PENDING** |
| WORLD selector と OTHER WORLDS の重複 | **PENDING** |
| Mobile Browse / Mobile Rock Crawler の badge | **HOLD・将来議論項目** |
| Dark mode 全体の再設計 | 別タスク |

---

## 18. 検証

`_audit/browse_sidebar_v5_check.py` が機械判定する（PC 1440px / ライト＋ダーク）。
**2026-08-30 時点で 174項目 FAIL 0。**

### 対象5画面

1. RCカー HOME（`pc/myrig-home-v3.html`）
2. RIG ROOT（`pc/myrig-browse-rigs-v3.html`）
3. Rock Crawler（`pc/myrig-browse-category-v3.html`）
4. PARTS ROOT（`pc/myrig-browse-parts-v3.html`）
5. Motor・ESC（`pc/myrig-browse-parts-v3.html?category=motor-esc`）

### 判定項目

- 5画面それぞれで current が §4 のとおり（**反証側も**: HOME / Category で Root を current にしない）
- **Root current が子カテゴリ current と同一の表示文法**（文字サイズ / weight / 行高 /
  左右padding / 背景 / 左バー を実測比較。Root 専用の帯や濃い下罫線が残っていないこと）
- **4軸ラベルが面の系統どおり**（RIG系 = `トップ/RIG/使用パーツ/RIGのLOG` /
  PARTS系 = `トップ/パーツ/装着RIG/装着RIGのLOG`）
- **相互参照軸・LOG軸を押したときに棚が空にならない**
- 関係件数が `使用パーツ ≤ 全パーツ` / `装着RIG ≤ 全RIG` を満たし、同一 scope で一貫している
- 4軸が Root でも存在する / WORLD ROOT には無い / 4軸が Directory より主役
- WORLD selector（chevron / overlay / 開閉 / disabled / 黒ベタ・縦棒・status文言なし）
- Root entry が子カテゴリより上に見える（高さ・太さ・インデント・下線）
- 表札が十分に太い・900が実ロードされている・Sidebar幅を活用
- 黄色が Sidebar 内でちらつかない（種別色面はCTAのみ。`::before` も走査）
- Breadcrumb に RCカー階層が入っている / App Header直下 / Hero外 / 現在地はリンクでない
- グループ見出しに件数がない / 準備中がリンクに見えない / 「他のカテゴリを探す」が無い
- **4軸を押したあと、表示グループどおりの棚だけが残る / トップで通常構成へ復帰する**
- 横スクロールなし / pageerror 0件

### 🔴 チェッカーの原則（L1 として扱う）

- **「クラスが付いているか」ではなく「見た目に差が出ているか」を見る。**
  2026-08-30、`is-current` は付いているのに CSS が打ち消していて Root current が
  見えない状態のまま、チェッカーは 140項目 FAIL 0 で通っていた
- **「押せるか」ではなく「押した結果どうなるか」を見る。**
  同じ理由で、4軸の master 系棚が全部消えていても通っていた
- **検査が空振りしていないことを検査する**（対象棚が0本なら判定は素通りする）
- **チェッカーを足したら、故障を注入して FAIL することを確認してから採用する**

### ダークモード

RCカー HOME / Rock Crawler / Motor・ESC の3面で、
無彩色current・表札・WORLD selector・Root entry が階層を失わないことを確認する。
判定は**地色との相対差**で行う。

---

## 19. 変更履歴

### V5.2（2026-08-30 / revision 037）— 4軸を関係ビューへ / Root current を統一

036 の CLOSE を「4軸の意味・表記」と「Root current の見せ方」に限って取り消した差分。
裁定原本: `_decisions/2026-08-30_browse-axis-relation-view-v1.md`

1. **§6** 4軸を「投稿種別の切替」→**「スコープ起点の関係ビュー」**へ再定義。
   RIG系 `トップ/RIG/使用パーツ/RIGのLOG` / PARTS系 `トップ/パーツ/装着RIG/装着RIGのLOG`。
   Root と Category で軸数を変えず、スコープだけ変える
2. **§9-2** Root current の**専用デザインを失効**し、子カテゴリ current と同一文法へ統一
3. **§16** 強さの序列から Root current の独立段を削除
4. **§18** 判定項目に「4軸ラベル」「相互参照軸・LOG軸が空でない」「関係件数の整合」を追加
5. 036 の**表示グループ裁定（`rig+rig_master` 等）は無改訂で有効**

### V5.1（2026-08-30 / revision 036）— PC Browse V5 CLOSE（→ 一部取り消し）

1. **§2** CURRENT WORLD カード → **WORLD selector（A案）** へ差し替え。旧カード記述は失効
2. **§4** Sidebar current を「WORLD selector / Directory current」の組み合わせとして再定義。反証側を明文化
3. **§9-2** **Root current** を新設。Root entry の寸法を変えずに current を示す
4. **§14** Directory 直前のキャプション「他のカテゴリを探す」の撤去を明記
5. **§16** 強さの序列に Root current を追加し、**地色との相対差で判定**することを明記
6. **§18** チェッカー 140 → **174項目**。4軸の表示グループと Root current の描画を追加。
   チェッカーの原則（L1）を新設
7. **§5** RIG ROOT が Rock Crawler の別バージョンに見えない条件を追記
8. **§17** PENDING / HOLD を明示し、CLOSE の範囲を確定

### V5（2026-08-29）

V4を正典として採用した上での差分。

1. **§0** HOMEを「RCカー」WORLD ROOTと再定義。WORLD / RIG ROOT / PARTS ROOT / CATEGORY の4階層を明確化
2. **§2** HOME Sidebar上部に CURRENT WORLD を新設（→ 8/30 に selector へ改訂）
3. **§5** RIG ROOT（`pc/myrig-browse-rigs-v3.html`）を新設。PC HOMEの兼任を解消
4. **§6** Rootにもローカル主ナビを置く。本数はDomain内継承で決める（旧 contract-v1 §6.1 は失効）
5. **§7** 表札タイトルを強化。文字数に応じた3段階・weight 900・幅を使い切る
6. **§10** Breadcrumb に WORLD 階層を追加。HOMEにも `Browse › RCカー` を出す（旧「HOMEに出さない」は失効）
7. **§18** チェッカーを `browse_sidebar_v4_check.py` → `browse_sidebar_v5_check.py` へ改訂

---

## 🔵 正典化しないもの（2026-08-30 / revision 037）

**文言・余白・表示件数などのUI調整は正典で縛らない。** 実装側で調整可能にしておく。

| 正典化する | 正典化しない |
|---|---|
| どの面にどの閲覧モード（ローカルナビ）が存在するか | 具体的な日本語ラベル（`トップ` `LOG` など） |
| 相互参照をナビにするか棚にするか | 見出し・説明文の言い回し |
| どういう関係を根拠に表示してよいか | 余白・表示件数・棚の並び順 |

後から `トップ` や `LOG` の語を変えたくなっても情報構造は壊れない。
**文言を磨くために構造を開け直さない。**
