# PC Browse 4軸の表示グループ（2026-08-30）— master系が消えた原因と、再発させないための記録

> **拘束力: L2（現在の確定仕様）**
> ただし §3「判定を1か所に持てないなら、検査で塞ぐ」は **L1** として扱う。
> ここを守らないと、判定の分散が別の面で同じ事故を起こす。

裁定日: 2026-08-30 (JST) / 裁定: イタヤ / 実装・検証: Cowork(Claude)
関連: `docs/ui/browse-sidebar-v5.md`（Sidebar V5 正典） /
`docs/ui/browse-display-contract-v1.md` §11（V5差分） /
検査: `_audit/browse_sidebar_v5_check.py`

---

## この記録で残す罠（結論だけ持ち帰らないこと）

「master系を含める」という**結論**より、そこへ至った**構造**の方が再発する。

1. **判定が複数箇所に分散していた** — 同じ規則を5つの実装が別々に持っていた（§2）
2. **基準画面だけが正しく、他面が追随していなかった** — 直したのは1面だけだった（§2）
3. **完全一致判定では master 系の棚が消える** — `rig_master` は `rig` に一致しない（§1）
4. **`data-entity-type` は書き換えず、visible 判定側でグループ化した** — 棚の意味を潰さない（§1）
5. **共通化できない期間は、正式チェッカーで再発を塞ぐ** — 分散を許すなら検査で担保する（§3）

---

## 0. どういう作業だったか

PC Browse V5（HOME / RIG ROOT / Rock Crawler / PARTS ROOT / Motor・ESC の5面）の
最終レビューで、**4軸主ナビを押すと master 系の棚が消える**ことが見つかった。

| 面 | 消えていた棚 | entity | 押した軸 |
|---|---|---|---|
| RIG ROOT | みんなのRIGに多い車種 | `rig_master` | RIG |
| PARTS ROOT | Tires & Wheels / Electronics & Power | `part_master` | パーツ |

**同じタイミングで `browse_sidebar_v5_check.py` は 140項目 FAIL 0 を出していた。**
不具合を検出できていない。この記録の主目的は仕様よりもそちらである。

---

## 1. 裁定 — 4軸の表示判定は「表示グループ」で行う

`data-entity-type` の完全一致ではなく、以下のグループで判定する。

| view | 表示する entity |
|---|---|
| RIG | `rig` / `rig_master` |
| PARTS | `part` / `parts` / `part_master` |
| LOG | `log` |
| トップ | その面の通常構成 |

**理由:** master 系（`rig_master` / `part_master`）は「その軸の実体そのもの」ではなく
**「その軸の集約ビュー」**である。RIG を見にきた人にとって「みんなのRIGに多い車種」は
RIG の話であって、RIG を絞ったときに消えるのは意味的に誤り。

### やらないこと

- **`data-entity-type` を `rig` / `parts` へ書き換えて解決しない。**
  棚が「実体」か「集約」かの区別は Next.js 実装でも要る情報で、
  表示の都合で潰すと後で復元できない。**判定側で吸収する。**
- **`part` / `parts` の語彙2系統をここで統一しない。**
  これは表示判定ではなく `data-entity-type` 側の話。別件として残す（§4）。
- **Search の `type` 語彙（`rig` / `parts` / `log`）とは無関係。**
  Search は URL の外部語彙、こちらは面内の表示グループ。混ぜない。

---

## 2. なぜ壊れたか — 判定が5か所に分散していた

4軸の visible 判定は、同じことを**5つの実装が別々に**持っていた。

| # | 場所 | 2026-08-30 修正前 |
|---|---|---|
| 1 | `pc/myrig-browse-category-v3.html`（静的CSS・**基準画面**） | rig+rig_master / part+part_master … **正しかった** |
| 2 | `pc/myrig-browse-rigs-v3.html`（JS注入） | `rig` のみ |
| 3 | 同上・**2本目のIIFE**（PARTS ROOT からのクローンが残存） | `rig` のみ |
| 4 | `pc/myrig-browse-parts-v3.html`（JS注入） | `rig` のみ / `part_master` 欠落 |
| 5 | `js/parts-category-demo.js`（PC Motor・ESC） | `rig` / `parts` のみ |

**基準画面だけが正しく、他が追随していなかった。**
BROWSE-CONTRACT-001 が扱った「カードが5系統に分裂」と**まったく同じ形**である。
部品の分裂は直したが、**判定ロジックの分裂は残っていた。**

