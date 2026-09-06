<!-- 原資料: GPT 最終統合案（2026-09-06）。イタヤが Claude へ転記したものを原文のまま保存。正典ではない。裁定は _decisions/2026-09-06_infra-cost-resilience-v1.md -->

【MyRIG インフラ／コスト耐性 — GPT最終統合案 2026-09-06】

CURRENT: MYRIG-20260905-061

GPT / Claude / Gemini の独立調査、公式料金・制限の再確認、
およびイタヤの運営条件
「本業ではなく副業・趣味規模。成功は歓迎するが、長期の赤字持ち出しは不可」
まで含めた現時点の最終案。

━━━━━━━━━━━━━━━━━━
■ 0. 結論
━━━━━━━━━━━━━━━━━━

MVPの推奨構成は以下。

Vercel Pro
+ Supabase Pro（Tokyo）
+ Cloudflare R2
+ Upstash
+ Cloudflare Turnstile
+ Custom SMTP

Cloudflare Images Hosted は使わない。

最大の設計思想は
「最安構成」ではなく、

1. 通常成長なら収益で十分吸収できる
2. Bot・バグ・急成長でも請求が青天井にならない
3. 赤字ならサービス全停止前に段階的に縮退できる
4. 将来Vercel等が高くなればapp層だけ移行できる
5. 個人開発者が長期間赤字を補填しない

という構造にすること。

━━━━━━━━━━━━━━━━━━
■ 1. MVP構成
━━━━━━━━━━━━━━━━━━

myrigrc.com
  → Vercel Pro
  → Next.js

DB / Auth / RLS
  → Supabase Pro
  → Tokyo ap-northeast-1

media.myrigrc.com
  → Cloudflare R2
  → ユーザー投稿画像の正本

Rate Limit / counters / emergency state
  → Upstash

Bot / abuse防御
  → Cloudflare Turnstile

認証メール等
  → Custom SMTP

CloudflareをVercelのreverse proxy前段には置かない。
メインWebはVercelへ直接。
CloudflareはR2・DNS・Turnstile等に使う。

━━━━━━━━━━━━━━━━━━
■ 2. Cloudflare Images → R2 は変更する
━━━━━━━━━━━━━━━━━━

これは3AIでほぼ合意。

Cloudflare Images Hostedは「画像が表示される回数」に比例して
配信料金が増えるため、Feed / Garage / Search / LOG等で
大量に画像を表示するMyRIGとの相性が悪い。

R2は保存容量と操作回数課金を中心とし、
Internet egressが無料。

したがって画像正本をR2へ変更する。

DBには完全URLを保存せず、

storage_key

を保存する。

例:

users/xxx/rigs/yyy/card.webp

描画時に

https://media.myrigrc.com/ + storage_key

を合成する。

これにより将来R2からS3等へ移行してもDB全行を書き換えずに済む。

画像変換はリクエスト時の動的変換を基本的に行わない。

アップロード時に一度だけ固定variantを生成して保存する。
具体的な生成場所（browser / Vercel Function / Worker）は実装時に決定し、
正典では固定しない。

クライアント側では事前縮小を行い、
サーバ側にも必ずhard validationを持たせる。

━━━━━━━━━━━━━━━━━━
■ 3. SupabaseはMVPで維持
━━━━━━━━━━━━━━━━━━

Supabase Pro Tokyoを採用する。

理由:

- Tokyoリージョンあり
- PostgreSQL
- 現行MyRIGのRLS設計を維持可能
- Auth / DB統合
- Spend Capあり
- MVP開発速度が高い

Supabase Proは現在100,000 Auth MAUを含む。

ただし重要なのは、

総MAU ≠ Authenticated MAU

であること。

未ログイン閲覧者はAuth MAUではない。

コストモデルでは必ず

- Total MAU
- Authenticated MAU

を分離する。

Authenticated率を正典で40%等に固定しない。

試算時のみ、

Base Case = 40%
Heavy Case = 70%
Stress Case = 100%

等で評価し、公開後は実測値へ置き換える。

Auth使用量が契約quotaの80%程度へ近づいたら、
Supabase継続 / Spend Cap解除 / Auth移行等を再審査する。

「80,000ユーザー」という固定数値ではなく
「quotaの80%」を判断基準とする。

━━━━━━━━━━━━━━━━━━
■ 4. Neon / D1 / VPS
━━━━━━━━━━━━━━━━━━

Cloudflare D1:
MVP非採用。

理由:
SQLite / 1 DB容量制約 / PostgreSQL RLS非互換 / 横断Feedとの相性。
MyRIGをD1に合わせて設計変更するコストの方が大きい。

Neon:
MVP非採用。

現在はTokyoリージョンがなく、
今回最重要の「請求上限耐性」でもSupabaseを置き換える決定打に欠ける。

ただし恒久REJECTにはしない。
将来Tokyo追加・料金・hard cap等が改善すれば再評価する。

VPS / dedicated:
MVPでは採用しない。

固定費は魅力だが、
DB保守・バックアップ・セキュリティ・障害対応を
ソロ開発者が背負うコストの方が大きい。

