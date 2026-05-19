---
date: 2026-05-19
tags: [knowledge, claude-code, authentication, troubleshooting]
project: all
---

# Claude Code 認証エラーからの復旧

## 問題

Claude Codeが以下のエラーで動かなくなる：

```
API Error: 401 Invalid authentication credentials
Please run /login
```

## 原因

認証情報が無効・期限切れになっている。

## 対策

### 手順

1. Claude Codeのプロンプトに `/login` を入力してEnter
2. ログイン方法の選択肢が表示される
3. **「Claude account with subscription」を選択**（マックスプラン・Proなどの場合）
4. ブラウザが自動で開く(または URL が表示される)
5. ブラウザで対象アカウントでログイン
6. 「Login successful」表示後、Enterで継続
7. Claude Code 通常画面に戻る

### 代替手段

- `/logout` してから再度 `claude` で起動
- ターミナルを再起動

## 補足

Claude Codeは定期的に認証が切れることがある。
`/login` コマンドは覚えておくと便利。

## 出典

whi-knowledge構築（2026/05/19）

---

## 関連ノート

- [[claude-code-permission-shortcut]]
