変数名は常にキャメルケースにして、名詞で始まるようにすべきです。変数を関数と区別する時に役立ちます。

関数は動詞で始めるべきです。

    //Good
    var count = 10;
    var found = true;

    // Bad
    var getCount = 10;
    var isFound = true;


null
---

`null`は、ほんのいくつかのケースでのみ使われる。

- 変数を初期化するとき(変数にはその後オブジェクトの値が代入されないかもしれない)
- 初期化された変数と比較するとき(オブジェクトの値を持つかもしれないし持たないかもしれない)
- 関数に渡す値としてオブジェクトが期待されているとき
- 関数の返り値としてオブジェクトが期待されているとき

`null` を使うべきでないケース

- 引数が渡されたかどうかをテストするときには`null`は使わない
- 初期化されていない変数と値`null`の比較テストをしない

    // Good
    var person = null;

    // Good
    function getPerson() {
      if (condition) {
        return new Person("Nicholas");
      } else {
        return null;
      }
    }

    // Good
    var person = getPerson();
    if (person !== null) {
      doSomething();
    }

<br>

undefined
------

初期化されていない変数は、undefinedという初期値を持ちます。
これは変数に実際の値が代入されるのを待っていることを意味します。

    // Bad
    var person;
    console.log(person === undefined) //true

これは動きますが、コードでのundefiendの仕様は避けることを推奨します。

理由

    // fooは宣言されていない
    var person;
    console.log(typeof person) //undefined
    console.log(typeof foo) //undefined

(文でfooを使おうとするとエラーが発生しますが、personを使おうとしてもエラーにはなりません)

typeofが`undefined`を返すときの意味を、1つのケース、つまり変数が宣言されていない時だけに限定しましょう。

後でオブジェクトの値が代入されるかもしれないし、されないかもしれない変数を使うときには、nullで初期化しておきましょう。

    // Good
    var person = null;
    console.log(person === null); //true

変数にnullを設定して初期化すれば、この変数にはいつかオブジェクトが代入されるべき、という変数の意図が示されます。

typeof演算子にはnull値に対して「object」を返すので、undefinedと区別できます。

<br>

オブジェクトリテラル
------

    // Bad
    var book = new Object();
    book.title = "aaa";
    book.author = "iii";

    // Good
    var book = {
      title: "aaa",
      author: "iii"
    };


<br>


文と式
---

波括弧は必ず使え。

    // Bad
    if (condittion) doSomething();

    // Good
    if (condition) {
      doSomething();
    }

    // Bad
    if (condition) { doSomething(); }


<br>

for-in
---

for-inループの問題は、オブジェクトのインスタンスプロパティだけでなく、プロトタイプで継承したプロパティも全て返すので、期待に反した結果で終わるかもしれない。

for-inループでは必ず`hasOwnProperty()`を使ってフィルタするのがベスト。

    var prop;

    for (prop in object) {
      if (object.hasOwnProperty(prop)) {
        console.log("name: " + prop);
        console.log("value: " + object[prop]);
      }
    }

結論 : for-inは使うな


<br>

即時関数
---

    // Bad
    var value = function() {

      // 関数本体

      return {
        message: "Hi"
      }
    }();

この例では、関数が即座に呼び出されるので、valueには最終的にオブジェクトが代入されます。

このパターンの問題は、変数への無名関数の代入と非常に似ている点です。

最後の行までみて括弧があるか確認しないと、それが無名関数の即時呼び出しだとわかりません。

    // Good
    var value = (function() {

      // 関数本体

      return {
        message: "Hi"
      }
    }());

<br>

等価性
---

    // 数値の1とtrue
    console.log(1 == true) // true
    console.log(1 === true) // false

    // 数値の0とfalse
    console.log(0 == false) // true
    console.log(0 === false) // false


---

varもとい変数宣言は1つ。

初期化されてない変数はあと。

<br>

関数式はbad

他の関数の内部で関数を宣言するときは、var文の直後で宣言


無名関数の括弧の位置に注意。即座に呼び出される関数は、関数呼び出し全体をカッコで囲む。

    // Good
    var value = (function() {

      return ~

    }());

    // Bad
    var value = (function() {

      return ~

    })();