将来の脱出路としてのみ残す。

━━━━━━━━━━━━━━━━━━
■ 5. VercelはMVP採用、永久固定しない
━━━━━━━━━━━━━━━━━━

Vercel ProをMVPで採用。

理由:

- Next.jsとの相性
- 開発速度
- 運用負荷が低い
- Spend Managementがある

ただしVercel固有APIへアプリを強く依存させない。

以下を守る。

- Vercel Blobを画像正本にしない
- provider固有ヘッダをrequest-context層に閉じ込める
- Rate limit storeを抽象化
- Vercel固有Cron等への依存を最小化
- 将来Workersでbuildできる状態を維持

将来のCloudflare Workers移行アダプタ名は固定しない。

OpenNext / vinext等は変化するため、
「移行時点のCloudflare公式推奨方式を採用」とする。

Workersへ移る条件も
「Vercel月$300」等で正典固定しない。

Vercel従量費が全体予算に対して無視できない割合になり、
移行コストより年間削減額が大きくなった時点で再評価する。

━━━━━━━━━━━━━━━━━━
■ 6. MVP時点のコスト感
━━━━━━━━━━━━━━━━━━

試算前提:

- Total MAU基準
- 40 PV / MAU / 月
- 動画なし
- R2固定variant
- Feed / Searchを適切にcache
- N+1等を回避
- Auth MAU超過なし
- 通常利用でBot暴走なし

あくまで計画用概算。

1,000 MAU
  月4万PV
  約 $45〜60
  約 7,000〜9,000円/月

10,000 MAU
  月40万PV
  約 $70〜90
  約 1.1〜1.4万円/月

50,000 MAU
  月200万PV
  約 $170〜230
  約 2.7〜3.6万円/月

100,000 MAU
  月400万PV
  約 $350〜420
  約 5.5〜6.6万円/月

100kにはPITR等の安全運用費も含めて余裕を持たせた想定。

MyRIGは既存RCブログが現在30日アクティブ約4.2万人あるため、
10万Total MAUは夢物語として扱わず、
MVP時点から10万MAU程度まで安全に進める設計にする。

━━━━━━━━━━━━━━━━━━
■ 7. 収益との関係
━━━━━━━━━━━━━━━━━━

MyRIGは副業・趣味プロジェクトであり、
長期間の赤字投資は前提にしない。

40 PV / MAUを前提にすると、
広告だけでインフラ費を回収するために必要な実効RPMは概算:

1,000 MAU
  4万PV / 約8千円
  → RPM 約200円

10,000 MAU
  40万PV / 約1.2万円
  → RPM 約30円

50,000 MAU
  200万PV / 約3万円
  → RPM 約15円

100,000 MAU
  400万PV / 約6万円
  → RPM 約15円

したがって正常に成長すれば、
固定費割合はむしろ小さくなる可能性が高い。

さらにMyRIGはRIG / PARTSページから
アフィリエイト収益を載せられる余地がある。

ただし「将来収益が出るはず」を理由に
赤字インフラを長期間維持しない。

━━━━━━━━━━━━━━━━━━
■ 8. 財務ガードレール
━━━━━━━━━━━━━━━━━━

これは今回の重要追加。

技術上のSpend Capだけでなく、
MyRIG自身に「副業として許容する支出上限」を持つ。

推奨運用:

収益が十分に立つ前:
  自己負担するインフラ予算の目安を月1万円程度に置く。
  超えそうなら無条件にプランアップするのではなく、
  cache / economy / restricted等を先に使う。

収益化後:
  予算判断は「予想収益」ではなく
  AdSense + Affiliate等の実現済み収益の
  trailing 3か月平均を基準にする。

通常:
  Infra Cost <= Revenueの20%程度を目標

20〜30%超:
  cost warning / 最適化検討

30〜40%超:
  economy運転や高コスト機能の制限を検討

50%近辺:
  追加投資ではなくrestricted / 構成変更を優先

Infra Cost > Revenueが継続:
  規模を縮退する。
  「将来伸びるから」という理由だけで赤字を長期間補填しない。

上記割合は運用目安であり正典固定値にはしない。
重要なのは、

「インフラ予算は実現済み収益とオーナーが明示した自己負担上限の
小さい方に拘束される」

という原則。

プロバイダのspend limitを引き上げる場合も、
収益実績またはイタヤの明示承認なしに自動で上げない。

━━━━━━━━━━━━━━━━━━
■ 9. Provider側の安全装置
━━━━━━━━━━━━━━━━━━

Vercel:
Spend Management ON。
通知 + Production pauseを最後のhard brakeとして使用。

Supabase:
Spend Cap ONを基本とする。
MAU / egress等の対象項目はquota超過前提で青天井にしない。

Cloudflare:
R2等はBudget Alertだけに依存しない。
Cloudflare側に完全なhard budget capが無い項目は
アプリ側rate limit / kill switchで防御。

Upstash:
max budgetを設定。

これらは「最後の停止装置」であり、
通常はその手前のdegraded modeで止める。

━━━━━━━━━━━━━━━━━━
■ 10. 多段Degraded Mode
━━━━━━━━━━━━━━━━━━

