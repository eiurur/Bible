スッキリわかるJava入門
======


## 9章

### 9.1.9 クラス型をメソッド引数や戻り値に用いる

    public class Wizard {
      String name;
      int hp;
      void heal(Hero h) {
        h.hp += 10;
      }
    }

### 9.2.7 ほかのコンストラクタを呼び出す

**コンストラクタの中から別のコンストラクタを呼び出す(エラー)**

    public class Hero {
      :
      // コンストラクタ①
      Hero(String name) {
        this.hp = 100;
        this.name = name;
      }

      Hero() {
        this.Hero("ダミー"); // コンストラクタ①を呼び出したいが、この行はエラーになるのでダメ
      }
    }

- エラーがでる原因
-- Javaではコンストラクタを直接、呼び出すことができないからです。

この制限には例外があり、それは**「コンストラクタの先頭で、別のコンストラクタを専用の文法を用いて呼び出す場合に限って」特別に許される**ということ。


#### 別コンストラクタの呼び出しに関するルール

`this.クラス名(引数);`の代わりに、`this(引数);`と記述する

**コンストラクタの中から別のコンストラクタを呼び出す(正常に動作)**

    public class Hero {
      :
      // コンストラクタ①
      Hero(String name) {
        this.hp = 100;
        this.name = name;
      }

      Hero() {
        this("ダミー");　
      }
    }


<br>

### 10.3.2 単純にフィールド値を取り出すだけのメソッド

フィールドはメソッドを経由してアクセスする。

    public class King() {
      void talk(Hero h) {
        System.out.println("ようこそ我が国へ、勇者" + h.getName() + "よ。");
      }
    }

### 10.3.5 getter / setterの存在価値

setterで「設定されようとしている値が妥当かを検査する」こともJavaプログラミングの定石です。

**setterメソッドの中で値の妥当性をチェックする**

    private String name;
    public void setName(String name) {
      if(name == null) {
        throw new IllegalArgumentException("名前がnullである。");
      }
      if(name.length() <= 1) {
        throw new IllegalArgumentException("名前が短すぎる");
      }
      if(name.length >= 8) {
        throw new IllegalArgumentException("名前が長すぎる。");
      }
      this.name = name;
    }


<br>

### 11.1.7 継承やオーバーライドの禁止

    public final classn String extends Object...

Javaでは、このような**宣言時にfinalが付けられているクラスは継承できない**ことになっています。

もし、クラスの継承は許可するものの、一部のメソッドについてのみオーバーライドが禁止したい場合は、そのメソッドの宣言にfinalをつけてください。

**宣言にfinalがつけられたメソッドは、子クラスでオーバーライドができない**ことになっています。

    public class Hero {

      // 子クラスでオーバーライド禁止
      public final void slip() {
        this.hp -= 5;
      }

      // 子クラスでオーバーライド可能
      public void run() {
        //
      }
    }

### 11.2.2 メソッドの呼び出し


### 11.2.3 親インスタンス部へのアクセス

#### SuperHeroの追加使用

> SuperHeroは、空を飛んでいる状態で`attack()`するとHeroへは1回だった攻撃を2回連続で繰り出すことができる

この条件に従うために、次のようなオーバーライドを思いつくかもしれません。

    public class SuperHero extends Hero {
      :
      public void attack(Matango M) {
        m.hp -= 5;
        if(this.flying) {
          m.hp -= 5;
        }
      }
    }

しかし、この方法では、将来Heroクラスのattack()メソッドの中身が変わった場合に困る事態に陥ります。

たとえば、Heroクラスのattack()メソッド内部が修正されたとしましょう。SuperHeroインスタンスを生み出し、fly()を呼び出し後でattack()を呼び出したらどうなるでしょうか？

> 「5ポイントダメージの攻撃が2回」のまま

このような場合には、子インスタンスが内部で親インスタンスを呼び出せばいいのです。

    public class SuperHero extends Hero {
      :
      public void attack(Matango m) {
        super.attack(m);  // 親インスタンス部のattack()を呼び出し
        if(this.flying) {
          super.attack(m);  // 親インスタンス部のattack()を呼び出し
        }
      }
    }

superとは、「親インスタンス部」を表す予約語です。これを利用すれば親インスタンス部のメソッドやフィールドに子インスタンス部からアクセスできます。

### 11.3.1 継承を利用したクラスの作られ方

Javaでは**「すべてのコンストラクタは、その先頭で必ず内部インスタンス部(=親クラス)のコンストラクタを呼び出さなければならない」**というルールになっています。

    public class SuperHEro() {
      super();
      //
    }

