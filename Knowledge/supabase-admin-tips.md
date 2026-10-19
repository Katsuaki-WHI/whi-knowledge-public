---
date: 2026-10-19
tags: [supabase, auth, 権限, 検査, ローカル]
project: both
related: [[head-request-hides-the-error-code]] [[throwaway-postgres-sql-check]] [[rls-four-stages-done-check]] [[local-env-files-override-and-prod-db]]
---

# Supabase を管理者として触るときの落とし穴

## 1. `listUsers` は「2段階確認の登録」を教えてくれない

★**`auth.admin.listUsers()` の戻り値には `factors` が入らない**（欄ごと無い）。
これを「登録していない」と読むと、**アプリを登録している人まで「まだ登録していません」と出る**（本番で実際に出た）。

- **Correct**＝人ごとに **`auth.admin.mfa.listFactors({ userId })`** を呼び、
  `status === "verified"` の TOTP があるかを見る。
- ★**一覧の画面では人数ぶん呼ぶことになる**ので `Promise.all` で同時に投げる
  （直列だと一覧が目に見えて遅くなる）。
- ★一般則＝**「返ってこなかった」と「無い」を同じに扱わない。**
  欄が無いのか、値が空なのかを**実物の戻り値で**確かめる（[[empty-vs-unreadable]] と同じ型）。

## 2. 「TRUNCATE を誰も持っていない（=0）」は、必ず false になる

★**表の持ち主（`postgres`）は必ず TRUNCATE を持つ。**
照合SQLに「TRUNCATE を持つロールが0件」と書くと**いつも false**＝嘘の不合格が出る。

- **Correct**＝見るのは「**`anon` / `authenticated` / `PUBLIC` が持っていないこと**」。
  持ち主（`postgres`）と `service_role` だけが並んでいるのが正しい姿。
- ★あわせて＝**RLS は TRUNCATE を止めない**（ポリシーが service_role だけの表でも、
  `anon` に TRUNCATE があれば表を空にできる）→ **全部の表から
  `TRUNCATE` / `REFERENCES` / `TRIGGER` を外す**。

## 3. 本番と同じ関数を「ローカルの Supabase」で通すときの段取り

★**これが通ると、DB が要る検査（ログイン・コード・失効）を本番に触らずに全部通せる。**

1. ★**使い捨ての別フォルダで立てる**（`supabase init` → `supabase start --workdir <そのフォルダ>`）。
   ★**製品のリポジトリで `supabase start` しない**＝`migrations/` に
   **`✅済み_…` や `⚠️使わない_…_戻す.sql` のような名前**が混ざっていると、
   CLI は**時刻で始まる名前だけ**を拾い、**戻す SQL のほうを当てる**ことがある。
   → **空の DB を立てて、必要な SQL だけ自分で当てる**ほうが速くて安全。
2. ★**CLI の版が違うと `config.toml` が読めない**（キー名が変わる＝新しい版の
   `[local_smtp]` は古い版では `[inbucket]`・`orioledb_version` は古い版に無い）。
   **立てる CLI と作る CLI を同じ版にする**か、キー名を合わせる。
3. SQL は **`docker exec -i supabase_db_<フォルダ名> psql -U postgres` に流し込む**
   （★手元に `psql` が無くても通る）。
4. ★**素の node から製品のコードを読むには2つ足りない**＝
   ⑴**TS と `@/` の解決**→ `npx tsx <script>`
   ⑵**`server-only` の解決**→ 製品が Next 任せでこのパッケージを持っていないことがある。
   **使い捨ての場所に空の `server-only`／`client-only` を置いて `NODE_PATH` で渡す**
   （★**製品の `package.json` も `node_modules` も触らない**）。
5. ★**「ローカル以外を指していたら最初に止まる門」を検査の1行目に置く**
   （URL に `127.0.0.1` が入っていなければ throw）。
   ★**門が効くことも1回確かめる**（★本番のURLでなく**偽のURL**で試す）。
6. ★**メールの鍵を渡さない**＝`RESEND_API_KEY` を環境に入れなければ1通も出ない。
   「送れたか」を返す欄（`mailDelivered`）が **false** になることまで検査に入れると、
   **送っていないことが毎回示される**。

## 4. 2つの時計を測ってしまう検査を書かない

★**DB の `now()` と、こちらの `Date.now()` は数百ミリ秒ずれる。**
「待ち秒数は60以下」のような**上限**を DB 越しの検査で見ると、
経過が -1 秒になって **61** が返り、**製品は正しいのに検査だけが落ちる**（実測）。

- **Correct**＝DB が要る検査では「**正の数が返ること**」まで。
  **上限・下限は純粋な関数の全数検査で見る**（★そこには時計が1つしかない）。
- ★一般則＝**検査が何を測っているかを1行で言えないなら、その検査は別のものを測っている。**
