# WHI Knowledge Base (Public Mirror) - INDEX

このリポジトリは、WHI（Work Happiness Inc.）の事業横断的な知識ベースの**公開ミラー**です。
claude.ai が `web_fetch` で読めるよう、機密でない範囲のみを公開しています。

機密情報（価格・卸値・インフラURL・トークン・プロジェクト現状）は
Private 本体リポジトリのみに保管されています。

> ★★**この INDEX は「1項目1行」です**（2026-10-24 手66 に 45,972 → 約1.6万字へ畳んだ）。
> ★**畳む前の詳しい要約は1字も消していません**＝[`Archive/INDEX-full-2026-10-24.md`](Archive/INDEX-full-2026-10-24.md) に
> **丸ごと凍結**してあります（★sha256 で同一を確認）。**中身を詳しく知りたいときは、各ファイル本体を開く。**
> ★**各行は元の要約からの抜き書き**（★要約を書き直していません＝`…` は字数で切った印）。

## 構成

### philosophy/ - 全判断の土台（Privateのみ保管）
WHIフィロソフィーは Project手順本文（claude.ai用）と
Private版 ~/whi-knowledge/philosophy/（Claude Code用）の2箇所に同内容で保管。
Public版には公開しない。
唯一の正は Project手順本文。詳細は Decisions/whi-philosophy-single-source-of-truth.md 参照。

