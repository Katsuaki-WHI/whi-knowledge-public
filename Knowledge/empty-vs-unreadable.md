---
date: 2026-10-05
tags: [supabase, 設計, 失敗の型, 検証]
project: (一般)
related: [[non-fatal-write-failure-and-column-mismatch]] [[select-column-mismatch-audit]] [[service-role-only-table-silent-empty]] [[fail-closed-for-irreversible-actions]]
---

# 「0件」と「読めなかった」を同じ値で表さない

**Supabase のクライアントは、表が無い・権限が無いときも例外を投げない。**
`{ data: null, error }` を返すだけ。だから `error` を見ないコードでは

- 「**まだ1行も無い**」（正常）と
- 「**読めなかった**」（壊れている）

が**まったく同じ値（null）になる**。症状は「何も無い」なので、
**壊れていることに気づけない**（エラーも出ない・画面も落ちない）。

## どこで効くか

いちばん効くのは**新しい表を足す工事**。順番はふつう「コードを先に出し、SQL を後で流す」なので、
**その間だけ表が存在しない窓**ができる。そこで

```ts
const { data } = await supabase.from("new_table").select("...").eq("id", x).maybeSingle();
const hasKey = !!data?.key;   // ← 表が無いときも false になる
```

と書いていると、**「表がまだ無い」が「その人はまだ設定していない」と同じ見え方**になる。
鍵・権限・購入の判定に使っていたら、**判定そのものが静かに崩れる。**

## やること

```ts
function throwIfUnreadable(error: { message?: string; code?: string } | null, where: string) {
  if (!error) return;                      // ★0件は error ではない（maybeSingle は null を返すだけ）
  throw new Error(`${where} を読めませんでした（code=${error.code ?? "?"}）: ${error.message ?? ""}`);
}

const r = await supabase.from("new_table").select("...").eq("id", x).maybeSingle();
throwIfUnreadable(r.error, "new_table");   // ← ここが肝
const row = r.data ?? null;                // 0件はここで素直に null
```

そのうえで**呼ぶ側が用途で倒す**：

| 用途 | 読めなかったとき |
|---|---|
| 有料コンテンツを見せるか | **見せない**（閉じる）＋専用の画面文言＋ログに大きく残す |
| 取り消せない操作（削除・送信） | **止める** |
| 一覧の飾り（印・バッジ） | **落とさない**。ただし**黙らせない**（ログに残す） |

★**「閉じる」と「止める」は、真偽値で見ると逆向きになる**ことがある。
　揃えるのは**値ではなく判断**（[[fail-closed-for-irreversible-actions]]）。

## 見つけ方（★これが本題）

机の上では見つからない。見つけたのは、

1. **その窓を実際に作って**（新しい表がまだ無い状態で、機能のスイッチを一時的に ON にして）
2. **塞ぎたい側（中身が返らない）だけでなく、ログも読んだ**から。

★**塞ぎたい側だけ見ると「効いた」で終わる。**
　中身が返らないのは**正しい結果**だが、**理由が違っていた**
　（「パスワードが無いから閉じた」のではなく「表が読めなかったから閉じた」）。
　ログに1行も出ていないことが、唯一の手がかりだった。

★一般則＝**デプロイしたら、塞ぐものと通すものの両方を叩く。
　そして「期待どおりの答え」が「期待どおりの理由」で返っているかをログで確かめる。**

## 似ているが別のもの

- [[non-fatal-write-failure-and-column-mismatch]]＝**書き込み**が静かに失敗する（列名違い）
- [[select-column-mismatch-audit]]＝**読み取り**の列名違いで表が丸ごと出ない
- [[service-role-only-table-silent-empty]]＝**権限**で空が返り、フォールバックが採用され続ける

★3つとも**症状が「何も無い」**で同じ。
　**だから「何も無い」を見たら、まず「読めなかったのではないか」を疑う。**