もしプログラマがコンストラクタの一行目に`super();`を書いていない場合、**`super();`という行が自動的に挿入**されます。

### 11.3.2 親インスタンス部が作れない状況

    public class Item {
      private String name;
      private int price;

      public Item(String name) {
        this.name = name;
        this.price = 0;
      }

      public Item(String name, int price) {
        this.name = name;
        this.price = price;
      }
    }

    public class Weapon extends Item {

    }

    public class Main {
      public static void main(String[] args) {
        Weapon w = new Weapon();
      }
    }

上のコードではエラーが発生します。

#### 理由

自動生成されたWeaponクラスのコンストラクタは、親クラスItemのコンストラクタを引数なしで呼ぼうとするため。

すなわち、こう直す必要がある。

    public class Weapon extends Item {
      public Weapon() {
        super("ななしの剣");
      }
    }

### 11.4.1 is-aの原則

- 正しい継承とは、**「is-aの原則」といわれるルールに則っている継承**のことです。

#### is-aの関係

子クラスis-a親クラス(子クラスは、親クラスの一種である)

A is-a Bは日本語で「AはBの一種である」という意味であり、この文書が意味的に自然であれば正しい継承です。

スーパーヒーローは「特殊能力を持った特別なヒーロー」ですが、あくまでヒーローの一種であることには間違いありません。

### 11.4.2 間違った継承の例

現実世界の登場人物同士に**概念としてis-aの関係がないにも関わらず継承を使ってしまうのが「間違った継承」**です。

    public class Item {
      private String name;
      private int price;
    }

    public class House extends Item {
      //
    }

上のようにItemクラスを継承してHouseクラスを作ることは原理上、可能です。実際、名前と値段のフィールドも継承され、問題なく動作します。

ですが、「House is-a Item(家はアイテムの一種である)」という文章には違和感を覚えませんか？勇者は冒険のために家を持ち歩くことはありません。

このように「フィールドやメソッドが流用できるから」という安易な理由で継承をしてはいけません。「動くか動かないか、便利か便利でないか」ではなく、**is-aであるかに基づいて**、継承は利用すべきです。

### 11.4.3 間違った継承が良くない理由

is-aの関係ではない継承を使ってはならない理由は2つあります。

- 将来、クラスを拡張していった場合に現実世界との矛盾が生じるから。
- オブジェクト指向の3大機能の最後の1つ「多態性」を利用できなくなるから

たとえば、アイテムは的に投げつけてダメージを与えることができるとしましょう。そこで、Itemクラスに「敵を投げつけたときにあたえるダメージを返すメソッド」、getDamage()を追加します。

    public class Item {
      :
      public int getDamage() {
        return 10;
      }
      :
    }

このメソッドは継承されHouseクラスでも利用可能になりますが、現実に沿って考えると**家を投げることなどできるわけがありません**し、そのダメージを算出する、getDamage()メソッドがHouseクラスに対して呼べること自体が極めて不自然です。

<br>

### 12.3.1 安全な「継承の材料」を実現するために

継承の材料となるクラスを作ろうとすると問題になる2つの不都合と3つの心配事について学びました。

- **不都合A**: 「詳細未定メソッド」の存在
  - **第一の心配事**: オーバーライド忘れ
  - **第二の心配事**: 「何もしない」との区別
- **不都合B**: 自由に選べる2つの利用法
  - **第三の心配事**: extends用クラスをnewされる

### 12.3.2 詳細未定メソッド専用の書き方

#### 第2の心配事

> 空のメソッドを作っておくと「現時点で中身を確定できないメソッド」なのか「何もしないメソッド」なのか区別がつかない。

#### 詳細未定メソッド(抽象メソッド)の宣言

> アクセス修飾子 abstract 戻り値 メソッド名(引数リスト);

    public class Character {
      :
      public abstract void attack(Matango m);
      :
    }

abstractをメソッドの宣言に付けることで「attack()というメソッドは少なくとも宣言はすべきなのだが、具体的にどう動くか、中身がどうなるかまでは現時点では確定できないので、メソッド内部の処理はここでは記載しない」という表明になります。

#### 第2の心配事は解決！

空メソッドは「何もしないメソッド」

抽象メソッドは「何をするか現時点で確定できないメソッド」

### 12.3.3 未完成のためにnewしてはいけないクラスの宣言

第三の心配事

> extends用のクラス(未完成部分を含む)を誤ってnewされる可能性がある。

Javaでは「未完成部分(=抽象メソッド)を1つでも含むクラス」は、次の構文に従って宣言しなければならないことになっています。

