#### プリミティブ値は、値そのものを比較

- === は同値演算子
- == は等値演算子

(ex)

```javascript
    var price1 = 10;  // プリミティブ値
    ver price2 = 20;
    ver price3 = new Number('10'); // 数値オブジェクトを生成
    ver price4 = price3;

    console.log(price1 === price2); // true

    // プリミティブ型数値 !== 数値オブジェクト
    console.log(price2 === price3); // false

    // オブジェクトは、その値そのものではなく参照によって比較されるため
    console.log(price3 === price4); // true
```

<br>

#### オブジェクトは同値判定に参照を使用

オブジェクトを比較するとき、これらは同じオブジェクトを参照している場合( = 2つのオブジェクトが同じ参照アドレスを持っている)のみ同値。

    var objectFoo = {same: 'same'};
    var objectBar = {same: 'same'};

    console.log(objectFoo === objectBar); // false

    var objectA = {foo: 'bar'};
    var objectB = objectA;

    console.log(objectA === objectB); // true


<br>

#### typeof演算子の戻り値

注意すべきなのは

**プリミティブ値**だと...

- null

**オブジェクト**だと...

- function

(ex)

    var myNull = null;

    console.log(typeof myNull); // 出力: object ...?
    /* 他のプリミティブ値はすべて対応する値を返す(undefined, string, number, boolean) */


    var myFunction = new Function('x', 'y', 'return x * y');

    console.log(typeof myFunction); // 出力: function ...?
    /* 他のオブジェクトはすべて"object"を出力する */


<br>

#### constructorプロパティの必要性

コンストラクタ関数を使ってオブジェクトを生成すると、そのインスタンスには`constructorプロパティ`が自動的に生成される。

この`constructorプロパティ`はオブジェクトを生成したコンストラクタ関数をポイントしている。

    var foo = {};
    console.log(foo.constructor === Object);  // true
    // Object()がfooを生成しているため

    console.log(foo.constructor); // Object() コンストラクタ関数が出力される

これは何が嬉しいのか？

他の人のコードを読む場合などに、何がこのインスタンスを生成したのかを確かめるときに便利な場合がある。

配列か、オブジェクトか、またはその他のものなのかを判別することができる。

<br>

プリミティブ値にも`constructorプロパティ`が存在する。

    var myFunction = new Function();
    var myFunctionL = function(){}; //リテラル宣言

    console.log(myFunction.constructor === Function);   // true
    console.log(myFunctionL.constructor === Function);  // true

<br>

#### instanceof演算子の必要性

instanceof演算子を使うと、あるオブジェクトが特定のコンストラクタ関数のインスタンスかどうかを判定することができる(返り値は`true`か`false`)

    // ユーザ定義コンストラクタ
    var CustomConstructor = function(){
      this.foo = 'bar';
    };

    // CunstomConstructorのインスタンスを生成
    var instanceOfCustomObject = new CustomConstructor();
    console.log(instanceOfCustomObject instanceof CustomConstructor); // true

    // ネイティブオブジェクトも同様に動作
    console.log(new Array('foo') instanceof Array); // true

すべてのオブジェクトはObject()を継承しているため、あるオブジェクトが`Object`のインスタンスかどうかを判定する場合、つねに`true`を返す。

**注** `instanceof`はオブジェクトにのみ使用できる。

    var str = new String('foo');
    var strL = 'bar';

    console.log(str instanceof String);   // true
    console.log(strL instanceof String);  // false

------

# オブジェクトとプロパティを扱う

#### delete演算子でプロパティを削除する

delete演算子は、指定したプロパティをオブジェクトから完全に削除することができる。

    var foo = {
      bar: 'bar'
    };

    delete foo.bar;

    console.log('bar' in foo);  // false
    // barはfooから削除されている。

- delete演算子は指定されたオブジェクトのプロパティのみを削除。プロトタイプチェーンで継承するプロパティを削除しない。
- delete演算子はオブジェクトからプロパティの存在を消す唯一の方法。プロパティに`undefined`や`null`を代入しても消えるわけではない。
- delete演算子で配列の要素を削除することもできる。その場合、指定した配列の要素は`undefined`になり、`length`は維持される。
-- 配列から要素自体を削除したいときは`splice()`を使う。

> array.splice(削除する要素のインデックス, 削除する要素の数);

    var array1 = [0, 1, 2, 3];
    delete array1[2];
    console.log(array1); // [ 0, 1, , 3 ]

    var array2 = [0, 1, 2, 3];
    array2.splice(2, 1); // [ 0, 1, 3 ]
    console.log(array2);


> <a href="http://www.gesource.jp/weblog/?p=4112" target="_blank">JavaScriptの配列の要素を削除する(delete演算子とspliceメソッド)</a>