### Knowledge/ - 技術知識・落とし穴
- [measure-before-you-fix.md](Knowledge/measure-before-you-fix.md) — 「直す」前に全数で数える（直すものが0件のこともある）。
- [supabase-admin-tips.md](Knowledge/supabase-admin-tips.md) — Supabase を管理者として触るときの落とし穴4つ＝⑴`auth.admin.listUsers()` は `factors` を欄ごと返さない（人ごとに `mfa.listFactors` を呼ぶ・★「登録していな…
- [head-request-hides-the-error-code.md](Knowledge/head-request-hides-the-error-code.md) — `head: true`（HEAD リクエスト）は本文を返さないので PostgREST のエラー code が受け取れない（PGRST205 も 42501 も空で返り、閉じているのに「読めてしまう」と出る）。
- [before-dropping-a-backup-table.md](Knowledge/before-dropping-a-backup-table.md) — バックアップ表を消す前に何を数えるか（行＝主キーで無い行／値＝列ごとに数える・★値が違うのは「壊れている」でなく「直した」ことがある／その値が他に残っていないか／一致しないなら削除 SQL を作らない／「戻す」SQL を…
- [rls-four-stages-done-check.md](Knowledge/rls-four-stages-done-check.md) — RLS を掛け終えたあと何をもって「閉じた」と言うか（外から実際に叩く／全表を叩き「200 だが0行」を空と RLS で分ける／同じ中身が出ていることを長さで示す／開くと書く画面を避ける／★鍵だけでなく「表」も写さずコー…
- [throwaway-postgres-sql-check.md](Knowledge/throwaway-postgres-sql-check.md) — 使い捨ての PostgreSQL で SQL を確かめるときの落とし穴4つ（`set role` の `SET` 行／stderr を捨てると失敗の種類が分からない／複数行 SQL を `-c` に埋めない／起動待ちは…
- [move-anon-to-service-role-stage1.md](Knowledge/move-anon-to-service-role-stage1.md) — RLS を掛ける前に「読み書きを anon から service_role へ移す」段で踏む5つの穴（触る表を全部数える／鍵を引数で受け取る部品は触らず渡す側を直す／`cookies()` が消えて静的化する／読むだけの…
- [kill-switch-before-the-first-call.md](Knowledge/kill-switch-before-the-first-call.md) — 【Public可】外とつなぐ機能は「出す」と「始める」を分けられる歯止めを先に持たせる。
- **組織分析レポートの設計材料**（★本文は Private のみ・ここには要約だけ） — あるクライアントとの商談（2026-10-02）から読み取った組織のニーズ9件（現場の一次情報が人伝えで歪んで経営に届かない／管理職層がマネジメントに向かず次の候補が見えない／経営会議にそのまま出せる1枚／部門をまたぐつ…
- [two-entrances-one-screen.md](Knowledge/two-entrances-one-screen.md) — 【Public可】お試し用の運営画面は「同じ画面・入口を2つ」にする（画面を2つ作らない）。
- [secrets-stay-in-the-platform.md](Knowledge/secrets-stay-in-the-platform.md) — 【Public可】秘密鍵は置き場所の中だけで使う＝検証は「鍵のある環境」でやる。
- [one-pass-is-not-a-fix.md](Knowledge/one-pass-is-not-a-fix.md) — 【Public可】1回通った＝直った、ではない。
- [mac-dev-environment-migration.md](Knowledge/mac-dev-environment-migration.md) — 【Public可】開発環境の Mac 移行手順（実施 2026-09-27〜28・M1→M5）。
- [philosophy-first-then-expert.md](Knowledge/philosophy-first-then-expert.md) — 有料の成果物を書かせるときは「土台になる価値観 → その上に立つ専門家の視点」の順で名乗らせる。
- [server-action-exports-are-public-api.md](Knowledge/server-action-exports-are-public-api.md) — 【Public可】`"use server"` の export は URL を持つ公開 API＝画面から呼ばれなくても外から呼べる。
- [long-task-step-by-step.md](Knowledge/long-task-step-by-step.md) — 【Public可】長い作業は「1回の返答を短く・1手ずつ止まる」。
- [edge-call-limits-margin-absolute.md](Knowledge/edge-call-limits-margin-absolute.md) — 【Public可】検査の「余白」は倍率でなく絶対値で持つ（壁が動かないとき）。
- [mistakes.md](Knowledge/mistakes.md) — 再発防止の核
- [contrast-from-rendered-pixels.md](Knowledge/contrast-from-rendered-pixels.md) — 文字の読みやすさは描画画素で測る。
- [retry-must-not-lose-the-good-one.md](Knowledge/retry-must-not-lose-the-good-one.md) — 作り直しを入れるなら「前の良かったほう」を持っておく（1回目は使えたのに2回目が壊れて何も届かない、を防ぐ）
- [budget-targets-must-sum-to-the-total.md](Knowledge/budget-targets-must-sum-to-the-total.md) — 【Public可】部分の目安は、全体の目安の中に収まっていないといけない。
- [show-a-sample-and-it-gets-copied.md](Knowledge/show-a-sample-and-it-gets-copied.md) — 【Public可】文体の見本を渡すと、語り口だけでなく中身ごと写される。
- [ai-invents-numbers-catch-them-at-the-gate.md](Knowledge/ai-invents-numbers-catch-them-at-the-gate.md) — 【Public可】AIが足した数字は、言葉で禁じずに保存前の検査で止める。
- [write-it-as-how-it-looks-to-you.md](Knowledge/write-it-as-how-it-looks-to-you.md) — 【Public可】「あなたにはこう見えています」と書かせると、断定が消える。
- [back-the-action-with-their-strength.md](Knowledge/back-the-action-with-their-strength.md) — 【Public可】一手は「その人の強みで支える」、そして「通った後の姿」を描く。
- [absolute-right-overlaps-growing-text.md](Knowledge/absolute-right-overlaps-growing-text.md) — 右端に絶対配置したボタンは、隣の文字が伸びれば必ず重なる（絶対配置は文字がどこまで伸びるか知らない）。
- [separate-permanent-and-temporary-copy.md](Knowledge/separate-permanent-and-temporary-copy.md) — 恒久的に見せる文と、実装までの一時的な文を1つの文字列にまとめない。
- [one-slot-two-states-not-two-buttons.md](Knowledge/one-slot-two-states-not-two-buttons.md) — 同じ場所に置くボタンは1つ。
- [coaching-design-general-lessons.md](Knowledge/coaching-design-general-lessons.md) — AIとの複数回の対話を設計するときの4つの判断。
- [align-two-views-fixed-row-height.md](Knowledge/align-two-views-fixed-row-height.md) — 【Public可】並べて見比べる画面は、行の高さを固定しないと必ずズレる。
- [dangerous-html-strip-document-styles.md](Knowledge/dangerous-html-strip-document-styles.md) — 【Public可】AIが書いたHTMLをページに挿し込むときは `<style>` と body/html 向けの指定を必ず取り除く。
- [same-id-new-wording-keep-the-old.md](Knowledge/same-id-new-wording-keep-the-old.md) — 【Public可】IDで集計しているものの文言だけ変えるときは、旧い文言を2か所（コードのコメント＋決定の記録）に残す。
- [local-env-files-override-and-prod-db.md](Knowledge/local-env-files-override-and-prod-db.md) — 【Public可】ローカルで本番DBに繋げないとき、原因は環境ファイルの優先順位。
- [relationship-facts-as-the-material.md](Knowledge/relationship-facts-as-the-material.md) — 有料レポートの材料は「誰が・どの目で・何を見たか」＝関係性の事実を主役にする。
- [verify-facts-not-sentences.md](Knowledge/verify-facts-not-sentences.md) — AI生成レポートの整合は「文の一致」でなく「事実の一致」で担保する。
- [one-field-one-role.md](Knowledge/one-field-one-role.md) — 同じ欄に2つの役割を持たせない。
- [forbid-narrowly-by-name.md](Knowledge/forbid-narrowly-by-name.md) — 禁止語は名指しで狭く書く。
- [repair-impact-is-every-reader-not-the-row.md](Knowledge/repair-impact-is-every-reader-not-the-row.md) — データ修復の影響範囲は「直す行の数」でなく「その値を読んでいる全ての計算」で数える。
- [placeholder-values-break-when-premise-changes.md](Knowledge/placeholder-values-break-when-premise-changes.md) — 合計が固定の配分どうしを比べる前提を作ったら、全経路で合計が揃っているか点検する。
- [no-controls-directly-on-gradient.md](Knowledge/no-controls-directly-on-gradient.md) — グラデーション背景の上にボタン・選択肢・小さな文字を直置きしない。
- [gap-display-axis-priority.md](Knowledge/gap-display-axis-priority.md) — 2つの見方を並べて差を見せる画面で「どの項目について語るか」は、目立ちやすさでなく画面の目的で決める。
- [disclosure-in-flex-heading.md](Knowledge/disclosure-in-flex-heading.md) — 「？」で開く折りたたみ部品を、横1行（flex）の見出しに置くと、本文が「？」の右に入り込んで見出しのほうが縦に潰れる（実測：狭い幅で見出しが4行に折り返し）。
- [broken-cache-deadlock-and-save-validation.md](Knowledge/broken-cache-deadlock-and-save-validation.md) — 壊れた生成物が「成功」として保存され、しかも作り直せないという2つの欠落が重なると、1件だけが永久に直らなくなる。
- [fail-closed-for-irreversible-actions.md](Knowledge/fail-closed-for-irreversible-actions.md) — 不可逆な操作のガードは fail-closed（確認できなければ止める）。
- [empty-vs-unreadable.md](Knowledge/empty-vs-unreadable.md) — 「0件」と「読めなかった」を同じ値で表さない。
- [same-emoji-two-meanings-in-one-row.md](Knowledge/same-emoji-two-meanings-in-one-row.md) — 同じ絵文字を、同じ行で別の意味に使わない。
- [service-role-only-table-silent-empty.md](Knowledge/service-role-only-table-silent-empty.md) — RLS で service_role 限定にした表を通常のクライアントで読むと、エラーにならず「空」が返る。
- [master-key-case-mismatch-silent-fallback.md](Knowledge/master-key-case-mismatch-silent-fallback.md) — 判定側のキーとマスターのキーで大文字小文字が違うと、エラーなしで別のデータが出る。
- [test-only-guard-blocks-production.md](Knowledge/test-only-guard-blocks-production.md) — 「テスト専用」のガードを機能そのものに掛けると、本番の機能が丸ごと止まる。
- [record-only-what-you-observe.md](Knowledge/record-only-what-you-observe.md) — 観測していないことを記録しない（列の名前が約束したことだけを書く）。
- [results-display-speed-design-guide.md](Knowledge/results-display-speed-design-guide.md) — 結果表示の速度設計ガイド（共通の型）。
- [2026-08-31-sqw-results-cache-readtime.md](Knowledge/2026-08-31-sqw-results-cache-readtime.md) — 結果の即表示キャッシュ：変更検知の「しるし」は共有カウンタ+1でなくread-time算出（各表にupdated_at＋BEFORE UPDATE touchトリガー＝更新中の自分の行だけ＋件数と更新日時の指紋RPC）。
- [2026-08-31-sqw-perf-region-and-results-cache.md](Knowledge/2026-08-31-sqw-perf-region-and-results-cache.md) — サーバーレス関数が異常に遅い（1往復~1.8s）根本原因=関数の実行リージョンとDBリージョンの地理的不一致。
- [2026-08-26-general-technical-findings.md](Knowledge/2026-08-26-general-technical-findings.md) — 一般技術Tips(2026-08-26)。
- [jsonb-selection-backward-compat-new-axis.md](Knowledge/jsonb-selection-backward-compat-new-axis.md) — jsonbの設定オブジェクトにキーを追記するだけで軸/条件を1つ増やす＝DB変更(DDL)・移行なしの後方互換。
- [resend-scheduled-email-30day-limit.md](Knowledge/resend-scheduled-email-30day-limit.md) — Resendの予約送信は30日先までが上限（公式明記）・超過時の挙動は未文書化→防御的に「APIエラー前提」で扱う。
- [ai-prompt-global-rule-and-bilingual-qa.md](Knowledge/ai-prompt-global-rule-and-bilingual-qa.md) — AIプロンプト2教訓。
- [tool-use-schema-flattening.md](Knowledge/tool-use-schema-flattening.md) — Tool Useスキーマで「1項目だけのオブジェクト」を作らない。
- [local-review-flow.md](Knowledge/local-review-flow.md) — ローカル確認の標準フロー（dev起動維持→URL1行提示→ユーザー確認→push）
- [one-reader-before-adding-a-format.md](Knowledge/one-reader-before-adding-a-format.md) — 同じデータの読み方を2箇所に書かない。
- [snapshot-before-changing-display-logic.md](Knowledge/snapshot-before-changing-display-logic.md) — 表示ロジックを変える前に、既存の結果をスナップショットで固め、保存前後のバイト比較で不変を証明してから次へ進む
- [verify-on-the-default-path-in-production.md](Knowledge/verify-on-the-default-path-in-production.md) — 生成経路が2つ（同期／非同期）あるとき、スキーマ・材料・検証は必ず本番の既定経路で通す。
- [build-pass-not-runtime-ok.md](Knowledge/build-pass-not-runtime-ok.md)
- [qr-quiet-zone-and-error-correction.md](Knowledge/qr-quiet-zone-and-error-correction.md) — 【Public可】QRが読めないときは、中身より先に静穏帯（周りの白い余白）と誤り訂正レベルを疑う。
- [base-url-consolidation-and-proof.md](Knowledge/base-url-consolidation-and-proof.md) — 【Public可】ベースURLを1箇所に集約する型と、「値が変わっていない」ことの証明。
- [delete-design-orphan-keys-and-log.md](Knowledge/delete-design-orphan-keys-and-log.md) — 【Public可】「消す」機能を作るときの3つ。
- [non-fatal-write-failure-and-column-mismatch.md](Knowledge/non-fatal-write-failure-and-column-mismatch.md) — 握り潰した失敗は画面にもログにも出ない／DBに書く・読む列名は実DBと機械的に突合する。
- [select-column-mismatch-audit.md](Knowledge/select-column-mismatch-audit.md) — 読み取りの列名不一致も気づかれない（書き込み側 non-fatal-write-failure と対）。
- [vercel-deploy-tips.md](Knowledge/vercel-deploy-tips.md) — 自動デプロイ不発時は空コミット／★Vercel CLI の一覧は commit sha を出さない＝どの commit が本番に載ったかは `vercel inspect --logs` の `Commit:` 行で照合…
- [supabase-rls-warning-false-positive.md](Knowledge/supabase-rls-warning-false-positive.md) — Supabase SQL Editor の「Potential issue detected（RLS）」は INSERT/UPDATE/列追加でも出る＝多くは誤検知。
- [api-cost-management.md](Knowledge/api-cost-management.md)
- [nakama-quest-visibility-rules.md](Knowledge/nakama-quest-visibility-rules.md)
- [ai-report-3files-sync.md](Knowledge/ai-report-3files-sync.md)
- [4-level-verification-protocol.md](Knowledge/4-level-verification-protocol.md) — 4段階検証プロトコル
- [rsc-marshalling-violation-columns-with-render.md](Knowledge/rsc-marshalling-violation-columns-with-render.md) — RSCマーシャリング違反
- [question-text-replacement-i18n.md](Knowledge/question-text-replacement-i18n.md) — i18nプレースホルダー方式
- [legacy-data-compatibility-check.md](Knowledge/legacy-data-compatibility-check.md) — 既存レコード後方互換性
- [anthropic-api-cost-incident-april-22.md](Knowledge/anthropic-api-cost-incident-april-22.md) — APIコスト事故記録
- [reset-vs-no-session-language-precision.md](Knowledge/reset-vs-no-session-language-precision.md) — 「リセット済み」言葉の精度
- [claude-md-size-limit.md](Knowledge/claude-md-size-limit.md) — CLAUDE.md は約15万字が上限。
- [claude-code-permission-shortcut.md](Knowledge/claude-code-permission-shortcut.md) — Claude Code Permission Shortcut
- [claude-code-large-file-instruction.md](Knowledge/claude-code-large-file-instruction.md) — 大きな指示書の渡し方
- [claude-code-login-recovery.md](Knowledge/claude-code-login-recovery.md) — 認証エラー復旧
- [claude-ai-memory-features-setup.md](Knowledge/claude-ai-memory-features-setup.md) — メモリー機能セットアップ
- [project-vs-non-project-search-scope.md](Knowledge/project-vs-non-project-search-scope.md) — Project内/外の検索スコープ
- [instruction-delivery-method.md](Knowledge/instruction-delivery-method.md) — 指示書のサイズ別渡し方
- [claude-ai-project-instructions-management.md](Knowledge/claude-ai-project-instructions-management.md) — Project「手順」管理
- [resend-local-vs-prod-key.md](Knowledge/resend-local-vs-prod-key.md) — ローカルResendキーは本番と別物・メール実物確認は送信ゼロ捕捉で
- [ai-tool-use-double-encoding-defensive-parse.md](Knowledge/ai-tool-use-double-encoding-defensive-parse.md) — AI Tool Useが章をJSON文字列で返す二重エンコード→検証なし保存→表示クラッシュ。
- [stripe-price-change-procedure.md](Knowledge/stripe-price-change-procedure.md) — Stripe価格変更手順(Price金額は編集不可・新Price作成→デフォルト→旧アーカイブ→price_id差替→pricing.ts更新→実機で表示=請求一致確認)。
- [nakama-english-style-guide.md](Knowledge/nakama-english-style-guide.md) — なかまクエスト英語表記ガイド(ゲーム・Web共通正本)。
- [nakama-admin-i18n-inline-debt.md](Knowledge/nakama-admin-i18n-inline-debt.md) — なかまWHI Adminの言語方式はinline三項分岐(辞書方式でない技術的負債)。
- [gate-after-payment-must-deliver.md](Knowledge/gate-after-payment-must-deliver.md) — 【Public可】試作の「通らなければ出さない」を、お金を受け取ったあとにそのまま使わない。
- [close-the-mouth-before-removing-the-gate.md](Knowledge/close-the-mouth-before-removing-the-gate.md) — 関門を外す片付けは「生成の口を止めてから」。
- [infra-before-production-launch.md](Knowledge/infra-before-production-launch.md) — 本番公開前に必須のインフラ確認（Vercel無料は非商用専用・上限超過で停止／Supabase無料は7日で自動停止・バックアップなし／公開予定が立ったら先に有料化）
- [overflow-x-hidden-breaks-sticky.md](Knowledge/overflow-x-hidden-breaks-sticky.md) — `overflow-x: hidden` は `position: sticky` を無効化する（片方の軸だけ hidden にすると もう片方の visible が auto に計算され、html/body 自身がスク…
- [dark-mode-fixed-background-contrast.md](Knowledge/dark-mode-fixed-background-contrast.md) — 背景固定（テーマトークン）のレポート系コンポーネントに dark:text-* を付けると、ダークモード端末で背景は反転せず文字だけ反転しコントラスト不足で読めなくなる。
- [edge-lifetime-from-measurement.md](Knowledge/edge-lifetime-from-measurement.md) — サーバーレスの寿命は「公式」でなく「実測」で決める／作り直しは別の実行にする。

