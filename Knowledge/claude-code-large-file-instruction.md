---
date: 2026-05-19
tags: [knowledge, claude-code, productivity, tips]
project: all
---

# Claude Codeへの大きな指示書の渡し方

## 問題

大きな指示書（数十KB以上のMarkdownなど）をターミナルに直接コピペすると、
ターミナルがフリーズすることがある。

## 対策

ファイルとして保存して、ファイルパスで渡す方式が確実：

### 手順

1. 指示書をファイル（例：`instruction.md`）としてダウンロード
2. `~/Downloads/` などのわかりやすい場所に保存
3. Macなら **Optionキー + 右クリック → 「パス名をコピー」** でフルパス取得
4. Claude Codeのプロンプトに以下を入力：

```
/Users/etokatsuaki/Downloads/instruction.md を読んで、その中に書かれている手順をすべて実行してください。

WHIフィロソフィーがすべての判断の土台です。
各ステップ完了時に必ず報告してください。
不可逆な変更の前には必ず確認してください。
```

## メリット

- ターミナルのフリーズ回避
- 指示書を後から再利用しやすい
- バージョン管理しやすい
- かつさん側で指示書の内容を事前確認できる

## 出典

whi-knowledge構築（2026/05/19）

---

## 関連ノート

- [[conversation-rules]]
