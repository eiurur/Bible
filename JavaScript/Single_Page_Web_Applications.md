アプリケーションを構築していく部分(本編)はさらっと読み飛ばした。

Node.jsやJavaScriptの紹介の章の説明が非常にわかりやすく、そっちがメインのように感じた

-------

# JavaScriptのおさらい



### プロトタイプベース

    // プロトタイプオブジェクトを定義する
    var proto = {
      sentence: 4,
      probition: 2
    };

    // オブジェクトコンストラクタを定義する
    var Prisoner = function(name, id) {
      this.name = name;
      this.id = id;
    };

    // コンストラクタをプロトタイプに関連付ける
    Prisoner.prototype = proto;

    // オブジェクトをインスタンス化する
    var syaro = new Prisoner('syaro', '4Y');
    var chiya = new Prisoner('chiya', '5G');

<br>

new演算子を使う代わりとして、`Object.create`メソッドが開発され、JavaScriptオブジェクト生成にプロトタイプベースの感覚を付け加えるのに使われている。

### Object.createを使ったオブジェクトの生成

    var proto = {
      sentence: 4,
      probation: 2
    };

    var syaro = Object.create(proto);
    syaro.name = "Syaro";
    syaro.id = "4Y";

    var chiya = Object.create(proto);
    chiya.name = "Chiya";
    chiya.id = "5G";

    console.log(syaro);
    console.log(chiya);

`Objejct.create`は引数としてプロトタイプを取り、オブジェクトを返す。

**メリット**

この方法では、プロトタイプオブジェクトに共通の属性とメソッドを定義し、それを使って同じ特性を共有する多数のオブジェクトを作成できる。

**デメリット**

`name`と`id`を個々に手動で設定するにはコードを繰り返さなければならない

**結論**

あまり洗練された方法ではない。


<br>

### 最終オブジェクトを生成して返すファクトリ関数を使う方法

    var proto = {
      sentence: 4,
      probation: 2
    };

    var makeWaitress = function(name, id) {

      var waitress = Object.create(proto);
      waitress.name = name;
      waitress.id = id;

      return waitress;
    }

    var syaro = Object.create('Syaro', '4Y');
    var chiya = Object.create('Chiya', '5G');

    console.log(syaro);
    console.log(chiya);

多分、これがベストプラクティス。

しかし、new演算子がオブジェクトを生成するのに最も一般的に使われる方法。

**互換性**

`Object.create`はIE9以降、Fx 4以降、Safari 5以降、chrome 5以降で動作する。

IE8以前のときは、別途宣言して、上書きする。

// コード

<br>

### JavaScriptにプライベート変数をもたらす


    var prison = (function() {
      var prison_name = 'Syaro'
        , jail_term = '15 years term'
        ;

      return {
        prisoner: prison_name + ' - ' + jail_term,
        sentence: jail_term
      };
    })();

    // Undefiend
    console.log(prison.prison_name);

    // それぞれの値が出力される
    console.log(prison.prisoner);
    console.log(prison.sentence);

変数`prison`には無名関数が格納されない。無名関数は実行されたので、無名関数の戻り値が変数`prison`に格納される。

また、`prison.prisoner`はいくつかの理由から更新されない。

`prisoner_name`と`jail_term`が変更できない理由は

- prison変数に格納された実行コンテキストの変数であり、この関数の実行がすでに完了しているのでこの実行コンテキストはもはや存在しない。
- このような属性は無名関数の実行時に一度設定され、決して更新されない。

なので、`prisoner_name`と`jail_term`を変更するには、呼び出すたびに変数にアクセスするメソッドに属性を変更する必要がある。


    var prison = (function() {
      var prison_name = 'Syaro'
        , jail_term = '15 years term'
        ;

      return {
        prisoner: function() {
          return prison_name + ' - ' + jail_term;
        },
        setJailTerm: function(term) {
          jail_term = term;
        }
      };
    })();

    // Undefiend
    console.log(prison.prison_name);

    console.log(prison.prisoner());
    console.log(prison.setJailTerm( 'Sentence commuited' ));
    console.log(prison.prisoner());


