# DECISION — Entity Actions を Shared Source へ統合（continuity 2「reaction」）

- 日付: 2026-09-07 JST
- revision: MYRIG-20260907-069
- 経緯: 068（continuity 1 CLOSE）のあと reaction を実画面化 → **4論点をイタヤ裁定** → 本実装へ統合
- モック側の実測記録: `myrig-mockup` `_state/MVP_CONVERGENCE_MAP.md` §8.16
- モック commit: `fb84702`

---

## 0. 統合前の現在地（実測 2026-09-07）

同じ「いいね」が **3つの別実装**になっていた。

| 場所 | 実装 | 動いていたか |
|---|---|---|
| Feed `.pc-feed-card` | page-local `[data-pc-like]`。`<span>` を直接 ±1 | 動く |
| **LOG Detail 本文 `<dt-actions>`** | `.dt-action[data-action]` | 🔴 **一度も動いていなかった** |
| RIG v15 / PARTS v1-open 右レーン | `.rail-action[data-action]` | 動く |

🔴 **原因は共有 JS のセレクタ `.rail-action[data-action]` にクラスが入っていたこと。**
契約も markup も正しく、**セレクタだけが漏れていた**ので、DOM を見る検査では見つからない。
065 の「挙動は `[data-action]` に対して1か所で実装する」という設計意図と実装がずれていた。

---

## 1. 裁定

### 1-1. reaction 更新

| # | DECISION |
|---|---|
| 1 | **楽観更新を採用する。** 押した時点で state / count を即時反映する |
| 2 | サーバー失敗時は元の state / count へ **rollback** する |
| 3 | 🔴 **黙って戻さず、短い notice を出す** |
| 4 | 🔴 **notice の見た目・文言で比較案を増やさない。既存の通知文法を再利用する** |

→ 実装は `SoT_app-shell.js` の既存トースト（`.app-toast` / `role="status"` / `aria-live="polite"`）を
`window.MyRIG.toast` として公開し、それを呼ぶ。**新しい通知の見た目を作らない。**

### 1-2. 未ログイン

| # | DECISION |
|---|---|
| 5 | **新しい UX 裁定にはしない。** likes / favorites / pins は `user_id NOT NULL` のユーザー操作である |
| 6 | **Feed / Detail とも同じ LoginRequired 契約へ統一する** |
| 7 | Feed だけ LoginRequiredModal があり LOG Detail では何も起きない状態は、**cross-surface 不整合として修正する** |

### 1-3. LOG Detail の rail actions

| # | DECISION |
|---|---|
| 8 | 🔴 **追加しない。** 現行の本文 inline actions を維持する |
| 9 | 🔴 **今回の reaction バッチを理由に、VISUAL CLOSE 済みの LOG Detail へ二重の action surface を追加しない** |

（置き場所が面ごとに違うことは Gate 5 DECISION 2 / 3 のとおり正しい。
LOG Detail = 本文 inline のみ／RIG・PARTS = 右レーンのみ。**揃えるのは見た目ではなく契約と挙動。**）

### 1-4. pin

| # | DECISION |
|---|---|
| 10 | **今回は PENDING 維持。** schema 上 LOG は pins の対象だが、Feed / LOG Detail のどこへ UI を出すかは別論点 |
| 11 | 🔴 **reaction の Shared Source 統合の blocker にしない** |

→ 実装は `data-action="pin"` を扱えるが、**出すかどうかはページの責務**。Feed / LOG Detail には出していない。

---

## 2. Shared Source 統合の要件（すべて満たすこと）

| # | 要件 |
|---|---|
| 12 | fixture の `sessionStorage` / `stopPropagation` による**二重実装回避を本実装として残さない** |
| 13 | Feed の page-local `[data-pc-like]` と Detail / Rail の action behavior を、**同じ `[data-action]` 契約・単一 state / behavior** へ統合する |
| 14 | 同じ LOG について、Feed の**おすすめ / 新着 / LOG Detail** 間で **pressed state / count / aria が一致する**こと |
| 15 | **未ログイン auth gate・楽観更新・rollback も同じ契約に含める** |
| 16 | RIG v15 / PARTS v1-open / LOG Detail / Feed を対象に、**共有変更後の非回帰を必ず再実行する** |
| 17 | 🔴 **LOG Detail inline `.dt-action` が共有ハンドラの class selector 漏れで一度も動いていなかったことを、非回帰項目として残す** |

