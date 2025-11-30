---
title: "フロントエンドカンファレンス関西2025 参加レポート"
emoji: "🐙"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["frontend"]
published: true
---

2025/11/30に[マイドームおおさか](https://www.mydome.jp/)で開催された[フロントエンドカンファレンス2025 関西](https://frontend-conf.osaka.jp/)に行ってきました！

![描画した角丸四角形](/images/nano72mkn-fec-kansai-2025/9591.png =200x)

前に登壇した時に参加レポートに書いてもらって嬉しかったので、視聴しながらメモしてたトークについて書いてみました！

## フロントエンドカンファレンス2025 関西

https://frontend-conf.osaka.jp/

https://fortee.jp/fec-kansai-2025/timetable

## 面白かったトーク

### 「え？！それ今ではHTMLだけでできるの！？」驚きの進化を遂げたモダンHTML
[Amemiya Riya](https://x.com/Riya31377928)さん
@[speakerdeck](1b04dbd741a84942877a88de2d913b7f)

Experimentalですが、`blocking`が気になりましたね
フォントを読み込むまで、レンダリングをブロックしたい時に使えるようですね
https://developer.mozilla.org/ja/docs/Web/API/HTMLScriptElement/blocking

あとは、`enterkeyhint`ですね！
スマホアプリではEnterをNextやSearchに変えるのは意識していましたが、webでは意識していなかったなと反省しました...

### 堅牢なフロントエンドテスト基盤を構築するために行った取り組み
[Shogo Fukami](https://x.com/react_nextjs)さん
@[speakerdeck](c2e02ab0b76d4537a3385fe331dc16c0)

最近アプリのE2Eテストを書くことがあったので、テストガイドラインは作って置きたいなと思いました。
また、BDD（behavior driven development）とGWT（Given-When-Then）、カスタムサブエージェントを使用したAIでテストを書くといった実務でも使えそうな知識が得られて満足しましたw
明日、会社の人に相談して取り入れてみようと思います！

### 心地よいアニメーションのつくりかた
[wattanx](https://x.com/pontaxx)さん
https://talks.wattanx.dev/2025/fec-kansai/

適切なイージングについてサンプルを入れながら解説してくれたのでわかりやすかったです！

トーク内で紹介されていた下記の本は読んでみようと思います！（いい本を教えてもらった
- [マイクロインタラクション ―UI/UXデザインの神が宿る細部](https://amzn.asia/d/atCaLGE)
- [オンスクリーン タイポグラフィ 事例と論説から考えるウェブの文字表現](https://amzn.asia/d/6p3EMDZ)

### その複雑な型、いつ使うんですか？OSSから学ぶ、高度な型定義の活用方法
[Shimmy](@naoya7076)さん
@[speakerdeck](843476cb77e64e4bb2d8db36114d81e1)

OSSでは結構複雑に型が入り組んでいて途中までたどりはするけど、最後まで辿れなかったりするんですよね...（諦め
なので、逃げないでまとめてくれてありがとうございます！
解説もありわかりやすかったです

### BCD Designに学ぶ、package by featureのための一貫したfeatureの切り方
[airRnot](https://x.com/airRnot1106)さん
@[speakerdeck](37036944e6b84c868a239cbdb9933253)

BCD Designは過去にさらっとみたことがありましたが、完全に忘れていました。
「何の何をどうするUI」という考え方、わかりやすくていいなと思いました！

### TypeScriptがブラウザで実行されるまでの流れを5分で伝えたい
[ジン](https://x.com/Jin_pro_01)さん
@[speakerdeck](d77c55f4a76746d8b0d8908b89d01b8d)

内容自体は復讐みたいな感じでしたが、ジンさんの話し方やまとめ方がとてもわかりやすかったなと感じました！

## 最後に
懇親会で色々話を聞きたかったんですけど、チケットを買い忘れて売り切れてしまい...参加できず残念でした...
みなさん、参加を決めたら早めに懇親会付きチケット買いましょうw

では、またどこかのカンファレンスに参加したらまたレポート出します！
LTしてくれたかた、カンファレンス運営の皆様お疲れ様でした！ありがとうございました！
