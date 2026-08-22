# v8カラー PC展開の試行と撤回 — 2026-08-22

**結果: 全ロールバック。** モックは commit `5c8fa55` の状態（PC は v7 のまま）。

## 何をやろうとしたか

モック是正キューB#2「v8カラーがPC 40ファイル中6ファイルのみ適用」の解消。
共有CSS `SoT_tokens-v6.css` を v8 へ変え、v7ローカル定義14ファイルを置換し、
`--cat-*-on` を敷設して `color:#fff` を置換した。

## なぜ撤回したか

**修正前より悪化していた。** 反証確認で「黄地に白文字」が**実レンダリングで14箇所**残存。

| | コントラスト |
|---|---|
| v7 緑 `#66b900` ＋ 白文字 | 2.47（低いが読める） |
| **v8 黄 `#FBFF00` ＋ 白文字** | **1.08（読めない）** |
| v8 黄 ＋ 黒文字 `#151515` | 16.87 |

つまり **`--cat-*-on` を1箇所でも取りこぼすと、そこは v7 のときより読めなくなる。**
「部分的に適用する」という進め方が成立しない作業だった。

## 取りこぼした3つの形

置換は「同一ルール内の `color`」と「親からの継承」だけを想定していたが、実際にはこの3形があった。

### ① 子要素が自前で `color:#fff` を持つ

```css
.create-soft__btn--rig { background: var(--cat-rig); color: var(--cat-rig-on); }  /* 直した */
.create-soft__plus     { color:#ffffff; }   /* ← 子。届かない */
.create-soft__label    { color:#ffffff; }   /* ← 子。届かない */
```

`SoT_app-shell.css` のこの形が **live PC 24ページ**に効いていた。

### ② `html[data-theme="dark"]` 側の上書き（特異度で負ける）

```css
html[data-theme="dark"] .app-header .create-soft__label { color:#ffffff; }  /* 0,3,0 */
.app-header .create-soft__btn--rig { color: var(--cat-rig-on); }            /* 0,2,0 → 負ける */
```

**ダークモードでは修正そのものが無効化されていた。**

### ③ 背景が `--cat-*` を間接参照している

```css
--master-cta-accent: var(--cat-rig);
.master-buy-gateway { background: var(--master-cta-accent); }
.master-buy-gateway-label { color:#fff; }
```

`background:var(--cat-rig)` という文字列で grep しても引っかからない。
**library-rig-master-detail のページ最大の購入CTAボタン**がこれだった。

## その他の発見（撤回後も残る事実）

- **共有トークンの実体が2つある** — `pc/assets/css/SoT_tokens-v6.css` と
  `css/sot/SoT_tokens-v6.css`。後者は v7 のままで、`css/mobile-shell.css` が
  後勝ちで上書きしているから救われているだけ。読み込み順に依存した危うい状態
- `pc/myrig-feed-v3.html` は v8適用済みページなのに `.act-badge` が
  `var(--cat-rig, #66b900)` のフォールバック形で、今回の対象から漏れていた
- **逆向きの同型障害が17箇所** — 黄「文字」が白に近い地に乗る形（CR 1.07）。
  `.dir-world__name` `.door--rig .door__label` `.pillar--rig .pillar__icon` など
- カタログ3本はローカルで v7 を定義しており、`-on` を足さないと `color` が無効化される
- `pc/css/common.css` に `--cat-*-text` という第3の命名系がある（どのHTMLからも未参照の死蔵）

## 次にやるなら

**部分適用は不可。全体を1バッチで、かつ以下の順で。**

1. **トークンの実体を1つに統合**（`pc/assets/css/` と `css/sot/` の二重化を解消）
2. **`color:#fff` / `#ffffff` / `rgba(255,255,255,*)` を全件棚卸し**し、
   カテゴリ色の上に乗るものを特定する。**セレクタの親子関係とダーク側の特異度まで追う**
3. 間接参照（`--master-cta-accent` 等）を洗い出す
4. v8適用と `-on` 敷設を**同時にコミット**
5. **ライト・ダーク両方**でコントラスト実測（静的解析だけでは②を見落とす）

見積もりを誤っていた。「共有CSS 2行で5ページ一括解決」は嘘で、
実際は**live PC 24ページ＋モバイル2ページに波及する作業**だった。

## 教訓

**「部分的に直せる」という判断自体が誤りだった。**
v7→v8 は、途中で止めると前より悪くなる種類の変更。
着手前に「取りこぼしたらどうなるか」を計算しておくべきだった。
