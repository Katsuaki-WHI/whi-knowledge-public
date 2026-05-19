---
date: 2026-05-12
tags: [decision, nakama-quest, authentication, mvp]
project: nakama-quest
---

# 2026-05-12：Magic Link認証はMVP後に実装

## 背景

有料コンテンツの本人確認をMVPに入れるか。

## 選択肢

- **A**：MVPに入れる
- **B**：Stripe実装と同フェーズで設計・実装

## 決定

**B（MVPには入れない・Stripe実装と同時に設計）**

## 理由

- MVPはテスト段階の不具合対応が目的
- 本人確認はスコープ外
- それまでは管理画面のバイブル再生成機能で運用カバー
- リリース後ロードマップに含める

## 出典

管理画面MVP実装の開始準備（2026/05/12）

---

## 関連ノート

- [[code-sales-payment-model]]
