# AI-RULES：知識ベース運用ルール

このファイルは、AI（claude.ai および Claude Code）が
whi-knowledge ディレクトリをどう扱うかを定めたものです。

**Claude Code はセッション開始時、最初にこのファイルを必ず読むこと。**

---

## 1. セッション開始時の読み込み手順（Claude Code）

新しいセッションを開始したら、以下を順番に実行：

1. `~/whi-knowledge/AI-RULES.md` を読む（このファイル）
2. `~/whi-knowledge/Knowledge/mistakes.md` を読む（過去の失敗からの学び）
3. `~/whi-knowledge/Preferences/` 配下を全部読む
4. このプロジェクトのCLAUDE.mdを読む
5. 今日のゴールに関連する Knowledge/ Decisions/ Projects/ を検索して読む
6. 「読みました」と報告してから作業開始

**例外**：明らかに知識ベースと無関係な一回きりの質問（例：「今何時？」）の場合はスキップ可。

---

## 2. 書き込みルール

### 2-1. いつ書くか
会話の流れで以下が発生したら、**その場で**書く。「あとで書く」は禁止。

**Knowledge/ に書く**：
- バグ・問題が解決した（原因と解決策をセットで）
- ライブラリ・API・ツールの新発見
- 環境構築・設定中に遭遇して解決した問題
- 「次回同じ作業をするとき知っていたかった」と思ったこと

**Decisions/ に書く**：
- 複数の選択肢から1つを選んだ（A vs B でなぜAを選んだか）
- 設計方針・ポリシーの決定

**Projects/ に書く**：
- プロジェクトの状態・バージョン・概要が変わったとき

**Preferences/ に書く**：
- かつさんの好み・働き方の新発見

### 2-2. 書き方の形式

各ノートは必ずYAMLフロントマターを含む：

```
---
date: YYYY-MM-DD
tags: [関連, タグ]
project: project-name
related: [[Other Note]]
---

# タイトル

本文。関連ノートは [[wiki link]] でリンク。
```

### 2-3. ファイル命名規則

- Knowledge: `topic-subtopic.md`（例：`vercel-deploy-tips.md`）
- Decisions: `YYYY-MM-DD-topic.md`（例：`2026-05-19-knowledge-base-setup.md`）
- Preferences: `category.md`（例：`conversation-rules.md`）
- Projects: `project-name.md`（例：`nakama-quest.md`）

---

## 3. mistakes.md への追記ルール（厳格）

`Knowledge/mistakes.md` に追記するのは、以下3条件**すべて**を満たすときのみ：

1. **かつさんから明示的に訂正された**（自分の観察ではない）
2. **再発しうるパターン**（一回きりの事故ではない）
3. **具体的にDo/Don'tが書ける**

形式：

```
YYYY-MM-DD：[何が間違っていたかを1文で]
**NG**：実際にやってしまった間違い
**Correct**：次回からの正しい行動
**Trigger**：このルールが適用される状況
```

---

## 4. 透明性ルール（最重要）

whi-knowledge に読み書きしたら、**必ずユーザーに報告**する。黙って読み書きしない。

報告例：
- 「whi-knowledge/Knowledge/xxx.md を読みました」
- 「whi-knowledge/Decisions/2026-05-19-xxx.md に決定を記録しました」
- 「whi-knowledge/Knowledge/mistakes.md に新しいエントリを追加しました」

---

## 5. Git同期ルール

whi-knowledge に書き込んだら、必ず以下を実行：

```bash
cd ~/whi-knowledge
git add .
git commit -m "Add: [何を追加したか]"
git push origin main
```

公開可能な範囲なら whi-knowledge-public にも反映：

```bash
cd ~/whi-knowledge-public
# 該当ファイルをコピー（戦略・価格情報は除く）
git add .
git commit -m "Add: [何を追加したか]"
git push origin main
```

コミット後「同期完了しました」と報告。

---

## 6. 作業スタイル

- シンプルさ・読みやすさを優先
- 不要な装飾・冗長な説明を排除
- 既存のパターン・命名規則を踏襲
- デプロイ・運用確認は自分で完結させ、ユーザーに依存しない
- かつさんは非エンジニアなので、専門用語は初出時に1行で説明
