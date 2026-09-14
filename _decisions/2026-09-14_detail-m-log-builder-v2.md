# Detail M / LOG の Builder card — v1（drop）を撤回し defer にする — 裁定原本 v2

- 日付: 2026-09-14 JST
- 対象 revision: MYRIG-20260914-101
- 実装: `myrig-mockup`（SHA は CURRENT 101 を参照）
- きっかけ: **Mac 実機確認**（revision 100 の裁定が過剰だった）
- 関連: Region Behavior Matrix **v1.9** §3 / §6.11、
  `_decisions/2026-09-14_detail-m-log-builder-v1.md`（**§1 の drop 部分は本書で失効**）、
  `_decisions/2026-09-14_detail-m-transform-v1.md`（Detail M の本体裁定）

---

## 0. 何を撤回するか

revision 100（`_decisions/…-log-builder-v1.md`）は、LOG の M について

> full Builder card / stats / large actions は M で明示 drop

と裁定した。**これは過剰で、失効させる。**

イタヤの元の意図は
**「Builder カードを LOG タイトルと本文の間に挟まない」**であって、
**「Builder カード自体を M から消す」ではなかった**。

🔴 **「本文の前に置かない」と「消す」は別の裁定である。**
実機所見から契約を書くときは、**遮断の是正**と**責務の削除**を取り違えない。
⛔ 位置の問題を drop で解こうとしない。

（v1 の他の部分 — 本文前に置かない・deck を作らない・clone しない・author row のリンク化 — は**有効**。）

---

## 1. 新裁定

```
LOG M =
  Identity
  → Main（body / photos / tags / dt-actions / Log facts / comments）
  → Builder card
  → RELATED
  → Footer
```

Builder card は **M で drop しない**。W の右 Rail から
**「Main 読了後・RELATED の直前」へ defer / adapt** する。

### Builder card は全部維持する

- avatar / name
- 公開RIG / 公開パーツ / 公開LOG の stats
- Follow
- ガレージを見る

### Identity 上部の author row も維持する

Public Garage へのリンク化（v1 で入れた `<a>`）も維持する。

**人物 identity が一部重複することは許容する。**

| | 責務 |
|---|---|
| 上部 author row | **投稿者の同定**（この記録は誰のものか）。本文を読む前に要る |
| 読了後 Builder card | **作者への回遊**（Follow / Garage / stats）。読み終えた人が次に向かう先 |

同じ人物を指していても、置かれる場所と目的が違う。
⛔ 「重複しているから片方を消す」で処理しない。

---

## 2. 禁止

- LOG タイトルと本文の間へ Builder card を戻さない
- Builder card / stats / actions を drop しない
- Builder card を clone しない（**同一 DOM のまま移動**する）
- RIG / PARTS の M を変更しない
- W（>=1025）の右 Rail を変更しない
- R-06（1024/1025 境界）を再 OPEN しない

---

## 3. 実装

撤回したのはページ側の**宣言 1 行**だけで、共有 transform 側に LOG 専用の分岐は無い。

```
revision 100: <dt-rail-builder data-m="merge:builder-identity" data-m-drop="stats,actions" …>
revision 101: <dt-rail-builder data-m="adapt-main" …>
```

`adapt-main` は**既存の adapt 文法**（受け皿は `.dt-main` の末尾＝本文の後・RELATED の前）。
LOG 専用の DOM 複製も、LOG 専用の受け皿も作っていない。
`data-m-drop` は使う面が無くなったので語彙ごと撤去した（空振りする検査を残さない）。

### 3-b. 受け皿は Aside の variant クラスを引き継ぐ

移動先の受け皿（deck / adapt）に、Aside が持つ `dt-rail--`* のクラス（現行は `.dt-rail--quiet`）を
**引き継がせた**。

`.dt-rail--quiet .rail-section{…}` のような**子孫セレクタは祖先が変わると効かなくなる**ので、
引き継がないと「同一 DOM を移動しただけ」なのに M で Widget の見た目（header の帯・border の濃さ）が変わる。
Builder card は M で最も目立つ要素になったので、ここで顕在化した。
⛔ 移動先ごとにスタイルを書き直さない（それは page-local の複製と同じ）。

---

## 4. Gate

| assert | 何を見るか |
|---|---|
| DM2 | **1024 の LOG で title の直後に Main 本文が始まる**（`.dt-main` の先頭が `.dt-lead`） |
| DM2 | **Builder card が本文（`.dt-lead`）より後** |
| DM2 | **Builder card が Main の最後（コメント）より後** |
| DM2 | **Builder card が RELATED より前** |
| DM2 | **M でも Builder card が可視**（drop していない） |
| DM2 | **stats / Follow / ガレージを見る が可視**（責務を欠いていない） |
| DM2 | **Builder は document 内に 1 つだけ**（clone 0） |
| DM2 | author row の行き先が Builder の「ガレージを見る」と同じ |
| DM7 | M で author 導線・Builder の Follow・ガレージを見る が `elementFromPoint` で取れる |
| DM1 | 1025（W）では Builder が元の Rail 位置へ完全復元。author row はリンクでも既定状態で下線なし |
| DM9 | 1024⇄1025 の 3 往復で DOM 完全復元・受け皿の増殖 0・deck が湧かない |
| DM6 | 横 overflow 0 |

故障注入（LOG 関連）

- `log-builder-deck-reinserted`: 本文の前へ戻す → DM2 / DM5 / DM7 / DM1 / DM9 が検知
- `log-builder-dropped`: M で `display:none` にする → DM2 / DM7 が検知
- `log-builder-cloned`: clone する → DM3 / DM2 / DM1 が検知
- `log-author-link-removed`: author row の導線を外す → DM2 / DM1 が検知

---

## 5. 検証

| | |
|---|---|
| `detail_m_transform_check` | **3,152 PASS / 0 FAIL** |
| 故障注入 | **17 種すべて単独で検知**（未検知 0） |
| 既存 19 本 | **0 FAIL** |
| VISUAL LOCK | LOG 1440 / 1280 / 1051、RIG 1440 / 1051、PARTS 1440 / 1280、Garage Owner Detail 1440 / 1040 / 1024 で **揺れで説明できない画素 0**。変化は **LOG の M だけ** |
| 実測（LOG 1024） | title 281 → 本文 333（直後）／コメント 1157〜1654 → **Builder 1687〜1941** → RELATED 1990。Builder は document 内 1 つ・stats 1・Follow 可視・`ガレージを見る` の href は author row と一致。横 overflow 0 |

🔴 100 は push 済みなので amend / force push はしていない。本書で失効させ、101 で修正した。