2026-08-30 に #2〜#5 を #1 と同じ文法へ揃え、各所へ `[AXIS-GROUP]` マーカーを入れた
（`grep -rn "AXIS-GROUP" pc/ js/` で所在が追える）。#1 は基準画面のため無変更。

---

## 3. 🔴【L1】判定を1か所に持てないなら、検査で塞ぐ

**踏んだ事故**

`browse_sidebar_v5_check.py` は Sidebar の**構造**（幅・順序・インデント・current クラス・
パンくず）しか見ておらず、**4軸を実際に押したあとの本文を見ていなかった**。
そのため master 系が全部消えていても 140項目 FAIL 0 で通った。

同じ穴が Root current にもあった。`is-current` クラスは JS が正しく付けていたのに
CSS 側が `background: transparent` で打ち消しており、**画面上は現在地が分からない**状態。
チェッカーはクラスと表示名しか見ないので、これも PASS していた。

**原則**

- **「クラスが付いているか」ではなく「見た目に差が出ているか」を検査する。**
  同一面の非current要素と実測を比較する。絶対値をハードコードしない
- **「押せるか」ではなく「押した結果どうなるか」を検査する。**
  状態を持つUIは、状態を遷移させてから測る
- **検査が空振りしていないことを検査する。**
  対象の棚が0本なら判定は素通りする。`AXIS_MUST_COVER` で
  「この面には master 系の棚が存在すること」を先に要求している
- **チェッカーを足したら、故障を注入して FAIL することを確認する。**
  2026-08-30 は rig_master / part_master をグループから外し、Root current の CSS を
  改修前へ戻した状態で 4 FAIL が出ることを確認してから採用した。
  **これをやらないチェッカーは「通ること」しか保証しない**

追加した判定（`browse_sidebar_v5_check.py` / 140 → 174項目）:

- 4軸 rig / parts / log の各 view で、表示グループどおりの棚だけが残るか（4面）
- トップへ戻すと通常構成が復帰するか
- 検査対象に必要な entity が揃っているか（空振り防止）
- Root current の5状態（HOME・Rock Crawler・Motor・ESC では Root に current を付けない、の反証込み）
- Root current が視覚的に描画されているか（非current Root との実測比較）
- Root current でも Root entry の寸法が変わらないか
- 4軸current ＞ Root current の強さの序列（地色との相対差）

---

## 4. PENDING — 4軸判定の共通化（Mobile Browse 着手時）

**今回は共通化しない。** PC Browse の見た目がようやく固まった直後に
広い refactor を始めると、固めたものを再度崩すため。

| 項目 | 状態 |
|---|---|
| PC の4軸判定が4実装に分散（#2〜#5） | 内容は揃えた。**構造は分散したまま** |
| `js/parts-category-demo.js` `initViewSwitch()` L322-325 のモバイル経路 | `data-entity-type === view` の**完全一致のまま**。PC の修正を横展開していない |
| `myrig-browse-rigs-v3.html` の4軸IIFE 2本重複 | 内容は同一へ揃えた。**統合していない** |
| `part` / `parts` の語彙2系統 | 未統一。表示判定側で吸収中 |

**再開トリガー: Mobile Browse に着手するとき。**
そのタイミングで PC / Mobile を通した4軸判定の共通化を1バッチで扱う。
それまでこの項目に対して裁定を催促しない。

> Mobile Browse・Mobile Rock Crawler の badge は従来どおり未着手 / HOLD 維持。
> **PC の修正を Mobile へ自動的に横展開しないこと。**

---

## 5. 同時に確定した Root current（2026-08-30）

RIG ROOT / PARTS ROOT で、Root entry 自身が現在地であることを示す。

- Root entry の寸法（インデント0 / 高さ46 / weight 900 / 件数前の縦罫 / 下罫線 / 直下の余白）は**変えない**
- 足すのは **ごく薄い無彩色の面 ＋ 下罫線の色を `--sb-line` → `--sb-text-2` へ**（太さは1pxのまま）
- 左の縦アクセントは付けない。Root は `padding-left:0` のため、子と同じ位置に置くと文字へ被り、
  インデントを足すと非current の Root と行頭がずれる＝Root寸法の変更になる
- カード化しない。カテゴリ色を使わない
- 強さ: **4軸current ＞ Directory 子current ＞ Root current**（面の地色比 / light・dark 両方で実測確認）

実装: `pc/assets/css/SoT_browse-sidebar-v5.css` の `.sb-dir-row--root.is-current` 1ブロックのみ。
JS 側（`js/browse-sidenav.js`）は元から `is-current` を正しく付けていた。
