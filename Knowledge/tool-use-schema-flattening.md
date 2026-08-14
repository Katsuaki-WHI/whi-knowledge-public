---
date: 2026-08-14
tags: [ai-report, tool-use, json, schema]
---

# Tool Use スキーマ設計の学び：1項目だけのオブジェクトを作らない

## 事象
- AIレポートを Tool Use（構造化出力）で生成する際、初回の実生成が `invalid tool_use json` で失敗した。
- 原因＝モデルが**単一 body しか持たないセクション**を、`{"body": …}` という **JSON文字列＋末尾に余分な `}`** という壊れたJSONで**二重エンコード**して返した。
- 同じスキーマ内でも、**プレーン文字列**のフィールド（opening/closing のような単一の文字列）では起きなかった。

## 対処
- **単一項目しか持たないオブジェクトは `string` にフラット化する**。
- 複数フィールドを持つセクションは object のままでよい。

## ★教訓
- **Tool Use のスキーマで「1項目だけのオブジェクト」を作らない。文字列にできるものは文字列にする。**
- 単一値を `{body: ...}` のようにネストすると、モデルがそのネストを「JSON文字列で埋める」誤生成をしやすく、二重エンコードで壊れる。
- 関連＝[[ai-tool-use-double-encoding-defensive-parse]]。

## デプロイの一般的注意（併記）
- Next.js/Vercel で Production Branch が `main` の構成では、作業ブランチへの push は Preview になり、Preview 環境に必要な環境変数（DB接続等）が無いとビルドが失敗することがある。本番反映は main への fast-forward push で行う。
- 関連＝[[build-pass-not-runtime-ok]]。
