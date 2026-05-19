---
date: 2026-05-19
tags: [vercel, deployment, lessons]
project: all
---

# Vercelデプロイのコツと落とし穴

## 自動デプロイが検出されないとき

GitHubにpushしてもVercelが自動デプロイをトリガーしないことがある。
（Webhook未検出・キャッシュ問題・GitHub連携の一時的不整合など）

### 対処法

空コミットを作って手動でWebhookをトリガーする：

```bash
git commit --allow-empty -m "trigger redeploy"
git push origin main
```

これでVercel側のWebhookが再起動し、デプロイが走る。

## ビルド成功でも本番エラーになるケース

詳細は [[build-pass-not-runtime-ok]] 参照。
ビルド成功＝本番OKではない。必ず実HTTPで確認すること。

## 環境変数の反映

環境変数を追加・変更したら、Vercelダッシュボードで該当環境変数を編集後、
再デプロイ（Redeploy）を明示的に実行する必要がある。
プロジェクトのRedeployボタンから手動で。

---

## 関連ノート

- [[mistakes]]
- [[build-pass-not-runtime-ok]]
