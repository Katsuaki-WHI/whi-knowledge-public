---
date: 2026-05-19
updated: 2026-09-04
tags: [knowledge, claude-code, productivity, tips, auto-mode]
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

---

## auto mode（2026-09-04 追加・これが本命）

毎回の「Yes / Yes always / No」を、**設定で一括して自動許可**できる。
セッションごとに押し直す必要がなくなるので、**「Yes always」より根本的**。

### 設定（ユーザー共通設定に書く）

`~/.claude/settings.json`

```json
{
  "env": { "CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION": "false" },
  "permissions": {
    "defaultMode": "auto",
    "allow": ["Read", "Edit", "Write", "Bash"],
    "ask": [
      "Bash(git push:*)",
      "Bash(git reset --hard:*)",
      "Bash(git branch -D:*)",
      "Bash(git push origin --delete:*)"
    ]
  }
}
```

※`CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION: false` は別件（承認文の形をした自動提案が
誤送信された事故の対策・[[mistakes]] 2026-08-31）。auto mode と同じファイルに同居している。

### 押さえておくこと

- **Claude Code v2.1.228 以上**が必要。
- ★**ユーザー共通設定（`~/.claude/settings.json`）に書く。プロジェクト側の `settings.json` では効かない。**
  プロジェクトに書いても無視されるので、「設定したのに効かない」と悩まないこと。
- ★**`git push` は auto でも `ask`（都度確認）のまま**にしてある。
  不可逆・外向きの操作は自動化しない（[[AI-RULES]] §5-4 の承認文ルールと整合）。
- **Shift+Tab** でモードを切り替えられる（auto ⇄ 通常）。一時的に慎重にしたいときに使う。
- **セッション内で押した「Yes always」は次回起動でリセットされる**（従来どおり）。
  恒久的にしたいなら上記の `defaultMode` を使う。

### 使い分け

- 読み取り・検索・ファイル編集・ビルドなど**繰り返す作業** → auto mode で流す
- **push・本番デプロイ・削除・DB変更** → auto でも都度確認（`git push` は ask のまま）＋
  承認文（「承認：」で始まる文）を受け取ってから実行

## 出典

Claude Codeの導入承認を得るための稟議書作成（2026/04/05）／auto mode は 2026-09-04 に設定・運用開始
