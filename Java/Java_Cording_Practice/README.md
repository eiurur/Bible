# 実践 Javaコーディング作法

## 第1章 命名に関する規約

### パッケージ名はすべて小文字にする


- パッケージ名の英単語は単数形

**Good**

    com.eiurur.myapp.dashboard

**Bad**

    com.eiurur.myapp.ZZL050
    com.eiurur.myapp.libraries

### パッケージ名は省略語を使用しない

- パッケージ名にはフルスペルの単語を使用する

**Good**

    com.eiurur.myapp.dashboard

**Bad**

    com.eiurur.myapp.dbrd

### 抽象クラスの名前には「Abstract」を付ける

**Good**

    abstract class SlipAbstract {

    }

    abstract class AbstractSalesOrderController {

    }

    public class SalesOrderController extends AbstractSalesOrderController {

    }

抽象メソッドと実装を持つメソッドの両方を備える抽象クラスは、このGoFのデザインパターンに則ったクラス設計や実装を行う際に必要なクラスです。

特に

- Abstract Factory
- Factory Method
- Bridge
- Decorator
- Template Method

などの実装で抽象クラスを使用します。

これらのデザインパターンの適用時に定義する抽象クラスの命名には、「Abstract」という単語を含めます。

### メソッドの役割の対称性を意識する

役割や機能が正反対になったメソッドを定義する場合は、使用する英単語の対称性を意識して命名してください。

**Good**

    addDetail
    removeDetail

【対義語例】

- add / remove
- start / stop
- first / last
- up / down
- open / close
- old / new
- insert / delte
- begin / end
- get / release
- show / hide
- lock / unlock
- next / previous
- get / set
- send / receive
- put / get
- source / target
- source / destination
- increment / decrement

### ゲッターメソッド名は「get + 属性名」とする

- ただし、boolean型の属性に対するゲッターの場合は、true/falseの識別が分かる名前にする

**Good**


    public SalesOrderSlip getSalesOrderSlip() {

    }

- インスタンスメソッドは極力`public`にしない。そのアクセス用にゲッター、セッターを定義する。
- すべてのインスタンス変数にセッターメソッドの定義が必要なわけではない。
  - オブジェクトの内部的かつ一時的な状態の区分を保持するための属性や、ステートフルなオブジェクトにおける前回の処理結果やUndo情報を保持するための属性にはゲッターを容易せず、`public`にすることを避けます。
- 定数を保持するフィールド(メンバ変数)の値をオブジェクトの外部から参照可能にするためには、定数用のゲッターを用意せず、定数の修飾を`public`にします。

### セッターメソッド名は「set + 属性名」とする

**Good**

    public void setSalesOrderSlip (SalesOrderSlip slip) {
      :
      salesOrderSlip = slip;
    }

    public void setCustomerName (String customerName) {
      :
      this.customerName = customerName; // 自動生成したセッターには、thisが埋め込まれる。そういったケースを特別とし、通常は推奨されない
    }

- インスタンスメソッドは極力`public`にしない。そのアクセス用にゲッター、セッターを定義する。
- セッターメソッドでは引数で渡された値のチェックロジックを実装したり、値の設定時の副作用(例えば以前の値を別の属性に退避させるなど)を実装したりする場合があります。
- すべてのインスタンス変数にセッターメソッドの定義が必要なわけではない。
  - オブジェクトの内部的かつ一時的な状態の区分を保持するための属性や、ステートフルなオブジェクトにおける前回の処理結果やUndo情報を保持するための属性にはゲッターを容易せず、`public`にすることを避けます。
- 引数とフィールド名が同じ名前の場合は、引数がフィールドを隠蔽してしまいます。
  - この衝突を回避するために引数側の名前をフィールド名と異なるものにするか、あるいは隠蔽を回避するためにフィールド名に「this」をつけてアクセスするかの対策を行います。

### booleanを返すメソッドはtrue/falseの識別がわかる名前にする

- isEmpty()
  - 空なら`true`を返す
- canRemove()
  - 削除できるなら`true`を返す
- hasChanged()
  - 変更があったなら`true`を返す
- exists()
  - 存在すれば`true`を返す
- existsStock()
  - 在庫があれば`true`を返す

他にも、`boolean`をよく返す慣用句がある

メソッドの先頭単語が`check`、`test`、`update`、`insert`、`commit`、`send`などになるメソッド

これらの単語を使ったメソッドは`true`(成功)または`false`(失敗)を返す。

### boolean型の変数はtrue/falseの識別がわかる名前にする

変数もメソッドと同じ。

- is + 形容詞
- can + 動詞
- has + 過去分詞
- 三単現動詞 + 名詞

いずれかのパターンで命名する。

### 引数名とフィールド名が同じになることを回避する

**Good**

    private String itemCode;
    private int quantity;
    public void reserveStock(String anItemCode, int aQuantity) {
      itemCode = anItemCode;
      quantity = aQuantity;
    }

**Bad**

    private String itemCode;
    private int quantity;
    public void reserveStock(String str, int quantity) {
      itemCode = str;
      quantity = quantity;
    }


- 引数に`a`や`an`の冠詞をつけて衝突を回避するほうが、一目で引数であることがわかり、メソッドの可読性が高いと言える。
- 冠詞ではなく、`_`を引数名の前あるいは後に付加する方法は推奨しません。


## 第2章 コーディングに関する規約

![](http://f.st-hatena.com/images/fotolife/p/pema/20131015/20131015212214_original.gif)