# WHI Knowledge Base (Public Mirror) - INDEX

このリポジトリは、WHI（Work Happiness Inc.）の事業横断的な知識ベースの**公開ミラー**です。
claude.ai が `web_fetch` で読めるよう、機密でない範囲のみを公開しています。

機密情報（価格・卸値・インフラURL・トークン・プロジェクト現状）は
Private 本体リポジトリのみに保管されています。

## 構成

### Knowledge/ - 技術知識・落とし穴
- [mistakes.md](Knowledge/mistakes.md) - 再発防止の核
- [build-pass-not-runtime-ok.md](Knowledge/build-pass-not-runtime-ok.md)
- [vercel-deploy-tips.md](Knowledge/vercel-deploy-tips.md)
- [api-cost-management.md](Knowledge/api-cost-management.md)
- [nakama-quest-visibility-rules.md](Knowledge/nakama-quest-visibility-rules.md)
- [ai-report-3files-sync.md](Knowledge/ai-report-3files-sync.md)
- [4-level-verification-protocol.md](Knowledge/4-level-verification-protocol.md) - 4段階検証プロトコル
- [rsc-marshalling-violation-columns-with-render.md](Knowledge/rsc-marshalling-violation-columns-with-render.md) - RSCマーシャリング違反
- [question-text-replacement-i18n.md](Knowledge/question-text-replacement-i18n.md) - i18nプレースホルダー方式
- [legacy-data-compatibility-check.md](Knowledge/legacy-data-compatibility-check.md) - 既存レコード後方互換性
- [anthropic-api-cost-incident-april-22.md](Knowledge/anthropic-api-cost-incident-april-22.md) - APIコスト事故記録
- [reset-vs-no-session-language-precision.md](Knowledge/reset-vs-no-session-language-precision.md) - 「リセット済み」言葉の精度
- [claude-code-permission-shortcut.md](Knowledge/claude-code-permission-shortcut.md) - Claude Code Permission Shortcut
- [claude-code-large-file-instruction.md](Knowledge/claude-code-large-file-instruction.md) - 大きな指示書の渡し方
- [claude-code-login-recovery.md](Knowledge/claude-code-login-recovery.md) - 認証エラー復旧
- [claude-ai-memory-features-setup.md](Knowledge/claude-ai-memory-features-setup.md) - メモリー機能セットアップ
- [project-vs-non-project-search-scope.md](Knowledge/project-vs-non-project-search-scope.md) - Project内/外の検索スコープ

### Decisions/ - 意思決定の記録（公開可能なもの）
- [third-party-ip-policy.md](Decisions/third-party-ip-policy.md)
- [i18n-strategy.md](Decisions/i18n-strategy.md)
- [engagement-survey-positioning.md](Decisions/engagement-survey-positioning.md)
- [2026-04-10-score-distribution-deferred.md](Decisions/2026-04-10-score-distribution-deferred.md) - スコア配分は最後に切替
- [2026-04-22-rate-limit-forced-landing.md](Decisions/2026-04-22-rate-limit-forced-landing.md) - APIラリー上限到達時は強制着地
- [2026-04-22-rally-cap-44-per-session.md](Decisions/2026-04-22-rally-cap-44-per-session.md) - ラリー上限44/セッション
- [2026-04-23-3month-accompaniment-7-emails.md](Decisions/2026-04-23-3month-accompaniment-7-emails.md) - 3ヶ月伴走漸減型7回
- [2026-04-23-team-pulse-survey-all-members.md](Decisions/2026-04-23-team-pulse-survey-all-members.md) - パルスサーベイ全員回答
- [2026-05-12-magic-link-postponed.md](Decisions/2026-05-12-magic-link-postponed.md) - Magic LinkはMVP後
- [2026-05-19-conversation-rules-v2.2.md](Decisions/2026-05-19-conversation-rules-v2.2.md) - 会話ルール v2.2 採用

### Preferences/ - 会話ルール・役割定義
- [conversation-rules.md](Preferences/conversation-rules.md) - 会話ルール v2.2
- [role-definition.md](Preferences/role-definition.md) - 役割定義

### ルール
- [AI-RULES.md](AI-RULES.md) - AIが知識ベースをどう扱うかのルール

---

## 使い方

### claude.ai（チャット）
新チャット開始時に以下のURLを冒頭で渡す：
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/INDEX.md
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/Knowledge/mistakes.md
