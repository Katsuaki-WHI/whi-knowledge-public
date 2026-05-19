---
date: 2026-05-19
tags: [knowledge, nextjs, rsc, bug]
project: all
---

# RSCマーシャリング違反：columns配列内のrender関数

## 問題

Server Componentの`columns`配列内に`render: (r) => <...>`関数を含み、
Client Componentに直接渡すとRSCマーシャリング違反で本番500エラー。

## NG例

```typescript
// Server Component
const columns = [
  { key: "name", label: "名前" },
  { key: "status", label: "状態", render: (row) => <Badge color={row.color}>{row.status}</Badge> } // ← 関数が含まれる
];

return <ClientTable columns={columns} data={data} />; // ← マーシャリング違反
```

## 原因

- 関数はRSC境界を越えてシリアライズ不可
- ビルドは通るが、ランタイムで500エラー
- L1〜L3 PASSでもL4で初めて検出される

## 対策

関数を含む`columns`定義はClient Component内に置く：

```typescript
// Client Component（"use client"）
"use client";

const columns = [
  { key: "name", label: "名前" },
  { key: "status", label: "状態", render: (row) => <Badge color={row.color}>{row.status}</Badge> } // ← Client内なのでOK
];

// Server Componentからはデータだけ渡す
// Server Component側
return <ClientTable data={data} />; // columnsを渡さない
```

## 出典

なかまサーベイ Stripe MVP デプロイ後の優先タスク整理 Step 3-23h

---

## 関連ノート

- [[mistakes]]
- [[build-pass-not-runtime-ok]]
- [[4-level-verification-protocol]]
- [[question-text-replacement-i18n]]
