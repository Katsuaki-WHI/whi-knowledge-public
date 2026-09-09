---
date: 2026-09-09
tags: [supabase, rls, sql-editor, false-positive, verification]
project: 横断（SQW・なかま）
related: [[2026-07-14-sqw-rls-design-final]] [[mistakes]]
---

# Supabase SQL Editor の「Potential issue detected（RLS）」は INSERT/UPDATE でも出る＝多くは誤検知

## 何が起きるか

Supabase の SQL Editor で SQL を実行しようとすると、実行前に
**「Potential issue detected with your query」**（RLS を無効化する・ポリシーを迂回する可能性がある、といった趣旨の警告）
が出て、確認を求められることがある。

**この警告は「RLS を触る SQL」だけに出るのではない。**
`INSERT` / `UPDATE` / `ALTER TABLE ... ADD COLUMN` のような、
**RLS の設定を一切変更しない SQL でも出る**（静的な文字列マッチに近い検知のため）。

## どう扱うか

1. **警告が出たこと自体を「RLS が変わる」の証拠にしない。** まず**自分の SQL に RLS 関連の文が実際に含まれているか**を読んで確かめる。
   - 本当に注意すべき文＝`alter table ... disable row level security` / `drop policy` / `create policy ... using (true)` / `grant ... to anon` など。
   - これらが**1つも含まれていなければ誤検知**。データの追加・更新・列追加は RLS の設定を変えない。
2. **確かめた結果を報告に書く。**「警告は出たが、SQL に RLS を変更する文は含まれない（`INSERT` のみ）」と明記して実行する。
3. **迷ったら実行前に、対象テーブルの現在の RLS 状態を控えておく**（`pg_policies` の行・`relrowsecurity`）。実行後に同じクエリで**変化がないこと**を確認すれば、誤検知だったことを事実で示せる。

```sql
-- 実行前後で比べる（変化なし＝RLS は動いていない）
select schemaname, tablename, policyname, cmd
from pg_policies where tablename = '<table>';

select relname, relrowsecurity
from pg_class where relname = '<table>';
```

## なぜ記録するか

RLS は SQW で4段構成の遮断を組んだ生命線（[[2026-07-14-sqw-rls-design-final]]）で、
「RLS の警告」は本来ならいちばん慎重に扱うべきもの。
だからこそ**誤検知で毎回止まる／逆に警告に慣れて本物を見落とす**の両方が起きやすい。
**警告の有無でなく SQL の中身で判断する**というルールを持っておくと、どちらも避けられる。

## 出典

SQWサーベイ2 パルスサーベイ 段階2〜6（2026-09-04〜09）。
`pulse_*` テーブルの追加・`email_templates` の文面 seed・`teams` への列追加などを
かつさんが SQL Editor で実行する際に毎回この警告が出た。いずれも RLS を変更する文は含まれていなかった。
