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

## どの commit が本番に載ったかの確認（Vercel CLI は commit sha を出さない）

`vercel ls --prod` / `vercel inspect` の**表形式の出力には commit sha が含まれない**（URL・状態・作成時刻・環境だけ）。
そのため「push した commit が本番に載ったか」を CLI の一覧だけでは確認できない。

**確認の順序**：

1. `vercel ls --prod` で**最新の Production デプロイのURL**を得る。
2. `vercel inspect https://<正式URL>` の **id（`dpl_…`）** が、その最新デプロイの id と**一致**することを見る
   （＝エイリアスが最新を指している。AI-RULES §5-4-C(2)）。
3. **commit の照合は「ビルドログの `Commit` 行」で行う**：

```bash
vercel inspect --logs https://<デプロイURL> 2>&1 | grep -m1 -E '^\s*(Branch|Commit):'
# → Branch: main / Commit: <sha> が出る。これを push した sha と突き合わせる
```

4. `git ls-remote origin main` の生ハッシュ（§5-4-D）と 3 の `Commit` が一致すれば「push した版が配信されている」と言える。

**やってはいけない**：`vercel ls` の一覧に自分の commit が「見えない」ことを根拠に
「デプロイされていない／別の commit が載っている」と結論づける（一覧はもともと sha を持たない）。

---

## 環境変数の反映

環境変数を追加・変更したら、Vercelダッシュボードで該当環境変数を編集後、
再デプロイ（Redeploy）を明示的に実行する必要がある。
プロジェクトのRedeployボタンから手動で。

---

## 関連ノート

- [[mistakes]]
- [[build-pass-not-runtime-ok]]
