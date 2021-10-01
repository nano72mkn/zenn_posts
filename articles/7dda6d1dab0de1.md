---
title: "expoでつくったenterpriseアプリがios15で起動できない問題を対処した"
emoji: "📱"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ios","reactnative"]
published: true
---

# 現象
ios15で新規インストール、インストール済み関係なくアプリが起動できない状態になっていた。

# 調査

Expoの下記のスレッドで今回の現象について話し合われていた。
https://forums.expo.dev/t/ios-15-cannot-launch-enterprise-signed-application/53701

要約すると

- Xcode13にアップデートしなきゃいけない
- `eas build`を使ってlocalでbuildすれば動く
- `expo build:ios`が機能していないよ

という感じっぽいですね

そして、AppleのDeveloper Forumsにも問題が報告されていました
https://developer.apple.com/forums/thread/682775

原因としては、enterprise appのcode signの仕様が変わったことっぽいです。
再度署名することで回避できそうなので、buildをし直していきます。

## easってなんじゃらほい

- ビルドするよ！
    - クラウドでビルドも署名もしちゃうよ
    - TestFlightとか使わないでテスターに配布しちゃう！！
- storeに公開するよ！
    - easならコマンド一発でstoreに公開できちゃう！
- アップデートもおまかせだよ！
    - 安全にアップデートするよ
    - 近日公開だからちょいまち！

っていってます。（大雑把）
今までできてたことは何が違うんじゃろ？ってお気持ちです。

ちなみにこの記事公開時は有料です。（expo課金してるので優先的に使えるみたい）

ちゃんと知りたい人は、下記公式サイトをチェック！
https://expo.dev/eas

# 対応

調査の結果、`expo build`は機能していないようなので`eas build`を試していきます。
（ちなみに、SDK42で`expo build`でも対応される予感）

easは初めてなので下記を参考に進めていきます。
https://docs.expo.dev/build/eas-json/

## `eas.json`をつくる

`Configuring EAS Submit with eas.json`を参考に`eas.json`を準備
```json:eas.json
{
  "build": {
    "release": {},
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    }
  }
}
```

さらに、xcode13でビルドしてもらえるように`image`を指定

コメントに「xcode13のimageつくったから、これつかいなはれ」って書いてあるので
感謝の気持ちを込めながらimageにコピペしていきます。 （ありがとう wkozyraさん）
> xcode13 image is available, to use it set image field in eas.json to macos-big-sur-11.4-xcode-13.0
https://forums.expo.dev/t/ios-15-cannot-launch-enterprise-signed-application/53701/22

easの使い方ドキュメントも一緒に乗っけてくれるとこもポイント高いですね。

`base`に追加することで、`extends`でばら撒くことができるようです！（最高）
```json:eas.json
{
  "build": {
    "base": {
      "distribution": "internal",
      "ios": {
        "image": "macos-big-sur-11.4-xcode-13.0"
      }
    },
    "release": {
      ...
    },
    "development": {
      "extends": "base",
      "developmentClient": true
    }
  }
}
```

- node
- yarn
- expoCli

あたりは設定した方が幸せになる気もするけど、一旦そのまま動かしてみる

:::details （関係ないので折り畳む）意気揚々とどうやってビルドするんじゃい！って探しまくってたら

> SDK 41+ apps are supported
EAS Build only supports SDK 41+ managed projects. You must upgrade your project to migrate to EAS Build.


おぅ...。
このプロジェクトSDK39じゃ...

ということでバージョンを上げます....
:::


## エラー対応編

### TypeError: Cannot read property 'enabled' of undefined

`eas logout` / `esa login` をしたら治るっぽい

### TypeError: Cannot destructure property 'sessionSecret' of 'body.data' as it is undefined.

`esa login`したら上記のエラーが出た

nodeのバージョンが問題なようです。

```bash
$ node -v
v14.0.0
```

nodenvを使用しているのでインストールされてる14系で最新のものにした

```text:.node-version
v14.17.5
```

`Logged in` 優勝
一個目のエラーもこれで治ったのかもしれない。


参考

- https://forums.expo.dev/t/eas-build-error-typeerror-cannot-read-property-enabled-of-undefined/51868/5
- https://forums.expo.dev/t/eas-login-as-member-of-organization/52012/10


## `eas build`始動

いよいよビルド。

```
$ eas build --platform ios
```

Appleのログインやらいろいろやって

とりあえず、`Build successful`でた!


# 最後に

ご飯食べて戻ってきたら`expo build:ios`でできるようにしたよってスレッドに投稿されてました。
めでたしめでたし
