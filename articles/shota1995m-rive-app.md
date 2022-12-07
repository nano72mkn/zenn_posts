---
title: "WebアニメーションはRiveが便利！"
emoji: "🎥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rive", "react"]
published: true
---

みなさんは、Web アニメーション使っていますか？
おそらく`Lottie`を使っている人が多いかと思います。

ただ、`Lottie`には大きな問題がありますね？

そうです。
Adobe の After Effects（AE）がないとアニメーションが作れないという問題です。
たくさんの人たちが嘆き苦しみながら AE を使うために Adobe に課金している事だと思います。

そんな人々を救うため、
最近では AE を使わなくてもアニメーションを作れるようにと`Lottie Lab`というサービスが開発されていたりします。
https://lottielab.com/

しかし、まだまだ公開には至っていない状態です。（2022/12/07 現在）

# そこで、Rive 登場

https://rive.app/

AE で行っていたアニメーションの作成から、書き出しまで全て Web 上で完結できるサービスです。

今回は、アニメーションの設定から React で動かすまでをやってみたいと思います！

一応、Github 置いておきます
https://github.com/shota1995m/example-rive-animation

## 早速使ってみる

### 新規ファイルを作成

画面右上にある`New File`を選択後、
`Black artboard`になっていることを確認し`Create`をクリックすると
このようなエディタが表示されたと思います！
![エディタ画面](/images/shota1995m-rive-app/editor.png)

### SVG 画像を持ってくる

unDraw から SVG 画像を使わせて頂きます！
https://undraw.co/illustrations

適当なものを DL したら、
エディタ上にドラッグ&ドロップしましょう！

そしたら、assets の方に入っていると思うので
またしても、ドラック&ドロップしてアートボード内に持っていきます！
![エディタ画面](/images/shota1995m-rive-app/dnd.png)

各 path を別々に操作することが可能です！
![エディタ画面](/images/shota1995m-rive-app/1.png)

## アニメーションをつける

早速この犬の画像を動かしていきましょう！

右上から`Animateモード`に切り替え、
左下にある`Timeline1`を選択して、画面下部に Timeline を表示します
![アニメーション](/images/shota1995m-rive-app/animate.png)

AE と同じようにキーフレームを打ってみると....

しっぽが動きました！！
![しっぽアニメーション](https://user-images.githubusercontent.com/10015803/206233505-4d41b8f3-34ae-4865-8dd1-c3ec45454f00.png)

アニメーションの作り方に関しては、公式からチュートリアル、ドキュメントがあるので見てみてください！
（みてるだけでワクワクする）

https://rive.app/learn-rive

### riv ファイルを取得する

`Download`→`For newest runtime`をクリックして riv ファイルをダウンロードしましょう！

![Download→For newest runtime](/images/shota1995m-rive-app/download.png)

ちなみに、初期は`new file`になっているので
ファイル名変更しとくと`[file name].riv`が DL できます

## React で動かしてみる

### React アプリを Create

まずは、ちょちょいと React アプリを生成します

```shell
npx create-react-app rive-dog-animation --template typescript

...
Happy hacking!
```

### @rive-app/react-canvas を install

Rive のアニメーションを再生するのに必要なプラグインが公式から出ているのでインストールします

https://github.com/rive-app/rive-react

```shell
npm i --save @rive-app/react-canvas
```

### riv ファイルを public へ

先ほど DL した riv ファイルを public へつっこみましょう！

### hooks を使って描画する

とりあえず、App.tsx を書き換えます。

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

div に`width`と`height`を入れているのは、RiveComponent が親要素に fit するように描画されるため指定しています。
fit 方法も、`Cover`や`Fill`などいろいろ選べます！

### 動作確認

`npm start`をして`http://localhost:3000/`を見てみると....

元気よく動いていますね！（可動部分ふやしてみました！

@[codesandbox](https://codesandbox.io/embed/ecstatic-mahavira-7p3hfi?fontsize=14&hidenavigation=1&theme=dark)

# まとめ

Rive 一本だけで web で動作するところまでいけるのは本当にすごいですよね！

昨日見つけてテンション上がりまくり太郎でした！
https://twitter.com/shota1995m/status/1600138751690231809?s=20&t=sqGA-dUARKkHMes07WVexA

別の作業しながらでしたが、
見つけでドキュメントを読んでから React で動かすまで約 1 時間で出来、Lottie と比べても学習コストはだいぶ低いと思います！
みなさんも是非、遊んでみてください！
