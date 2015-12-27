CSS3開発者ガイド
======

# 2章 メディアクエリ

## 2.3 メディアフィーチャー

以下のメディアクエリでは、ブラウザのビューポートの幅が400ピクセル以上かどうか判定しています。この条件に該当する場合、h1要素の背景に画像が表示されます。

    @media (min-width: 400px) {
      h1 {
        background: url('landscape.jpg');
      }
    }


## 2.3 モバイルファーストのWeb開発

**ダメな例**

    E {
      background-image: url('huge-image.jpg');
    }

    @media (max-width: 600px) {
      E {
        display: none;
      }
    }

このような画像は、たとえ非表示でもダウンロードされキャッシュに保持されることになる。

その結果、ページの読み込みにかかる時間が増大し、帯域幅も不必要に消費されます。


**いい例**

    @media (min-width: 600px) {
      E {
        background-image: url('huge-image.jpg');
      }
    }

こうすれば、小さな画面のデバイスには画像はダウンロードされません。


# 3章 セレクタ

## 3.2 CSS3の新しい属性セレクタ

**前方一致の属性セレクタ**

    a[title^='image'] {
      //
    }

test.html

    <li>
      <a href="http://example.com" title="Image Library">Image</a> <!-- ここだけが該当する -->
    </li>
    <li>
      <a href="http://example.com" title="Free Image Library"></a>
    </li>
    <li>
      <a href="http://example.com" title="Free Sound Library"></a>
    </li>

先頭セレクタは、ハイパーリンクに対して視覚的な情報を加えたい場合に特に便利です。

- 外部サイトへのハイパーリンクの先頭にリンクアイコンを表示させたり

**後方一致の属性セレクタ**

    a[title$='library'] {
      //
    }


先頭セレクタ同様に、末尾セレクタもハイパーリンクに視覚的な効果おｗ与えるのに利用できます。

    a[href$='.pdf'] { ... }
    a[href$='.doc'] { ... }
    a[href$='.rss'] { ... }

**任意の部分文字列の属性セレクタ**

    a[title*='image'] { ... }

test.html

    <li>
      <a href="http://example.com" title="Image Library">Image</a> <!-- ここと該当する -->
    </li>
    <li>
      <a href="http://example.com" title="Free Image Library"></a>　<!-- ここが該当する -->
    </li>
    <li>
      <a href="http://example.com" title="Free Sound Library"></a>
    </li>

任意セレクタは文字列の出現場所を問わないため、3つの新しい属性セレクタの中で最も柔軟です。

## 3.3 一般的な兄弟要素コンビネータ

    E + F { ... } // 隣接する兄弟要素コンビネータ
    E ~ F { ... } // 一般的な兄弟要素コンビネータ


# 4章 疑似クラスと疑似要素

## 4.1 構造を表す疑似クラス


**:nth-child()**

- 子要素全体の中での位置
- 単純に何番目の子要素かということが問題になる

_

    p:nth-child(2n) {
      font-weight: bolder;
    }

test.html

    <div>
      <h2>AAA</h2>
      <p>Lorem ipsum.</p> // これ
      <p>Dolor sit amet.</p>
      <p>Dolor sit amet.</p> // これ
    </div>

**:nth-of-type()**

- 特定の型の子要素の中での位置

_

    p:nth-of-type(2n) {
      font-weight: bolder;
    }

test.html

    <div>
      <h2>AAA</h2>
      <p>Lorem ipsum.</p>
      <p>Dolor sit amet.</p>　// これ
      <p>Dolor sit amet.</p>
    </div>

pの要素で偶数番目に適用。

**:nth-last-child()**

- nth-child()とは逆に最後の子要素からカウントを行う。

**:nth-last-of-type()**

- nth-of-type()とは逆に最後の子要素からカウントを行う。

**:first-of-type**

- 先頭の子要素に対してルールを適用するためのセレクタ。
- nth-of-typeをより限定的にしたもの。

**:last-child**

- 最後の子要素と指定された方を持つ最後の子要素の選択に利用できます。

**:last-of-type**

- last-childのof-type版。

**:only-child**

- 兄弟要素がまったくない場合に適用
- 一つしか子要素がない場合には:only-childを使う。

**:only-of-type**

- 同じ型の兄弟要素がない場合に適用
- 他の型の要素もある中から1つだけの要素を取り出したい場合に使う。

## 4.2 その他の疑似クラス

**:target**

Githubやstackoverflowの強調表示に使うやつ(多分)

**:empty**

- 子要素もテキストノードも含まない空の要素が選択される。

例

    <tr>
      <td></td>
      <td>a</td>
      <td>b</td>
    </tr>

