---
title: "タブUIをアクセシブルにする"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["frontend", "a11y", "アクセシビリティ", "ui"]
published: false
---

アクセシブルなタブUIを作ったので、実装時に調べたことやポイントをまとめます。

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
    <button　role="tab">Tab2</button>
    <button　role="tab">Tab3</button>
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
    <button　role="tab">Tab2</button>
    <button　role="tab">Tab3</button>
  </div>
  ...
<div>
```

上記の実装で、「Tab1」にフォーカスがあたった際に **「Tab1、選択中、タブ、1/3、hoge、 タブグループ」** と読み上げてくれます。
※ hogeが`aria-label`で指定したテキスト

#### `tab`に`aria-selected`をつける
どのタブが選択されているかを判定できるようにするために、`aria-selected`を付与します。
また、別のタブを選択したときに`aria-selected`の状態を切り替えます。

#### `tabpanel`に`hidden`をつける
表示されているタブパネル以外には`hidden`を付与し、非表示にします。

※`hidden`属性を付与しているときは、すでにアクセシビリティツリーから削除されているため`aria-hidden`は不要です。

### キーボードで操作できるようにする
WCAGの達成基準2.1.1で下記のように言われております。
> 達成基準 2.1.1 キーボード (レベル A): コンテンツのすべての機能は、個々のキーストロークに特定のタイミングを要することなく、キーボードインタフェースを通じて操作可能である。ただし、その根本的な機能が利用者の動作による終点だけではない軌跡に依存する入力を必要とする場合は除く。

この項目はレベルAなので、対応していきます。

タブUIでクリアするべきキーボードの操作は4つあるので一つづつ対応します。

#### `tab`のフォーカスを左右キーで移動させる
#### フォーカスが`tablist`の外から移動してくる場合、アクティブな`tab`に移動させる
#### フォーカスがアクティブな`tab`にある場合、紐づいている`tabpanel`に移動させる

#### おまけ： `tab`にフォーカスが当たっている時にDeleteキーを押したら削除
`tab`が削除可能な場合は、Deleteキーで選択されているタブを削除できるようにする必要があります。

### aria属性

## 実装のポイント

## まとめ
以上が、タブUIをアクセシブルにするためのポイントでした。
UIライブラリを使っていると、わからないような細かい対応もあり、とても楽しく実装できました。

また、何か間違いや足りない対応などがありましたら、お気軽にコメントしていただけると嬉しいです！
最後まで読んでいただきありがとうございました。

## 参考
- [ARIA: tab ロール | mdn web docs](https://developer.mozilla.org/ja/docs/Web/Accessibility/ARIA/Attributes/aria-hidden)
- [aria-hidden | mdn web docs](https://developer.mozilla.org/ja/docs/Web/Accessibility/ARIA/Attributes/aria-hidden)
- [Webアプリケーションアクセシビリティ ――今日から始める現場からの改善](https://gihyo.jp/book/2023/978-4-297-13366-5)
- [達成基準 2.1.1: キーボードを理解する](https://waic.jp/translations/WCAG21/Understanding/keyboard.html)
