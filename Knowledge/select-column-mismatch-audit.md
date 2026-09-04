---
date: 2026-09-04
tags: [database, supabase, postgrest, verification, non-fatal]
project: 共通（なかまクエスト・SQWサーベイ2）
related: [[non-fatal-write-failure-and-column-mismatch]] [[build-pass-not-runtime-ok]]
---

# 読み取りの列名不一致も気づかれない。select の列は実DBと機械的に突合する

書き込み（[[non-fatal-write-failure-and-column-mismatch]]）と**同じことが読み取りでも起きる**。
違うのは症状で、**書き込みは「データが入らない」・読み取りは「表が丸ごと出ない」**。
どちらも例外にならず、画面はエラーを出さずに"何も無い"ように見える。

## 実例（2026-09-04・なかま WHI Admin）

`getMemberDetailForWhiAdmin` が `coaching_schedules` から **`week, type`** を select していたが、
実列は **`week_number, schedule_type`**。

```ts
// 誤（毎回 42703 で失敗していた）
.from("coaching_schedules").select("week, type, scheduled_at, sent_at")
```

- PostgREST は `42703 column coaching_schedules.week does not exist` を返す。
- 呼び出し側は `const { data: sch } = await ...` と**error を受け取っていない**ため、
  `sch` が `null` → `schedules = []` → **「📅 メール配信スケジュール」表が一度も表示されなかった。**
- 気づけなかった理由＝**「まだ旅を始めていないから予定が無いのだろう」と読めてしまう**。
  空表示は"正常な空"と見分けがつかない。

## なぜ型チェックで止まらないか

`select()` の引数は**ただの文字列**で、返る行の型も `Record<string, unknown>` 相当。
`s.week` と書いても**存在しない列を読んでいることを tsc は検知しない**（`undefined` になるだけ）。
**tsc PASS・next build PASS でも列名の誤りは残る。**

## 突合のやり方（読み取りだけでできる・1回で全件）

コードから `.from("X").select("...")` の組を抜き出し、そのまま実DBへ投げる。
**エラーが返れば不一致**（`42703`）、返らなければ一致。データは1行も変えない。

```js
const re = /\.from\("([a-z_0-9]+)"\)\s*\n?\s*\.select\(\s*"([^"]+)"/g;
// 抽出した [table, cols] を順に：
const { error } = await supabase.from(table).select(cols).limit(1);
console.log((error ? "NG " + error.code : "OK") + "  " + table + "  [" + cols + "]");
```

実列そのものを見たいときは `select("*")` を1行取って `Object.keys(row)`。

## 結果（なかま `src/lib/actions/whi-admin.ts`・2026-09-04）

**全 52 件の select を突合し、不一致は 1 件だけ**（上記 `coaching_schedules`）。
→ 列名を `week_number, schedule_type` に直し、`schedules` へのマップも合わせて修正。
実画面で Week1〜4 のリマインド／振り返り8行が表示されることを確認した。

## チェックリスト

1. **DBに触るファイルを直すときは、まずそのファイルの select を全部突合する**（1回・数十秒）。
   直す前に回すと「今回の不具合の範囲」が確定できる。
2. **`const { data } = await ...` で error を捨てない。** せめて `console.error` に出す
   （出していれば今回も早く気づけた）。
3. **「0件」「表が出ない」を見たら、まず列名の不一致を疑う。** 空データと区別がつかない。
4. **列名を変えた／テーブルを作り直したら、そのテーブルを読む全コードを grep する。**
5. tsc・build は列の実在を検証しない。**実DBに投げるまで分からない。**

## 関連

- 書き込み側の同じ罠＝[[non-fatal-write-failure-and-column-mismatch]]（organic の `team_members.email`・15チーム全件0行）
- ビルド成功は動作の証明にならない＝[[build-pass-not-runtime-ok]]
