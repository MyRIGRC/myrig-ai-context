# RLSセキュリティモデル 裁定記録 v1

**status: ADOPTED（2026-08-22 イタヤ裁定・revision017）**
**2026-08-22 GPT外部監査（revision018監査）で以下の残課題を追加指摘・同日中に反映済み:**
**likesが親公開判定から漏れていた／favoritesの公開カウントに親公開判定が無かった／
部分UNIQUEインデックスの実DDLが無かった。詳細は`docs/schema/myrig_db_schema_v1_6.md`本文（確定版）を参照。
本ファイルは裁定時点の検討記録として保存する。**
**対象: GPT外部監査 最優先4系統C（`_audit/gpt-review-20260821.md`）**
**関連: `docs/schema/myrig_db_schema_v1_6.md` RLS節**

---

## 1. 問題（現物確認済み・4件）

現行スキーマのRLS方針は「SELECT全公開」を基本にしており、非公開データを保護できていない。

| # | 対象 | 現状定義 | 現状RLS | 問題 |
|---|---|---|---|---|
| C-1 | `pins` | 「**非公開**・一時保存」（296行） | SELECT全公開（582行） | 定義とRLSが正面から矛盾 |
| C-2 | `favorites`/`pins` | ― | SELECT全公開 | `rigs/parts/maintenance_logs.is_public=false` でも、`favorites`/`pins`の個別行はDB直参照で読める。Public Garageの非表示規定（page-role-matrix）をDB層で迂回できる |
| C-3 | `images` | 親（rig/part/log）に従属 | SELECT全公開（Cloudflare URL推測不可能性で担保） | 親が非公開でも`images`行は読め、URLを取得できる |
| C-4 | `comments` | 親に従属 | `status='published'`のみで判定 | 親の`is_public`を検査していない。非公開RIGへのコメントも全公開になる |

共通する構造: **`entity_type`+`entity_id`のpolymorphic参照先（rig/part/log）の`is_public`を、子テーブルのRLSが一切見ていない。**
「UI側でPublic Garageから隠す」という対策は、DB直接クエリに対しては無力。

## 2. 対策案

### 案A: 子テーブルのSELECTポリシーで親の公開可否をJOIN判定する（推奨）

`favorites`/`pins`/`images`/`comments`のSELECTポリシーを、以下のような共通パターンに変更する。

```sql
-- 例: images
CREATE POLICY images_select ON images FOR SELECT USING (
  user_id = auth.uid()
  OR (
    CASE entity_type
      WHEN 'rig' THEN EXISTS (SELECT 1 FROM rigs WHERE id = entity_id AND is_public = true AND deleted_at IS NULL)
      WHEN 'part' THEN EXISTS (SELECT 1 FROM parts WHERE id = entity_id AND is_public = true AND deleted_at IS NULL)
      WHEN 'log' THEN EXISTS (SELECT 1 FROM maintenance_logs WHERE id = entity_id AND is_public = true AND deleted_at IS NULL)
    END
  )
);
```

`pins`は「非公開」定義なので、他人からのSELECTは常に不可（owner限定）にする。
`favorites`は「公開カウント」の要件があるため、**個別行は非公開のまま、集計値（COUNT）だけを別関数/ビューで公開**する。

- 長所: RLSの原則（DB層で完結）を守れる。ポリモーフィック参照は既存の`entity_type`/`entity_id`構造のままで対応可能
- 短所: ポリシー内のCASE分岐がテーブル数×3種類になり、schema変更（entity_type追加等）のたびにポリシーも追随が要る
- 影響範囲: `images`/`favorites`/`pins`/`comments`の4ポリシーの書き換え。アプリ側の集計ロジック（likes/favorites件数取得）を関数経由に変更する必要がある可能性

### 案B: 集計値のみをキャッシュ列/ビューで公開し、個別行は常にowner限定にする

`likes`/`favorites`/`pins`/`comments`を全てowner限定（`user_id = auth.uid()`のみSELECT可）にし、
「◯件いいねされている」等の表示は`rig_stats`のような集計ビュー経由に統一する。

- 長所: ポリシーがシンプルになる（JOIN不要）。将来のテーブル追加にも強い
- 短所: 集計ビューの新規設計・実装コストが発生する。「誰が いいねしたか」を公開する要件がある場合は別途対応が要る
- 影響範囲: 案Aより設計変更が大きい。ビュー新設、集計更新のタイミング設計（trigger/materialized view）が必要

### 案C: 現状維持＋アプリ層でのみ制御（非推奨）

RLSは変更せず、Next.js側のクエリで必ず親の`is_public`をチェックする運用にする。

- 長所: DB変更が不要
- 短所: **CORE/GPT監査が既に指摘した通り「UI非表示はアクセス制御にならない」の再演。** APIエンドポイントを増やすたびに同じチェックを漏れなく実装する必要があり、実装ミスが即データ漏洩に直結する。RLS導入の意味が失われる

## 3. 推奨

**案A**を推奨する。理由:

- `pins`/`favorites`は「一覧表示」より「自分の管理データ」としての利用が主で、他人に個別行を見せる要件がそもそも薄い（Public Garageでは非表示の規定が既にある）
- `images`/`comments`は「親が公開なら子も公開」という直感的な規則で説明でき、既存のUI仕様（Public Garageでの非表示ルール）と矛盾しない
- 案Bの集計ビュー新設は、現時点でパフォーマンス要件が明確でない（`myrig_db_schema_v1_6.md`556行「like_count/favorite_countはCOUNTで取得、キャッシュは問題発生時に追加」）ため、先取りして複雑化する必要が薄い

## 4. 裁定結果（2026-08-22 確定）

1. **案A採用**
2. `favorites`の「公開カウント」要件は**維持**（COUNT用の関数/ビュー経由。GPT監査で「親が公開の場合のみ」の条件を追加）
3. `pins`は**完全非公開（owner限定）で確定**
4. 実装タイミング: **モックアップ完成後、Next.js着手時**

反映先: `docs/schema/myrig_db_schema_v1_6.md` RLS節（確定版）。本ファイルは検討経緯の記録として残す。
