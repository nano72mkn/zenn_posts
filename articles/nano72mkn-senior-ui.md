---
title: "シニア向けUIデザインの基本と注意点"
emoji: "👴"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["UI", "UX", "accessibility", "frontend", "mobile"]
published: false
---

こんにちは、[nano72mkn](https://x.com/nano72mkn)です。

本記事はフロントエンドカンファレンス北海道 2025で話した「おじいちゃんに優しいUIをつくってみた」を文章化したものです。

https://speakerdeck.com/nano72mkn/oziitiyanniyou-siiuiwotukututemita

## シニアが直面する課題

シニア世代がWebサービスやアプリを利用する上で、以下のような課題があります。

### 視覚能力の低下
- **老眼**: 近くのものが見えにくくなる
- **コントラスト感度の低下**: 色の区別がつきにくくなる  
- **視覚的な走査速度の低下**: 画面を見渡して目的のものを見つけるのに時間がかかる

### 運動コントロール
- **手先の器用さの低下**: 細かい操作が困難になる
- **手と目の協調能力の低下**: 小さなボタンの操作が難しくなる

### 認知能力の低下
- **短期記憶の減少**: 多くの操作が必要な場合、何をしようとしているのか忘れやすくなる

これらの課題を意識してUIを設計する必要があります。

## 基準となる最低ライン

まずは一般的なガイドラインで推奨されている最低基準を確認しましょう。

### タッチ領域
- **iOS HIG**: 44pt以上
- **Material Design**: 48dp以上

### フォントサイズ
- **本文**: 16px（Health Literacy Online推奨）

### コントラスト
- **WCAG AA基準**: 4.5:1以上
- **大きい文字**: 3:1以上（18pt/14pt太字以上）

ここまでは「若年層にも必要な最低限」の対応です。

## シニア向け対策

より具体的なシニア向けの対策を見てみましょう。

### タッチ領域を大きくする
高齢者向けのUI研究では、**19mm前後**（約120dp / 54pt）のボタンがシニア世代に好まれており、特に器用さが低下している方では19mm以上でタッチするときの精度が安定するという研究結果があります。

### フォントサイズを大きくする
Health Literacy Onlineでは、高齢者やロービジョンのユーザー向けに**19px以上**が推奨されています。

### コントラストを高くする
**WCAG AAA基準の7:1以上**にすることで、老眼などのロービジョンのユーザーでも読みやすくなります。

### 実際の比較

基準に沿って作ったダイアログと、シニア向けに対策したダイアログを比較してみましょう。

| 基準に沿って作ったダイアログ | シニア向けに対策したダイアログ |
| --- | --- |
| ![基準](/images/nano72mkn-senior-ui/frontendo2025-8808.png =300x) | ![シニア向け](/images/nano72mkn-senior-ui/frontendo2025-8809.png =300x) |

より見やすくなっていることが分かります。

## ⚠️ システムフォントサイズの罠

ネイティブアプリを開発する際に特に注意が必要なのが、システムのフォントサイズ設定です。

![ユーザーから送られてきた画面](/images/nano72mkn-senior-ui/frontendo2025-8815.png =250x)
*ユーザーから送られてきた画面（再現イメージ）*

実際にユーザーから送られてきた画面を見ると、フォントサイズが19pxよりもとても大きく表示されてしまっていました。

### iOSの場合
「設定 > アクセシビリティ > さらに大きな文字」で以下の設定が可能です：

- **通常の範囲**: 最大約200%まで拡大
- **「さらに大きな文字」をON**: 最大約310%まで拡大

![iOS設定画面](/images/nano72mkn-senior-ui/frontendo2025-8819.png =250x)
*iOS設定 > アクセシビリティ > さらに大きな文字*

シニア世代はスマートフォンのシステムフォントサイズを調整し、見やすいサイズに変更して使用しています。310%まで拡大されると、そのままではレイアウトが崩れてしまいます。

## 対応方法

Apple Human Interface Guidelinesでは、**レイアウト調整による対応を最優先**とすることが推奨されています。

### 1. レイアウトの調整（最優先）

まず、あらゆるフォントサイズに対応できるレイアウト設計を行います：

#### スタックレイアウトへの変更
横並び（水平スタック）から縦積み（垂直スタック）に切り替えることで、文字が大きくなってもテキストが重複するのを防げます。

#### 行数の制限解除
テキストの行数制限を解除し、内容が完全に表示されるようにします：

```tsx
<Text numberOfLines={0} /* 行数制限なし */>
  長いテキスト内容...
</Text>
```

#### 列数の動的調整
フォントサイズの増大に応じて列数を減らし、読みやすさを向上させます。

### 2. フォントサイズ上限の設定（補完的対応）

レイアウト調整でも対応できない場合の最終手段として、フォントサイズの上限を設定します：

#### React Nativeの場合
```tsx
<Text maxFontSizeMultiplier={2.0 /* 例: 200%上限 */}>
  テキスト内容
</Text>
```

#### SwiftUIの場合
```swift
Text("テキスト内容")
  .dynamicTypeSize(DynamicTypeSize.large...DynamicTypeSize.xxxLarge)
```

**注意**: フォントサイズの上限設定は、レイアウト調整による対応が困難な場合にのみ使用してください。ユーザーのアクセシビリティ設定を制限することになるためです。

## まとめ

### シニア向けUIはサイズアップが基本
- **タッチ領域**: 19mm前後（約120dp/54pt）
- **フォントサイズ**: 19px以上
- **コントラスト**: 7:1以上（WCAG AAA基準）

### アプリの場合はシステム設定に注意
- シニアはシステムフォントサイズを**最大310%**まで拡大する可能性がある
- テスト時にはシステムフォントのサイズを変更して確認する

シニア向けUIの基本は「見やすく、押しやすく」することです。これらの対策により、より多くのユーザーにとって使いやすいアプリケーションを作ることができます。

## 参考資料

- [高齢者のためのユーザインタフェースデザイン](https://www.kindaikagaku.co.jp/news/20191202.html)
- [公益社団法人 日本眼科医会 - 40代で始まる目の老化](https://www.gankaikai.or.jp/health/37/)
- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)
- [Material Design](https://m3.material.io/)
- [WCAG 2.2 - コントラスト (最低限)](https://waic.jp/translations/WCAG22/Understanding/contrast-minimum)
- [Health Literacy Online - Section 5.3](https://odphp.health.gov/healthliteracyonline/design-easy-scanning/use-readable-font-thats-least-16-pixels)
- [Touch Screen User Interfaces for Older Adults: Button Size and Spacing](https://www.researchgate.net/publication/225367546_Touch_Screen_User_Interfaces_for_Older_Adults_Button_Size_and_Spacing)
