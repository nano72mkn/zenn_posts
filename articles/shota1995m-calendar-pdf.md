---
title: "PDF-LIBでカレンダーを作れるサイトを作ったので、PDF-LIBについて書く"
emoji: "🗓"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "pdf", "javascript"]
published: true
publication_name: "stafes_blog"
published_at: 2022-12-24
---

この記事は、[スターフェスティバル Advent Calendar 2022](https://qiita.com/advent-calendar/2022/stafes)の 24 日目の記事です。

みなさん！ クリスマスイブですね！
ユダヤ暦では日没から一日が始まっていたので、
24 日の午後（イブニング）からメリクリって言っても良いらしいですねー

前日は、[まつや](https://zenn.dev/okoge_matsuya)の[2000 行の SQL が流れてた話](https://zenn.dev/stafes_blog/articles/dcd8511f8900c4)でした！

---

# はじめに

今年（2022 年）の 8 月ごろ、親から来年（2023 年）の PDF カレンダーが欲しいと相談されていました。
それならサクッと出来そうだなーと思って・・・気づいたら 12 月になっていました。

すでに 2023 年の PDF カレンダーは入手できたそうですが、
また来年の 8 月になったら欲しいと言われるだろうなと思うので作ってみました。

PDF カレンダー作成サイトを作った時に使った PDF-LIB について書いていこうと思います。

# 構成

- React： v18.2.0
- PDF-LIB：　 v1.17.1

# まずは、PDF-LIB を使って PDF を生成する

今回、PDF を生成するものには PDF-LIB を選びました。

https://github.com/Hopding/pdf-lib

## インストール

```bash
npm install --save pdf-lib
```

## 初期設定

`PDFDocument`を生成します

```tsx:index.tsx
import { useEffect, FC } from "react";
import { PDFDocument } from "pdf-lib";

export default Component: FC = () => {
useEffect(() => {
    (async () => {
      const pdfDoc = await PDFDocument.create();
    })();
  }, []);

  return <></>;
};

export default Component;
```

（サンプルのため、雑に useEffect を使っています！ごめんなさい！）

## ページを追加

```diff tsx:index.tsx
...
export default Component: FC = () => {
useEffect(() => {
    (async () => {
      const pdfDoc = await PDFDocument.create();
+      const page = pdfDoc.addPage([595.28, 841.89]); // A4
    })();
  }, []);

  return <></>;
};
```

## フォントの設定

`page.drawText`でテキストを表示できます。

```diff tsx:index.tsx
...
export default Component: FC = () => {
useEffect(() => {
    (async () => {
      const pdfDoc = await PDFDocument.create();
+      const timesRomanFont = await pdfDoc.embedFont(StandardFonts.TimesRoman);
      const page = pdfDoc.addPage([595.28, 841.89]); // A4
    })();
  }, []);

  return <></>;
};
```

## テキストの追加

`page.drawText`でテキストを表示できます。

```diff tsx:index.tsx
...
+ import { PDFDocument, rgb, StandardFonts } from "pdf-lib";

export default Component: FC = () => {
useEffect(() => {
    (async () => {
      const pdfDoc = await PDFDocument.create();
      const timesRomanFont = await pdfDoc.embedFont(StandardFonts.TimesRoman);
      const page = pdfDoc.addPage([595.28, 841.89]); // A4
+      const { height } = page.getSize();
+      const fontSize = 30;
+      page.drawText("Creating PDFs in JavaScript is awesome!", {
+        x: 50,
+        y: height - 4 * fontSize,
+        size: fontSize,
+        font: timesRomanFont,
+        color: rgb(0, 0.53, 0.71)
+      });
    })();
  }, []);

  return <></>;
};
```

## iframe で描画する

`pdfDoc.saveAsBase64`で`base64`を取得できるので`iframe`で埋め込みます。

```diff tsx:index.tsx
import { useEffect, useState, FC } from "react";
import { PDFDocument, rgb, StandardFonts } from "pdf-lib";

const Component: FC = () => {
+  const [uri, setUri] = useState<string | null>(null);
  useEffect(() => {
    (async () => {
      const pdfDoc = await PDFDocument.create();
      const timesRomanFont = await pdfDoc.embedFont(StandardFonts.TimesRoman);
      const page = pdfDoc.addPage([595.28, 841.89]); // A4
      const { height } = page.getSize();
      const fontSize = 30;
      page.drawText("Creating PDFs in JavaScript is awesome!", {
        x: 50,
        y: height - 4 * fontSize,
        size: fontSize,
        font: timesRomanFont,
        color: rgb(0, 0.53, 0.71)
      });

      const dataUri = await pdfDoc.saveAsBase64({ dataUri: true });
      setUri(dataUri);
    })();
  }, []);

+  if (uri === null) {
+    return <p>生成中</p>;
+  }

  return (
+    <iframe
+      id="pdf"
+      title="pdf"
+      style={{ width: 1000, height: 500 }}
+      src={uri}
+    ></iframe>
  );
};

export default Component;
```

こんな感じで PDF を生成できます。

## 日本語フォントに対応する

上記で紹介した[フォントの設定](#フォントの設定)では PDF-LIB が準備しているものだけしか使えないため、`ttf`を使って日本語を使えるようにします。

`ttf`自体は、Google Fonts などから好きなものを持ってきてください。

### @pdf-lib/fontkit をインストール

```bash
npm install --save @pdf-lib/fontkit
```

### フォントを適用する

fontkit を設定し、`ttf`を`embedFont`に食わせたら日本語フォントも使用できるようになります！

```diff tsx:index.tsx
+import fontkit from "@pdf-lib/fontkit";
...
  const pdfDoc = await PDFDocument.create();
+  pdfDoc.registerFontkit(fontkit);
-  const timesRomanFont = await pdfDoc.embedFont(StandardFonts.TimesRoman);
+  const latoRegularBytes = await fetch("/fonts/Lato-Regular.ttf").then(
+    (res) => res.arrayBuffer()
+  );
+  const latoRegular = await pdfDoc.embedFont(latoRegularBytes);
  const page = pdfDoc.addPage([595.28, 841.89]); // A4

+  page.drawText("JavaScriptでPDFを作成するのはすごい!", {
    x: 50,
    y: height - 4 * fontSize,
    size: fontSize,
+    font: latoRegular,
    color: rgb(0, 0.53, 0.71)
  });
...
```

今回はフロント側で使用しているので`fetch`を使ってますが、`node.js`であれば`fs`を使って読み込ませることができます。

# 大変だったところ/いまいちポイント

## PDF-LIB の初期位置

ページの左下から x と y を指定する必要があり、
ページの height を指定して左上に 1 回持ってきてから計算するのが面倒くさかったです。

## rgb()

色を指定する際に`rgb(0,0,0)`のように指定するのですが、0~255 ではなく 0~1 で指定する必要があり、思った色が出せないで苦労しました。

## PDF 内リンク

PDF 内のリンクを設定する関数が準備されていませんでした。

下記の issue を参考に自分で実装する必要がありました。
https://github.com/Hopding/pdf-lib/issues/555

さらに、テキストにリンクを貼りたいだけでもリンクの範囲を指定する必要があり、微調整に時間がかかるということがありました。

この辺りは、jsPDF の[textWithLink](https://raw.githack.com/MrRio/jsPDF/master/docs/module-annotations.html#~textWithLink)で簡単に実装できそうです。
この記事を書いている時にみつけてしまい、後悔しとります。

# まとめ

ある程度作業終わったところで`jsPDF`を見つけ、PDF-LIB でつまづいたところが簡単に実装できそうな感じがしました。
サクッと終わらせようと技術選定をサボってしまった罰ですね・・・

ということで、今回 PDF-LIB を使用して作った PDF カレンダー作成サービスが ↓ こちらになります！

https://calpdf.code-lab.xyz/

今後、テーマを増やしたり UI を調整したり・・・いろいろアップデートしていこうと思っています！

# 採用頑張ってます！

https://stafes.notion.site/stafes/d0996a00d77d418280982797c7e16001

気軽に相談、ご応募お待ちしております！
ウェルウェルカムカム！！

---

実は Twitter もあります！
よかったら見てみてくださいー
https://twitter.com/stafes_tech