### クロージャ

JavaScriptはガベージコレクションを採用している。
単純なメモリ管理方式では、関数の実行が完了するとその関数内で作成されたすべてをメモリから削除するだろう。
関数の実行が完了しているので、その実行コンテキスト内のものにはもはやアクセスする必要はないと思われる。

    var prison = (function() {
      var prisoner = 'Josh Powell';

      return {
        prisoner: prisoner
      };
    })();

    console.log(prison.prisoner);


文字列Josh Powellはprison.prisonerに格納されており、prisoner変数にもうアクセスできないので、このモジュールのprisoner変数をメモリに保持する理由はない。

prison.prisponerの値は文字列Josh Powellである。prisoner変数を指していないのである。

    var prison = (function() {
      var prisoner = 'Josh Powell';

      return {
        prisoner: function() {
          return  prisoner
        }
      }
    })();

    console.log(prison.prisoner());

これで、prison.prisonerを実行するたびにprisoner変数にアクセスする。prison.prisoner()はprisoner変数の現在の値を返す。

ガベージコレクタが登場してメモリを削除した場合、prison.prisonerを呼び出すと、Josh Powellの代わりにundefinedを返す。

**クロージャとはなにか？**

クロージャは、変数を作成した実行コンテキスト外から変数にアクセスできるようにしておくことで、**ガベージコレクタがメモリから変数を削除しないようにするプロセス**である。

現在の実行コンテキスト外からのprisoner変数への動的アクセスを伴う関数を格納することで、クロージャが作成される。

**まとめ**

- JavaScriptはガベージコレクタを採用
- アクセスのできない不要な変数は即開放。
- 変数にアクセスするための関数を格納することで、メモリに保持。


クロージャは、関数の中の関数
クロージャは、現在の実行コンテキストの変数にアクセスする関数を現在の実行コンテキスト外の変数に保存して作成する。

<br><br>


# データベース


<br>

## MongoDBを選ぶ理由

<br>


**データ変換がない**

MySQL/Ruby on RailsとJavaScriptで書かれた従来のWebサーバアプリケーションだと、開発者は、クライアントに対して`SQL->Active Record->JSON`に変換し、サーバに対して`JSON->Active Record->SQL`に変換するコードを書く必要がある。

3つの言語(SQL、Ruby、JavaScript)、3つのデータフォーマット(SQL、Active Record、JSON)、4つの変換処理があることになる。

一方。MongoDB、Node.js、JavaScriptだと、データマッピングは、クライアントに対して`JSON->JSON->JSON`、サーバに対して`JSON->JSON->JSON`になる。

1つの言語(JavaScript)、1つのデータフォーマット(JSON)で済み、データ変換は発生しない。これによって、従来のシステムが非常に簡潔なものになる。


<br>

**スケーラブルで高性能**

MongoDBは、あまり高価でないサーバを使って、水平にスケールするように設計されている。RDBの場合、簡単にスケールさせるには、より高価なハードウェアを購入するしかない。MongoDBの場合。、サーバを追加することで簡単に能力と性能を強化できる。


<br>

**動的スキーマ**

RDBは、どんなデータが表に格納可能かを定義するためにスキーマが必要になるが、MongoDBではスキーマは必要ない。

コレクションには任意のJSONドキュメントを格納できる。


<br><br>


# キャッシュ

<br>


おおまかに分類すると以下の4つ。

- Webストレージ
- HTTPキャッシング
- サーバキャッシング
- データベースキャッシング

<br>


## キャッシングの機会

<br>


**Webストレージ**

クライアントに文字列を格納し、アプリケーションからアクセスできる。

(ex)

localStorage、sessionStorage

<br>


**HTTPキャッシング**

サーバからのレスポンスを格納するクライアント側のキャッシング。

<br>


**サーバキャッシング**

処理済みのサーバレスポンスをキャッシングするのに使うことが多い。

