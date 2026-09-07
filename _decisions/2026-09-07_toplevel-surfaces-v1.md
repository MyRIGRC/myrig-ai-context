# DECISION — Browse / Feed / Library は独立したトップレベル体験である

- 日付: 2026-09-07 JST
- revision: MYRIG-20260907-071
- 経緯: 068 で PROPOSAL として記録 → continuity 裁定がまとまった区切りで **DECISION へ昇格**（イタヤ裁定）

---

## 裁定

**ヘッダーの Browse / Feed / Library は「同じコンテンツを別表示する3つのタブ」ではない。**
それぞれが独立した目的・回遊文法を持ち、**その面だけを使っていても成立するトップレベルの体験面**である。

| 面 | 目的 |
|---|---|
| **Browse** | RIG / PARTS / カテゴリ等から世界を探索する discovery / exploration の面。「何があるか」「どんな RIG やパーツがあるか」「このカテゴリには何があるか」を辿る。**Browse だけでも探索体験が成立する** |
| **Feed** | 人・RIG・LOG の活動を時間軸で見る面。投稿を読み、reaction し、comment を読み書きし、次の投稿へ進む。**投稿を読むたびに別ページへ追い出さない** |
| **Library** | メーカー・製品・マスター情報を調べる参照系の面。**Community の簡易版でも Browse の下位ページでもない。Library だけで製品情報を調べる体験が成立する** |

---

## 🔴 独立して成立する ≠ 互いに分断する

MyRIG らしさは、それぞれの世界を壊さずに**自然な文脈で別の世界へつながる**ことである。

**「別の世界へ遷移させること」が主目的なのではない。
今いる面で目的を果たしたうえで、興味が生まれた場所に自然な扉を置く。**

想定する接続の例:

- Feed → 気になる RIG → RIG Detail → Base Model → Library → 同モデルを使った別 RIG → Browse
- Browse → 気になる LOG → LOG Detail → author → Public Garage → Follow → その後 Feed で活動を見る
- Library → 製品情報 → その製品を使っている RIG → Community 側へ

---

## ⚠️ この原則の誤用を禁じる

🔴 **この原則を理由に Browse / Feed / Library の見た目や構造を機械的に揃えない。**

目的が違うので **UI 文法は違ってよい**。むしろ違うのが正しい。

**共通化するのは、同じ entity・同じ state / behavior / aria など「同一責務の部分」だけ。**
（Gate 5 DECISION 2〜4 と同じ線引き。069 の Entity Actions 統合がその実例）

---

## この原則から既に導かれているもの

| | |
|---|---|
| 068 | Feed の閲覧体験は原則 Feed 内で完結。「続きを読む」は Feed 内展開、コメントも Feed 内。LOG Detail は**常設の出口**として残すが毎回の必須遷移にはしない |
| 068 | LOG Detail は Feed の「完全版ページ」ではなく **canonical Garage record**。Browse / Search / Garage / Related から LOG を発見したときの**正規の受け皿** |
| 068 | author 導線は Public Garage（`/user/[username]`）。**Feed 内に「そのユーザーだけの Feed」等の新 surface を作らない** |
| 071 | Feed のタブを3本に（`_decisions/2026-09-07_feed-tabs-v1.md`）。Feed が独立した体験である以上、Feed 内に3つの入口が要る |