<br>

#### プロトタイプチェーンの流れ

1. 自身のプロパティ
2. 自身のprototypeプロパティ
3. コンストラクタ関数のprototypeプロパティ
4. Object()のprototypeプロパティ
5. それでも見つからないなら`undefined`を返す


<br>

#### `for-in`ループは使うべきではない

`for-in`ループでは、対象オブジェクトのプロパティだけではなく、プロトタイプチェーンから継承したプロパティも列挙する。

`for-in`を使うときは`hasOwnProperty()`メソッドを併用すること。

    var key
      , cody = {
        age: 33
      , gender: 'male'
    };
    for(key in cody) {

      // プロトタイプチェーンから継承されたプロパティを除く
      if(hasOwnProperty(key)) {
        console.log(key);
      }
    }


------

# オブジェクト(Object())

#### プロパティ名の制限

プロパティ名が以下の条件に該当する場合は必ず文字列で指定する必要がある。

- JavaScriptの予約語である場合(classなど)
- スペース、もしくは特殊文字を含んでいる場合(英数字、_、$以外のすべての文字)
- 数字で始まる場合

    var obj = {
        name: 'honoka'
      , gender: 'famale'
      , 'group name': 'μ\'s'
    };


<br>

#### Object.prototypeに追加したものは、すべてのオブジェクトのプロトタイプチェーンに影響を与え、for-inループでも列挙対象となる

すなわち、Object.prototypeを編集することは禁止すべき。


------

# 関数 Function(())

#### 関数は常に値を返す

    var sayHi = function() {
      return 'Hi!';
    };

    console.log(sayHi()); // 出力: Hi!

戻り値を指定していない場合、**undefined**が返される。

    var yelp = function(){
      console.log('I am Honoka!');
      // return文を記述しない場合でも関数はundefinedを返す。
    };

    console.log(yelp() === undefined); // true

すべての関数は常に値を返す。

<br>

#### JavaScriptの関数は第一級関数

そもそも第一級関数とは第一級オブジェクトとして扱うことのできる関数で、第一級オブジェクトは以下のような性質を持つ。


- 変数やデータ構造を格納することができる
-- 関数は、変数、配列、オブジェクトに格納できる
- サブルーチン(関数やプロシージャ)のパラメータとして渡すことができる
-- 関数は引数として関数に渡せる
- サブルーチンの戻り値となることができる
-- 関数は戻り値にすることができる。
- 実行時に構築することができる
- 独自の存在を持つ(名前に依らない存在である)


<br>

#### arguments.callee

**callee**

実行中の関数を参照するプロパティ

このプロパティは関数のスコープ内でその関数自身を参照する場合に利用できる。

関数を再帰的に呼び出すときに便利。

    // calleeプロパティを使って関数自身を呼ぶ
    var foo = function foo() {
      console.log(arguments.callee);  // 出力: [Function: foo]  つまりこの関数の内容
      // arguments.callee() で foo() を再帰的に実行することができる。
    }();


<br>

#### 関数インスタンスのlengthプロパティとarguments.length

`arguments`オブジェクトは`length`という特殊なプロパティを持っている。

-> 実行時に関数に渡された引数の数を返す。

    var myFunction(z, s, d) {
      return arguments.length;
    }

    console.log(myFunction());  // 出力: 0

    `Function()`インスタンスのlengthプロパティにアクセスすると、関数が期待するパラメータの数を取得することができる。

    var myFunction = function(a, b, c, d, e, f, g) {
      return myFunction.length;
    }

    console.log(myFunction()); // 出力: 7


<br>

#### 関数式を即時実行

    var sayWord = function() {
      console.log('word 2 yo mo !');
    }();

    // 出力: 'word 2 yo mo !'


<br>

#### 無名関数を即時実行

    //　一番広く使われている方法

    (function(msg) {
      console.log(msg);
    })('Hi');

※注※

ECMAScript標準によると、関数はその場で実行する関数を括弧に収める必要がある。(つまり、関数を式に変更する処理が必要)


<br>

#### 関数を引数として取り、関数を取り返すことができる関数を高階関数と呼ぶ。


<br>

#### 関数は自身を呼ぶことができる(再帰)

    var countDownFrom = function countDownFrom(num) {
      console.log(num);
      num--;
      if(num < 0) {
        return false;
      }

      // 無名関数の場合でも、arguments.callee(num); を使って呼ぶことができる。(非推奨)
      countDownFrom(num);
    }

    countDownFrom(5);

-------

###　グローバル関数

