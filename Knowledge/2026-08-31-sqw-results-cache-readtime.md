---
date: 2026-08-31
tags: [sqw-survey, performance, caching, invalidation, database, read-time-stamp]
project: sqw-survey
related: [[2026-06-13-sqw-report-display-speed-progressive]] [[2026-08-31-sqw-perf-region-and-results-cache]]
---

# SQW 結果表示の即表示キャッシュ（read-time方式の「しるし」＋共通キャッシュ）本番稼働

速度改善 第3弾。すべての結果表示（個人結果・公開チーム結果・運営ページ結果・統合分析の塊）を、
一度計算したら「行き来しても即表示」にする共通キャッシュを1つ入れた。**本番反映済み（main `b10eeb9`・Vercel Ready・本番実機で全項目合格）**。

## 設計の核：しるし（version stamp）は「書き込みカウンタ」でなく「read-time算出」
- **やりたいこと**：結果はほとんど変わらない前提で強めに覚え、変化は確実に拾う。表示時は重い集計をせず「しるし」だけ軽く確認し、変わったときだけ取り直す。
- **却下した案（重要）＝teams に version 列を持ちトリガーで +1**：全書き込みで共有の teams 1行に書くと、**300名同時回答で同一行の行ロックが直列化＝渋滞**。本番実測で「同一行への同時150UPDATE＝総1.9秒直列化」を確認。保存パス（最重要・過去4/24障害の教訓）に共有書き込みを足すのは危険 → 却下。
- **採用＝read-time方式**：各表の行に `updated_at`（nullable・既定なし＝即時追加/大表でも書換なし）を足し、**BEFORE UPDATE トリガーで「更新中の自分の行」にだけ日時を付ける**（同一行のみ＝追加ロックなし＝共有行の競合ゼロ）。しるしは**表示時にRPCで1回算出**：
  - 追加＝`count(*)` と `created_at` で検知／更新＝`updated_at` で検知／削除＝`count(*)` の減少で検知。
  - `results_stamp_team(team_id)` / `results_stamp_project(project_id)` が「件数と更新日時の指紋」を1本の文字列で返す（member_token等は扱わない＝匿名性の防衛線不変）。
- ★**選別せず全INS/UPD/DELを拾う**（列を選ばない）＝漏れゼロ。実データで全8種（回答追加/リセット/メンバー増減/役割/言語/立場/チーム設定＝経営オプション/塊構成）でしるしが進むのを確認。

## 共通キャッシュ（迂回ゼロ）
- **キー＝`計算式バージョン ∷ スコープ ∷ しるし`**。しるしが同じなら覚えた結果を即返す。しるしが変わると別キー＝古い行は参照されなくなる（**明示的な無効化/削除が不要**＝read-time stampの美点）。
- **計算式バージョン `RESULTS_FORMULA_VERSION`（scoring.ts）**＝scoring.ts を変えたら必ず更新（CLAUDE.md 絶対ルール9）。全キャッシュを一括で切り替える安全弁。
- **2層**：①ブラウザ側 `getCachedResults`（メモリ＋sessionStorage・同じ人の行き来が即）②サーバー側 `getCachedResultsServer`＋`results_cache` 表（service_role専用・計算1回で別端末/多人数も再利用・ai_report_cache と同方針）。
- **24時間の安全網**：覚えてから24hで必ず作り直す（万一しるしに漏れがあっても翌日には正しくなる）。サーバー側は `created_at > now()-24h` で照会＋古い行を掃除。
- **迂回ゼロ**：対話的な結果表示の重い取得（getTeamResults / getTeamResultsByInviteCode / getProjectAnalysisData）は**すべて共通関数経由**をgrepで突合。対象外＝Excel/CSV/PDF出力・定性集約・推移グラフ（別計算 computeTeamCategoryScoresById）＝対話画面でないため妥当。

## 実装の要点
- しるし取得の軽いサーバーアクション：`getTeamStampByAdminToken` / `getTeamStampByInviteCode` / `getProjectStampByAdminToken`（token→id→RPC）。
- サーバーキャッシュ層はクライアントから stamp/teamId を受け取り**既存の重い関数（getTeamResults 等）は無改修**で包む＝低リスク。ブラウザ側とサーバー側は同じ scope∷stamp キーを共有（一括印刷は運営ページと同じ `adminResults:teamId` を共有＝相互再利用）。
- 統合分析の塊：素データ（getProjectAnalysisData）をプロジェクトしるしで包む。塊のスコープ切替は取得済み素データからクライアント即計算（従来どおり）。

## 本番実機の実測（2026-08-31）
- 2回目＝キャッシュで速度改善：公開JA 3457→571ms（6倍）・公開EN 1153→397ms・個人 1154→578ms・統合分析 5326→4683ms。数値一致・日英・pageerror0。
- 回答追加→反映：低スコア完了セッションを1件追加→しるし進行→**公開結果 speed 200→126.5（古い200を返さず新データ反映）**→削除で200復帰（原状復帰）。
- サーバーキャッシュ：別ブラウザ（クライアント空）でも `results_cache` HITで数値一致。
- Excel出力・メール送信は現状どおり（メール関連ファイルは第3弾で無変更）。

## 教訓
- しるし（変更検知）は**共有カウンタへの書き込みでなく、read-time で件数＋updated_at から算出**すると、高同時数の保存パスにロック競合を足さずに漏れゼロを実現できる。BEFORE touch は「更新中の自分の行」だけなので競合を生まない。
- キャッシュキーに「しるし＋計算式バージョン」を含めると、無効化は**別キーになるだけ＝明示削除が要らない**（古い行は参照されず24hで掃除）。
- 「行き来即表示」の主因はクライアントのロード順＋重い集計の往復。region同拠点化（[[2026-08-31-sqw-perf-region-and-results-cache]]）と合わせて効く。
- 残課題＝計算関数3系統（computeResultsData／buildTeamResultsData／computeTeamCategoryScoresById）の一本化（#3の59.4%乖離教訓の観点）・推移グラフのキャッシュは次候補。
