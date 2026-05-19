---
date: 2026-05-19
tags: [knowledge, claude-code, productivity, tips]
project: all
---

# Claude Code Permission Shortcut

## 問題

Claude Codeで毎回「Yes / Yes always / No」を選ぶのが煩雑。
特に類似コマンドが連続するときに作業フローが分断される。

## 対策

「**2（Yes, and don't ask again for ...）**」を選ぶと、以降同様コマンド・ドメイン・パスが自動許可される。

## 使い分け

- 単発の重要操作（DB変更・本番デプロイ等）：**1（Yes）** で都度確認
- ファイル作成・読み込み等の繰り返し操作：**2（Yes always）** で効率化
- `-glob-` パターンが出たら **2** で同種を全自動化

## 注意

- 不可逆な操作（削除・本番デプロイ）には使わない
- セッション内のみ有効・次回起動時は再度問われる

## 出典

Claude Codeの導入承認を得るための稟議書作成（2026/04/05）
