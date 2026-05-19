---
date: 2026-05-19
tags: [mistakes, lessons, do-not-repeat]
project: all
---

# mistakes.md - 再発防止の核

かつさんから明示的に訂正された、再発しうるパターンを記録。
新規追加は AI-RULES.md の「3. mistakes.md への追記ルール」3条件を満たすときのみ。

---

## 2026-05-19：Build PASS は本番OKを意味しない

**NG**：`npm run build` が成功したら「実装完了」と報告する
**Correct**：ビルド成功後、必ずブラウザベースの実HTTPリクエストフローで動作確認してから「実装完了」と報告する
**Trigger**：Next.js/RSC（React Server Components）を使うプロジェクトでの実装完了報告時。
RSCマーシャリング違反はビルド成功でも本番でエラーになるため、ビルド成功だけでは品質を担保できない。

---

## 2026-05-19：「調査済み・正常」報告を鵜呑みにしない

**NG**：Claude Codeが「動作確認しました、正常です」と報告したら、それで完了扱いにする
**Correct**：ブラウザベースの実HTTPリクエストフローで、かつさんもしくはClaude Code自身が実際に画面・APIを叩いて確認する
**Trigger**：実装完了報告・バグ修正完了報告のとき。
過去に「正常」報告のあと本番で再現した事例あり。

---

## 2026-05-19：APIエラー時の連打でコストが跳ねる

**NG**：APIエラー発生時、原因究明前に再実行を繰り返す
**Correct**：エラー発生時はまず原因究明。Spend Limit設定とアラート閾値を必ず設定。連打しない。
**Trigger**：Anthropic API・OpenAI APIなど従量課金APIのデバッグ時。
過去に予期せぬコスト消費が発生した。詳細は [[api-cost-management]]。

---

## 2026-05-19：不可逆な変更を事前確認なしに実行しない

**NG**：「削除して」「本番デプロイして」を即実行
**Correct**：実行前に必ずユーザーに確認。「本当に削除して良いですか？」「本番に反映しますか？」と一拍置く。
**Trigger**：削除コマンド・本番デプロイ・DBスキーマ変更・データ移行など、戻せない操作。

---

## 2026-05-19：ai_promptsテーブルは使わない

**NG**：AIプロンプトをDBの`ai_prompts`テーブルから取得する実装
**Correct**：AIプロンプトはコード（whi-philosophy.ts等）でのみ管理。`ai_prompts`テーブルは`is_active=false`固定で参照禁止。
**Trigger**：AIレポート生成・コーチング生成のプロンプト管理時。

---

## 2026-05-19：DiSC関連語をユーザー画面に出さない

**NG**：なかまクエストのユーザー向け画面・レポート本文に D/i/S/C 記号やDiSC関連語を表示
**Correct**：ユーザー向けは「戦士⚔️」「芸人🎭」「僧侶🛡️」「学者📐」の4種で統一。DiSC理論は裏のロジックのみ。
**Trigger**：なかまクエストのユーザー向けコンテンツ実装時。Wiley社IP保護のため。
詳細は [[third-party-ip-policy]]。

---

## 2026-05-19：Gallup関連語をユーザー画面に出さない

**NG**：SQWサーベイ2のユーザー向け画面・レポート本文に Gallup Q12・CliftonStrengths・各資質名等を表示
**Correct**：裏のロジックとして参照はOK。表面は自分たちの言葉に言い換える。
**Trigger**：SQWサーベイ2のユーザー向けコンテンツ実装時。Gallup社IP保護のため。
詳細は [[third-party-ip-policy]]。

---

## 2026-05-19：日英片方だけの更新禁止

**NG**：日本語版だけ・英語版だけを更新する
**Correct**：全画面・全コンテンツを日英両方同時に更新する。英語版が主、日本語版が従の位置づけ。
**Trigger**：UIテキスト・レポート・メール文面の編集時。
詳細は [[i18n-strategy]]。

---

## 2026-05-19：文言ハードコーディング禁止

**NG**：コード中に「送信する」など表示文字を直接書く
**Correct**：辞書ファイル（ja.json/en.json等）に分離し、コードはキーで参照する
**Trigger**：UIコンポーネント・メール文面・通知メッセージなど、ユーザー向け文字列の実装時。
詳細は [[i18n-strategy]]。

---

## 2026-05-19：Vercel自動デプロイが検出されないときは空コミット

**NG**：Vercelに変更が反映されないと「なぜか反映されない」と悩む
**Correct**：`git commit --allow-empty -m "trigger redeploy"` で空コミットを作りpush、Webhookを手動トリガー
**Trigger**：Vercelの自動デプロイが検出されない・反映されないとき。
詳細は [[vercel-deploy-tips]]。

---

## 2026-05-19：指示書を分割しない

**NG**：「追加指示書」「補足」「修正版」と分けて出す
**Correct**：常に最新版の1つだけが「実行すべき指示書」。方針変更時は過去の指示書を破棄して作り直す。
**Trigger**：claude.ai がClaude Codeへの指示書を作成するとき。
詳細は [[conversation-rules]]。

---

## 2026-05-19：ファイル操作・DB操作は1つずつ

**NG**：複数のファイル変更・DB変更をまとめて一気に実行
**Correct**：1つずつ丁寧に案内・実行する。段階的に進める（DB先・コード後）。既存運用への影響をゼロに保つ。
**Trigger**：ファイル変更・DB変更が複数発生する作業時。

---

## 2026-05-19：insight-comments.tsのカテゴリキー名は完全一致

**NG**：`insight-comments.ts` のカテゴリキー名を `questions.ts` と微妙に異なる形で書く
**Correct**：両ファイルのカテゴリキー名は完全一致が必須。
正しいキー：`landscape/road/rope/tire/body/attitude/cargo/diversity/happiness`
**Trigger**：なかまクエストのカテゴリ別コメント機能を編集時。

---

## 2026-05-19：AIレポートプロンプト変更は3ファイル同時更新

**NG**：whi-philosophy.tsだけ更新してquestions.tsやhtml-template.tsを更新しない
**Correct**：必ず①whi-philosophy.ts ②questions.ts ③html-template.ts の3ファイルを同時更新
**Trigger**：AIレポートプロンプトの改善・変更時。
詳細は [[ai-report-3files-sync]]。

---

## 2026-05-19：GoldButtonの文字色にグラデーション指定するとバグる

**NG**：GoldButtonの`color`にグラデーション文字列（`linear-gradient(...)`）を指定する
**Correct**：`color`には単色（`#180800`等）のみ指定。グラデーション文字列はcolorで無効値となりブラウザフォールバック動作する。
**Trigger**：なかまクエストのGoldButtonコンポーネント使用時。

---

## 2026-05-19：選択肢ボタンを多用しない

**NG**：claude.ai がブレスト・設計議論中に選択肢ボタン（ask_user_input）を頻繁に出す
**Correct**：文章で論点と推奨を提示。「全部おすすめでOK」「A・B」のような短い判断で進められるようにする。
**Trigger**：claude.ai との会話中、特にブレスト・設計議論のとき。
詳細は [[conversation-rules]]。

---

## 2026-05-19：同じ内容を繰り返さない

**NG**：タイムライン・確認事項・ステップ予告等を何度も繰り返す
**Correct**：同じ情報は1回だけ。冗長な表組み禁止。読み返し疲労を排除。
**Trigger**：claude.ai の応答全般。
