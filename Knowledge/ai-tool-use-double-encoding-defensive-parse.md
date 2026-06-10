---
date: 2026-06-11
tags: [anthropic, tool-use, json, defensive-parse, nextjs, rsc, supabase, cookie]
project: nakama-quest
related: [[mistakes]], [[4-level-verification-protocol]], [[rsc-marshalling-violation-columns-with-render]]
---

# AI Tool Use出力の二重エンコード問題と防御的パース／サーバー間fetchのCookie落とし穴

## 事象（2026-06-11・しののん事象）
なかまクエストで「ベーシック・GLBは出るのに冒険バイブルだけ表示されず、管理画面から再生成もできない」。
調査の結果、独立した3つの問題が重なっていた。

### 1. AI Tool Useが一部の章を「JSON文字列」で返す（非決定的）
Anthropic Claude の Tool Use（構造化出力）は、複雑なネスト構造で**稀に一部のキーを
「オブジェクト」ではなく「JSON文字列（二重エンコード）」で返す**ことがある。
- 例：`{ "chapter1": "{\n  \"intro\": ... }", "chapter2": { ... } }`
  （chapter1だけ文字列、他はオブジェクト）
- Tool Use の input_schema で `type:"object"` と正しく定義していても起こる（スキーマの誤りではない）。
- **さらに悪いケース**：その文字列が**そもそも不正なJSON**になることがある。
  実例では章本文に未エスケープの二重引用符（`その"過程"が…`）が混入し `JSON.parse` が失敗した。

### 2. 検証なし保存 → 表示時クラッシュ
保存処理（同期 route / 非同期 Edge Function）が `JSON.stringify(toolUse.input)` を
**型検証なしでそのまま保存**していた。表示側は「外側JSONがパースでき chapter1 キーがあるか」
しか見ておらず、chapter1 が文字列でも検証を通過 → レンダラで `data.chapter1.talents.map()` を実行 →
文字列に `.talents` は無く undefined.map で例外 → ページがクラッシュ（=「生成されていない」体感）。

### 3. サーバー間fetchはCookieを自動転送しない → 内部API認証が403
管理画面の再生成は Server Action から自分のAPI（`/api/nakama/bible`）を `fetch` で呼ぶ実装。
API側に「force_regenerate は管理者のみ」というCookie検証ゲート（`verifyWhiAdminSession()` が
`cookies()` を読む）があったが、**サーバー間 `fetch` はブラウザのCookieを自動転送しない**ため、
新規リクエストにセッションCookieが無く、ゲートが必ず `403 forbidden_admin_only` を返していた。

## 対策
### 防御的パース（保存時・表示時の両方）
- 共通関数 `normalizeBibleChapters(input)`：top-level の `chapterN` キーの値が string なら
  `JSON.parse` を試み、オブジェクト/配列なら展開（非破壊・冪等）。失敗時は文字列のまま残す。
- 表示側：normalize後に**全章がオブジェクトか検証**し、ダメなら例外を投げず
  「データ形式エラー（再生成してください）」へ**優雅に着地**（クラッシュ回避）。
- **重要な限界**：二重エンコードでも「有効なJSON文字列」なら復旧できるが、
  「無効なJSON文字列」（未エスケープ引用符等）は parse 不能 → **復旧は再生成のみ**。
  正規表現で無理に引用符を補修するのは内容破損リスクが高く非推奨。

### Cookie転送
- 内部 `fetch` のヘッダに `Cookie: <session_name>=<value>` を明示付与する
  （`cookies().get(NAME)?.value` を読んで載せる）。認証ゲート自体は維持。

## 落とし穴・設計教訓
- **重要データはTool Useで構造的に強制しても、章単位の型までは保証されない**。
  Tool Use出力は「章ごとにオブジェクトか」を保存前に検証してから保存する。
- **サーバー間fetchはCookie非転送**。内部APIに認証ゲートがあるなら明示的にCookie/ヘッダを渡す。
  「ブラウザでは通るが内部呼び出しで403」はこのパターンを疑う。
- **Deno Edge Function は Next.js の src/lib を import できない**。同等ロジックは
  ミラー実装し、コメントで「ミラー元」を明記して同時更新する（runtime境界による許容された重複）。
- **Build PASS ≠ 本番OK**（[[mistakes]]）。今回はローカル実ブラウザ検証で
  「既存データは parse 復旧不能（=再生成が必要）」という指示書の前提崩れを**デプロイ前に発見**できた。
  実データで証拠を取ってから結論を出す原則が機能した好例。

## 検証（本番・実ブラウザ Playwright）
- WHI Admin再生成 → 「✓ 再生成を開始しました」（403解消）
- 約64秒後に冒険バイブルが全文表示（len 32k→72k・データ形式エラー消失）日英とも
- GLB・ベーシックにデグレなし
