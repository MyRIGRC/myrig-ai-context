# MyRIG BOOT — Project Instructionsに最低限含まれているべき項目

> **拘束力: L1（恒久ルール・逸脱禁止）**
>
> このファイルは**Project Instructions（カスタム指示）用の参照原文**であって、
> Project Knowledgeへ同期させるものではない。
>
> ⚠️ **全文貼り替え用のテンプレートではない。** これは「最低限これだけは書かれていること」
> を定めたもの。各AIの指示が既にこれを上回っている場合、上書きすると劣化する。
> 2026-08-24、ChatGPT側の指示は既にこれより詳細だった
> （`CURRENT_STALE`と`CURRENT_UNAVAILABLE`の区別、正典優先順位7段、Task Routing、
> L1/L2/HOLD、App/Research分離）。**差分だけ直すこと。**
>
> 新規に指示を書き起こす場合や、該当節が存在しない場合のみ、下の本文をそのまま使う。
>
> **なぜKnowledgeではなくInstructionsなのか**: Knowledgeに置いたファイルはそれ自体が
> 同期対象になり、また古くなる余地が生まれる。入口を守るために入口を同期対象にするのは
> 本末転倒。Instructionsは常に読み込まれ、Sync nowも要らず、ズレようがない。
>
> **なぜ正典に全文を置かないのか**: CORE / CURRENT の全文をKnowledgeへ複製すると
> GitHub側と二重管理になる。2026-08-24 に実際に「GitHub 030 / Knowledge 029」と
> 「CoworkとGPTが独立にrevision 027を採番」の2件が起きた。詳細は CORE を参照。

**更新:** 2026-08-24（新設）
**このファイル自体の正本:** GitHub `MyRIGRC/myrig-ai-context` の `_AI/MyRIG_BOOT.md`

---

## 貼り付け本文（ここから下をコピー）

```text
MyRIG CURRENT参照ルール

MyRIGの仕様判断・設計・実装・監査を行う前に、必ず正典を参照する。
正本は GitHub の MyRIGRC/myrig-ai-context（main）である。

■ 作業開始時に取得するもの

1. revision.txt
2. _AI/MyRIG_CORE.md
3. _AI/MyRIG_CURRENT.md

revisionが一致しない、または取得できない場合は仕様判断を停止する。
過去会話・Memory・古いKnowledgeを正典として扱わない。

■ revisionは2箇所を突き合わせる（CDNキャッシュ対策）

revision.txt だけを見て信じないこと。
必ず revision.txt と _AI/MyRIG_CURRENT.md 冒頭の revision を両方取得し、
一致することを確認してから作業を始める。

食い違った場合はCDN/プロキシの古いキャッシュを疑い、再取得する。
再取得しても食い違うなら仕様判断を停止し、その旨を報告する。
推測でどちらかを正としない。

理由: 2026-08-25、GitHub main から revision.txt を取得したところ
MYRIG-20260825-001 が返った（正しくは 033）。1つ古いのではなく最初期の値で、
明らかにおかしいので気づけたが、もし 032 のような"それらしい値"が
返っていたら1つ前の正典で作業してしまうところだった。
2ファイルが同時に同じ古さでキャッシュされる確率は低いので、
突き合わせるだけで大半は検出できる。

■ 参照経路（使える方を優先する）

1. GitHub main を直接読める環境
   （Cowork / Claude Code / GitHubコネクタ接続済みのChatGPT）
   → GitHub main を正とする。Sync now は不要。
   Cowork / Claude Code は ~/Desktop/MyRIG/myrig-ai-context を直読みしてよい。
   その場合は git fetch と git status -sb で remote との乖離が無いことを確認する。

2. GitHub main を直接読めない環境
   → Project Knowledge を使う。ただし push 後に Sync now を押していないと古い版を見る。
   revisionが最新でない疑いがあるときは、その旨を明示する。

3. どちらも参照できない場合
   → "CURRENT_UNAVAILABLE" と報告し、仕様依存の判断を進めない。
   独自の中間ステータスを作らない。

■ 正典の全文をProject Knowledgeへ置かない

CORE / CURRENT の全文をKnowledgeへ複製すると、GitHub側と二重管理になり
「GitHub 030 / Knowledge 029」のようなズレが発生する。
Knowledgeへ置くのは参照ルールまでとし、本文はGitHubから取得する。

■ 書き込み

正典への書き込みは Cowork（Claude）に一本化する。GPT は READ 専用。
読むのは自由だが、修正して push しない。
（2026-08-24、CoworkとGPTが独立に revision 027 を採番する事故が発生したため）

■ フォルダ選択時の注意

GitHub Integration / コネクタでフォルダを選ぶ場合は、
_AI _audit _state docs に加えて _archive _decisions _proposals も必ず含めること。
特に _decisions（裁定原本）が外れていると、二次記述だけで判断して誤診する。

■ 回答冒頭

実際に読み取った revision を必ず明示する。
例: CURRENT: MYRIG-20260824-030
```

