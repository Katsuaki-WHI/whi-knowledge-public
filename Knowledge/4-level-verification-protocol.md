---
date: 2026-05-19
tags: [knowledge, testing, verification, quality]
project: all
---

# 4段階検証プロトコル（L1〜L4）

## 背景

「正常です」報告が信頼できない・本番でだけ落ちるパターンが頻発。
L1〜L3の検証だけでは不十分。

## 4段階の定義

### L1：ビルド検証
- `tsc --noEmit`
- `next build`
- TypeScriptの型エラー・ビルドエラーがないことを確認

### L2：静的解析
- コードgrep
- import確認
- リンタの実行

### L3：単体クエリ
- Service Role直接クエリ
- 個別関数の単体テスト

### L4：ブラウザ経由・実HTTPリクエスト ★最重要
- `npm run dev` でローカル起動
- 実際のブラウザでアクセス
- 実HTTPリクエストを発生させて確認
- ユーザーが触る経路と同じフローで動作確認

## 重要原則

**L1〜L3 PASSでもL4で失敗することがある。**

実例：
- RSCマーシャリング違反（[[rsc-marshalling-violation-columns-with-render]]）
- 翻訳キー関数による500エラー（[[question-text-replacement-i18n]]）
- 既存DBレコード非互換（[[legacy-data-compatibility-check]]）

これらはL1〜L3 PASSでもL4で落ちる。

## 適用ルール

重要な変更は必ずL4まで実施してから完了報告する。
L4を省略してはいけないケース：
- Server/Client Component境界の変更
- RSC関連の変更
- DBスキーマ・質問構成の変更
- i18n辞書の変更
- 認証・課金フローの変更

## 出典

なかまサーベイ Stripe MVP デプロイ後の優先タスク整理 Step 3-23h

---

## 関連ノート

- [[mistakes]]
- [[build-pass-not-runtime-ok]]
- [[rsc-marshalling-violation-columns-with-render]]
- [[question-text-replacement-i18n]]
- [[legacy-data-compatibility-check]]