#### 1つでも抽象メソッドを含むクラスの宣言

    アクセス修飾子 abstract class クラス名 {
      :
    }

たとえば

    public abstract class Character {
      String name;
      int hp;

      public void run() {
        //
      }

      public abstract void attack(Matango m);
    }

このようにabstractが付いたクラスは特に**抽象クラス**と呼ばれます。

#### 抽象クラスの制約

**抽象クラスは、newによるインスタンス化が禁止される**

たとえば、次のコードはコンパイルに失敗します。

    Character c = new Character();

    // Characterはabstractです。インスタンスを生成することはできません。

#### 第2の心配事は解決！

抽象クラスとして宣言すれば、間違ってもnewされることはない。

### 12.3.4 オーバーライドの強制

#### 第1の心配事

> 未来の開発者が、詳細未定メソッドをオーバーライドし忘れる可能性がある。

    public class Dancer extends Character {
      public dance() {
        //
      }
    } // attackをオーバーライドし忘れている

「未完成状態のクラスであるDancerは、abstractを付けて抽象クラスにしなければならない」というエラーメッセージが表示され、コンパイルは失敗します。

    public class Dancer extends Character {
      public dance() {
        //
      }

      // 親から継承した「詳細未定のattack()」を上書く
      public void attack(Matango m) {
        //
      }
    }

#### 第1の心配事も解決！

メソッドを抽象メソッドとして宣言すれば、未来の開発者にオーバーライドを強制できる。

<br>

以上で3つの心配事がすべて解決しました。抽象クラスと抽象メソッドを用いることで、未来の開発者が安全かつ便利に利用できる「継承の材料」となるクラスを開発できるのです。

### 12.3.5 多階層の抽象継承構造

抽象クラスの直下の子クラスで「すべての抽象メソッドをオーバーライドしてメソッドの中身を実装する」必要があるわけではありません。

**すべての抽象メソッドの中身が確定しなければabstractを外すことは許されず**、つまり**newして利用できません**。

### 12.4.1

### 12.4.2

抽象クラスの抽象クラスだけを特別扱いする文法がJavaにはある。

継承階層を上に辿ると上流のクラスは、すべて抽象クラスになります。

そしてJavaでは次の条件を満たす特に抽象度が高いクラスを、インターフェースとして特別扱いします。

#### インターフェースとして特別扱いできる2つの条件

1. すべてのメソッドは抽象メソッドである。
2. 基本的にフィールドは1つも持たない。

例

    public abstract class Character {
      public abstract void run();
    }

このクラスには抽象メソッドしかなく、フィールドもありません。このまま抽象クラスとしておいてもよいのですが、以下のような構文を用いてインターフェースとして定義することも可能です。

    public interface Character {
      public abstract void run();
    }

なお、「インターフェースとして宣言されたもののメソッドは、自動的にpublicかつabstractになる」というルールがあるので、普通は次のように書きます。

    public interface Character {
      void run();
    }

インターフェースは基本的に「普通のフィールド」を持ちません。しかし、「public static finalがついたフィールド(定数)」だけは宣言が許されます。

さらに「public static final」を省略してもよいことになっています。

    public interface Circle {
      double PI = 3.141592; // 自動的にpublic static finalが補われる
    }

### 12.4.3 インターフェースの名前の由来

public interface CleaningService {
  Shirt washShirt(Shirt s);
  Towl washTowl(Towl t);
  Coat washCoat(Coat c);
}

このCleaningServiceはシャツとタオル、そしてコートを渡せば、それを洗って返してくれます。また、すべてのメソッドは抽象メソッドであり、処理内容が記述されていません。

つまり、**「どのようにして洗うか」というクリーニング屋の内部で行われる作業については明かされていない**わけです。

このCleaningServiceインターフェースは、まるでクリーニング店の店頭メニューのようですね。

店頭メニューは、クリーニング屋に「こういう仕事を受け付けますよ」と表明するためのものです。そしてお客さんはメニューを見て「この仕事をお願いします」と依頼をします。

つまり、メニューは店主とお客さんとの接点(interface)の役割を果たしているのです。

### 12.4.4 インターフェースの実装

    public class KyotoCleaningShop implements CleaningService {
      private String ownerName;
      private String address;
      private String phone;

      public Shirt washShirt(Shirt s) {
        // 大型洗濯機 15分
        // 業務用乾燥機 30分
        // スチームアイロン 5分
        return s;
      }

      public Towl washTowl(Towl t) {
        //
      }

      public Coat washCoat(Coat c) {
        //
      }
    }