## ここまで

---

## 補足（貼り付け不要・運用メモ）

### 指示を入れただけでは読めない

Project Instructions は「GitHub mainを読め」と**命じるだけ**で、**アクセス手段は与えない**。
実際に読めるかどうかは、その環境で **GitHubコネクタが接続されているか**で決まる。

- 接続済み → GitHub main を直読みできる。Sync now 不要
- 未接続 → 指示に何と書いてあっても読めない。`CURRENT_UNAVAILABLE` になるのが正しい挙動

**確認方法**: そのAIに「今のCURRENTのrevisionは？」と聞く。

- `CURRENT: MYRIG-20260824-030` のように**実取得した値**を答える → 接続OK
- 「取得できない」「Knowledgeにある版では」と答える → 未接続。設定 → コネクタ で接続する
- revisionを答えずに仕様を語りだす → **指示が効いていない**。指示側を見直す

### 環境ごとの実測（2026-08-24）

| 環境 | 正典の読み方 | Sync now |
|---|---|---|
| デスクトップCowork | ローカルclone直読み ＋ `git fetch` | 不要 |
| デスクトップGPT | GitHubコネクタで直読み | 不要 |
| ブラウザGPT | GitHubコネクタで直読み（設定 → コネクタ で接続） | 不要 |
| ブラウザ通常チャット（Knowledge経由） | Project Knowledge | **必要** |

ChatGPTのGitHub接続は、プロジェクトの「情報源を追加する」ダイアログではなく
**設定 → コネクタ（Connectors / アプリ）** から行う。読み取り専用。

### 採用しなかった案

- **Google Drive経由でミラーする**
  → ブラウザGPTがGitHubを直接読めることが確認できたため不要。
  3つ目のコピーと2つ目の同期機構が増え、今日事故を起こした「同じ内容の複数コピー」を
  自分から増やす形になる。
  なお `.git` を含むフォルダをDrive同期するのは**やらないこと**。
  Driveはgitのオブジェクト/indexへの同時書き込みを想定しておらず、リポジトリが壊れうる。

- **CORE / CURRENT の全文を Project Knowledge へ置く**
  → 二重管理になり revision がズレる。上記のとおり実際に発生した。

### リポジトリは2本ある。混ぜない

| リポジトリ | 内容 | 反映方法 |
|---|---|---|
| `myrig-mockup` | モック（PC・モバイル両方） | ターミナルで `mockup`（push＋Vercel deploy） |
| `myrig-ai-context` | 正典 | `cd ~/Desktop/MyRIG/myrig-ai-context && git push` |

`mockup` が push するのは **`myrig-mockup` だけ**。正典は別リポジトリなので手動pushが要る。
2026-08-24、モック側が同期済みの状態で `mockup` を実行して `Everything up-to-date` と
出たが、正典側は未push 4本のまま残っていた。
