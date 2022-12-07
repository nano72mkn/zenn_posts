---
title: "WebアニメーションはRiveが便利！"
emoji: "🎥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rive","react"]
published: true
---

みなさんは、Webアニメーション使っていますか？
おそらく`Lottie`を使っている人が多いかと思います。

ただ、`Lottie`には大きな問題がありますね？

そうです。
AdobeのAfter Effects（AE）がないとアニメーションが作れないという問題です。
たくさんの人たちが嘆き苦しみながらAEを使うためにAdobeに課金している事だと思います。

そんな人々を救うため、
最近ではAEを使わなくてもアニメーションを作れるようにと`Lottie Lab`というサービスが開発されていたりします。
https://lottielab.com/

しかし、まだまだ公開には至っていない状態です。（2022/12/07 現在）

# そこで、Rive登場

https://rive.app/

AEで行っていたアニメーションの作成から、書き出しまで全てWeb上で完結できるサービスです。

今回は、アニメーションの設定から

## 早速使ってみる

### 新規ファイルを作成

画面右上にある`New File`を選択後、
`Black artboard`になっていることを確認し`Create`をクリックすると
このようなエディタが表示されたと思います！
![エディタ画面](/images/shota1995m-rive-app/editor.png)

### SVG画像を持ってくる

unDrawからSVG画像を使わせて頂きます！
https://undraw.co/illustrations

適当なものをDLしたら、
エディタ上にドラッグ&ドロップしましょう！

そしたら、assetsの方に入っていると思うので
またしても、ドラック&ドロップしてアートボード内に持っていきます！
![エディタ画面](/images/shota1995m-rive-app/dnd.png)

各pathを別々に操作することが可能です！
![エディタ画面](/images/shota1995m-rive-app/1.png)

## アニメーションをつける

早速この犬の画像を動かしていきましょう！

右上から`Animateモード`に切り替え、
左下にある`Timeline1`を選択して、画面下部にTimelineを表示します
![アニメーション](/images/shota1995m-rive-app/animate.png)


AEと同じようにキーフレームを打ってみると....

しっぽが動きました！！
![しっぽアニメーション](/images/shota1995m-rive-app/tail_animate.png)

この調子で動かせるところ全てにキーフレームを打つと...
![完成アニメーション](/images/shota1995m-rive-app/all_animate.png)

動かすために作られたSVGではないので、いろいろおかしいですがそれっぽくなったと思います！

アニメーションの作り方に関しては、公式からチュートリアル、ドキュメントがあるので見てみてください！
（みてるだけでワクワクする）

https://rive.app/learn-rive

### rivファイルを取得する

`Download`→`For newest runtime`をクリックしてrivファイルをダウンロードしましょう！

![Download→For newest runtime](/images/shota1995m-rive-app/download.png)

ちなみに、初期は`new file`になっているので
ファイル名変更しとくと`[file name].riv`がDLできます

## Reactで動かしてみる

### ReactアプリをCreate

まずは、ちょちょいとReactアプリを生成します

```shell
npx create-react-app rive-dog-animation --template typescript

...
Happy hacking!
```

### @rive-app/react-canvasをinstall

Riveのアニメーションを再生するのに必要なプラグインが公式から出ているのでインストールします

https://github.com/rive-app/rive-react

```shell
npm i --save @rive-app/react-canvas
```

### rivファイルをpublicへ
先ほどDLしたrivファイルをpublicへつっこみましょう！

### hooksを使って描画する

とりあえず、App.tsxを書き換えます。

```typescript:src/App.tsx
import { useRive } from "@rive-app/react-canvas";

function App() {
  const { RiveComponent } = useRive({
    src: "dog.riv",
    autoplay: true,
  });

  return (
    <div style={{ width: "500px", height: "500px" }}>
      <RiveComponent />
    </div>
  );
}
```

divに`width`と`height`を入れているのは、RiveComponentが親要素にfitするように描画されるため指定しています。
fit方法も、`Cover`や`Fill`などいろいろ選べます！

### 動作確認

`npm start`をして`http://localhost:3000/`を見てみると....

元気よく動いていますね！

![Webで動く様子](/images/shota1995m-rive-app/web_animation.png)


# まとめ
Rive一本だけでwebで動作するところまでいけるのは本当にすごいですよね！

昨日見つけてテンション上がりまくり太郎でした！
https://twitter.com/shota1995m/status/1600138751690231809?s=20&t=sqGA-dUARKkHMes07WVexA

別の作業しながらでしたが、
見つけでドキュメントを読んでからReactで動かすまで約1時間で出来、Lottieと比べても学習コストはだいぶ低いと思います！
みなさんも是非、遊んでみてください！
