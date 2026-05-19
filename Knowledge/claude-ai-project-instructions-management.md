---
date: 2026-05-19
tags: [knowledge, claude-ai, project, instructions, management]
project: all
---

# claude.ai Project「手順」（カスタムインストラクション）の管理

## 概要

claude.ai の Project には「手順」と呼ばれるカスタムインストラクション機能がある。
Project内の全チャットで自動適用される。

## 場所

Project画面 → 右サイドバー → 「手順」セクション

## 編集方法

1. 「手順」セクション右上の鉛筆アイコンをクリック
2. 編集画面が開く
3. テキストを編集
4. 保存

## 文字数制限

明確な公式情報はないが、数千行レベルの長文でも保存可能（実証済み）。
WHIフィロソフィー完全版 + 会話ルール v2.0 を全文貼り付け成功している。

## 編集時の注意

### 1. 既存内容のバックアップ
編集前に既存内容を全文コピーして手元に保存する。
誤って消した場合の復旧手段として。

### 2. whi-knowledge との同期
Project手順内に「会話ルール vX.X」や添付ファイル名のバージョン表記があれば、
whi-knowledge/Preferences/conversation-rules.md と**同期させる**運用。
片方だけ更新すると認識のずれが発生する。

### 3. Project添付ファイルとの整合性
Project手順から添付ファイル名で参照している場合、添付ファイルの差し替えも必要。
例：「20260519_WHI_conversation_rules_v2.1.md」を v2.2 に差し替えるなど。

## ベストプラクティス

- WHIフィロソフィー全文を埋め込む（Public化せず claude.ai で使うため）
- 会話ルールの主要項目を要約版で記載（詳細は添付ファイルへリンク）
- 新チャット開始時の5ステップを明文化
- whi-knowledge への参照ルートを記載

## 出典

whi-knowledge構築（2026/05/19）

---

## 関連ノート

- [[conversation-rules]]
- [[claude-ai-memory-features-setup]]
