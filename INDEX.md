# WHI Knowledge Base (Public Mirror) - INDEX

このリポジトリは、WHI（Work Happiness Inc.）の事業横断的な知識ベースの**公開ミラー**です。
claude.ai が `web_fetch` で読めるよう、機密でない範囲のみを公開しています。

機密情報（価格・卸値・インフラURL・トークン・プロジェクト現状）は
Private 本体リポジトリのみに保管されています。

## 構成

### philosophy/ - 全判断の土台（Privateのみ保管）
WHIフィロソフィーは Project手順本文（claude.ai用）と
Private版 ~/whi-knowledge/philosophy/（Claude Code用）の2箇所に同内容で保管。
Public版には公開しない。
唯一の正は Project手順本文。詳細は Decisions/whi-philosophy-single-source-of-truth.md 参照。

### Knowledge/ - 技術知識・落とし穴
- [mistakes.md](Knowledge/mistakes.md) - 再発防止の核
- [disclosure-in-flex-heading.md](Knowledge/disclosure-in-flex-heading.md) - **「？」で開く折りたたみ部品を、横1行（flex）の見出しに置くと、本文が「？」の右に入り込んで見出しのほうが縦に潰れる**（実測：狭い幅で見出しが4行に折り返し）。原因＝部品がボタンと本文を**兄弟で返す**ため、呼び出し側が flex だと**本文もその行の flex アイテムになり**、既定の `nowrap` で次の行へ逃げられない。**同じ部品でも flex でない呼び出し元では正しく下に出る**ので、片方の画面だけ壊れて見え原因が部品側だと気づきにくい。直し方は**2つセット**＝(a) 本文に `flex-basis:100%`（＋`width:100%`）(b) 呼び出し側の見出しに `flex-wrap:wrap`（**wrap が無いと `flex-basis:100%` は改行を起こさない**）。flex でない呼び出し元では `flex-basis` は無視＝既に正しい場所を壊さずに直せる。あわせて＝本文の**太さを明示**（見出しの太字を継承する）／**`<p>` の入れ子を作らない**（SSRで開いた状態を描くとハイドレーションのずれ）／`aria-expanded`+`aria-controls`・**Enter と Space を実際に押して確認**。★検証＝「押したら開く」で終わらせず、**本文の矩形がボタンの下端より下にあるか**を実測し、**同じ部品の全呼び出し元**で確認する。一般化＝**折りたたみ部品は「本文がどこに出るか」を部品側で保証する**。
- [broken-cache-deadlock-and-save-validation.md](Knowledge/broken-cache-deadlock-and-save-validation.md) - **壊れた生成物が「成功」として保存され、しかも作り直せない**という2つの欠落が重なると、1件だけが永久に直らなくなる。実例＝AIの構造化出力が1章だけ二重エンコード＋**末尾に閉じ括弧が1つ余分**で返し、救済パースが1文字で失敗→画面は「データ形式エラー」、生成ボタンは「jobIdが返らない」。①生成側が**保存前に検証していない**ため壊れた出力を保存しジョブをcompletedにする＝**失敗の痕跡が残らず、壊れている状態と正常な空が見分けられない** ②生成APIが**キャッシュの「有無」だけで打ち切る**ため、壊れたキャッシュがあると新規ジョブが作られず**袋小路**（強制再生成は管理者限定で本人にも運営者にも逃げ道なし）。守ること＝**保存の前に検証し通らなければ失敗として記録**／**キャッシュを返す条件は「ある」でなく「使える」**／**逃げ道を管理者専用にしない**（連絡が来なければ壊れたまま誰も気づかない）／救済パースは対症療法で本命は保存前検証／**エラー文言を実態と合わせる**（「古い形式」表示の実体は1文字多いだけ・バージョン列はハードコードで画面は見てすらいなかった＝文言が原因を誤指しすると調査が最初から逸れる）。**対処済み（2026-09-11）**＝(a)袋小路を塞ぐ(b)末尾の余分な閉じ括弧を**1つだけ**落として再挑戦（何度も削ると途中で切れたデータまで読めたことにする）(c)保存前検証(d)到達できない再生成コードを削除（残すと「ここから直せる」と誤読される）。実装の判断＝**判定は1つの関数に集約**（画面ごとに書くと直した場所と効く場所がずれる）／**同じ構造の兄弟にも同時に当てる**／**壊れたものの復旧は本人ができるようにする**。検証の型＝**救済関数は「救わない側」のテストのほうが大事**／**既存データ全件を新しい判定関数そのもので通す**／**最後は実物で1回通す**（部品のテストが全部通っても経路がつながっている保証にはならない）。**本番1行を直すときの型**（丸ごとバックアップ＋sha256を更新直前に再照合して不一致なら中断／表示側と同じロジックで検証してからUPDATE／更新日時など余計な列を触らない／実画面のスクショを自分で見る）つき。関連=[[build-pass-not-runtime-ok]]
- [same-emoji-two-meanings-in-one-row.md](Knowledge/same-emoji-two-meanings-in-one-row.md) - **同じ絵文字を、同じ行で別の意味に使わない**。一覧は上から目を走らせる場所なので、同じ記号が並ぶと読み手は「同じもの」と受け取る＝**1行の中で1つの絵文字が2つの意味を持つと、一覧としての機能そのものが落ちる**。実例＝メンバー行に「上位商品を買った印」と「役職の印」が同じ絵文字で並んだ。★**両方ともそれぞれの画面では正しい記号**で、ぶつかったのは**別々に決まった2つの正しさが1つの行で初めて隣り合ったから**。★**例外ではなく普通に起きる組み合わせだった**（その商品を買うのはたいていその役職の人＝ほぼ毎回ぶつかる）→**ぶつかる条件が「よくある組み合わせ」かどうかを先に確かめる**。気づきにくい理由＝どちらの画面も単体では正しい／**読み上げや吹き出しでは意味が取れるので機械的な検証は全部通る**（目で一覧を眺めて初めて分かる）／実装者は意味を知っているので並んでいても 読めてしまう。直し方＝①**grep で記号の意味を全部数える**（2つ以上あること自体は悪くない・問題は「同じ行に同時に出るか」）②**残すのは外の画面と揃っている側**（動かすとそこで新しい不一致ができる）・同じ行で意味が決まらない側を文字など別の表し方に変える③**同じ意味の他の場所まで機械的に変えない**（ぶつかっていない画面は触らない＝「揃える」を目的にすると差分が広がる）。予防＝一覧の1行に記号を足すときは**その行に既にある記号を全部数えてから決める**／記号の対応表を1箇所に持つ／**検証は必ず一覧を目で見る**（要素の有無・読み上げ文言・件数では検出できない）。
- [service-role-only-table-silent-empty.md](Knowledge/service-role-only-table-silent-empty.md) - **RLS で service_role 限定にした表を通常のクライアントで読むと、エラーにならず「空」が返る**。読んだ値をフォールバックに流していると**フォールバック値が常に採用され続け、例外も出ないので誰も気づかない**（実例＝操作ログの「実行者」が毎回フォールバックの固定値で記録され、ログ自体は正常に書けているため気づけなかった）。★気づきにくい理由＝**落ちる先がもっともらしい既定値**で出力が壊れて見えない／書き込み側は成功する。直し方＝①**その表をどのクライアントで読めるかを書く前に確認**（同じ表を読む既存コードが service_role を使っていないか見る）②**フォールバックが採用された状態と本来の値が採用された状態を両方とも実際に見る**③握り潰す範囲は「失敗しても本体を止めない」に限り、**値が取れなかったことまで無かったことにしない**。★検証＝「動いた」でなく**書いた行を読み返して中身を確かめる**。一般化＝**RLS は「読めない」を「エラー」でなく「空」で表現する**ので、権限で守られた表を読むコードは**空＝権限不足の可能性**を最初から想定する。
- [results-display-speed-design-guide.md](Knowledge/results-display-speed-design-guide.md) - 結果表示の速度設計ガイド（共通の型）。「表示されるものはすべて同じ仕組みで速くする」＝機能ごとに別のキャッシュを発明しない。全体図＝しるし(stamp)→共通キャッシュ関数→2層の記憶(ブラウザmemory+sessionStorage/サーバーキャッシュ表)→表示(数値は不変)。しるし＝read-time算出の「件数＋更新日時の指紋」（共有カウンタ書き込み禁止＝保存パスに競合を足さない・個人情報は含めない）。新表示のレシピ5手順＝①範囲(スコープキー)②しるし(複数対象またぎは各しるしを連結合成し新RPC不要)③共通関数を通す④迂回ゼロをgrep確認⑤検証(変更前と完全一致・2回目即・pageerror0・実データでしるし変化・画像を自分で見る)。禁止＝重い集計の直接呼び出し/独自キャッシュ発明/しるしにPII/計算式バージョン更新忘れ/共有カウンタ方式。対象＝対話的な結果表示・対象外＝ファイル出力とAIレポート(専用キャッシュ)。関連=[[2026-08-31-sqw-results-cache-readtime]]
- [2026-08-31-sqw-results-cache-readtime.md](Knowledge/2026-08-31-sqw-results-cache-readtime.md) - 結果の即表示キャッシュ：変更検知の「しるし」は共有カウンタ+1でなく**read-time算出**（各表にupdated_at＋BEFORE UPDATE touchトリガー＝更新中の自分の行だけ＋件数と更新日時の指紋RPC）。理由=version列+トリガー+1は高同時数で同一行の行ロックが直列化（実測150同時UPDATE=1.9s直列）＝保存パスに共有書き込みを足す危険。全INS/UPD/DEL拾う（追加=count/created_at・更新=updated_at・削除=count）＝漏れゼロ。キャッシュキー=計算式VER∷スコープ∷しるし＝無効化は別キーになるだけ（明示削除不要）＋24時間の安全網。教訓=変更検知はread-timeでcount+updated_atから算出すれば高同時数の書き込みパスに競合を足さず漏れゼロ。
- [2026-08-31-sqw-perf-region-and-results-cache.md](Knowledge/2026-08-31-sqw-perf-region-and-results-cache.md) - サーバーレス関数が異常に遅い（1往復~1.8s）根本原因=関数の実行リージョンとDBリージョンの地理的不一致。Vercel x-vercel-id `<入口エッジ>::<関数実行リージョン>::…` の2つ目で実行地を確認（関数デフォルト=iad1米国東部）。利用者もDBもアジアなのに関数だけ米国＝毎往復に太平洋横断。対策=vercel.jsonのregionsをDBと同拠点に（コード不変・追加費用なし）→運営画面19.9s→3.1s(6.5倍)。教訓=1往復が遅い時はまず関数リージョンとDBリージョンの一致を疑う（エッジが近くても関数が遠いと毎往復に遅延）。
- [2026-08-26-general-technical-findings.md](Knowledge/2026-08-26-general-technical-findings.md) - 一般技術Tips(2026-08-26)。①JSのString.replaceは最初の1個しか置換しない=同じプレースホルダが複数あると2個目以降が残る→replaceAll(またはg付き正規表現)(実例:メール本文の{currentCount}が3回中2回そのまま)②表がコンテナより広いと常に横スクロール(実例826pxの表を764pxの器に)→狭い幅では表でなくカードに切替・ブレークポイント見直し(768→1024px)。関連=[[build-pass-not-runtime-ok]]
- [jsonb-selection-backward-compat-new-axis.md](Knowledge/jsonb-selection-backward-compat-new-axis.md) - jsonbの設定オブジェクトにキーを追記するだけで軸/条件を1つ増やす＝DB変更(DDL)・移行なしの後方互換。要＝**既存レコードは新キーを持たない→読み取り側で「欠如なら既定値(旧来の意味)とみなす」1行で吸収**＝移行不要・既存結果不変。新キーは任意/欠如は既定に倒す/jsonbはオブジェクトそのまま渡す(JSON.stringify禁止)。前提＝検索集計をアプリ側でやる構造
- [resend-scheduled-email-30day-limit.md](Knowledge/resend-scheduled-email-30day-limit.md) - Resendの予約送信は**30日先まで**が上限（公式明記）・超過時の挙動は未文書化→防御的に「APIエラー前提」で扱う。「開始時に全通を一括予約」は全配信が30日内のときだけ成立・数ヶ月〜1年先は定期実行(cron)で拾う
- [ai-prompt-global-rule-and-bilingual-qa.md](Knowledge/ai-prompt-global-rule-and-bilingual-qa.md) - AIプロンプト2教訓。①全体ルール（書式・禁止・必須）は**後から足す別用途フィールドにも適用**される→用途が違うフィールドは明示的な例外指定が必要。②生成品質は**全言語で実物を読む**まで完了にしない（同じプロンプトでも言語で出方が変わる・1言語だけだと見落とす）
- [tool-use-schema-flattening.md](Knowledge/tool-use-schema-flattening.md) - Tool Useスキーマで「1項目だけのオブジェクト」を作らない。単一bodyセクションを{"body":…}のJSON文字列＋余分な}で二重エンコード誤生成しinvalid tool_use jsonで失敗→プレーン文字列にフラット化すると解消。文字列にできるものは文字列に。併記=Next.js/Vercelで作業ブランチpushはPreview・env無しでビルド失敗・本番反映はmainへff push
- [local-review-flow.md](Knowledge/local-review-flow.md) - ローカル確認の標準フロー（dev起動維持→URL1行提示→ユーザー確認→push）
- [build-pass-not-runtime-ok.md](Knowledge/build-pass-not-runtime-ok.md)
- [qr-quiet-zone-and-error-correction.md](Knowledge/qr-quiet-zone-and-error-correction.md) - 【Public可】QRが読めないときは、中身より先に**静穏帯（周りの白い余白）**と**誤り訂正レベル**を疑う。`react-qr-code` は **`viewBoxSize`=モジュール数＝余白ゼロ**で、**呼び出し側の padding がそのまま静穏帯**になる（規格は4モジュール以上）。**`level` 未指定の既定は `L`**（最弱）。実例＝padding 14px でも1モジュール4.55pxなら**3.08モジュールで不足**。★**「保存する画像だけ正しく、画面と印刷が不足」になりやすい**（自前canvasでは意識するがライブラリ任せでは忘れる）。直し方＝設定を1ファイルに集約／QRは共通部品を通す／**静穏帯pxは決め打ちにせず `Math.ceil(表示size/モジュール数*4)` で切り上げ**／枠線・影・角丸は静穏帯の外へ／誤り訂正を上げるとモジュールが細るので表示サイズも上げる／地は固定の白。検証＝**描画画素で実測**・**画面/印刷/コピー画像の3経路**・モジュール数で誤り訂正を確認（L=29×29→M=33×33）・**実デコード**（macOS Vision＝iPhoneカメラと同エンジン）・読み取り限界の比較。併記＝QRは必ず失敗する人が出るので**招待コード手入力の入口を1行案内する**。関連=[[2026-08-23-sqw-email-qr-findings]]
- [base-url-consolidation-and-proof.md](Knowledge/base-url-consolidation-and-proof.md) - 【Public可】ベースURLを1箇所に集約する型と、**「値が変わっていない」ことの証明**。散らばり方を危険度で分類＝ドメイン直書き★高／`env||直書き`中／**`window.location.origin` のみは低＝寄せない**（いま開いているホストに自動追従＝移行で直す必要がない）。★線引き＝**ドメイン直書きと env 直読みだけ寄せる**。関数は**2つに分ける**＝`serverBaseUrl()`（window を見ない）と `getBaseUrl()`（ブラウザは origin）。★**初回描画の初期値には必ず `serverBaseUrl()`**（`getBaseUrl()` だとハイドレーションのズレ）。★**寄せない例外は理由をコードに書く**（決済の戻り先は `env||リクエストのホスト` が正しい＝本番URL固定にすると切替期間中に別ホストへ飛ぶ）。★**証明＝env を外した状態で `git stash` により集約前後を採り diff**（全箇所の評価値／メール本文のURL／実画面のURL）。メールは**実送信せず**ビルダーの定数の決め方を実ファイルから読んで置換して比べる。**env を設定した状態でも1回見る**＝集約前は経路ごとにバラバラという食い違いこそ直したかったもの。★**Vercel の Secret 型 env は `vercel env pull` でも値が読めない**（空で降る）→ URL のような秘密でない設定値は **Config 型**にする。回り道＝`NEXT_PUBLIC_*` は**クライアントJSに焼き込まれる**のでデプロイ後に配信チャンクを grep すればビルド時の値が分かる。関連=[[vercel-deploy-tips]]
- [delete-design-orphan-keys-and-log.md](Knowledge/delete-design-orphan-keys-and-log.md) - 【Public可】「消す」機能を作るときの3つ。①★**送り手側に `{相手のトークン: 値}` の形で保存されるデータは、消した人の「宛先キー」が他人の中に残る（孤児キー）**＝削除処理に「同じ範囲の他メンバーからそのキーを取り除く」を組み込む。★**表示は変わらないことが多い**（集計が生きている行から数え直す作りなら誰の対象にもならない）＝だから気づかれず溜まる。掃除の目的は整合性であって表示の修正ではない→**掃除の前後で表示値が1件も変わらないことを実測して示す**と安全に適用できる。②**ログは削除より前に書く**（後では件数を数えられない）・**入口ごとに別のaction名**・**ログ失敗で削除を止めない**。③**確認画面に何が消えるかを実数で**（0件のときも書く／**実際に消えるものだけ**を並べる／取り返しのつかなさに比例した強さ）。④お金が動いたものは運営者の操作で消せないようにし**サーバー側で止める**・窓口を1つに決める・★**FKの `ON DELETE CASCADE` は「宙に浮く」でなく「一緒に消える」**＝completed があれば削除しないことが、そのまま会計証跡の守り方になる。関連=[[non-fatal-write-failure-and-column-mismatch]]
- [non-fatal-write-failure-and-column-mismatch.md](Knowledge/non-fatal-write-failure-and-column-mismatch.md) - **握り潰した失敗は画面にもログにも出ない**／DBに書く・読む列名は実DBと機械的に突合する。実例1（書き込み）=存在しない列を渡す upsert が毎回42703で失敗し**対象15件すべてが0行**なのに画面は正常で誰も気づかなかった（呼び出し側が console.error だけで return null を省略＝non-fatal）。★戻り値が Record<string,unknown> のため **tsc/build は両方PASS＝型チェックは列の実在を検知しない**。実例2（読み取り・同日）=`week, type` を select（実列 `week_number, schedule_type`）→ 42703 で**一覧表が一度も表示されていなかった**。`const { data } = await` で**error を分割代入で捨てており痕跡すら残らない**。★**共通点＝症状が「何も無い」**（0行／表が出ない）で、**壊れている状態と正常な空が区別できない**。突合は読み取りだけでできる（実列=select("*")のキー／存在確認=select("<列名>")の42703）。★直す前に同じ突合を他の書き込み・読み取りにも回して**範囲を確定**する。関連=[[select-column-mismatch-audit]]・[[build-pass-not-runtime-ok]]
- [select-column-mismatch-audit.md](Knowledge/select-column-mismatch-audit.md) - **読み取りの列名不一致も気づかれない**（書き込み側 non-fatal-write-failure と対）。症状が違う＝書き込みは「データが入らない」・読み取りは**「表が丸ごと出ない」**で空データと見分けがつかない。★tsc/build は列の実在を検知しない（select は文字列・行の型は Record<string,unknown>）。突合は読み取りだけで全件できる＝コードから `.from("X").select("...")` を正規表現で抜き、そのまま実DBへ .limit(1) で投げてエラーの有無を見る（データ不変）。**1ファイル全52件を突合し不一致は1件**と範囲を確定してから直した実績。チェックリスト5点（直す前に全件突合／error を捨てない／「0件・表が出ない」はまず列名を疑う／列名変更時は読む側を grep／実DBに投げるまで分からない）。
- [vercel-deploy-tips.md](Knowledge/vercel-deploy-tips.md) - 自動デプロイ不発時は空コミット／★Vercel CLI の一覧は commit sha を出さない＝どの commit が本番に載ったかは `vercel inspect --logs` の `Commit:` 行で照合する
- [supabase-rls-warning-false-positive.md](Knowledge/supabase-rls-warning-false-positive.md) - Supabase SQL Editor の「Potential issue detected（RLS）」は INSERT/UPDATE/列追加でも出る＝多くは誤検知。警告の有無でなく SQL の中身で判断する（`disable row level security`／`drop policy`／`create policy ... using (true)`／`grant ... to anon` が無ければ RLS は変わらない）。確かめ方＝実行前後で `pg_policies` と `pg_class.relrowsecurity` を比べ変化なしを示す
- [api-cost-management.md](Knowledge/api-cost-management.md)
- [nakama-quest-visibility-rules.md](Knowledge/nakama-quest-visibility-rules.md)
- [ai-report-3files-sync.md](Knowledge/ai-report-3files-sync.md)
- [4-level-verification-protocol.md](Knowledge/4-level-verification-protocol.md) - 4段階検証プロトコル
- [rsc-marshalling-violation-columns-with-render.md](Knowledge/rsc-marshalling-violation-columns-with-render.md) - RSCマーシャリング違反
- [question-text-replacement-i18n.md](Knowledge/question-text-replacement-i18n.md) - i18nプレースホルダー方式
- [legacy-data-compatibility-check.md](Knowledge/legacy-data-compatibility-check.md) - 既存レコード後方互換性
- [anthropic-api-cost-incident-april-22.md](Knowledge/anthropic-api-cost-incident-april-22.md) - APIコスト事故記録
- [reset-vs-no-session-language-precision.md](Knowledge/reset-vs-no-session-language-precision.md) - 「リセット済み」言葉の精度
- [claude-md-size-limit.md](Knowledge/claude-md-size-limit.md) - CLAUDE.md は約15万字が上限。超えると後半（未処理タスク・絶対ルール・最近の記録）が読まれない。エラーは出ない。運用＝直近2週間だけ残し古い記録は archive へ移動／15万字を超えない／判断の本体は Decisions に置き CLAUDE.md は索引にする
- [claude-code-permission-shortcut.md](Knowledge/claude-code-permission-shortcut.md) - Claude Code Permission Shortcut
- [claude-code-large-file-instruction.md](Knowledge/claude-code-large-file-instruction.md) - 大きな指示書の渡し方
- [claude-code-login-recovery.md](Knowledge/claude-code-login-recovery.md) - 認証エラー復旧
- [claude-ai-memory-features-setup.md](Knowledge/claude-ai-memory-features-setup.md) - メモリー機能セットアップ
- [project-vs-non-project-search-scope.md](Knowledge/project-vs-non-project-search-scope.md) - Project内/外の検索スコープ
- [instruction-delivery-method.md](Knowledge/instruction-delivery-method.md) - 指示書のサイズ別渡し方
- [claude-ai-project-instructions-management.md](Knowledge/claude-ai-project-instructions-management.md) - Project「手順」管理
- [resend-local-vs-prod-key.md](Knowledge/resend-local-vs-prod-key.md) - ローカルResendキーは本番と別物・メール実物確認は送信ゼロ捕捉で
- [ai-tool-use-double-encoding-defensive-parse.md](Knowledge/ai-tool-use-double-encoding-defensive-parse.md) - AI Tool Useが章をJSON文字列で返す二重エンコード→検証なし保存→表示クラッシュ。防御的パース＋全章型検証＋無効JSONは再生成のみ。併記:サーバー間fetchはCookie非転送で内部API認証403
- [stripe-price-change-procedure.md](Knowledge/stripe-price-change-procedure.md) - Stripe価格変更手順(Price金額は編集不可・新Price作成→デフォルト→旧アーカイブ→price_id差替→pricing.ts更新→実機で表示=請求一致確認)。表示だけ変えると不当表示
- [nakama-english-style-guide.md](Knowledge/nakama-english-style-guide.md) - なかまクエスト英語表記ガイド(ゲーム・Web共通正本)。King Leo/Magic Recipe/Warrior等/禁止語(Lion King・Sensei Conlan・古語・comrade)。型名The Sensei(CS)は別レイヤー
- [nakama-admin-i18n-inline-debt.md](Knowledge/nakama-admin-i18n-inline-debt.md) - なかまWHI Adminの言語方式はinline三項分岐(辞書方式でない技術的負債)。SQW Admin実装時に「SQWは辞書方式＋なかまAdminの辞書化」をセットで行う確定タスク。辞書キーは関数でなく文字列＋プレースホルダー
- [infra-before-production-launch.md](Knowledge/infra-before-production-launch.md) - 本番公開前に必須のインフラ確認（Vercel無料は非商用専用・上限超過で停止／Supabase無料は7日で自動停止・バックアップなし／公開予定が立ったら先に有料化）
- [overflow-x-hidden-breaks-sticky.md](Knowledge/overflow-x-hidden-breaks-sticky.md) - **`overflow-x: hidden` は `position: sticky` を無効化する**（片方の軸だけ hidden にすると もう片方の visible が auto に計算され、html/body 自身がスクロール枠になるため sticky が実質 static になる）。見分け方＝`getComputedStyle(document.body).overflowY` が auto/scroll。直し方＝①overflow-x を外す ②外せないならその要素だけ `position: fixed`（余白は決め打ちにせず ResizeObserver で実測）③`overflow-x: clip` はスクロール枠を作らないので sticky が生きる。★「別プロダクトで動くからこちらも動くはず」と考えない（同じCSSでも globals.css の overflow 指定の有無で結果が変わる）
- [dark-mode-fixed-background-contrast.md](Knowledge/dark-mode-fixed-background-contrast.md) - 背景固定（テーマトークン）のレポート系コンポーネントに dark:text-* を付けると、ダークモード端末で背景は反転せず文字だけ反転しコントラスト不足で読めなくなる。dark: を持たない固定文字色トークンを使う