### 契約（consumer が守るもの）

| 属性 | 意味 |
|---|---|
| `data-action` | `like` / `favorite` / `pin`。**これが挙動の入口** |
| `data-base-count` | その時点の表示値。`aria-pressed="true"` で始まる場合、自分の1票は base に入っている |
| `aria-pressed` | 押されているか。トグルするものだけ持つ |
| `data-auth` | 未ログインで弾く操作。値は auth-guard-spec の context。**share には付けない** |
| `data-entity-key` | 祖先のどこかに1つ。**同じ entity は同じ key** |

数え方は既存の共有規則のまま（変えていない）:
初期 `pressed=true` → 表示 = base + (pressed ? 0 : -1)／初期 `false` → base + (pressed ? 1 : 0)。

**見え方はページの語彙、契約は共通。** `.rail-action`（♥/♡ ＋ 数字）／`.dt-action`（アイコン ＋ `.dt-action__count`）／
Feed の `.pc-feed-card__action`（アイコン ＋ `span`、押下は `.is-on`）は**表示 variant** であって別実装ではない。

---

## 3. 実装の所在（正本）

| | |
|---|---|
| **`pc/assets/js/SoT_entity-actions.js`** | **Entity Actions の Shared Source。** state / count / aria / 挙動 / 認証ゲート / 楽観更新 / rollback、および LoginRequired（markup / CSS / 文言 / capture ハンドラ）を持つ |
| `pc/assets/js/SoT_app-shell.js` | `window.MyRIG.toast` を公開。**通知の文法はこの1つだけ** |
| `pc/assets/js/SoT_detail-markup.js` | `<dt-actions>` / `<dt-rail-actions>` が `data-auth` を出す |
| 読み込む面 | Feed / LOG Detail v1 / RIG v15 / PARTS v1-open の4面 |

🔴 **書き戻し禁止（L1 相当）**

- `SoT_detail-components.js` に `.rail-action[data-action]` の挙動を戻さない
- `myrig-feed-v3.html` に `[data-pc-like]` の挙動を戻さない
- `myrig-feed-v3.html` に page-local の LoginRequiredModal を戻さない
- 共有側のセレクタに**クラスを混ぜない**（`[data-action]` だけで拾う）

---

## 4. 非回帰（恒久）

**`_state/entity_actions_check.py`** を新設した。**38項目 / 38 PASS / 0 FAIL**（`--selftest` 付き）。

「契約があるか」ではなく **「押したら実際に count と `aria-pressed` が変わるか」** を見る。
今回の欠陥はセレクタ漏れで、**DOM を見る検査では見つからない**ため。

見る項目: 4面すべてでトグル往復 ／ 同じ LOG が複数パネルで一致 ／ Feed → Detail で引き継ぐ ／
未ログインで LoginRequired が開き count が動かない ／ `?ea_fail=100` で戻って通知が出る ／
重複実装が復活していない ／ 共有側のセレクタにクラスが混ざっていない。

画素非回帰: RIG v15 1440/light ／ PARTS v1-open 1280/dark ／ LOG Detail 1440/light ／
Feed 1440/light・1280/dark とも **揺れで説明できない画素 0**。
無関係面の対照（Home / Browse RIGs）も **0**。

---

## 5. モックの都合（本番では消える）

- ページをまたぐ state はサーバーが持つ。モックに無いので `sessionStorage` を代役にしている。
  **`SoT_entity-actions.js` の `PERSIST` の中だけ**に閉じてあり、ここを差し替えれば API に載る
- どの LOG かは `?log=` で渡す（本番は `/log/[id]`）。受けは `js/detail-state-demo.js` が
  `data-entity-key` に載せ替えるので、**共有側はモックのクエリを知らない**
- `?ea_fail=<0-100>` は rollback を実画面で見るためのもの

---

## 6. まだ決めていないこと

- `pin` を Feed / LOG Detail のどこに出すか（066 から継続の PENDING）
- `?log=<id>` の本番 URL 設計
- RIG / PARTS が inline actions を持つかどうか（**現状は持たない**）
