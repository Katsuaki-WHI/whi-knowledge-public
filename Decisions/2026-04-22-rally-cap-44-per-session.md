---
date: 2026-04-22
tags: [decision, nakama-quest, api-cost, rate-limit]
project: nakama-quest
---

# 2026-04-22：ラリー上限は44/セッション

## 背景

1セッションのラリー上限をいくつにするか。

## 選択肢

- **A**：100ラリー/日
- **B**：44ラリー/セッション

## 決定

**B（フェーズごとに上限）**

内訳：
- Week0：10ラリー
- 振り返り：8×3週 = 24ラリー
- Week4：10ラリー
- 合計：44ラリー

## 理由

- 売価¥2,000・原価率30%→上限$4/セッション
- 1日100ラリーは仕組み上ありえない（Week1への進行に最低3〜5日必要）
- コスト換算で通常20〜30ラリー・上限到達でも$3.04に収まる
- APIコスト$27.50事故（[[anthropic-api-cost-incident-april-22]]）の再発防止

## 出典

SQWサーベイ2となかまクエストの開発（2026/04/25）

---

## 関連ノート

- [[api-cost-management]]
- [[anthropic-api-cost-incident-april-22]]
- [[2026-04-22-rate-limit-forced-landing]]
