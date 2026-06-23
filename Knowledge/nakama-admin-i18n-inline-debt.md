---
date: 2026-06-24
tags: [i18n, admin, nakama, sqw, tech-debt]
project: nakama-survey / sqw-survey
related: [[question-text-replacement-i18n]]
---

# なかま WHI Admin の言語方式は inline 分岐（辞書化はSQW Admin実装とセットの確定タスク）

## 事象
なかまクエストの WHI Admin 画面（`/admin/whi/*`）は、日英の文言を **コンポーネント内の inline 三項分岐**（`locale === "ja" ? "..." : "..."`）で出している。
これは i18n 方針（文言ハードコーディング禁止・辞書ファイル分離・キー参照）に対する**技術的負債**。

## なぜ今すぐ直さないか
- なかま Admin は単独で正常動作し、表示も日英そろっている（機能上の不具合はない）。
- 単独で辞書化すると差分が大きく、これから新規実装する SQW Admin と二重作業になる。
- 共通デザインは「なかま Admin を手本に SQW へ横展開」する方針。

## 確定タスク（先送りにしない）
**SQW Admin を実装するときに、なかま Admin の辞書化とセットで行う。**
- SQW Admin は最初から辞書方式で実装する。
- 同じタイミングで なかま Admin の inline 分岐を辞書方式へ移行し、共通の辞書基盤を使う。

## 注意
- i18n 辞書のキーは**関数でなく文字列＋プレースホルダー**で実装する（既出の落とし穴）。