### Decisions/ - 意思決定の記録（公開可能なもの）
- [third-party-ip-policy.md](Decisions/third-party-ip-policy.md)
- [i18n-strategy.md](Decisions/i18n-strategy.md)
- [engagement-survey-positioning.md](Decisions/engagement-survey-positioning.md)
- [2026-04-10-score-distribution-deferred.md](Decisions/2026-04-10-score-distribution-deferred.md) - スコア配分は最後に切替
- [2026-04-22-rate-limit-forced-landing.md](Decisions/2026-04-22-rate-limit-forced-landing.md) - APIラリー上限到達時は強制着地
- [2026-04-22-rally-cap-44-per-session.md](Decisions/2026-04-22-rally-cap-44-per-session.md) - ラリー上限44/セッション
- [2026-05-12-magic-link-postponed.md](Decisions/2026-05-12-magic-link-postponed.md) - Magic LinkはMVP後
- [2026-05-19-conversation-rules-v2.2.md](Decisions/2026-05-19-conversation-rules-v2.2.md) - 会話ルール v2.2 採用
- [2026-06-02-report-as-coaching-dialogue.md](Decisions/2026-06-02-report-as-coaching-dialogue.md) - レポートは診断でなく対話の出発点（抽象原則のみ・具体は非公開）

### Preferences/ - 会話ルール・役割定義
- [conversation-rules.md](Preferences/conversation-rules.md) - 会話ルール v2.2
- [role-definition.md](Preferences/role-definition.md) - 役割定義
- [document-versioning.md](Preferences/document-versioning.md) - 読み物・ドキュメントのバージョン管理ルール（改訂時に版番号付与・ファイル名にも入れる・系統別番号体系・対応関係を記録）

### ルール
- [AI-RULES.md](AI-RULES.md) - AIが知識ベースをどう扱うかのルール

---

## 使い方

### claude.ai（チャット）
新チャット開始時に以下のURLを冒頭で渡す：
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/INDEX.md
- https://raw.githubusercontent.com/Katsuaki-WHI/whi-knowledge-public/main/Knowledge/mistakes.md
