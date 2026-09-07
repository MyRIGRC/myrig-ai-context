# DECISION — Feed ↔ LOG Detail continuity 1「基本導線」CLOSE

- 日付: 2026-09-07 JST
- revision: MYRIG-20260907-068
- 経緯: 067 の Gate 5 CLOSE 後、比較案を実画面（`pc/myrig-feed-v3.html` の fixture）で確認 → イタヤ裁定
- モック側の実測記録: `myrig-mockup` `_state/MVP_CONVERGENCE_MAP.md` §8.15

---

## 0. 前提 — Feed の位置づけ

**Feed の閲覧体験は原則 Feed 内で完結させる。投稿を読むたびに別ページへ追い出さない。**

**LOG Detail は Feed の「完全版ページ」ではない。** 用途の違う2面という 066 の DECISION を維持したうえで、
LOG Detail は **canonical Garage record として独立して存在**し、
Browse / Search / Garage / Related から LOG を発見したときの**正規の受け皿**になる。

---

## 1. 本文

| # | DECISION |
|---|---|
| 1-1 | 「続きを読む」は **Feed 内展開**。**LOG の長さで挙動を変えない**（inline 展開になったり Detail 遷移になったりしない） |
| 1-2 | 通常の長さの LOG は、**Feed 内で全文を読める** |
| 1-3 | 超長文だけ、Feed 内表示の上限まで展開した**末尾**に「このログの全文を記録ページで読む →」を出す |
| 1-4 | 🔴 **長文の境界値を正典で固定しない。** 当面「1000字前後」を **fixture 値**として扱い、実画面で後から調整する |

---

## 2. action row

| # | DECISION |
|---|---|
| 2-1 | **like / comment / share / LOG Detail の4つを、同じ action row 内の4等分**にする |
| 2-2 | 🔴 **LOG Detail を後付けの別列（auto 列）として扱わない。** 4つとも「この LOG に対してできること」なので同じリズムにする |
| 2-3 | LOG Detail は `<a>`。`title` / `aria-label` =「**このログの記録を見る**」。キーボードで到達できること |
| 2-4 | アイコンは **document / record 系を維持**。🔴 **アイコン違いの比較案を増やさない** |

> **失効（戻さない）:** 初版の「3等分 grid ＋ 右に 36px の auto 列」。
> 記録だけ後付けの細い列に見えて仲間外れになった。

---

## 3. コメント

| # | DECISION |
|---|---|
| 3-1 | **comment action でカード内に conversation を開く**（Feed 内で完結） |
| 3-2 | 初期表示は **最新2件程度** |
| 3-3 | **その場でコメント投稿できる** |
| 3-4 | 続きは「**さらに ○ 件**」でカード内に展開する |
| 3-5 | 🔴 **Feed と LOG Detail のコメントは同一 state・同一データ。** 別実装・別状態にしない |
| 3-6 | UI は**新造しない**。Detail Conversation と同じ責務を共有したうえで、**Feed 用 compact variant** として設計する |

---

## 4. author 導線

| # | DECISION |
|---|---|
| 4-1 | Feed カードの **avatar / 表示名 / `@handle`** は **Public Garage（`/user/[username]`）** へリンクする |
| 4-2 | 🔴 **時刻はリンクにしない**（author の同一性ではない） |
| 4-3 | 🔴 **Feed 内に「そのユーザーだけの Feed」等の新しい surface を作らない。** `/feed/@handle` は作らない |
| 4-4 | 「この人の投稿だけ見たい」という需要は、**Public Garage 側の LOG 一覧**で満たす |
| 4-5 | Public Garage 自体の詳細設計は**別バッチ**。今回付けるのは**リンク責務だけ** |

**正典の裏づけ**（`docs/ui/page-role-matrix-v1.md`）:
`/user/[username]` = Public Garage（他人のガレージ公開ビュー）／ユーザー識別は `@` なし・`@` は表示のみ／
User Profile 専用ページは作らず Public Garage View が兼務。

### a11y

- avatar リンクは **`tabindex="-1"` のみ**。表示名リンクと行き先が同じなので Tab を重複させない
- 🔴 **`aria-hidden="true"` は付けない。** クリックできる interactive element を
  支援技術から**完全に隠す**形になるため。「Tab 重複を避ける」ことと「支援技術から消す」ことは別

---

## 5. Shared Source（採用後にやること）

現状、実行時の Feed カードは page-local `.pc-feed-card` で、
Detail の `<dt-actions>` が持つ `data-action` / `data-base-count` / `aria-pressed` を持っていない。

**今回は fixture のままで CLOSE する。先に大規模リファクタしない**（イタヤ指示）。
採用が済んだこの内容をもとに、**reaction / comment の state / count / aria / behavior を
Shared Source へ統合する**のが次の作業。065 / Gate 5 DECISION 4 の延長線上にある。

---

## 6. 今回の blocker にしないもの（記録のみ）

- モックの Public Garage が固定ユーザー1面（`@trail_builder`）しか無く、どの author も同じ面に着く
- その Public Garage が `h1` を持っていない → **後の Garage 監査で拾う**
- モックに LOG ごとの面が無いので、どの LOG カードも同じ `myrig-log-detail-v1.html` に着く

---

## 7. まだ決めていないこと（continuity の続き）

Feed 2タブ正典（`page-role-matrix-v1.md` は「おすすめ / フォロー中」）と現行3タブ実装の不整合。
reaction の state / count 一貫性と Shared Source 境界。
