# WHI Knowledge Base (Public Mirror) - INDEX

このリポジトリは、WHI（Work Happiness Inc.）の事業横断的な知識ベースの**公開ミラー**です。
claude.ai が `web_fetch` で読めるよう、機密でない範囲のみを公開しています。

機密情報（価格・卸値・インフラURL・トークン・プロジェクト現状）は
Private 本体リポジトリのみに保管されています。

## 構成

### philosophy/ - 全判断の土台
- [whi-philosophy.md](philosophy/whi-philosophy.md) - WHIフィロソフィー完全版
- [tone-and-expression.md](philosophy/tone-and-expression.md) - トーン・表現原則
- [report-design-principles.md](philosophy/report-design-principles.md) - レポート設計原則

### Knowledge/ - 技術知識・落とし穴
- [mistakes.md](Knowledge/mistakes.md) - 再発防止の核
- [build-pass-not-runtime-ok.md](Knowledge/build-pass-not-runtime-ok.md)
- [vercel-deploy-tips.md](Knowledge/vercel-deploy-tips.md)
- [api-cost-management.md](Knowledge/api-cost-management.md)
- [nakama-quest-visibility-rules.md](Knowledge/nakama-quest-visibility-rules.md)
- [ai-report-3files-sync.md](Knowledge/ai-report-3files-sync.md)

### Decisions/ - 意思決定の記録（公開可能なもの）
- [third-party-ip-policy.md](Decisions/third-party-ip-policy.md)
- [i18n-strategy.md](Decisions/i18n-strategy.md)
- [engagement-survey-positioning.md](Decisions/engagement-survey-positioning.md)

### Preferences/ - 会話ルール・役割定義
- [conversation-rules.md](Preferences/conversation-rules.md) - 会話ルール v2.1
- [role-definition.md](Preferences/role-definition.md) - 役割定義

### ルール
- [AI-RULES.md](AI-RULES.md) - AIが知識ベースをどう扱うかのルール

---

## 使い方

### claude.ai（チャット）
新チャット開始時に以下のURLを冒頭で渡す：
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/INDEX.md
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/Knowledge/mistakes.md
