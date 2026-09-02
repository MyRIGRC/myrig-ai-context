# 裁定: 共有UI Single Source 原則と、モック共有化の実行順序

- 日付: 2026-09-02
- 状態: ADOPTED
- 裁定: イタヤ（Cowork 棚卸し → GPT 監査2回 → Cowork 反論 → 合意）
- 反映先: `_AI/MyRIG_CORE.md`「共有UI Single Source 原則」/ `_AI/MyRIG_CURRENT.md` NOW節
- revision: MYRIG-20260902-041

## 1. 何を決めたか

### DECISION-1 共有UI Single Source 原則【L1 / CORE】

同一責務・同一挙動を持つ横断UIは、2面目へ展開する段階で共有部品へ昇格し、
昇格後は page-local に markup / style / behavior / accessibility state を複製しない。
設計探索中の page-local 試作は許容する。実装方式は正典で固定しない。
恒久的な新旧二重管理は禁止。移行期間の並存は後継名・対象consumer・撤去条件の明記が条件。

### DECISION-2 Header の Single Source 化は PARTS / LOG Detail 着手前に行う

現在形は cx（「＋投稿する」／P22-C7）。search / browse 3面で CSS 25ルール・JS が完全一致しており、
右レーンと違って詰める余地が無い。Header を先に共有化しないと、新規Detail 2面を作る時点で
cx の21面目・22面目のコピーが生まれる。Browse CLOSE 面は pixel parity 必須。

### DECISION-3 Detail 右レーンは「部品箱」にする。丸ごと1部品にしない

候補6部品: Rail Section Shell / Builder / Entity Actions / Share / Library Bridge / Feed・Shelf List。
RIG / PARTS / LOG Detail は各面の責務に応じてこれを組む。
Entity Actions は CSS だけでなく `aria-pressed`・件数増減・icon line/fill・focus-visible・状態遷移を含む。
（根拠: v14r6 で HTML/CSS を直しても page-local JS が絵文字を書き戻した）

### DECISION-4 旧コードの扱い（P4 CLOSE）

CORE「物理DELETEは禁止」は **DB恒久ルール節**のルールであり、Git管理下のソースコードには適用しない。
- DBレコード・データの物理DELETEは禁止（従来どおり）
- Git管理下の失効コードは、全consumer移行確認後に active tree から通常削除してよい
- 履歴は Git が保持する。**`_archive` へ複製しない**

## 2. なぜそうしたか

### 棚卸しの実測（2026-09-02 / `myrig_pc_Ver3/pc` 全56面）

| | 行数 |
|---|---|
| 共有CSS 13本 | 4,064 |
| 共有JS 4本 | 2,186 |
| インラインCSS 56面合計 | **33,291** |
| インラインJS 56面合計 | 8,339 |

- ヘッダー現在形（cx）: 共有側に無し。page-local に **20面**複製（cx採用6面＋rig-detail系14面）
- `sot-templates/` 7本: include機構が無くコピー元の見本。参照0が5本、残り3本も「元にした」コメントのみ
- Web Component 12個のうち5個（`myrig-gc` / `rig-lc` / `log-pc` / `log-cp` / `log-feed`）は使用0
- v6のRIG詳細サイドバーは `catalog-v6` の共有部品で組まれていたが、v9〜v14 の再設計で全部インライン化された
- ガレージ6面に `pit-*` 約25クラスが丸ごとコピー

### 「Web Component」を正典に書かない理由

Next.js へ移れば React Component になる。技術を固定すると正典が嘘になる。
固定すべきは「同一責務のUIを複製しない」という原則の方。CORE の正典化判断基準
（黙ってズレたときのコスト / 複数AIが別々に実装すると壊れる）に合致するのは原則の側。

### 「探索中は許容」を入れる理由

v12 → v14r6 の試行錯誤は、この例外が無ければ全部違反になる。
MyRIG はモック探索型で、page-local で自由に試してから「これで行く」で昇格する運用が合う。

### `_archive` へ複製しない理由（GPT指摘・Cowork同意）

`_archive/` に `SoT_component-catalog-v6.css` のコピーが10本以上あり、grep のたびに現行資産と混ざる。
2026-09-02 の棚卸しでも除外処理が要った。コードは Git 履歴が退避先であり、複製は「どれが現行か」を増やすだけ。
Cowork は当初 CORE の DELETE 禁止を広く読んで裁定を求めたが、CORE 該当行は「DB恒久ルール」節に
置かれており対象はDB・データ。解釈が割れたので、この理由を記録する。

### 旧資産を新資産で置き換えるとき「失効表記」を必須にした理由

`SoT_browse-sidebar.css` と `-v5.css`、`pc/assets/css/` と `css/sot/` の同名SoT、と
新旧並存が放置された実例が既に2つある。新ファイルを足すだけでは3例目になる。

## 3. 実行バッチ境界

| | バッチ | 入口 | 出口 |
|---|---|---|---|
| A | RIG Detail 右レーン仮確定（`v14r6`） | — | イタヤ「これで行く」 |
| B | Header Single Source 化（cx / app-nav 6ルール / ダーク地 → `SoT_app-shell`。create-soft 失効。旧23面置換） | cx 確定済み | チェッカー2本 FAIL 0・全面 pageerror 0・Browse CLOSE 面 pixel parity |
| C | Detail 部品箱新設（`SoT_detail-rail.css` / `SoT_detail-components.js`）＋ RIG Detail 載せ替え | A 完了 | インライン版と表示・状態遷移とも差分ゼロ（3ボタン×4状態＋フォロー実測） |
| D | PARTS / LOG Detail 新規作成 | **B と C 完了** | page-local の Header / 部品コピー 0 |
| E | Footer / Garage `pit-*` / 未使用資産の掃除 | 実体再確認 | — |
| F | PC v8 横断（`tokens-v6.css`） | 独立 | 両テーマ実測・`--cat-*-on` 取りこぼし0・故障注入 |

A ∥ B → C → D。E・F は独立。

## 4. PENDING

- Detail 部品箱6つの境界: A 完了後に確定
- 個別旧資産の失効（`sot-templates/` 7本 / 未使用 Web Component 5個 / `SoT_skeleton.css` / `browse-sidebar.css` 新旧）: **リポジトリ全体で実体再確認後**に確定。棚卸し値のみで確定しない
- PC v8 の実装状態と `color-token-v8.md` の不一致: 実モックは Home / Feed / Search / Browse 3面が page-local `:root` で v8 済み、残り23面は v7。推測で解消せず F で裁定
- `--color-accent-hover: #A86F1F`（茶）が青の hover として不整合。既存は `opacity` で回避。F で扱う