### Decisions/ - 意思決定の記録（公開可能なもの）
- [third-party-ip-policy.md](Decisions/third-party-ip-policy.md)
- [i18n-strategy.md](Decisions/i18n-strategy.md)
- [engagement-survey-positioning.md](Decisions/engagement-survey-positioning.md)
- [2026-04-10-score-distribution-deferred.md](Decisions/2026-04-10-score-distribution-deferred.md) — スコア配分は最後に切替
- [2026-04-22-rate-limit-forced-landing.md](Decisions/2026-04-22-rate-limit-forced-landing.md) — APIラリー上限到達時は強制着地
- [2026-04-22-rally-cap-44-per-session.md](Decisions/2026-04-22-rally-cap-44-per-session.md) — ラリー上限44/セッション
- [2026-05-12-magic-link-postponed.md](Decisions/2026-05-12-magic-link-postponed.md) — Magic LinkはMVP後
- [2026-05-19-conversation-rules-v2.2.md](Decisions/2026-05-19-conversation-rules-v2.2.md) — 会話ルール v2.2 採用
- [2026-06-02-report-as-coaching-dialogue.md](Decisions/2026-06-02-report-as-coaching-dialogue.md) — レポートは診断でなく対話の出発点（抽象原則のみ・具体は非公開）
- [2026-10-12-edge-call-limits-canon.md](Decisions/2026-10-12-edge-call-limits-canon.md) — AI の呼び出しの守り＝正典（2製品共通・★Public 版＝費用の数字は落としてある）。
- [2026-10-05-security-baseline-structure.md](Decisions/2026-10-05-security-baseline-structure.md) — セキュリティの土台（構造だけ）。
- [2026-10-06-benchmark-import-pseudonymized.md](Decisions/2026-10-06-benchmark-import-pseudonymized.md) — 過去データを物差しにするときの個人情報の扱い＝仮名加工情報として扱う。
- [2026-10-04-unlimited-plan-structure.md](Decisions/2026-10-04-unlimited-plan-structure.md) — 使いたい放題プランの設計（構造だけ・金額は非公開）。
- [2026-09-19-paid-report-design-principles.md](Decisions/2026-09-19-paid-report-design-principles.md) — 有料レポートの設計原則（試作で確定・一般化版／製品固有の設問構成・データ規模・価格は非公開）。

### Preferences/ - 会話ルール・役割定義
- [conversation-rules.md](Preferences/conversation-rules.md) — 会話ルール v2.2
- [role-definition.md](Preferences/role-definition.md) — 役割定義
- [document-versioning.md](Preferences/document-versioning.md) — 読み物・ドキュメントのバージョン管理ルール（改訂時に版番号付与・ファイル名にも入れる・系統別番号体系・対応関係を記録）

### ルール
- [AI-RULES.md](AI-RULES.md) — AIが知識ベースをどう扱うかのルール

---

## 使い方

### claude.ai（チャット）
新チャット開始時に以下のURLを冒頭で渡す：
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/INDEX.md
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/Knowledge/mistakes.md
