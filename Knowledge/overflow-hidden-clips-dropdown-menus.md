---
date: 2026-09-10
tags: [css, overflow, dropdown, portal, ui, nakama]
project: nakama-survey
related: [[overflow-x-hidden-breaks-sticky]] [[mistakes]]
---

# `overflow: hidden` の枠の中に置いたドロップダウンは切られる（ポータル＋上下反転で直す）

角丸のカードを作るためによく付ける `rounded-2xl overflow-hidden` は、
**その中の `position: absolute` なメニューも一緒に切り落とす**。
症状は「一覧の下の方の行だけメニューが見えず操作できない」で、
上の方の行では普通に開くため**バグと気づかれにくい**。

## 見分け方
メニューを開いた状態で、メニューから上に祖先をたどり
`overflow` が `visible` 以外の要素があるかを見る。

```js
let e = menu.parentElement, clipping = [];
while (e && e !== document.body) {
  const cs = getComputedStyle(e);
  if (cs.overflow !== 'visible' || cs.overflowX !== 'visible' || cs.overflowY !== 'visible')
    clipping.push(e.className || e.tagName);
  e = e.parentElement;
}
```
1つでも挙がれば、そこで切られている。

## 直し方＝2つセットで入れる（片方だけでは足りない）

**(a) `createPortal` で `document.body` に描く**
親の `overflow` と stacking context の影響を受けなくなる。

**(b) 開く方向を自動で反転させる＋最後にビューポート内へクランプする**
(a) だけだと、画面最下部の行では今度は**ビューポートの外**にはみ出す。
ボタンの矩形を実測し、下に入りきらなければ上向きに開く。

```js
const b = btn.getBoundingClientRect();
const mh = menu.offsetHeight, GAP = 8, MARGIN = 8;
const below = window.innerHeight - b.bottom, above = b.top;
let top = b.bottom + GAP;
if (below < mh + GAP + MARGIN && above > below) top = b.top - GAP - mh;
top = Math.max(MARGIN, Math.min(top, window.innerHeight - mh - MARGIN));
```

## あわせて要る細かいこと
- **位置が決まるまで `visibility: hidden`**（測るために描画は必要だが、
  一瞬 (0,0) に出るのを防ぐ）。
- **スクロールは `capture: true` で拾う**＝祖先のどのスクロール枠が動いても
  ボタンに追従する（`position: fixed` は自分では追従しない）。
- **項目が多いときのために `maxHeight` + `overflowY: auto`**。
- **z-index はモーダルより下**に置く（メニューは項目を押した時点で閉じるので
  重ならないが、万一重なってもモーダルが前に来るようにする）。
- 外側クリック判定は**ボタンとメニュー本体の2つ**を見る
  （ポータルにするとメニューは同じ DOM ツリーに居ないため、
  従来の「ラッパー1つに `contains`」では閉じてしまう）。

## 検証は「見えるか」でなく「押せるか」で判定する
要素の存在・boundingBox だけでは、他の要素に覆われていても PASS になる。
`document.elementFromPoint(cx, cy)` が本当にその項目を返すか（ヒットテスト）
まで確認する。加えて **一覧の先頭・中間・最終の3位置**、および
**ボタンを画面下端60pxに置いた最悪ケース**を通す。

## 出典
なかまサーベイ チーム運営ページの ⋮ 操作メニュー（2026-09-10・commit `41c9062`）。
メンバー一覧の器が `rounded-2xl overflow-hidden` で、一番下のメンバーの
メニューが**まったく見えず削除等が一切できなかった**。
修正後、実測で `parentIsBody: true` / 切る祖先0 / 器（`overflow: hidden`）の外
であることを確認した。

★同じ `overflow` が原因でも症状が違う姉妹ケース＝[[overflow-x-hidden-breaks-sticky]]
（`overflow-x: hidden` が `position: sticky` を無効化する）。
**「レイアウトが変」と感じたら、まず祖先の `overflow` を疑う。**
