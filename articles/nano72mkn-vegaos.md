---
title: "React NativeでFire TV Stickのアプリを作れるらしい。【Vega OS】"
emoji: "📺"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "reactnative", "vegaos", "firetv", "frontend"]
publication_name: "stafes_blog"
published_at: 2025-12-06 07:00
published: true
---
どうも、[nano72mkn](https://x.com/nano72mkn)です。
この記事は[スタフェス アドベントカレンダー 2025](https://qiita.com/advent-calendar/2025/stafes)の6日目の記事です。

## はじめに
2025/11/26に開催された[React Native Meetup #23](https://react-native-meetup.connpass.com/event/373376/)で「VegaOS」というものを知りました。

AmazonがFire TV StickやEcho向けに提供しているOSで、なんとReact Nativeでアプリが作れるらしいです。

作れるなら、やってみたいなということで実際に手を動かしてみました。
この記事は、VegaOSを触ってみた体験レポートです。

ちなみに、LTを聞いた時には早速Vega OS対応のFire TV Stickを買っていましたw
https://x.com/nano72mkn/status/1993631615675928617

## VegaOSって何？
VegaOSは、AmazonがFire TV StickやEcho向けに提供しているオペレーティングシステムです。

- **公式ドキュメント**: https://developer.amazon.com/ja/apps-and-games/vega
- **特徴**: React Nativeベース
- **対象デバイス**: Fire TV Stick、Echo Show など

React Nativeの知識があれば、すぐに開発を始められるっぽい...です

## 環境構築

とりあえず、環境構築から始めます。

### Vega SDKのインストール

[Vega SDK](https://developer.amazon.com/ja/docs/vega/0.21/install-vega-sdk.html)をインストールすると、必要なものはすべて用意できます。
しかし、**Vega SDKをインストールするには、Amazon Developer Portalのアカウントが必須**なので、最初にアカウントの作成orログインをして置きましょう

**手順:**
1. [Amazon Developer Portal](https://developer.amazon.com/)でアカウント作成
2. ログイン後、[SDKインストールページ](https://developer.amazon.com/ja/docs/vega/0.21/install-vega-sdk.html)をリロードまたは再度アクセス
3. curlコマンドが表示されるので手順に沿ってインストールをする

https://developer.amazon.com/ja/docs/vega/0.21/install-vega-sdk.html


### プロジェクトの作成

VS Codeの拡張機能として提供されている**Vega Studio**がとても便利でした。
Vega StudioはVega SDKをインストールする時に勝手に追加されます。

Vega Studioでは下記機能が提供されていました
- プロジェクト管理
- ビルドモードの選択
- デバッグ用デバイスの管理
- ChromeDevTool

**プロジェクトの作成手順**
1. Vega StudioのProjectsからCreate Vega Projectをクリックをします
![Vega StudioのProjectsからCreate Vega Projectをクリック](/images/nano72mkn-vegaos/CleanShot-2025-12-05at18.29.28@2x.png =250x)
2. テンプレートの選択。今回は**hello-world**を選択
![Select a project templateでテンプレートを選択する](/images/nano72mkn-vegaos/CleanShot-2025-12-05at18.31.09@2x.png)
3. 保存先のディレクトリを選択
4. プロジェクト名を入力
5. package nameを入力
6. 完成🎉

指示された通りに進めるだけで、プロジェクトが完成しました！
めっちゃ楽

### 動かしてみる

Vega StudioのProjectsにある自分のプロジェクトにマウスをHoverさせると、再生ボタンみたいなのが表示されるので、クリックしてみると....

![Vega Studioからプロジェクトをシミュレーターで動かす様子](/images/nano72mkn-vegaos/CleanShot-2025-12-05at18.36.44.gif)

シミュレーターが起動し、実際に動かすことに成功しました！
まじで楽

![Hello World!が表示されているVega OSのシミュレーター](/images/nano72mkn-vegaos/CleanShot-2025-12-05at18.34.26@2x.png)

### 10分でHello Worldまで行けた

Vega SDKのインストールから、実際にシミュレーターで動かすまで10分！
驚くほど早く動作確認まで辿り着けました。

ここまで用意してくれているのは、開発を始めるまでのハードルが低くなって嬉しいですね！

## シンプルな時計のアプリを作ってみる

動いた！ってだけではつまらないので、再描画を行うシンプルな時計のアプリを作ってみることにします。

### まずは、ちょっと変更してみる

React Nativeと同じ開発体験を味わえるのか？ってのが結構大事なのでテキストを変更してみて、リアルタイムで変更されるのかをみてみます。

`Hello World!`を書き換えてみます。

App.tsxを覗いてみると、見慣れたコードが。
（長いので同じような部分は省略しています）

```tsx:src/App.tsx
export const App = () => {
  const [image, setImage] = useState(images.kepler);

  const styles = getStyles();

  return (
    <ImageBackground
      source={require('./assets/background.png')}
      style={styles.background}>
      <View style={styles.container}>
        <View style={styles.links}>
          <View style={styles.headerContainer}>
            <Text style={styles.headerText}>Hello World!</Text>
            ...
          </View>
          <Link
            linkText={'Learn'}
            onPress={() => {
              setImage(images.learn);
            }}
            testID="sampleLink"
          />
          ...
      </View>
      ...
    </ImageBackground>
  );
};

const getStyles = () =>
  StyleSheet.create({
    background: {
      color: 'white',
      flex: 1,
      flexDirection: 'column',
    },
    ...
  });
```

一旦、`Hello World!`から`ヤッホー`に書き換えてみます
![Hello World!からヤッホーに書き換えてる様子](/images/nano72mkn-vegaos/CleanShot-2025-12-05at18.37.59.gif)

サクッと変更できました！
もちろんですけど、React Nativeと同じ体験のまま開発ができそうです。

### 実際に時計のアプリをちゃちゃっと実装

こんな感じに書き換えてみた。
```tsx:App.tsx
export const App = () => {
  const styles = getStyles();
  const [timeValue, setTimeValue] = useState("-");

  useEffect(() => {
    const interval = setInterval(() => {
      const date = new Date();
      const timeString = date.toLocaleTimeString();
      setTimeValue(timeString);
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return (
    <ImageBackground
      source={require('./assets/background.png')}
      style={styles.background}>
      <View style={styles.container}>
        <Text style={styles.timeValueText}>{timeValue}</Text>
      </View>
    </ImageBackground>
  );
};
```

シミュレーターを見てみると...

![宇宙の背景の上に時間が表示されたアプリ](/images/nano72mkn-vegaos/CleanShot-2025-12-05at20.54.07.gif)


動いてる！！！
簡単すぎる！！！

## うまくいかなかったポイント

とても簡単に実装はできたのですが、
色々試していた時にうまくいかなかったところがありました。

### Reactバージョンの互換性問題

うまくいかなかったのは**Reactバージョンの互換性問題**でした。

#### 問題の発生

スタイリングライブラリ「uniwind」を使おうとしたところ、以下の問題が発生：

- **uniwind 1.1.0**: React 19.0.0以上が必要
- **VegaOS**: React 18.2.0

#### 試したこと

**1. React 19へのアップグレード**
```bash
npm install react@19
```
→ **結果**: アプリが起動しなくなった

**2. `--legacy-peer-deps`フラグを使用**
```bash
npm install uniwind tailwindcss --legacy-peer-deps
```
→ **結果**: インストールはできたが、アプリが起動しなくなった

#### 原因（かも？）

React Native Meetupのうっすらと残る記憶と合わせて考察すると、**VegaOS自体にReactパッケージが組み込まれている**的なかんじだった気がします。

つまり、プロジェクトの`package.json`でReactのバージョンを変更しても、VegaOS内部のReactが使われているのでは？と推測しています

:::message
実際のスライドや録画などが見つからなかったため、完全に推測なので**間違っている可能性が大いにあります**。
:::

#### 結論

結局１時間では動かすことは出来ませんでした。

- React Nativeでサポートされている範囲でも特に困らないので、できる範囲で楽しむ
- React Native Meetupでサポートされているライブラリも多数紹介されていたので、そのドキュメントが見つかれば勝確


## まとめ

### 良かった点

- **Vega Studioの開発環境**: VS Code拡張として提供されており、開発体験が良い
- **Hello Worldまでが早い**: 約10分で動作確認まで到達
- **React Nativeの知識が活かせる**: 既存の知識で開発可能

### 課題・心残り

- **実機テストができなかった**: Amazonアプリストアへのアップロード時の本人確認に失敗し、Fire TV Stickでの動作確認ができなかった（Vega OS対応のFire TV Stickを買ったので、問い合わせてみる！）
- **Reactバージョンの制約**: React 19が使えないため、最新のライブラリが使えない可能性がある
- **リリースまで試せなかった**: 本人確認に失敗したため、リリースプロセスも確認できず...

### 感想

Fire TV Stickという大画面デバイス向けのアプリ開発は、新しい可能性を感じさせてくれました。実機テストができなかったのは心残りですが、VegaOSの開発の流れを理解できたのは良い経験でした。

興味がある方は、ぜひ試してみてください！
