---
date: 2026-05-19
tags: [knowledge, i18n, nextjs, rsc, lessons]
project: all
---

# i18n翻訳キーはプレースホルダー方式・関数禁止

## 問題

i18n辞書のキーを関数として実装するとServer Component→Client Componentの境界で渡せず本番500エラー。

## NG例

```typescript
// 辞書
adminForceCompleteModalBody: (count) => `現在 ${count} 名で進めます。`

// Server Componentから直接渡せない
<ClientModal text={dict.adminForceCompleteModalBody(5)} /> // 関数自体がpropsに含まれるとエラー
```

## 原因

Next.jsの制約：関数はpropsとして直接渡せない（"use server"明示が必要）。
RSC境界を越えて関数をシリアライズできない。

## 対策

文字列＋プレースホルダー形式にする：

```typescript
// 辞書（OK）
adminForceCompleteModalBody: "現在 {currentCount} 名で進めます。"

// 呼び出し側（Server/Client問わずOK）
const text = dict.adminForceCompleteModalBody.replace("{currentCount}", String(count));
<ClientModal text={text} />
```

## 出典

管理画面MVP実装の開始準備 Step 5

---

## 関連ノート

- [[mistakes]]
- [[i18n-strategy]]
- [[rsc-marshalling-violation-columns-with-render]]
