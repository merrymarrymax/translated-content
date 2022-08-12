---
title: Three.js を使った基本的なデモの作成
slug: Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Three.js
tags:
  - 3D
  - Animation
  - Beginner
  - Canvas
  - Games
  - Tutorial
  - WebGL
  - camera
  - lighting
  - rendering
  - three.js
translation_of: Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Three.js
---
{{GamesSidebar}}

ゲームの一般的な 3D シーンは、たとえ最もシンプルなものであっても、座標系に配置された図形、それらを実際に見るためのカメラ、見栄えを良くするためのライトや素材、生き生きと見せるためのアニメーションなど、標準的なアイテムが含まれています。 **Three.js** は、他の 3D ライブラリーと同様に、一般的な 3D 機能をより迅速に実装するためのヘルパー関数が組み込まれています。この記事では、開発環境の設定、必要な HTML の構成、 Three の基本オブジェクト、基本的なデモの作成方法など、 Three を使用するための本当の基本を紹介します。

> **Note:** 私たちは Three を有名な [WebGL](/ja/docs/Web/API/WebGL_API)ライブラリーの 1 つであり、簡単に使い始められるという理由で選びました。Three が他の WebGL ライブラリーと比べて優秀だというつもりはありません。 [CopperLicht](https://www.ambiera.com/copperlicht/index.html), [GLGE](http://www.glge.org/), [PlayCanvas](https://playcanvas.com/) など、気軽に試してみてください。

## 環境構築

Three.js で開発を始める上で、必要なものはあまりありません。少なくとも、

- [WebGL](/ja/docs/Web/API/WebGL_API) によく対応した最新のブラウザー、例えば最新の Firefox や Chrome を使用していることを確認することです。
- デモを保存するディレクトリーを用意してください。
- [最新で最小版の Three.js](https://threejs.org/build/three.min.js) をそのディレクトリーに保存してください。
- ブラウザーの別のタブで [Three.js のドキュメント](https://threejs.org/docs/) を開いてください — 参照するのに便利です。

## HTML の構造

これが今回使用する HTML のコードです。

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>MDN Games: Three.js demo</title>
  <style>
    body { margin: 0; padding: 0; }
    canvas { width: 100%; height: 100%; }
  </style>
</head>
<body>
<script src="three.min.js"></script>
<script>
  const WIDTH = window.innerWidth;
  const HEIGHT = window.innerHeight;
  /* すべての JavaScript コードをここに置きます */
</script>
</body>
</html>
```

これには、文書の {{htmlelement("title")}} のような基本情報と、Three.js がページに挿入する {{htmlelement("canvas")}} 要素の `width` と `height` を、ビューポート空間全体を埋めるために 100% に設定するためのいくつかの CSS が含まれています。最初の {{htmlelement("script")}} 要素は、 Three.js ライブラリをページ内に入れ、 2 つ目の要素内に例のコードを書きます。すでに 2 つのヘルパー変数が含まれており、ウィンドウの `width` と `height` が格納されています。

先に進む前に、このコードを新しいテキストファイルにコピーしデモ用ディレクトリーに`index.html`として保存しましょう。

## レンダラー

レンダラーとは、ブラウザ上でシーンを正しく表示するためのツールです。レンダラーにはいくつかの種類があります。既定では WebGL ですが、その他に Canvas、SVG、CSS、DOM を使用することができます。これらはすべてのレンダリング方法が異なるため、 WebGL の実装と CSS の実装は異なります。目標を達成する方法はさまざまですが、ユーザーにとっての体験は同じになります。このアプローチのおかげで、希望する技術がブラウザーで対応していない場合、代替を使用することができます。

```js
const renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(WIDTH, HEIGHT);
renderer.setClearColor(0xDDDDDD, 1);
document.body.appendChild(renderer.domElement);
```

新しい WebGL レンダラーを作成し、画面上の空き領域全体にフィットするようにサイズを設定し、DOM 構造をページに追加しているところです。最初の行にある `antialias` 引数にお気づきかもしれませんが、これは図形の縁をより滑らかにレンダリングするためのものです。 `setClearColor()` メソッドは、背景を既定値の黒ではなく、明るい灰色に設定しています。

このコードを 2 つ目の {{htmlelement("script")}} 要素に、 JavaScript のコメントのすぐ下に追加してください。

## シーン

シーンは全てが起こる場所です。新たにオブジェクトを作るときは、シーン内にそれを追加することで画面に表示されるようになります。 Three.js ではシーンは `Scene` オブジェクトで表します。前項のコードの下にこれを追加し、シーンを作成しましょう。

```js
const scene = new THREE.Scene();
```

追加されると、 `.add()` 関数を使いオブジェクトをそのシーンに追加できるようになります。

## カメラ

レンダリングされたシーンはありますが、私たちの作品を見るためにカメラを追加する必要があります。カメラのない映画のセットを想像してみてください。以下の行で、3D 座標系にカメラを設置し、シーンの方向に向けることで、ようやく何かを見ることができるようになります。

```js
const camera = new THREE.PerspectiveCamera(70, WIDTH/HEIGHT);
camera.position.z = 50;
scene.add(camera);
```

上記の行を、先に追加した行の下に追加してください。

他の種類のカメラ（Cube、Orthographic）もありますが、最もシンプルなのは Perspective（遠近法）です。このカメラを初期化するには、視野と縦横比を設定する必要があります。前者は見える範囲を設定するのに使用し、後者は画面上のオブジェクトがレンダリング時に正しい比率を持ち、引き伸ばされたように見えないようにするために重要です。上のコードで設定している値を説明しましょう。

- 視野率に設定した値 70 は、値を大きくすればするほど、カメラが映し出すシーンの量が増えるというもので、実際に試してみることができます。通常のカメラビューと魚眼効果を想像してください。既定値は 50 です。
- アスペクト比はウィンドウの現在の幅と高さに設定されるため、動的に調整されます。固定された比率を設定することもできます。例：16 ⁄ 9、これはワイドスクリーンテレビのアスペクト比です。既定値は 1 です。
- `z` 位置は 50 単位で、カメラと `z` 軸上のシーンの中心との距離です。ここでは、カメラを後ろに移動して、シーン内のオブジェクトを見ることができるようにしています。 50 はちょうどいい感じです。近すぎず遠すぎず、またオブジェクトの大きさによって、与えられた視野の中でシーンに留まることができます。 `x` と `y` の値は指定しない場合、既定で 0 になります。

これらの値を試してみて、シーンで見えるものがどのように変わるかを見てみるとよいでしょう。

> **Note:** これらの座標（カメラの z 位置など）の引数に決まった単位は存在しないため、シーンに適している単位 (ミリメートル、メートル、フィートやマイルでも) で構いません。あなたの決めるところです。

## シーンのレンダリング

全ての準備が終わりましたが、まだ私たちは何も目にしていません。レンダラーを作ったなら、全てをレンダリングしましょう。 `render()` は、そのレンダリングを `requestAnimationFrame()` の助けを借り行います。このコードは、全フレームで常にシーンがレンダリングされるようになります。

```js
function render() {
  requestAnimationFrame(render);
  renderer.render(scene, camera);
}
render();
```

新しいフレームごとに `render` 関数が呼び出され、`renderer` が `scene` と `camera` をレンダリングします。関数宣言の直後、ループを開始するために初めてこの関数を呼び出しています。

もう一度、この新しいコードを、前に追加したコードの下に追加してください。ファイルを保存して、ブラウザーで開いてみてください。グレーのウィンドウが表示されるはずです。おめでとうございます。

## ジオメトリー

シーンが正しくレンダリングされたら、 3D 図形の追加を開始します。開発スピードを上げるために、 Three.js は定義済みのプリミティブを多数提供しており、これを使用すれば、 1 行のコードで即座に図形を作成することができます。立方体、球体、円柱、そしてもっと複雑な図形も用意されています。与えられた図形に必要な頂点や面の描画などの詳細は、 Three フレームワークで処理されるので、私たちはより高度なコーディングに集中することができるのです。まず、立方体の図形のジオメトリーを定義して、 `render()` 関数のすぐ上に続くことを追加します。

```js
const boxGeometry = new THREE.BoxGeometry(10, 10, 10);
```

このコードでは、 10 x 10 x 10 の簡単な立方体が生成されます。ジオメトリーだけでは不十分で、図形には素材が必要です。

## 素材

素材とは、オブジェクトに応じた、その表面にある色や質感を表すものです。ここでは、シンプルな青色を選んでボックスを塗装します。使用できる素材は、あらかじめ定義されているものが多数あります。基本 (Basic), フォン (Phong), ランバート (Lambert) です。後の 2 つは後で使ってみましょう。

```js
const basicMaterial = new THREE.MeshBasicMaterial({color: 0x0095DD});
```

前項で追加した定義の下にこれを追加しましょう。

遂に素材も使えるようになりました。さて、次は何をしますか？

## メッシュ

素材をシェイプのジオメトリーに適用させるには、メッシュを使用します。メッシュは、素材をシェイプの表面に適用してくれます。

```js
const cube = new THREE.Mesh(boxGeometry, basicMaterial);
```

もう一回前項で追加したコードの下にこれを追加しましょう。

## 立方体をシーンに追加する

これまでに、ジオメトリーや素材を定義して立方体を作り出しました。最後に私たちが行うべきことはシーンに追加することです。さきほどのコードの下にこれを追加してください。

```js
scene.add(cube);
```

コードを保存してページを更新すると、オブジェクトがカメラの方向を向いているのでオブジェクトは正方形に見えます。オブジェクトの良いところは、シーン内で移動できるということです。例えば、私たちの思うままに回転や拡大縮小を行ったり。立方体を少し回転させ、複数の面を見てみましょう。また、コードの下にこれを追加します。

```js
cube.rotation.set(0.4, 0.2, 0);
```

おめでとうございます！ 3D 環境でオブジェクトを作成しましたね。これは、あなたが最初に考えたよりも簡単だったしょうか？こんな感じです。

![Blue cube on a gray background rendered with Three.js.](cube.png)

ここまでのサンプルコードは、ここで配布しています。

{{JSFiddleEmbed("https://jsfiddle.net/end3r/bwup75fa/","","350")}}

[GitHub でチェックアウト](https://github.com/end3r/MDN-Games-3D/blob/gh-pages/Three.js/cube.html)することもできます。

## シェイプや素材の追加

今度は、このシーンにさらに図形を追加し、他の図形や素材、照明などを調べてみましょう。立方体を左側に移動して、友達のための領域を作ってあげましょう。前の行のすぐ下に、次の行を追加してください。

```js
cube.position.x = -25;
```

では、さらに図形や素材を増やしてみましょう。フォンの素材で包まれたトーラスを追加するとどうなるでしょうか。立方体を定義している線のすぐ下に、次の線を加えてみてください。

```js
const torusGeometry = new THREE.TorusGeometry(7, 1, 6, 12);
const phongMaterial = new THREE.MeshPhongMaterial({color: 0xFF9500});
const torus = new THREE.Mesh(torusGeometry, phongMaterial);
scene.add(torus);
```

`TorusGeometry()` メソッドの引数で定義します。引数は `radius`, `tube diameter`, `radial segment count`, `tubular segment count` です。フォンの素材は箱のシンプルな基本素材よりも光沢があるように見えますが、今のところトーラスはただの黒にしか見えません。

もっと楽しい定義済み図形を選べます。もう少し遊んでみましょう。トーラスを定義している行の下に、次の行を追加してください。

```js
const dodecahedronGeometry = new THREE.DodecahedronGeometry(7);
const lambertMaterial = new THREE.MeshLambertMaterial({color: 0xEAEFF2});
const dodecahedron = new THREE.Mesh(dodecahedronGeometry, lambertMaterial);
dodecahedron.position.x = 25;
scene.add(dodecahedron);
```

今回は、 12 の平らな面を持つ正十二面体を作成します。引数 `DodecahedronGeometry()' はオブジェクトの大きさを定義します。ランバート素材を使用します。フォンと似ていますが、光沢が少ないほうがいいでしょう。ここでも今のところ黒です。オブジェクトを右に移動して、箱やトーラスと同じ位置にはならないようにしています。

上記のように、新しいオブジェクトは現在、ただ黒く見えるだけです。フォンとランバートの両方の素材をきちんと見えるようにするには、光源を導入する必要があります。

## 光源

Three.js では様々なタイプの光源が利用できます。最も基本的なものは `PointLight` で、これは懐中電灯のように定義された方向にスポットライトを照らすように動作します。以下の行を、図形の定義の下に追加してください。

```js
const light = new THREE.PointLight(0xFFFFFF);
light.position.set(-10, 15, 50);
scene.add(light);
```

白い光の点を定義して、その位置をシーンの中心から少し離して設定し、図形の一部を照らすようにし、最後にシーンに追加します。これはうまくいき、 3 つの図形がすべて見えるようになりました。アンビエント、ディレクショナル、ヘミスフィア、スポットなど、他のタイプのライトについても、ドキュメントで確認する必要があります。それらをシーンに配置して、どのように影響するかを試してみてください。

![Shapes: blue cube, dark yellow torus and dark gray dodecahedron on a gray background rendered with Three.js.](shapes.png)

でも、これではちょっと退屈そうですね。ゲームでは、たいてい何かが起こっています。アニメーションを見たりすることもあるでしょう。そこで、この図形に少し命を吹き込んで、アニメーションをつけてみましょう。

## アニメーション

立方体の位置を調整するために、すでに回転を使用しました。また、図形を拡大縮小したり、位置を変更したりすることもできます。アニメーションを表示するには、レンダリングループの中でこれらの値を変更し、各フレームで更新する必要があります。

### 回転

回転させるのは簡単です。各フレームで与えられた回転方向の値を追加します。以下のコードを `render` 関数内の `requestAnimationFrame()` 呼び出しの直後に追加してください。

```js
cube.rotation.y += 0.01;
```

これにより、立方体がフレームごとにほんの少し回転し、アニメーションが滑らかに見えるようになります。

### 拡大縮小

また、オブジェクトを拡大縮小することもできます。一定の値を適用して、一度だけ大きくしたり、小さくしたりするのだ。もっと面白いことをやってみよう。まず、経過時間をカウントするためのヘルパー変数 `t` を実装します。これを `render()` 関数の直前に追加します。

```js
let t = 0;
```

さて、フレームごとに値が増えるようにしましょう。 `requestAnimationFrame()` のすぐ後にこれを書き足しましょう。

```js
t += 0.01;
torus.scale.y = Math.abs(Math.sin(t));
```

私たちは `Math.sin` を使用して、非常に興味深い結果にたどり着きました。 `sin` は周期的な関数なので、これはトーラスを拡大縮小し、そのプロセスを繰り返します。拡大縮小する値を `Math.abs` でラップして、 0 以上の絶対値を渡しています。 sin は -1 から 1 までの値なので、負の値はトーラスを予期しない方法でレンダリングするかもしれません。この場合、半分くらいは黒く見えます。

遂に、動かします。

### 動かす

回転と拡大縮小の他に、オブジェクトをシーン上で移動させることもできます。 `requestAnimationFrame()` 呼び出しのすぐ下に、次のようなものを追加します。

```js
dodecahedron.position.y = -7*Math.sin(t*2);
```

これは、各フレームの y 軸に `sin()` 値を適用することによって、正十二面体を上下に動かし、よりクールな見た目になるように少し調整するものです。これらの値を変えてみて、アニメーションにどのような影響を与えるか見てみましょう。

## まとめ

これは最終的なコードです。

{{JSFiddleEmbed("https://jsfiddle.net/rybr720u/","","350")}}

あなたは今までのコードを [GitHubで見る](https://github.com/end3r/MDN-Games-3D/blob/gh-pages/Three.js/shapes.html)こともできるし、ローカル環境で遊びたいと思ったら[リポジトリーをフォークする](https://github.com/end3r/MDN-Games-3D/)こともできます。 Three.js の基本が理解できたでしょう。このページの親ページである [ウェブ上の 3D に関するドキュメント](/ja/docs/Games/Techniques/3D_on_the_web)に戻ることもできます。

WebGL を実際に触ることで、内部で何が起こっているのかをより理解することもできます。私たちの [WebGL ドキュメンテーション](/ja/docs/Web/API/WebGL_API)を参考にしてみてください。