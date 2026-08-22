# GPT GitHub WRITE Test — 2026-08-22

status: TEST / non-canonical
created: 2026-08-22 16:39 JST
writer: GPT via GitHub connector
base_revision: MYRIG-20260822-015

## Purpose

GPTから `MyRIGRC/myrig-ai-context` の GitHub main へ直接WRITEできることを実地確認する。
このファイルはWRITE経路の疎通確認専用であり、MyRIGの仕様・裁定・HOLDを変更しない。

## Preconditions verified by GPT

WRITE直前にGitHub mainから以下を再取得した。

- `revision.txt` = `MYRIG-20260822-015`
- `_AI/MyRIG_CORE.md` = 複数WRITE経路の競合防止ルールを含む最新版
- `_AI/MyRIG_CURRENT.md` = `revision: MYRIG-20260822-015`

`revision.txt` と CURRENT revision の一致を確認した上でWRITEを実行した。

## Expected verification on Cowork / Claude side

次回ローカルcloneを触る前にCOREルールどおり、working tree確認 → `git fetch` → local HEAD と `origin/main` の差分確認 → 必要なら `git pull` を行う。

確認項目:

1. `origin/main` に本ファイルが存在する
2. 本ファイルの内容がこの記録と一致する
3. GPTのcommitをpull後、local HEADとorigin/mainが一致する
4. `revision.txt` / CURRENTは015のままであり、正典revisionは変更されていない

## Result

GPT側のWRITEが成功した場合、このcommit自体を実地WRITE成功の証拠とする。