ところで、全国チェーンのクリーニング店では、どの店でも同じ店頭メニューを使っていることがあります。おそらく本社で作ったメニューを、すべての店で提示しているのでしょう。

#### クラスにはないインターフェースの特権

異なる実装が衝突する問題が発生しないため、複数の親インターフェースによる多重継承が認められている

### 12.4.6 インターフェースの継承

あるインターフェースを定義する場合、ゼロから開発せずに既存のインターフェースを拡張することもできます。

    public interface Human extends Creature {
      public void talk();
      public void watch();
      public void hear();
      // さらに、親インターフェースからrun()を継承
    }

使い方

    public Class Fool extends Charater implements Human {
      // Character から hp や getName() などのメンバを継承している
      // Character から継承した抽象メソッド attack() を実装
      public void atack(Matango m) {
        //
      }

      // さらに Human から継承した4つの抽象メソッドを実装
      public void talk(){...}
      public void watch(){...}
      public void hear(){...}
      public void run(){...}
    }

<br>

### 13.2.1 ざっくり捉えるための文法

    Character c = new SuperHero();

以後、変数cを利用するときは、**本当はSuperHeroクラス**なのですが、**あくまでCharacterとして捉えて利用する**ことです。

このように、多態性を活用するためには「箱の型」と「中身の型」という異なる2つの型が関係してきます。

そして**あるインスタンスをどのようにとらえるかは、どの型の変数に代入するか(箱の型)で決まります**。。


### 13.2.2 できる代入、できない代入

絵に描いてみて嘘にならないインスタンスの代入は許されます。

しかし、つぎのような代入はエラーになります。

    float f = new Hero();
    Item i = new Hero();
    SuperHero sh = new Hero();

絵に描いてれば分かるように、これらはいずれも嘘になってしまうからです。

### 13.2.3 継承のもう1つの役割

extendsやimplementsはプログラマが「is-aの関係」をJavaに伝える手段でもあるのです。

そのため、いくら私たち人間が考えた一般常識ではis-aの関係であっても、Javaの継承関係で2つのクラスが繋がっていなければ代入できません。

- Character <- Hero <- SuperHero
- Human <- Man
- Human <- Woman

常識では「スーパーヒーローは人間の一種」だが、クラス同士は無関係

    Human h = new Hero(); // これはエラー



### 13.2.4 箱の型に継承クラスを使う

あなたが命あるあらゆるものの親として、新たにLifeインターフェースを作ったと考えましょう。

しかし、インターフェースLifeが定義されている場合、`new Life()`とすることはできません。

Lifeをインスタンス生成のためには利用できなくても、Lifeを変数の型(表面に「Lifeが入っています」と書かれた箱)として使う分にはまったく構いません。

    public interface Life {
      :
    }

    public class Main {
      public static void main(String[] args) {
        Life if = nme Wizard(); // Wizardは生き物の一種
      }
    }

### 13.3.1 捉え方の違いは使い方の違い

この節では「あるインスタンスをザックリ捉えるのと、厳密に捉えるのでは、利用にどのような違いがでてくるか」を見ていきます。

例え話

- 紙
  - 水分を含むことができる
- 絵
  - 水分を含むことができる
  - 見て楽しめる
- お金
  - 水分を含むことができる
  - 見て楽しめる
  - 物を買うことができる


この例えが示唆しているのは、**「まったく同一である1つの存在に対して、複数の異なる捉え方ができる」**ということと、**「何かを利用する人は、それを何と捉えているかによって、利用方法が変わる」**ということです。

あいまいで抽象的なほど用途は限定され、具体的に捉えるほど用途が増えていきます。

### 13.3.2 呼び出せるメソッドの変化

この「捉え方が変わると、利用方法が変わる」という現実世界の現象は、実はJava仮想世界でもちゃんと再現されています。

    public abstract class Character {
      String name;
      int hp;
      public abstract void attack(Matango m);
      public void run() {
        :
      }
    }

    public class Wizard extends Character {
      int mp;

      public void attack(Matango m) {
        m.hp -= 3;
      }

      public void fireball(Matango m) {
        m.hp -= 20;
        this.mp -= 5;
      }
    }

Wizardは魔法使いとしてattack()やfireball()のメソッドを持っていますので、インスタンス化すればattackさせたりfireballを使わせたりできます。

    public class Main {
      public static void main(String[] args) {
        Wizard w = new Wizard();
        Matango m = new Matango();
        w.name = "シャロ";
        w.attack(m);
        w.fireball(m);
      }
    }

