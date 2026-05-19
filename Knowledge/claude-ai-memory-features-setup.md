---
date: 2026-05-19
tags: [knowledge, claude-ai, settings, memory, productivity]
project: all
---

# claude.ai のメモリー機能セットアップ

## 概要

claude.ai には複数の記憶関連機能があり、すべて有効化することで効果が最大化される。

## 設定箇所

claude.ai の **Settings → 機能タブ**

## 必須ON項目

### 1. チャットを検索・参照（Search and reference chats）
- 過去チャットを明示的に検索できる
- 「あの時の◯◯の話を探して」と頼める
- Project内：Project内のチャットを検索
- Project外：Project外の全チャットを検索

### 2. チャット履歴からメモリーを生成（Generate memory from chat history）
- 過去チャットを自動的に要約してメモリー化
- 新チャット冒頭で自動的にコンテキストとして提供
- 24時間ごとに更新
- 各Projectは独自の別個のメモリー空間を持つ

### 3. コード権限のリクエスト（通知）
- Claude Codeが承認を求めるとき、プッシュ通知が届く
- 不可逆変更（削除・本番デプロイ）の事故予防に有効

## OFFのまま推奨

### コネクタの探索
- Claude が自発的に新しいコネクタの導入を提案してくる機能
- 既に必要なコネクタが接続済みなら不要
- ONにするとシンプルさを損なう

## メモリー管理

「メモリーの表示と管理」から、Claudeが記憶している内容を確認・編集できる。
不要な情報・古い情報を削除可能。

## 出典

whi-knowledge構築（2026/05/19）

---

## 関連ノート

- [[project-vs-non-project-search-scope]]
- [[conversation-rules]]