以下のルールを適用すると

    td:empty {
      background: red;
    }

最初のtd要素にだけルールが適用される。

**:root**

- ドキュメントツリーの中で最初に現れる要素が選択される。

**:not()**

- 引数として指定された要素以外の、すべての要素が選択される。

書式の例

    div > :not(p) { color: red; }

先頭以外のすべてを斜体にしたい。

    p:not(:first-child) {
      font-style: italic;
    }

<br>

# 5章 Webフォント

ページ上にWebフォントを表示するには、まず`@font-face`というルールを使ってフォントを定義する。

![フォント例]()


## 5.3 Web上でフォントを使うためのライセンス

Faas(Font as a Service)ってのを使え。

- Fontdeck
- TypeKit


<br>

# 6章 テキストの効果とスタイル

## 6.3 オーバーフローの制限


    p {
      overflow: hidden; // 境界の外部にコンテンツが表示されないようになっています。
      text-overflow: ellipsis; // はみ出た部分のテキストは...で置き換えられる。
      white-space: nowrap;  // テキストが複数行に折り返されることがない。
    }

## 6.5 行の折り返しのコントロール

- break-word
-- コンテナ要素からあふれないように、長い単語を折り返したいときに指定する(なかむらさんのtumblrっぽいあれ)

_

    p.break {
      word-wrap: break-word;
    }

<br>

# 8章 複数の背景画像

    h2 {
      background-image; url('l1.svg'), url('l12.svg');
      background-position: 95% 85% 50% 50%;
      background-repeat: no-repeat;
    }


各レイヤーは逆順に配置され、先頭に記述されている画像が最上位のレイヤーとして表示されます。


# 12章 二次元の変形

**transaform**

**translate**

    E {
      transform: translate(x, y);
    }

例

    E {
      transform: translate(20px, 15%);
    }

**scale**

    E {
      transform: scaleX(value) scaleY(value);
    }

例

上下左右の両方向に2倍する

    E {
      transform: scaleXX(2) scaleY(2);
    }

**skew**

角度を変える

    E {
      transform: skewX(value) skewY(value);
    }

<br>

# 14章 トランジションとアニメーション

## トランジション

CSSのトランジションとは、あるプロパティの値を徐々に変化させることによるアニメーションを意味します。

トランジションのduration(transition-duration)のデフォルト値はゼロです。
つまり、トランジションを行うために指定が必須なのはプロパティだけです。(transition-propertyのデフォルト値はall)

### transition-timing-functionのキーワード一覧

- ease
- linear
- ease
- ease-out
- ease-in-out

### 構文

    E {
      transition: property duration timing-function delay;
    }

例

    h1 {
      transition: font-size 2s ease-out 250ms;
    }

## アニメーション

トランジションは便利ですが、何らかのプロパティの値が変化したときにしか発生しないという制約があります。

CSS Animationモジュールはこの制約を受けず、要素に対してアニメーションの設定を直接行えます。

### キーフレーム

アニメーションの作成の第一歩として、トランジションの開始と終了の時点を表すキーフレームを定義します。

例

    @keyframes expand {
      from { border-width: 4px; }
      50% { border-width: 12px; }
      to {
        border-width: 4px;
        height: 100%;
        width: 100%;
      }
    }

継承は個々のキーフレームごとに発生します。つまり、上の例でいうと、`to`で`border-width`を指定しなかった場合、ボーダーの太さは対象の要素に当初から適用されていた値になります。

キーフレームの定義は時間の順に並んでいる必要もありません。

宣言の内容が競合している場合、記述順に基づいて後にあるものが優先される。

### animation-name

@keyframesルールで定義した名前を参照するために使われます。

**構文**

    E { animation-name: name; }

実際の例

    div { animation-name: expand; }

<br>

**animation-duration**

**animation-timing-function**

**animation-delay**

**animation-iteration-count**

**animation-direction**

アニメーションは先頭から末尾に向かって発生しますが、この方向を反転させることもできます。

順方向が`normal`、逆方向が`alternate`


**animation-fill-mode**

**animation-play-state**

アニメーションがアクティブな状態か否かを指定するためのプロパティ

`running`なら実行中、`paused`なら実行中でないことを表す。

**短縮記法**

    E { animation: name duration timing-function delay iteration-count direction fill-mode play-state; }

実際の例

    div { animation: expand 6s ease-iin 2s 10 alternate both running; }

<br>

# 15章 Flexboxレイアウト

> <a href="http://flexboxfroggy.com/" target="_blank">Flexbox Froggy - A game for learning CSS flexbox</a>