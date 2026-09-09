---
date: 2026-09-08
tags: [css, sticky, layout, admin, nakama, sqw]
project: なかまクエスト（なかまサーベイ）
related: [[build-pass-not-runtime-ok]] [[sqw-ui-design-rules]]
---

# `overflow-x: hidden` が `position: sticky` を無効化する（同じCSSなのに片方だけ壊れる）

## 症状
Admin の左サイドバー（濃色）とトップバー（パンくず・ログアウト）が、
**長いページをスクロールすると画面外へ流れ去る**。サイドバーの濃色が「途中で切れて」見える。
CSSは `position: sticky; top: 0; height: 100vh` と正しく書かれているのに効いていない。

## 原因
`globals.css` の

```css
html { overflow-x: hidden; }
body { overflow-x: hidden; max-width: 100vw; }
```

**CSS仕様上、片方の軸だけ `hidden` にすると、もう片方の `visible` は `auto` に計算される。**
つまり `overflow-y: auto` になり、`body` 自身がスクロール枠（スクロールコンテナ）になる。
`sticky` は「最も近いスクロール枠」を基準に貼り付くが、その `body` は高さが内容ぶんあるだけで
**それ自体はスクロールしない**ため、貼り付く機会が来ない＝実質 `static` と同じ挙動になる。

実測（Chrome・DevTools不要・`getComputedStyle` で確認できる）:
```
bodyOverflowX: "hidden"  →  bodyOverflowY: "auto"   ← 書いていないのに auto になる
sidePosition: "sticky"   scrollY 1716 のとき sideTop -1716（＝画面外へ流出）
```

## 見分け方（1分でできる）
```js
getComputedStyle(document.body).overflowY   // "auto"/"scroll" なら sticky は効かない
getComputedStyle(document.documentElement).overflowY
```
`sticky` が効かないときは、まず祖先（html/body を含む）の `overflow` を疑う。
`transform` / `filter` / `contain` を持つ祖先も別要因になるが、**実務でいちばん多いのはこれ**。

## 直し方（状況別）

| 状況 | 対処 |
|---|---|
| `overflow-x: hidden` を外せる | 外すのが最短。ただし**なぜ入れたか**を先に確認する |
| 外せない（横スクロール防止に必要） | **その要素だけ `position: fixed` に変える**。fixed は祖先の overflow に影響されない |
| 対象範囲を限定できる | `overflow-x: clip` に替える（`clip` はスクロール枠を作らないので sticky が生きる。ただし `:has()` 等でスコープを絞る必要があり、対応ブラウザも新しめ） |

### `fixed` にするときの注意
1. **流れから外れるぶんの余白を自分で空ける**（サイドバー＝`margin-left`／トップバー＝`padding-top`）。
2. ★**その余白を決め打ちにしない**。高さが可変な要素（パンくず等）は狭い幅で折り返して伸びる。
   実測例＝768px以上 55px／390px 55〜79px／320px 61〜133px（言語・画面名・幅で変わる）。
   `ResizeObserver` で実測して CSS変数に入れ、余白と必ず一致させる。
3. **「内側をスクロールコンテナにする」案は、外に置かれた要素（フッター等）があると二重スクロールバーになる**。
   採る前に `root.contains(footer)` で位置関係を確かめる。

## いちばんの教訓
**同じコンポーネントCSSでも、プロダクトのグローバルCSS次第で挙動が変わる。**
なかまと SQW はサイドバーのCSSが**一字一句同じ**なのに、なかまだけ壊れていた。
違いは `globals.css` の `overflow` 指定の有無だけ。
「向こうで動いているから、こちらも動くはず」と考えない。**壊れている側で実測して原因を特定する。**

そして**グローバルの `overflow-x: hidden` は安易に外さない**（回答者向け画面の横スクロール0を
守っていることが多い）。直すのは**壊れている画面のコンポーネント側**に閉じる。

## 出典
なかまサーベイ WHI Admin（2026-09-08 本番反映・commit `dd014ca` / `ae9d8af`）。
