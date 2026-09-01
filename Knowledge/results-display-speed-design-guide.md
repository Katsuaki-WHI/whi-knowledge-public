---
date: 2026-09-01
tags: [sqw-survey, nakama, performance, caching, design-guide, results-display, read-time-stamp, whi-common]
project: sqw-survey
related: [[2026-08-31-sqw-results-cache-readtime]] [[2026-08-31-sqw-perf-region-and-results-cache]] [[2026-06-13-sqw-report-display-speed-progressive]]
---

# 結果表示の速度設計ガイド（WHI共通の型）

「表示されるものはすべて同じ仕組みで速くする」ための共通の型。**新しい結果系の表示を作るときは、必ずこの型に乗せる。機能ごとに別のキャッシュ・別の高速化を発明しない。**
初出＝SQWサーベイ2（結果の即表示キャッシュ 第3弾＝[[2026-08-31-sqw-results-cache-readtime]]／第3弾補完＝推移グラフ・定性集約・統合まとめPDF を同じ型へ）。

---

## 1. 全体図（しるし → 共通関数 → 2層の記憶 → 表示）

```
[表示したい範囲(スコープ)]                     例: このチームの結果 / このプロジェクトの塊 / この回の推移 / この定性
      │
      ▼
[しるし(stamp)を軽く取る]  ← RPC(results_stamp_*) 1本。件数＋更新日時の指紋。重い集計はしない
      │   (read-time方式：共有カウンタに書き込まない＝保存パスに競合を足さない。詳細は 第3弾ノート)
      ▼
[共通関数 getCachedResults / getCachedResultsServer]
   キー = 計算式バージョン ∷ スコープ ∷ しるし
      │
      ├─ しるしが同じ → 前に覚えた結果を即返す（重い集計を呼ばない）
      └─ しるしが違う → fetcher で1回だけ計算し、覚える（＝新しいキーで保存。古いキーは自然に参照されなくなる）
      │
      ▼
[2層の記憶]
   ① ブラウザ側 getCachedResults        … メモリ＋sessionStorage（同じ人の行き来が即）
   ② サーバー側 getCachedResultsServer   … results_cache 表（service_role専用・別端末/多人数で再利用）
      │
      ▼
[表示]  数値は fetcher の戻りそのまま＝不変（覚えて即返すだけ。中身の計算は変えない）
```

- **しるし（stamp）＝変更検知**。「書き込みカウンタ」でなく **read-time 算出**（件数＋各行の updated_at から表示時に1回算出）。理由は 第3弾ノート（300名同時保存にロック競合を足さない）。
- **無効化は自動**：しるしが変われば別キーになるだけ。明示的な削除は不要。24時間で必ず作り直す安全網つき。
- **計算式バージョン `RESULTS_FORMULA_VERSION`（scoring.ts）** をキーに含む＝計算式を変えたら全キャッシュが一括で切り替わる。

---

## 2. 新しい結果表示を作るときのレシピ（5手順）

新しく「重い集計を見せる画面／出力」を足すとき、必ずこの順で組む。

1. **① 範囲（スコープ）を決める** — 何を1単位として覚えるか。`teamByInvite:<teamId>` / `adminResults:<teamId>` / `project:<projectId>` / `trend:<今の回teamId>` / `qual:<teamId>` / `projectQual:<projectId>:<scope>` のように **一意なキー文字列** を決める。スコープに引数（絞り込み・回・立場など）が効くなら、その引数も安定した順序でキーに入れる（配列は昇順ソートしてから連結）。
2. **② しるし（stamp）を用意する** — その範囲の「件数＋更新日時の指紋」を返す RPC を使う。単一チーム＝`results_stamp_team`／プロジェクト＝`results_stamp_project`／複数チームにまたがる（推移など）＝各チームの `results_stamp_team` を**連結して合成**する（新RPCを足さずに済むならアプリ側合成でよい）。しるしに member_token・氏名・メールは絶対に含めない（匿名性の防衛線）。
3. **③ 共通関数を通す** — 重い取得は必ず `getCachedResults(scope, stamp, fetcher)`（ブラウザ側）と、その fetcher の中で `getCachedResultsServer(scope, stamp, heavyFetch)`（サーバー側）を通す。**重い関数を直接呼ばない。** 計算方法が別（数値／自由記述など）でも「覚えて即返す」型は共通。
4. **④ 迂回ゼロを確認する** — その重い集計を呼ぶ**すべての経路**（画面・印刷・別タブ・バンドル）をgrepで洗い出し、全部が共通関数を通っているか確認する。1つでも素で呼んでいたら「行き来しても再計算」になり、キャッシュが効かない／画面ごとに二重取得になる。
5. **⑤ 検証する** — 表示内容が**変更前と完全一致**すること（数値は不変が原則）＋**2回目が即**＋**日英**＋**pageerror 0**。しるしは**実データで変化を実際に起こして**進むこと（回答追加→反映→原状復帰）。スクリーンショットを撮り**自分で画像を見て**確認する（要素の存在・pageerror0だけを根拠にしない＝[[mistakes]] 2026-08-26）。