さまざまなユーザのためのデータを格納できる最初のキャッシング形式。

(ex)

Redis、Memcached

<br>

**データベースキャッシング(クエリキャッシング)**

データベースがクエリの結果をキャッシュするのに使うので、以降の同じクエリではデータを再び集める代わりにキャッシュを返す。


<br>

## 実装の簡単さ

HTTPキャッシング　= データベースキャッシング > Webストレージ = サーバキャッシング


<br><br>

![caching](https://dl.dropboxusercontent.com/u/31717228/images/SPA/20150110_171035581_iOS.jpg)

図 キャッシングでのリクエスト / レスポンスサイクルの短縮



<br>


## HTTPキャッシング

HTTPキャッシングを使うと、サーバレスポンスをクライアントに格納し、何度もサーバを往復しなくてよくなる。HTTPキャッシングが従う手順には2つのパターンがある。

1. サーバで鮮度を調べずにキャッシュから直接提供する。
1. サーバで鮮度を調べ、最新であればキャッシュから提供し、古ければサーバレスポンスを提供する。

HTTPキャッシングでは、くクライアントにサーバから送信されたレスポンスのヘッダを調べさせる。クライアントが探す属性には`max-age`、`no-cache`、`last-modified`の3つの主要な属性がある。この属性はそれぞれ、データのキャッシュ期間をクライアントに告げる働きをする。


<br>

**max-age**

最速のデータアクセス方法だけど、危険だから使うのはやめとけ。とりあえず`max-age`属性の設定は0だ。

0に設定すると、クライアントは必ずサーバを調べて、コンテンツがまだ有効であることを確認する。


<br>

**no-cache**

no-cache属性は`max-age=0`と同様に機能する。が、微妙に違う。

no-cache属性はキャッシュのデータを使う前にサーバで再検証するようにクライアントに通知するが、中間サーバが警告メッセージを受けたとしても古いコンテンツを提供できないようにもする。

ただ、色々経緯があって、なんやかんやあった結果、no-cacheヘッダ付きでロードされたリソースが非常に遅くなるようになった。

クライアントにリソースをキャッシュさせないことが望みの振る舞いならば、代わりに`no-store`属性を使うべきだ。


<br>

**no-store**

`no-store`属性は、クライアントや中間サーバに、そのリクエストやレスポンスに関する情報をキャッシュに**決して格納しない**ように通知する。

これは伝送のプライバシーを改善するのに役立つが、決kして完璧なセキュリティ方式ではない。適切に実装されたシステムでは、データの追跡が全くなくなる。


<br>

**last-modified**

`Cache-control`が設定されていない場合、クライアントは`last-modified`日付に基づいたアルゴリズムを頼りに、データのキャッシュ機期間を決める。これに委ねろ。

他にもキャッシュを扱う属性がたくさんあるが、`max-age`、`no-cache`、`no-store`、`last-modified`を修得するとアプリケーションのロード時間が大幅に速くなる。


しかし、他のクライアントが同じリクエスト行った場合、HTTPキャッシングは役に立たない。代わりに、データをサーバでキャッシングする必要がある。


<br>


## サーバでキャッシング

クライアント側からの動的データのリクエストにサーバが最も高速に応答するための方法は、キャッシュから提供する方法である。

データをサーバにキャッシュする一般的な方法は、`Memcached`と`Redis`である。

キャッシュの全般的な目的は、サーバ負荷の削減とレスポンス時間の高速化。

サーバキャッシングは、SPAに行き過ぎている。

では、どのようなときにWebアプリケーションへのサーバキャッシングの追加を検討すべきか？

データベースやWebサーバがボトルネックになりつつあることが判明したときだ。


<br>


## データベースクエリキャッシング

クエリキャッシングは、データベースが特定のクエリの結果をキャッシュするときに生じる。

MongoDBでは、OSのファイルシステムを使って自動的にクエリキャッシングに対応してくれる。

特定のクエリの結果をキャッシュする代わりに、MongoDBにはインデックス全体をメモリに保持しようとする。


