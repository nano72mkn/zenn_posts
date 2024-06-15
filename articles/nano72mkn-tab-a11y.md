---
title: "タブUIをアクセシブルにする"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["frontend", "a11y", "アクセシビリティ", "ui"]
published: true
publication_name: "stafes_blog"
---

どうも、[nano72mkn](https://x.com/nano72mkn)です。
アクセシビリティを意識してタブUIを作ったので、実装時に調べたことやポイントをまとめます。

## タブUIについて

まず、初めにタブUIと言われて思い浮かべるのは、この形だと思います。
![タブUIの画像](</images/nano72mkn-tab-a11y/tab_ui.png>)

このUIは、２つのパーツに分けることができます。

１つ目は、「タブ」と呼ばれるパーツ
![タブパーツ](/images/nano72mkn-tab-a11y/tab.png)

２つ目は、「タブパネル」と呼ばれるパーツ
![タブパネル](</images/nano72mkn-tab-a11y/tab_panel.png>)

この２つのパーツをがっちゃんこして、タブUIは出来ています。

## タブUIをアクセシブルにする

roleとaria属性を付与してアクセシビリティ対応をする。

### roleを付与する

付与する必要があるものは下記の３つ

- `tab`
- `tablist`
- `tabpanel`

#### tabとtablist
タブには`tab`、複数のタブを囲っている要素には`tablist`のroleを付与する
![タブにはtab、タブを複数まとめたものにはtablistのroleをつける](</images/nano72mkn-tab-a11y/tab_role-1-2.png>)

```html
<div id="tab">
  <div role="tablist">
    <button role="tab">Tab1</button>
    <button role="tab">Tab2</button>
    <button role="tab">Tab3</button>
  </div>
  ...
<div>
```

#### tabpanel
タブパネルに`tabpanel`のroleをつける
![タブパネルにtabpanelのroleをつける](</images/nano72mkn-tab-a11y/tab_role-3.png>)
```html
<div id="tab">
  ...
  <div role="tabpanel">Tab Panel 1</div>
  <div role="tabpanel">Tab Panel 2</div>
  <div role="tabpanel">Tab Panel 3</div>
</div>
```

### 支援技術に対応する
何のタブなのか、タブの状態はどうなっているかを支援技術に伝えるために、aria属性を付与します

#### `tablist`に`aria-label`をつける
タブにフォーカスした際に何のタブグループなのかを知らせるための`aria-label`を設定します。

```html
<div id="tab">
  <div role="tablist" aria-label="hoge">
    <button role="tab">Tab1</button>
    <button role="tab">Tab2</button>
    <button role="tab">Tab3</button>
  </div>
  ...
<div>
```

上記の実装で、「Tab1」にフォーカスがあたった際に **「Tab1、選択中、タブ、1/3、hoge、 タブグループ」** と読み上げてくれます。
※ 「hoge」が`aria-label`で指定したテキスト

#### `tab`に`aria-selected`をつける
どのタブが選択されているかを判定できるようにするために、`aria-selected`を付与します。
また、別のタブを選択したときに`aria-selected`の状態を切り替えます。

```html
<div id="tab">
  <div role="tablist" aria-label="hoge">
    <button role="tab" aria-selected="true">Tab1</button>
    <button role="tab" aria-selected="false">Tab2</button>
    <button role="tab" aria-selected="false">Tab3</button>
  </div>
  ...
<div>
```

#### `tab`と`tabpanel`を紐づける
紐づける為にやることが２つあります

1. `tab`に`aria-controls`を指定
2. `tabpanel`に`aria-labelledby`を指定

```html
<div id="tab">
  <div role="tablist" aria-label="hoge">
    <button
      role="tab"
      id="tab-1"
      aria-selected="true"
      aria-controls="tabpanel-1">
      Tab1
    </button>
    ...
  </div>
  <div
    role="tabpanel"
    id="tabpanel-1"
    aria-labelledby="tab-1">
    Tab Panel 1
  </div>
  ...
<div>
```

`aria-labelledby`の影響で、タブパネルにフォーカスを当てたときにも **「本文、Tab1、タブパネル」** とタブのラベルも読み上げてくれます。

#### `tabpanel`に`display: none;`をつける
表示されているタブパネル以外には`display: none;`を付与し非表示にします。

```html
<div id="tab">
  ...
  <div role="tabpanel">Tab Panel 1</div>
  <div role="tabpanel" style="display: none;">Tab Panel 2</div>
  <div role="tabpanel" style="display: none;">Tab Panel 3</div>
</div>
```

※`display: none;`を付与しているときは、すでにアクセシビリティツリーから削除されているため`aria-hidden`は不要です。

### キーボードで操作できるようにする
WCAGの達成基準2.1.1で下記のように言われております。
> 達成基準 2.1.1 キーボード (レベル A): コンテンツのすべての機能は、個々のキーストロークに特定のタイミングを要することなく、キーボードインタフェースを通じて操作可能である。ただし、その根本的な機能が利用者の動作による終点だけではない軌跡に依存する入力を必要とする場合は除く。

この項目はレベルA（最低基準）なので、対応していきます。
タブUIでクリアするべきキーボードの操作は4つあり、JSで実装します。

#### `tab`のフォーカスを左右キーで移動させる
左右の矢印キーを押した際に、タブのフォーカスを移動するようにします。

「Tab１」にフォーカスがあった場合に左キーを押したときには「Tab3」へ
「Tab3」にフォーカスがあった場合に右キーを押したときには「Tab1」へフォーカスが移るようにします。

![左右キーでフォーカスを移動している様子](/images/nano72mkn-tab-a11y/focus_move.gif)

vue.jsであれば、`@keydown.right`と`@keydown.left`を使うとサクッとできます。

#### フォーカスが`tablist`の外から移動してくる場合、アクティブな`tab`に移動させる
これを実現するためには、`tabindex`を調整する必要があります。

`tabindex`は、`-1`が指定されていると順次ナビゲーションでは到達できなくなるので、その性質を使います。

```html
<div id="tab">
  <div role="tablist" aria-label="hoge">
    <button role="tab" aria-selected="false" tabindex="-1">Tab1</button>
    <button role="tab" aria-selected="true" tabindex="0">Tab2</button>
    <button role="tab" aria-selected="false" tabindex="-1">Tab3</button>
  </div>
  ...
<div>
```

アクティブなタブは`tabindex`を0にし、それ以外は`tabindex`を-1にしてフォーカスが当たらないようにします。

![フォーカスが外の要素からタブに移動する際に、アクティブなタブに移動する様子](/images/nano72mkn-tab-a11y/focus_move_2.gif)

外の要素から「Tab1」をスキップし、「Tab2」にフォーカスが移動することを確認できます。

#### フォーカスがアクティブな`tab`にある場合、紐づいている`tabpanel`に移動させる
`tabindex="0"`を付与するだけで対応できます。

```html
<div id="tab">
  ...
  <div role="tabpanel" tabindex="0">Tab Panel 1</div>
  <div role="tabpanel" tabindex="0">Tab Panel 2</div>
  <div role="tabpanel" tabindex="0">Tab Panel 3</div>
</div>
```

![フォーカスがアクティブなタブから対象のタブパネルに移動する様子](/images/nano72mkn-tab-a11y/focus_move_3.gif)

フォーカスが「Tab2」から「Tab3」ではなく、直接紐づいているタブへ移動しているのを確認できます。

#### おまけ： `tab`にフォーカスが当たっている時にDeleteキーを押したら削除
`tab`が削除可能な場合は、Deleteキーで選択されているタブを削除できるようにする必要があります。

## まとめ
上記対応を行うと、下記codepenのようになります。
@[codepen](https://codepen.io/miyahirashota/pen/WNBEzRq)

以上が、タブUIをアクセシブルにするためのポイントでした。
UIライブラリを使っていると、わからないような細かい対応もあり、とても楽しく実装できました。

また、何か間違いや足りない対応などがありましたら、お気軽にコメントしていただけると嬉しいです！
最後まで読んでいただきありがとうございました。

## 参考
サクッと説明しちゃっているので、詳しく知りたい方は、下記参考記事を読んでみてください！
- [ARIA: tab ロール | mdn web docs](https://developer.mozilla.org/ja/docs/Web/Accessibility/ARIA/Roles/tab_role)
- [aria-hidden | mdn web docs](https://developer.mozilla.org/ja/docs/Web/Accessibility/ARIA/Attributes/aria-hidden)
- [aria-controls | mdn web docs](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-controls)
- [tabindex | mdn web docs](https://developer.mozilla.org/ja/docs/Web/HTML/Global_attributes/tabindex)
- [Webアプリケーションアクセシビリティ ――今日から始める現場からの改善](https://gihyo.jp/book/2023/978-4-297-13366-5)
- [達成基準 2.1.1: キーボードを理解する](https://waic.jp/translations/WCAG21/Understanding/keyboard.html)
