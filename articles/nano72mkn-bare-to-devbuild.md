---
title: "Expo Bare workflow から Development Build & CNG へ移行した"
emoji: "📱"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["reactnative", "expo", "react", "ios", "android"]
published: true
publication_name: "stafes_blog"
---

どうも、[nano72mkn](https://x.com/nano72mkn)です。

:::message
この記事は[スタフェス アドベントカレンダー 2025](https://qiita.com/advent-calendar/2025/stafes)の13日目の記事です。
:::

自分を含む3名の新しいチーム体制でアプリ開発を進めることになり、
React Native アプリを Bare Workflow から **Expo Development Build & Continuous Native Generation(CNG)** に移行しました。環境差によるビルドトラブルを解消し、開発体験を大きく改善した知見を共有します。

## なぜ Bare Workflow から移行したのか？

もともと前任エンジニア 1名で開発をしていた為、**その人の環境のみで動けばOK**という状態でした。
しかし、チームが 3名（内２名はアプリ開発未経験者）に変更され、各メンバーが自分のローカル環境でビルドを行う必要が出てきましたが、ここで問題が顕在化します。

特にアプリ開発が初めてのメンバーにとって、環境構築からビルド、動作確認までのハードルが高い状況でした。

React Native のローカルビルドでは、

- Xcode
- CocoaPods
- Android SDK
- Kotlin / Gradle
- JDK
- Ruby

などの影響を強く受け、以下のような問題が頻発しました。

- iOS は動くが Android が動かない
- Android は動くが iOS が動かない
- そもそも初期セットアップからビルドが通らない
- 環境差で動作が揃わない

**「誰かの環境では動く」状態**となり、結局アプリ開発経験のある自分だけが iOS/Android のどちらも動かせる状況になりました。

さらに、証明書・署名鍵の管理も課題でした。

- iOS: 証明書・プロビジョニングプロファイルの管理と有効期限対応
- Android: Keystore ファイルとパスワードの管理・共有

これらの作業は複雑で、結局アプリ開発経験のある自分が対応せざるを得ない状況でした。

このままでは属人化してしまうため、

> **環境差や証明書管理に左右されず、チーム全員が開発に集中できる仕組みが必要だぞ！**

と判断し、Expo の **EAS Build / Development Build** の採用を決めました。

## 移行時にやったこと

Bare Workflow から Expo(CNG) への移行では、いくつか注意すべきポイントがありました。実際の移行で特に気をつけた3つのポイントを紹介します。

### パッケージバージョンの統一

Expo 移行時は **Expo Doctor** を使って、Expo SDK と各種パッケージの互換性を確認しました。

```bash
npx expo-doctor
```

Expo Doctor が検出した問題の多くは自動で解決できましたが、一部は手作業での調整が必要でした。

### ネイティブ設定の app.config.ts への集約

Bare Workflow 時代は、ネイティブの設定を直接ファイルに記述していました。

* iOS: Info.plist
* Android: AndroidManifest.xml

Expo 移行後は、これらの設定を **app.config.ts に集約**しました。

これにより、環境ごとに異なる設定（アプリ名、bundleId など）も app.config.ts で環境変数を使って切り替えられるようになり、管理がシンプルになりました。

```ts:app.config.ts(簡易版)
const APP_VARIANT = process.env.APP_VARIANT;

const VARIANT_CONFIG = {
  development: {
    name: 'Dev: Hoge',
    icon: './assets/icon-development.png',
  },
  preview: {
    name: 'Preview: Hoge',
    icon: './assets/icon-preview.png',
  },
  production: {
    name: 'Hoge',
    icon: './assets/icon.png',
  },
} as const;

const currentConfig = VARIANT_CONFIG[APP_VARIANT];
```

### Expo公式ライブラリへの移行

Bare Workflow から Expo に移行する際、`ios/` と `android/` フォルダを削除するタイミングは重要な判断ポイントでした。

まず、既存のネイティブ実装を **Expo公式ライブラリで置き換えられるか** を検証しました。

ネイティブで実装していたものは、位置情報取得のみだったので **expo-location** に移行しました。必要な機能はすべてカバーできることを確認できました。

**最終的にネイティブフォルダを完全削除**し、Expo の CNG（Continuous Native Generation）で生成する運用に切り替えることができました。

## Development Build 運用で気づいたポイント

### 複数環境の Development Build 管理

本番・ステージング・ローカルなどの複数環境は、`app.config.ts` で環境変数を使い

- アプリ名（Dev: hoge / Preview: Hoge）
- アイコン
- bundleId / applicationId（com.example.hoge.dev / com.example.hoge.preview）

を切り替えています。
これにより、**環境ごとに独立した dev build** を作れるため、検証が安全かつ明確になりました。

## Expo Development Build に移行したことによるちょっとしたデメリット

### いつビルドし直すべきか問題（未経験者が最も戸惑うポイント）

Development Build を使い始めて最も難しかったのが、

> **「この変更は EAS Update で反映できる？ それとも再ビルドが必要？」**

という判断でした。特にアプリ開発未経験者にとって、この判断基準が分かりづらく、何度も迷うポイントになりました。

EAS Buildは頻繁に使うこともできないので、ビルドすべきかどうかの判断が必要です。

#### 再ビルド必須の変更

以下のような変更を行った場合は、必ず Development Build を再生成する必要があります

- ネイティブモジュールを追加した
- Config Plugin を追加した
- カメラ・通知などパーミッション追加が必要な変更
- Info.plist / AndroidManifest.xml に影響する設定
- bundleId / applicationId の変更
- Expo SDK のアップデート

導入したライブラリによってネイティブに影響するかどうか変わるため、慣れるまでは判断に困るポイントです。

## 移行後に得られたメリット

開発体験が大きく改善されたことで、日々の作業のストレスが少なくなり、開発リズムが安定しました。

### PdM の検証時にビルドが（ほぼ）不要になった

PdM への検証フローは Firebase App Distribution で Preview ビルドを都度配信していたため、
**ビルド作成 → 配布 → インストールで毎回 30 分以上かかる**という課題がありました。

Development BuildをPdMの検証端末にもインストールしているので、EAS Updateを組み合わせることで検証時にビルドをしなくてもブランチごとに配信をして動作確認をしてもらえる環境になりました。

- **PdM の検証時にビルドが不要になった** → 確認フローがスムーズに
- Preview の配布が**30 分** → EAS Update なら **10 秒**

### チーム内の環境差トラブルがゼロに

EAS Buildを使用するので、ビルド時のトラブルやローカル環境のトラブルがなくなり、
開発がスムーズに行われるようになりました。

### チームオンボーディングが劇的に改善
Bare Workflow 時代は環境構築だけで

- 慣れていても半日
- 不慣れだと 1〜2 日

かかっていました。

Development Build 導入後は、（すでに dev build がある前提）

- 経験者 → **10 分**
- 初心者 → **30 分**

で動作確認ができるようになりました。

## まとめ

業務の合間に進め、約2ヶ月かけて移行を完了しました。

Expo Development Build は、単なるビルド方式の変更ではなく、

- チーム開発の安定化
- PdM とのフィードバックループ高速化
- オンボーディング改善
- 環境差トラブルの解消

といった **開発プロセス全体に影響するアップグレード** でした。
