---
title: "React Native Skiaを使って動くチェックボックス作ってみた"
emoji: "✅"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["reactnative", "skia", "ui"]
published: true
published_at: 2024-06-24 09:00
---

どうも、[nano72mkn](https://x.com/nano72mkn)です。
今回はReact Native Skiaを使って「動く！チェックボックス」を作ってみました!
とりあえず、この方法で出来たぞって感じなので間違えてるかもしれません！

## Skiaってなに？
SkiaとはGoogleが開発しているオープンソースの2Dグラフィックライブラリで、
Google Chrome、ChromeOS、Android、Flutterなどで使われているっぽいですね。

https://skia.org/

ちなみに、Flutterのドキュメントを見ると今後Skiaの後継のImpellerになっていくっぽいですね
https://docs.flutter.dev/perf/impeller

## 本題：チェックボックスを作っていく

完成品はこちら↓
![チェックボックスの完成品](/images/nano72mkn-rn-skia-checkbox/check-animation-preview.gif)
このチェックボックスを作っていきます

### 四角形を作る

#### Rectangleを指定する
```typescript
const x = 0;
const y = 0;
const width = 100;
const height = 100;

const rectangle = rect(x, y, width, height);
```

#### SkPathを生成し、`rectangle`を追加
`Skia.Path.Make`で`SkPath`を生成し、`SkPath`の引数に`Rectangle`を追加
```typescript
const rectPath = Skia.Path.Make();
rectPath.addRect(rectangle);
```

#### SkPathを描画してみる
作った`SkPath`はPathコンポーネントを使って描画します。
```tsx
<Path path={rectPath} color="#ffffff" />
```

画面上に表示する際は、Canvasコンポーネントで囲ってあげる必要があります。
`style`には`width`と`height`を指定して、描画エリアを確保する必要があります。
```tsx
<Canvas style={{ width, height }}>
  <Path path={rectPath} color="#ffffff" />
</Canvas>
```

#### 描画した四角形
![描画した四角形](/images/nano72mkn-rn-skia-checkbox/rect-preview.jpeg =200x)

::::details コードの全体
```tsx
import { Skia, rect, Path, Canvas } from '@shopify/react-native-skia';

const x = 0;
const y = 0;
const width = 100;
const height = 100;
const rectangle = rect(x, y, width, height);

const rectPath = Skia.Path.Make();
rectPath.addRect(rectangle);

export default function Index() {
  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <Canvas style={{ width, height }}>
        <Path path={rectPath} color="#ffffff" />
      </Canvas>
    </View>
  );
}
```
::::

### 作った四角を角丸にする
先ほどのスクショを見てもらったらわかる通り、めっちゃ角が尖っているので丸くしてあげます。

#### 作った`rectangle`に角丸を指定する
`rrect`に先ほど作った`rectangle`を食わせて、角丸を指定します。
```diff typescript
+const rx = 10;
+const ry = 10;

const rectangle = rect(x, y, width, height);
+const roundedRect = rrect(rectangle, rx, ry);
```

#### SkPathを微修正
`addRect`から`addRRect`に変更して`roundedRect`を渡します
```diff typescript
const rectPath = Skia.Path.Make();
-rectPath.addRect(rectangle);
+rectPath.addRRect(roundedRect);
```

#### 描画した角丸四角形
![描画した角丸四角形](/images/nano72mkn-rn-skia-checkbox/rrect-preview.jpeg =200x)

::::details コードの全体
```tsx
import { Skia, rect, Path, Canvas, rrect } from '@shopify/react-native-skia';

const x = 0;
const y = 0;
const width = 100;
const height = 100;
const rx = 10;
const ry = 10;

const rectangle = rect(x, y, width, height);
const roundedRect = rrect(rectangle, rx, ry);

const rectPath = Skia.Path.Make();
rectPath.addRRect(roundedRect);

export default function Index() {
  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <Canvas style={{ width, height }}>
        <Path path={rectPath} color="#ffffff" />
      </Canvas>
    </View>
  );
}
```
::::

### 四角形の枠線を作る
`style`を`stroke`に指定
`strokeWidth`で枠線の太さを指定できます。
```tsx
const strokeWidth = 5;

...
<Path path={rectPath} color="#3ea8ff" style="stroke" strokeWidth={strokeWidth} />
```

このまま描画すると、スクショのように不恰好になります
![枠線のプレビュー](/images/nano72mkn-rn-skia-checkbox/rrect-path-preview.png =200x)

これは、`Canvas`のサイズと`rectPath`の描画位置が線の太さを考慮されていない為、線の半分がはみ出してしまい不恰好になりました。

#### 線の太さを考慮して調整する

##### 四角形の位置を調整する
`x`と`y`は、はみ出している線の太さの半分ずらしてあげます
```diff tsx
+const strokeWidth = 5;
-const x = 0;
+const x = 0 + strokeWidth / 2;
-const y = 0;
+const y = 0 + strokeWidth / 2;
```

##### Canvasのサイズを調整する
上下左右に線の太さの半分がはみ出ているので、足して線の太さ分大きくしてあげます。
```diff tsx
+const canvasWidth = width + strokeWidth * 2;
+const canvasHeight = height + strokeWidth * 2;
...
-      <Canvas style={{ width, height }}>
+      <Canvas style={{ width: canvasWidth, height: canvasHeight }}>

```

##### 描画してみる
位置を調整した後は、きれいに表示されています！
![位置を調整した後の描画](/images/nano72mkn-rn-skia-checkbox/canvas-size-preview.png =200x)
::::details コードの全体
```diff tsx
import { Skia, rect, Path, Canvas, rrect } from '@shopify/react-native-skia';

+const strokeWidth = 5;
-const x = 0;
+const x = 0 + strokeWidth / 2;
-const y = 0;
+const y = 0 + strokeWidth / 2;
const width = 100;
const height = 100;
...
+const canvasWidth = width + strokeWidth * 2;
+const canvasHeight = height + strokeWidth * 2;

...
export default function Index() {
  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
-      <Canvas style={{ width, height }}>
+      <Canvas style={{ width: canvasWidth, height: canvasHeight }}>
        <Path path={rectPath} color="#ffffff" />
        <Path path={rectPath} color="#3ea8ff" style="stroke" strokeWidth={strokeWidth} />
      </Canvas>
    </View>
  );
}
```
::::

### チェックマークを作る
一番楽しいところである、チェックマークを作っていきます。

#### SkPathを作る
今回は、`SkPath`の`lineTo`を使って描画していきます。
前回の`rectangle`と同じように、`Skia.Path.Make`します。
```ts
const checkPath = Skia.Path.Make();
```

#### lineを引く
まず最初に、漢字でいう「一画目」、書道でいう「起筆」「入り」の位置を決めます。
左から1/4、上から半分くらいの位置にポンと筆を立てます
```ts
checkPath.moveTo(25, 50);
```

あとは感性に従いながら、チェックマークの折り返し地点まで筆をすすめ
```ts
checkPath.lineTo(40, 70);
```

勢いよく「終筆」を決める。
```ts
checkPath.lineTo(85, 35);
```

完成したものを`Canvas`で描画すると
```tsx
<Path path={checkPath} color="#3ea8ff" style="stroke" strokeWidth={10} />
```

こうなります。
![チェックマークのプレビュー](/images/nano72mkn-rn-skia-checkbox/check-preview.png =200x)

うまく説明できなくてすみません...w
`moveTo`、`lineTo`はもしかしたら計算式とかなんかゴニョごにょするものがあるかもしれないです。
が、感性に従って書くのも楽しいですよ

### 動かす
アニメーションをつけるときは、`react-native-reanimated`を使います
https://docs.swmansion.com/react-native-reanimated/


#### タップできるようにする
`View`から`Pressable`に変更し、`onPress`で`isChecked`の状態を切り替えられるようにしました
```diff tsx
export default function Index() {
  const [isChecked, setIsChecked] = useState(false);
  return (
-    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
+    <Pressable style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }} onPress={() => setIsChecked(v => !v)}>
     ...
-    </View>
+    </Pressable>
  );
}
```

#### チェックが入ったときに、枠線が出てくるようにする
`useSharedValue`をreanimatedからimportしてきます。
```tsx
const progress = useSharedValue(0);
```

`isChecked`が`true`の場合に、`progress`が1になるようにします。
```tsx
  useEffect(() => {
    if (!isChecked) {
      progress.value = withTiming(0);
      return;
    }
    progress.value = withTiming(1);
  }, [isChecked]);
```

`Path`の`end`に`progress`を指定します。
```tsx
<Path path={rectPath} color="#3ea8ff" style="stroke" strokeWidth={strokeWidth} end={progress} strokeCap="round" />
```

`strokeCap`に`round`を指定するとこで、Pathの切り口が丸くなります

##### 枠線のアニメーションプレビュー

![枠線のアニメーション](/images/nano72mkn-rn-skia-checkbox/line-animation-prebiew.gif)
::::details 全体のコード
```diff tsx
export default function Index() {
  const [isChecked, setIsChecked] = useState(false);
+  const progress = useSharedValue(0);

+  useEffect(() => {
+    if (!isChecked) {
+      progress.value = withTiming(0);
+      return;
+    }
+    progress.value = withTiming(1);
+  }, [isChecked]);

  return (
    <Pressable style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }} onPress={() => setIsChecked(v => !v)}>
      <Canvas style={{ width: canvasWidth, height: canvasHeight }}>
        <Path path={rectPath} color="#ffffff" />
-        <Path path={rectPath} color="#3ea8ff" style="stroke" strokeWidth={strokeWidth} />
+        <Path path={rectPath} color="#3ea8ff" style="stroke" strokeWidth={strokeWidth} end={progress} strokeCap="round" />
        <Path path={checkPath} color="#3ea8ff" style="stroke" strokeWidth={10} />
      </Canvas>
    </Pressable>
  );
}
```
::::

### チェックマークにもアニメーションをつける
枠線と同様、`end`と`strokeCap`をつけます。
`strokeJoin`は折り返しの時に尖ってほしくないので、`strokeCap`と同様に`round`を指定します。
```tsx
<Path path={checkPath} color="#3ea8ff" style="stroke" strokeWidth={10} end={progress} strokeCap="round" strokeJoin="round" />
```

## 完成
チェックマークにもアニメーションを設定したら完成です！
![チェックマークにもアニメーションを設定](/images/nano72mkn-rn-skia-checkbox/check-animation-preview.gif)

自分の場合は、背景もチェックと同じタイミングで背景色も変わるようにしてみました！
@[tweet](https://twitter.com/nano72mkn/status/1803470679272267986)

## まとめ
React Native Skiaでチェックボックスを作ろう！って動き始めてから、このチェックボックスを完成させるまでに2時間もかかってしまいました...w

youtubeにReact Native Skiaの動画がたくさん上がってますが、結構クオリティが高いものばかりで、線の引き方や角丸の作り方などちょっとづつ参考になりそうなものを集めて...とやっていたら時間が経っていました。

僕みたいなちょっと触ってみたい！という方に届いたら嬉しいです！
