---
date: 2026-10-07
tags: [postgrest, supabase, 検証]
project: nakama-quest
related: [[empty-vs-unreadable]] [[throwaway-postgres-sql-check]] [[before-dropping-a-backup-table]]
---

# `head: true`（HEAD リクエスト）はエラーの中身を隠す

PostgREST / supabase-js で**件数だけ欲しいとき**に使う
`select("*", { count: "exact", head: true })` は **HTTP の HEAD** を投げる。
**HEAD は本文を返さない**ので、**PostgREST が本文に入れている
`code` / `message` / `details` が受け取れない**。

つまり：

- 表が無い → 本来 `PGRST205` → **`code` が空**
- 権限が無い → 本来 `42501` → **`code` が空**

★**`error` は truthy になるが、中身が無い。**
だから `error.code === "42501"` のような判定は **必ず false** になり、
**「閉じているのに『読めてしまう』と出る」「消えているのに『まだある』と出る」**。

## どうする

★**「無い／読めない」を確かめるところは、本文の返る GET で見る。**

```
// ✗ 件数は取れるが、落ちた理由は取れない
await sb.from(t).select("*", { count: "exact", head: true });

// ○ 落ちた理由（code / message）まで返る
await sb.from(t).select("*").limit(1);
```

**件数が欲しいだけのところは `head: true` のままでよい**（速い）。
**分けて使う。**

## なぜ気づきにくいか

- **成功しているときは、どちらでも同じに見える**（差が出るのは落ちたときだけ）。
- 落ちたときの表示が「**空**」なので、
  **「エラーが無い＝通った」と読めてしまう**。
  ★**同じ種類の間違い**＝[[empty-vs-unreadable]]（「0件」と「読めなかった」を同じ値で表さない）。

## 一般則

★**「失敗したこと」を合格条件にするなら、失敗の種類まで見る。**
そして**失敗の種類が受け取れる呼び方を選ぶ。**
受け取れない呼び方のまま種類を判定すると、**検査は静かに嘘をつく**。

出典＝なかまクエスト バックアップ表削除の反映確認（2026-10-07）。
「2表とも消えている」「6表とも閉じている」が**どちらも実際は正しかったのに**、
検査だけが8件の不合格を出した。