normal

↓

economy
- anonymous cache TTL延長
- Feed件数削減
- anonymous Search制限
- expensive recommendation停止
- 高コスト処理停止

↓

restricted
- 新規Signup停止
- 画像アップロード停止
- 高コストwrite停止
- テキストLOG等の軽い機能は可能な限り維持

↓

readonly
- 新規write停止
- Public RIG / PARTS / LOG / Garage閲覧中心

↓

frozen
- Originへの通常アクセス停止
- Cloudflare側の静的survival pageへ切替
- データは保持

「サービス全面停止」へ一気に行かない。

━━━━━━━━━━━━━━━━━━
■ 11. Emergency State
━━━━━━━━━━━━━━━━━━

Supabaseを緊急状態の権威にしない。

Supabase障害・Spend Cap発動時に
緊急状態を取得できなくなるため。

MVP Vercel期:
  authority = Upstash

ただしUpstashも停止し得る。

Emergency Stateを取得不能の場合:

Public READ:
  economy相当で継続

Signup / Image upload / expensive WRITE:
  restricted相当としてfail closed

つまり
「設定ストアが壊れたからnormal運転へ戻る」
ことは禁止。

frozenだけはUpstashと独立し、
Cloudflare DNS切替等の別レバーを持つ。

将来Workers移行後:
  authorityをWorkers KV等のEdge側へ移すことを検討。

━━━━━━━━━━━━━━━━━━
■ 12. DB負荷改善
━━━━━━━━━━━━━━━━━━

ベンダー変更より先にアプリ設計を改善する。

現行正典にある

view_countをページ表示ごとに直接UPDATE

は変更候補。

Redis等へbufferし、
一定時間ごとにDBへbatch反映する。

like / favorite / comment件数についても、
毎表示でCOUNT(*)しない。

counter列等をtransaction/trigger等で維持する。

Feed:
- cursor pagination
- 1回のqueryで必要情報を取得
- N+1禁止
- 無限バックグラウンドpolling禁止

Search:
MVPはPostgreSQLの機能を使用。
外部Search SaaSを最初から導入しない。

Realtime中心のFeedもMVPでは使わない。

━━━━━━━━━━━━━━━━━━
■ 13. Abuse制限
━━━━━━━━━━━━━━━━━━

通常ユーザーに「投稿を我慢させる」目的の制限ではなく、
機械・Bot・バグには絶対到達させない上限を置く。

現在の画像枚数制限
RIG / PARTS / LOG
は維持。

さらに、

- 画像サイズhard validation
- 1日upload上限
- 1日投稿上限
- Search rate limit
- Comment / Like rate limit
- Signup Turnstile
- Presigned upload
- MIME / actual content検証

等を持つ。

上限値は実装・運用値なので正典へ細かく固定しない。

━━━━━━━━━━━━━━━━━━
■ 14. 採用 / 非採用
━━━━━━━━━━━━━━━━━━

MVP採用:
- Vercel Pro
- Supabase Pro Tokyo
- Cloudflare R2
- Upstash
- Turnstile
- Custom SMTP

MVP非採用:
- Cloudflare Images Hosted
- Cloudflare D1
- Neon
- 自前VPS
- Vercel Hobby
- 外部MAU課金Auth SaaS
- 外部Search SaaS
- Realtime中心Feed

ただしNeon / VPS等は恒久REJECTではない。
将来条件が変化すればゼロベースで再評価する。

━━━━━━━━━━━━━━━━━━
■ 15. 正典への残し方
━━━━━━━━━━━━━━━━━━

CURRENTには、

- 採用構成
- R2化
- storage_key
- 可搬性原則
- cost safety / degraded mode
- Total MAU と Auth MAUを分離すること
- DB負荷改善方針
- 将来再評価ポイント

など、後続AIが知らないと壊れる内容を残す。

2026年時点の料金単価、
「Vercel $300」
「Auth 80,000」
「月1万円」
「20% / 30% / 50%」
等の変動する運用値はCURRENTへ恒久固定しない。

具体料金・今回の比較・不採用理由は

_decisions/2026-09-05_infra-cost-resilience-v1.md

等へ日付付きの「罠の地図」として残す。

━━━━━━━━━━━━━━━━━━
■ 最終判断
━━━━━━━━━━━━━━━━━━

MyRIGは
「アクセス増 = 赤字増」
になる構成にはしない。

また
「将来100万ユーザーになるかもしれない」
ことを理由に、
今から複雑で運用負荷の高い構成にも行かない。

MVPではVercel + Supabase Tokyoのmanaged環境で
開発速度と安全性を取り、
画像の最大変動費だけR2へ逃がす。

通常成長時は収益でインフラ費を吸収し、
収益より費用の方が速く伸びた場合は、

cost optimization
→ economy
→ restricted
→ readonly
→ frozen

の順で止める。

そしてapp層は将来Workers等へ逃げられる状態を最初から維持する。

「成功しても破産しない」
「成功しなくても長期間赤字を抱えない」

この2つを同時に満たす構成をMyRIGのインフラ方針とする。
