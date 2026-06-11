---
date: 2026-06-11
tags: [stripe, pricing, payment, nakama-quest, procedure]
project: nakama-quest
related: [[mistakes]]
---

# Stripe の価格変更手順

## 大原則
- **Stripe の Price オブジェクトの金額は編集できない**（会計整合のための仕様）。
  値上げ/値下げ/税込化は「新しい Price を作る」しかない。
- **表示価格だけ変えて実請求額とズレると不当表示**になる。表示（コードの定数）と
  請求（Stripe の price_id が指す Price）は必ず揃える。

## 手順（6 ステップ）
1. **新 Price を作成**（Stripe Dashboard）。税込なら税込金額で作る。
2. その商品の**デフォルト Price に設定**。
3. **旧 Price をアーカイブ**（新規購入で使われないように）。
4. **コードの price_id を新 ID に差し替え**（なかまクエスト：`src/lib/stripe/client.ts` の `STRIPE_PRICES`）。
5. **表示定数を更新**（なかまクエスト：`src/lib/stripe/pricing.ts` の `PRICE_DISPLAY`。単一 source of truth）。
6. **実ブラウザで「表示＝請求」の一致を確認**（購入ボタン文言＋ Checkout 画面の金額）。

## 注意点
- 表示と請求は別物。①〜⑤を片方だけやると不整合になる。④⑤は必ずセット。
- 記録用の単価を持つコード（例：一括購入の `KNOWN_UNIT_AMOUNT`）があれば、そこも合わせて更新する
  （実請求は price_id 基準だが、`purchases.amount` 等の記録がズレる）。
- 領収書メール等は保存 amount から生成するなら自動追従する（金額文字列をハードコードしない設計が望ましい）。
- 日本円は**税込（総額表示）が義務**。USD/EUR は日本の総額表示義務の対象外（market ごとに判断）。

## 今回の実例（2026/06/11）
- 冒険バイブル ¥500 → **¥550（税込）** / GLB ¥2,000 → **¥2,200（税込）**。
- 新 price_id（税込）を作成→デフォルト化→旧アーカイブ→`client.ts` 差し替え→`pricing.ts` 更新→本番実ブラウザで
  「購入ボタン ¥550（税込）/ ¥2,200（税込）」と Checkout 金額一致を確認。USD/EUR は据え置き。
