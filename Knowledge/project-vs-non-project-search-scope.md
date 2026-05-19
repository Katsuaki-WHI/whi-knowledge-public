---
date: 2026-05-19
tags: [knowledge, claude-ai, project, search]
project: all
---

# claude.ai：Project内/外の検索スコープ

## 問題

過去チャット検索を使うとき、Project内/外でスコープが異なる。
理解しないまま使うと、検索したいチャットが見つからない事故が起こる。

## 仕様

| 開いた場所 | 検索範囲 |
|---|---|
| Project内で新チャットを開く | そのProject内のチャットのみ |
| Project外で新チャットを開く | Project外の全チャット（他Projectは含まない） |

つまり、**ProjectをまたいだChat検索はできない**。

## 判断ルール

過去チャットを検索したいとき：

1. **過去チャットがどこに蓄積されているか確認**
2. 蓄積されている場所と同じ場所で新チャットを開く
   - Project内に蓄積 → そのProject内で開く
   - Project外（一般チャット）に蓄積 → Project外で開く

## WHIの場合

「サーベイプラットフォーム構築」Project内に過去チャットが蓄積されているため、
**そのProject内で新チャットを開く**のが正解。

## 出典

whi-knowledge補完作業（2026/05/19）

---

## 関連ノート

- [[claude-ai-memory-features-setup]]
- [[conversation-rules]]