---

## 3. やってはいけないこと

- **重い集計の直接呼び出し**（共通関数を通さず `getTeamResults` / `getProjectAnalysisData` / 推移・定性の計算を素で呼ぶ）。→ キャッシュが効かず、同じデータを画面ごとに二重取得する。
- **機能ごとに別のキャッシュ・別の高速化を発明する**（独自の `useMemo` 貯め込み、独自テーブル、独自TTL）。→ 型が分裂し、後から誰も追えなくなる。**必ず getCachedResults 系に乗せる。**
- **しるしに個人情報を含める**（member_token・氏名・メール・個人結果URL）。→ 匿名性の防衛線を破る。しるしは「件数＋更新日時の指紋」だけ。
- **計算式を変えたのに `RESULTS_FORMULA_VERSION` を更新しない**（CLAUDE.md 絶対ルール9）。→ 古いキャッシュが返り続け、画面の数値が新式に切り替わらない。
- **共有カウンタ方式のしるし**（teams に version 列＋トリガー+1 等）。→ 300名同時回答で同一行ロックが直列化＝保存パスが渋滞（第3弾で却下済み。read-time 算出にする）。
- **「覚えて即返す」ことと「重い集計をしないで済ませる」ことを混同する**：キャッシュは高速化であって、匿名性・公開ゲート・認可の代わりにはならない。fetcher 側で従来どおり認可・公開判定を必ず通す。

---

## 4. 対象・対象外の線引き

- **対象＝対話的な結果表示**（行き来・再表示・タブ切替がある画面）。個人結果・公開チーム結果・運営ページ結果・統合分析の塊・推移グラフ・定性集約・統合まとめPDF。
- **対象外①＝ファイル出力**（Excel/CSV/PDFデータ生成）。一回きりの出力で行き来しない。ただし出力が内部で重い素データを取るなら、その素データ取得だけは共通の `...Cached` 経由にして再利用してよい（害はない）。
- **対象外②＝AIレポート**：生成物には既存の専用キャッシュ（`ai_report_cache`）がある。結果表示キャッシュに混ぜない。

---

## 5. SQWでの現在地（この型に乗っているもの）

| 表示 | スコープキー | しるし |
|---|---|---|
| 運営ページ 結果 | `adminResults:<teamId>` | results_stamp_team |
| 公開・個人のチームタブ | `teamByInvite:<teamId>` | results_stamp_team |
| 統合分析 塊（数値） | `analysis:<projectId>`（ブラウザ）＋`project:<projectId>`（サーバー） | results_stamp_project |
| 一括印刷 | チームごと `adminResults:<teamId>` を共有 | results_stamp_team |
| **推移グラフ**（第3弾補完） | `trend:<今の回teamId>` | **チェーン各回 results_stamp_team の合成** |
| **定性集約（チーム）**（第3弾補完） | `qual:<teamId>` | results_stamp_team |
| **定性集約（プロジェクト）**（第3弾補完） | `projectQual:<projectId>:<scope>` | results_stamp_project |
| **統合まとめPDF**（第3弾補完） | 素データを `project:<projectId>` で共有 | results_stamp_project |

- 推移しるしは**アプリ側合成**（各回の `results_stamp_team` を `~` で連結）＝新RPC不要・本番DB変更なし。`startTeamId`＝見ている回（チェーン末尾）＝見る回が違えば別スコープ。
- 定性は計算方法が別（自由記述のグルーピング）だが同じ「覚えて即返す」型に乗る。シャッフル順は**しるしが同じ間は固定**（投稿順は隠れたまま＝匿名性は不変）。定性のしるしは回答の増減で進む（定性回答は回答送信時に survey_answers と同時に作られるため results_stamp_team で捕捉できる。qualitative_responses 単独更新の経路はない）。

---

## 6. 関連

- 仕組みの技術記録（第3弾・read-time しるしの詳細と却下案）＝[[2026-08-31-sqw-results-cache-readtime]]
- リージョン同拠点化（関数↔DBの往復を減らす・キャッシュと合わせて効く）＝[[2026-08-31-sqw-perf-region-and-results-cache]]
- プログレッシブ表示（先出し）＝[[2026-06-13-sqw-report-display-speed-progressive]]
- 検証の心得＝[[mistakes]] 2026-08-26（機械的PASSにしない・画像を見る）／build pass≠runtime ok＝[[build-pass-not-runtime-ok]]
