---
date: 2026-05-19
tags: [ai-report, prompt, nakama-quest]
project: nakama-quest
---

# AIレポートプロンプト変更時の3ファイル同時更新ルール

## ルール

AIレポートのプロンプト改善・変更時は、以下3ファイルを**必ず同時更新**：

1. `src/lib/ai/whi-philosophy.ts`
2. `src/lib/survey/questions.ts`
3. `src/lib/report/html-template.ts`

片方だけの更新は禁止。プロンプト内容と質問内容とテンプレートが食い違うとレポート品質が崩れる。

## プロンプト管理の絶対ルール

- AIレポートプロンプトは**コードのみ**で管理
- DBの`ai_prompts`テーブルは**使用禁止**（`is_active=false` 固定）
- whi-philosophy.tsが唯一の正本

## insight-comments.ts のカテゴリキー名

`questions.ts` と完全一致が必須。正しいキー：
- `landscape`
- `road`
- `rope`
- `tire`
- `body`
- `attitude`
- `cargo`
- `diversity`
- `happiness`

## 個人 vs チーム視点の使い分け

- 個人コメント（PERSONAL）：「あなたの目には〜」認識視点
- チームコメント（TEAM）：「このチームのワゴンは〜」客観的事実視点

視点を混在させない。

---

## 関連ノート

- [[mistakes]]
- [[report-design-principles]]