WizardはCharacterの一種なので、Character型変数に代入することが可能です。しかし、いざ代入するとfireballを呼び出そうとする行でエラーが起きます。

    public class Main {
      public static void main(String[] args) {
        Wizard w = new Wizard();
        Character c = w;
        Matango m = new Matango();
        c.name = "シャロ";
        c.attack(m);
        c.fireball(m);  // この行でエラー
      }
    }


なぜか。Character型の変数に代入するということは中身のインスタンスを「(HeroだかWizardだか分からないけど)なにかのキャラクター」程度にざっくり捉えるということに他なりません。よって、**箱の中身がWizardであることを忘れてしまいます。**

**確実に言えることは「この箱に入っているのは、キャラクターの一種であること」だけ**です。

### 13.3.3 メソッドを呼び出せた場合に動く処理

前章では「どの箱に入れるかによって、呼べるメソッドが変わる」ことを学習しました。

では次に「もし、**メソッドが呼べたとしたら**、その動きはどうなるか」についてモンスター関連クラスを題材に実験

    public abstract class Monster {
      public void run() {
        System.out.println("モンスターは逃げ出した");
      }
    }

    public abstract class Silme extends Monster {
      public void run() {
        System.out.println("スライムは逃げ出した");
      }
    }

    public class Main {
      public static void main(String[] args) {
        Slime s = new Slime();
        Monster m = new Slime();
        s.run();  // スライムは逃げ出した。
        m.run();  // スライムは逃げ出した。
      }
    }

変数sも変数mも中身はあくまでスライムです。逃げろという命令が届きさえすれば、当然スライムが逃げます。

このように、実際に動くメソッドの中身はインスタンスの型(中身の型)によって決まります。それが**どんな型の箱に入っているかは関係ありません**

#### 箱の型と中身の型

- 箱の型
-- どのメソッドを「呼べるか」を決定する
- 中身の型
-- メソッドが呼ばれたら、「どう動くか」を決定する

### 13.5.2 同一視して配列を利用する

多態性を利用する(ザックリ捉えることによる)メリットを挙げます。



    public class Main {
      public static void main(String[] args) {
        Character[] c = new Character[5];
        c[0] = new Hero();
        c[1] = new Hero();
        c[2] = new Thief();
        c[3] = new Wizard();
        c[4] = new Wizard();

        // 宿屋に泊まる
        for(Character ch : c) {
          ch.addHp(50);
        }
      }
    }


### 13.5.3 同一視してザックリとした引数を受け取る

ダメな例

    public class Hero Extends Character {


      public void attack(Matango m) {
        System.out.println(this.name + "の攻撃！");
        System.out.println("敵に10ポイントのダメージを与えた");
        m.hp -= 10;
      }

      public void attack(Goblin g) {
        System.out.println(this.name + "の攻撃！");
        System.out.println("敵に10ポイントのダメージを与えた");
        g.hp -= 10;
      }

      // 以下スライム用と続く
    }

改善

    public class Hero extends Character {
      public void attack(Monster m) {
        System.out.println(this.name + "の攻撃！");
        System.out.println("敵に10ポイントのダメージを与えた");
        m.hp -= 5;
      }
    }

attack()メソッドの引数に注目してください。「攻撃する相手は、ザックリみれば何らかのキャラクターであれば何でもいい」という表明です。

このようなattack()メソッドであればMonsterクラスを継承しているSlimeやGoblin、そして将来登場するモンスターたちも攻撃することができます。

### 13.5.4 ザックリ利用しても、ちゃんと動く

多態性の本当のすごさは、ただ単に処理をまとめられるという単純なものだけじゃない。

    public class Main {
      public static void main(String[] args) {
        Monster[] monsters = new Monsters[3];
        monsters[0] = new Slime();
        monsters[1] = new Goblin();
        monsters[2] = new Matengo();

        for(Monster m : monsters) {
          m.run();  // 同じ指示を繰り返す
        }
      }
    }

**実行結果**

スライムは、体をうねらせて逃げ出した。
ゴブリンは、腕を振って逃げ出した。
マタンゴは、急いで逃げ出した。

指示を出す側は、それぞれにモンスターに対して同じように「とにかく逃げろ」と、いいかげんな支持を繰り返しているだけです。

一方、モンスター達は「逃げろ」と言われたら、きちんと独自の方法で逃げます。「どう逃げるか」については自分で理解していて、その方法を使って逃げるのです。

このように、**呼び出し側は相手を同一視し、同じように呼び出す**のに、**呼び出された側は、きちんと自分に決められた動きをする**という特性から「多態性」という名前が付けられています。
