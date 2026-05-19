---
date: 2026-05-19
tags: [nextjs, rsc, deployment, lessons]
project: all
---

# Build PASS は本番Runtime OKを意味しない

## 問題

`npm run build` が成功しても、本番環境で実行時エラーになることがある。
特にNext.js（App Router）でRSC（React Server Components）を使う構成では、
ビルド時に検出できないマーシャリング違反（サーバー→クライアントへのデータ受け渡しエラー）が発生する。

## 原因

- RSC境界を越えてfunction・Date・Map・Set・classインスタンス等のシリアライズ不可能な値を渡している
- ビルド時の静的解析では検出されず、ランタイム実行時に初めてエラー化する

## 対策

1. ビルド成功 ≠ 完了 と認識する
2. 必ずブラウザベースの実HTTPリクエストフローで動作確認する
3. Server Component → Client Component のpropsはJSONシリアライズ可能な値のみ
4. 日付はISO文字列、Mapは配列に変換してから渡す

## チェックリスト

実装完了報告前に必ず：
- [ ] `npm run build` 成功
- [ ] `npm run start` でローカル本番モード確認
- [ ] ブラウザで実際のページを開いて動作確認
- [ ] Vercel preview でも確認

---

## 関連ノート

- [[mistakes]]
- [[vercel-deploy-tips]]