- encodeURI
-- 引数として渡した文字列にURLエンコードを行い、その文字列を返す。ただし、英数字と特殊文字(; , / ? : @ & = + $ - _ . ! ~ * ' ( ) #)のエンコードは行わない。

- encodeURIComponent
-- 引数として渡した文字列にURLエンコードを行い、その文字列を返す。ただし、英数字と特殊文字(- _ . ! ~ * ' ( ))のエンコードは行わない。

<br>


#### グローバルプロパティ、グローバル変数

- var演算子を伴って変数を宣言する場合、その変数はグローバル変数となる。
- var演算子を使用しない場合はグローバルプロパティとして宣言される。


グローバルオブジェクトはスコープチェーンの最後に参照される場所。
グローバルオブジェクトを明示的に指定しない場合でも、常に暗黙的に指し示されている。

-------

### this

**this**とは、関数のスコープないで有効な値で、実行中の関数をプロパティもしくはメソッドとして保持しているオブジェクトへの参照。

#### `call()`や`apply()`を使ってthisの値をコントロールするy

関数を呼び出す際に`call()`や`apply()`を使うとこによって、その関数内のthisがポイントするオブジェクトを自分で設定することができる。

(ex: call)

    var myObject = {};

    var myFunction = function(param1, param2) {

        // 後に call() で呼ばれる際、thisはmyObjectを参照
        this.foo = param1;
        this.bar = param2;
        console.log(this); // 出力 : { foo: 'foo', bar: 'bar' }
    }

    myFunction.call(myObject, 'foo', 'bar');

    console.log(myObject); // 出力 : { foo: 'foo', bar: 'bar' }


(ex: apply)

    var myObject = {};

    var myFunction = function(param1, param2) {

        // 後に call() で呼ばれる際、thisはmyObjectを参照
        this.foo = param1;
        this.bar = param2;
        console.log(this); // 出力 : { foo: 'foo', bar: 'bar' }
    }

    myFunction.apply(myObject, ['foo', 'bar']);

    console.log(myObject); // 出力 : { foo: 'foo', bar: 'bar' }

**関数を呼び際、関数のスコープに設定されるthisの値をオーバーライドする方法であることを覚えておいてください。**


<br>

####

- new演算子を使ってコンストラクタ関数が呼ばれる場合、その関数内の`this`は**「これから生成されるオブジェクト」**を指す。

(ex)

    var Person = function(name) {
        this.name = name || 'Honoka Kosaka';
    }

    var umi = new Person('Umi Sonoda');

    console.log(umi.name);  // 出力 : Umi Sonoda

- new演算子を使わない場合は`this`はPerson関数が呼び出されるコンテクストに依存する。

(ex)

    var Person = function(name) {
        this.name = name || 'Honoka Kosaka';
    }

    var umi = Person('Umi Sonoda');

    // umi.name はundefinedのため、エラーが発生
    console.log(umi.name);

    // この場合のthisはグローバルオブジェクトのため、渡した引数はwindow.nameに格納される
    console.log(window.name); // 出力 : Umi Sonoda


<br>

#### プロトタイプメソッド内のthisは生成されるインスタンスを参照する。

- コンストラクタの`protptype`に追加された関数を実行する際、そのプロトタイプ関数内の`this`はメソッドが呼ばれたインスタンスを参照する。

-------

### スコープとクロージャ

#### スコープは関数実行時ではなく関数定義時に決められる。

これは、**静的スコープ**とも呼ばれる。

スコープチェーンは関数を呼び出す前に生成される。
そのため、クロージャを生成することができる。

　クロージャを一言で表すと、**「スコープチェーンに存在する変数への参照を保持している関数」**です。


-------

### 関数のprototypeプロパティ

Function()コンストラクタを明示的に呼び出していない場合であっても、すべての関数はFunction()コンストラクタによって生成される。

そして、関数インスタンス生成時には、その関数にprototypeプロパティにからのオブジェクトが自動的に与えられる

    var myFunction = function() {};
    console.log(myFunction.prototype);  // 出力 : object{}
    console.log(typeof myFunction.prototype);   // 出力 : 'object'

**prototypeプロパティはFunction()コンストラクタによって生成されるものだということをしっかり理解すること**


関数生成時のprototypeプロパティの生成を明示的に表現してみると、

    var myFunction = function() {};
    myFunction.prototype = {};  // prototypeプロパティを追加し、値に空のオブジェクトを設定
    console.log(myFunction.prototype);  // 空のオブジェクトを出力　(出力 : {})

上の例は、いつもJavaScriptが行っていることを複写しただけ。


<br>

#### コンストラクタ関数から生成されたインスタンスはそのコンストラクタのprototypeプロパティにリンクする

    Array.prototype.foo = 'foo';    // Array() のすべてのインスタンスがfooプロパティを継承
    var myArray = new Array();

    // *.constructor.prototypeを使用する(冗長な)方法でfooの値をトレース
    console.log(myArray.constructor.prototype.foo); // 出力 : 'foo'

    // 当然プロトタイプチェーンを使うこともできる
    console.log(myArray.foo);   // 出力 : 'foo'

myArray.__proto__(もしくは myArray.constructor.prototype)はArray.prototypeの値を参照している。


<br>

#### プロパティチェーンの終着点はObject.prototype

辿る順番は

1. myArrayオブジェクトのプロパティ
1. Array.prototypeのプロパティ
1. Object.prototypeのプロパティ
1. それでも見つからないなら　undefined


<br>

#### プロトタイプチェーンは最初に見つけたプロパティを返す


<br>

#### 継承チェーンを生成する

次の例は、あるChef()インスタンスにおいてプロパティが見つからなかった場合、次にPerson()オブジェクトに探しにいくよう、Chef()オブジェクトにPerson()を強制的に継承させている。

強制的に継承をつなげるには、Personのインスタンスを`Chef.prototype`の値に設定するだけです。

    Chef.prototype = new Person();

ex)

    var Person = function() {
        this.bar = 'bar';
    };
    Person.prototype.foo = 'foo';

    var Chef = function() {
        this.goo = 'goo';
    };
    Chef.prototype = new Person();
    var cody = new Chef();

    console.log(cody.foo);
    console.log(cody.bar);
    console.log(cody.goo);

この例は、ネイティブオブジェクトと同じ仕組みを利用したもの。

例えば、Object()コンストラクタ関数によって生成されたオブジェクトは、`Object.prototype`に格納された空のオブジェクトを参照(継承)します。

上の例では、`Chef()`コンストラクタ関数のprototypeに`Person()`インスタンスオブジェクトを代入し、Chef()の各インスタンスが`Person.prototype`オブジェクトを継承するようにしている。

つまり、**デフォルトの`Chef.prototype`が`Person()`インスタンスに置き換えられ**、プロトタイプチェーンは`Person.prototype`を経由する。

-------

### Array

#### 配列のlengthを設定して要素の追加・削除

lengthプロパティに値を設定することにより配列の長さを再設定することができる。

lengthに配列が現在持っている要素の数よりも**大きい数を指定**する -> その分だけ`undefined`値を持った要素を追加する

現在持っている要素の数よりも**少ない数を設定**すると、新しいlengthの数になるまで配列の後ろから要素を削除する。


    var myArray = ['blue', 'red', 'green', 'orange'];
    console.log(myArray.length);    // 4
    myArray.length = 99;
    console.log(myArray.length);    // 99

    myArray.length = 1;     // myArray.splice(1) is equaled. インデックス1以降の要素はすべて削除される
    console.log(myArray[1]);
    console.log(myArray);

------


### String

String のインスタンスメソッド

- match(regexp)
-- 引数として渡した正規表現とのマッチングを試みる。1つ以上マッチする場合は結果を配列で返し、マッチしない場合は`null`を返す。

- search(regexp)
-- 引数として渡した正規表現とのマッチングを試みる。マッチする場合はインデックスを返し、マッチしない場合は-1を返す。

- slice(start, end)
-- startとendで指定した位置の範囲の文字列を返す。元の文字列は変更されない。

- substr(start, length)
-- startで指定した位置からlengthで指定した長さの文字列を返す。元の文字列は変更されない。

-------

### null

`undeifned`と`null`を混同しないように

プロパティは設定されているものの、何らかの理由で値を使用することができない場合は、プロパティが空の値を持っていることを明示するために、`undeifned`ではなく`null`を使うべきです。

- undefined
-- 存在自体が設定されていないことをJavaScriptが伝えるための値

- null
-- 値は利用可能ではないが、今後利用できる見込みが存在する場合に使用する。


<br>

#### typeof null は'object'を返す

そのまま

------

### undeifned

undefinedを変数に代入する

    foo = undefined; など

のは避けて、JavaScriptだけがundefinedを使うようにするべきです。

変数やプロパティがその時点で利用不可能であることを指定するには、`undefined`ではなく`null`を使うべきです。

-------

### まとめ

- プリミティブ型の変数を同値比較(===)する場合は、それらの値が同じであれば同値とみなされる。一方、オブジェクトを同値比較する場合は、同じ参照型を持っている場合のみ同値とみなされる。つまり、オブジェクトを参照している場合のみ同値とみなされる。

- スコープチェーンは関数定義時に生成される。これは関数が実行されるコンテクストと一致するとは限らない、そのため呼ばれる場所が異なっていても、関数には本来記述されたスコープへのアクセスを持たせることができる。これは**クロージャ**と呼ばれる。