---
date: 2026-05-28
tags: [nakama-quest, sqw-survey, email, resend, local-dev, verification]
project: nakama-quest
related: [[build-pass-not-runtime-ok]] [[4-level-verification-protocol]]
---

# ローカルResendキーは本番と別物・メール実物確認は「送信ゼロ捕捉」で行う

## 発見

- ローカル `.env.local` の `RESEND_API_KEY` は、本番Vercelの検証済みキーと**別物**だった。
- ローカルキーは送信ドメイン（`mail.nakama-survey.com`）が**未検証**・月間クォータも極小の
  限定キーで、検証済みドメインからの**実送信ができない**
  （Resendが `403 "domain is not verified"` を返す）。
- そのため、メール文面の実物確認をローカルで「実送信」しようとすると失敗する。
  しかも Resend SDK は 403/429 を throw せず `{ data: null, error: {...} }` で返すため、
  呼び出し側で `ok:true` でも **`id` が空文字なら未送信**として扱う必要がある。

## Do（次回こうする）

- メール本文の実物確認は、**対象の送信関数を本番と同じ引数で呼び**、
  グローバル `fetch` を横取りして **実送信せずに** リクエストボディ
  （`subject` / `html` / `text`）を捕捉し、HTMLに書き出して目視する。
  - 手順：`globalThis.fetch` を差し替え → `api.resend.com/emails` 宛なら
    body を捕捉してモック応答（`{ id: "..." }`）を返す → 送信関数を動的importして呼ぶ。
  - これで「本番と同一の生成結果」を**送信ゼロ**で確認できる。
- 確認用の使い捨てスクリプトは**確認後に削除**する（書き出したHTMLは目視用に残してよい）。
- 本番（Vercel）は検証済みキーのため、**実運用の配信は正常**。実物の最終確認が必要なら
  本番デプロイ後に確認するか、ローカルに検証済みキーを一時的に入れて行う。

## Trigger

- なかまクエスト / SQWサーベイ2 のメール文面を変更し、実物（HTML/レイアウト/CTA/リンク）を
  確認したいとき。特にローカルから実送信が失敗するとき。

## 注意

- APIキーの実値は記録しない（本ノートにも書かない）。
- 「ローカルと本番でキー・検証済みドメイン・クォータが異なりうる」ことを前提に、
  ローカルの送信結果＝本番の挙動、と早合点しない。
