---
date: 2026-09-27
tags: [開発環境, Mac, 移行, 手順, 横断]
project: 横断
related: [[local-env-files-override-and-prod-db]] [[claude-code-login-recovery]] [[base-url-consolidation-and-proof]] [[AI-RULES]]
---

# 開発環境の Mac 移行手順（M1 → M5）

実施＝**2026-09-27〜28**（M1 Mac → M5 Mac）。
★**要点は2つ**＝⑴**git に入らないもの（`.env.local`）は自分で運ぶ** ⑵**ユーザー名を変えない**。

## ① 全リポジトリを push（ahead 0 にする）

移行前に、**ローカルにしか無いコミットを0にする**。
各リポジトリで `git status -sb` の1行目に `ahead` が出ない状態にしてから進む。

```bash
for d in ~/sqw-survey ~/nakama-survey ~/whi-knowledge ~/whi-knowledge-public; do
  echo "=== $d ==="; git -C "$d" status -sb | head -3
done
```

★**未コミットの変更も残さない**（移行アシスタントはファイルを運ぶが、
「これは移したつもりだったか」を後から確かめられるのは**リモートに在るもの**だけ）。
★プロダクト本番への push は承認が要る（[[AI-RULES]] §5-4）＝**承認が下りない変更は、移行前に急いで push しない**。
その場合は**ローカル commit まで**で止め、移行後に同じブランチが在ることを確かめる。

## ② `.env.local` を iCloud の一時フォルダに退避

`.env.local` は **`.gitignore` の対象**（`.env*`）なので **git では運ばれない**。
**移行前に、iCloud Drive の一時フォルダへコピーしておく。**

- 実際に在るのは **2つ**＝`~/sqw-survey/.env.local`・`~/nakama-survey/.env.local`。
- ★**中身は見ない・貼らない・記録に残さない**（本番の鍵が入っている）。
- ★**⑤で必ず消す**（残すと、鍵が同期先のどこかに残り続ける）。

## ③ 移行アシスタントで「別のMacへ」・★ユーザー名は同一にする

- 新しい Mac の初期設定で**移行アシスタント**を使う（旧Mac側は「別のMacへ」）。
- ★★**ユーザー名（＝ホームフォルダ名）を旧Macとまったく同じにする。**
  違う名前にすると `/Users/<名前>/…` が変わり、**CLAUDE.md・whi-knowledge・スクリプトに書いてある
  `~/…` 以外の絶対パスが全部ずれる**（例＝`~/whi-knowledge/Decisions/…` を指す記録）。
  ★ここは後から直すのが一番高い。**最初に合わせる。**

## ④ 新Mac で確認する（1つずつ・全部通ってから作業を始める）

```bash
xcode-select -p          # コマンドラインツール（git などの土台）
node -v                  # Node.js
git --version
which claude             # Claude Code
brew --version           # Homebrew
```

```bash
# 3リポジトリ（＋ whi-knowledge-public）の状態と .env.local の在処
for d in ~/sqw-survey ~/nakama-survey ~/whi-knowledge ~/whi-knowledge-public; do
  echo "=== $d ==="; git -C "$d" status -sb | head -3; ls -l "$d/.env.local" 2>/dev/null
done
```

```bash
gh auth status           # GitHub CLI
vercel whoami            # Vercel
supabase projects list   # Supabase（ログインしているか）
```

```bash
npm run dev              # 画面が出るところまで（★ローカル dev は本番DBを見る＝書き込まない）
```

★**ログインは移行で運ばれないことがある**（`gh` / `vercel` / `supabase` / `claude`）。
**運ばれていなければ、その場でログインし直す**（対話ログインは自分で実行する）。
★`npm run dev` は**ローカルの画面 × 本番DB**なので、**本番検証と混同しない**（[[local-env-files-override-and-prod-db]]）。

## ⑤ 退避フォルダを削除する

`.env.local` を戻したら、**②で作った iCloud の一時フォルダを消す**。
★**「あとで消す」は禁止**＝鍵は置き忘れる。**戻したその場で消す。**

## つまずきやすいところ

| ところ | 何が起きるか |
|---|---|
| ユーザー名を変えた | 絶対パスを書いた記録・スクリプトが全部ずれる（③） |
| `.env.local` を運ばなかった | `npm run dev` が起動しない／鍵が無く生成が動かない（②） |
| `ahead` が残ったまま移行した | そのコミットが新Macに在るかを、リモートと比べて確かめられない（①） |
| CLI のログインが運ばれない | `gh` / `vercel` / `supabase` / `claude` を入れ直す（④） |
| 退避フォルダを消し忘れた | 本番の鍵が同期先に残る（⑤） |

★一般則＝**移行で運ばれないものは2種類**＝**git に入らないファイル**と**ログインの状態**。
この2つを、移行の前に一覧にしてから始める。
