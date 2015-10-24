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

### クラスやメソッドのアクセス修飾子の宣言は適切な権限で行う

- フィールドは、変数として定義されたもの(finalが付かない)なら原則として`private`にします。下位クラスへのインタフェースとして必要なものを`protected`にします。原則としてフィールドは`public`やアクセス修飾子なしにしません。

- 定数のフィールド(`final`が付く)は、自クラス以外から参照されるものを`public`にします。はそれ以外の定数は`private`にします。

**Good**

    public abstract class AbstractSlip {

    }

    public class OrderSlip extends AbstractSlip {
      public static final String SLIP_NAME = "発注伝票";
      private int detailCount = 0;

      public int getDetailCount() {

      }
    }

**Bad**

    abstract public class AbstractSlip {

    }

    public class OrderSlip extends AbstractSlip {
      final public static String SLIP_NAME = "発注伝票";  // 修飾子の記述順の違反
      public int detailCount = 0;  // publicな可視性の必要がない

      public int getDetailCount() {

      }
    }

カプセル化に慣れるまでは、以下のような簡単化した指針でアクセス修飾子を使います。

- クラスはすべて`public`にする
- 他のオブジェクトから呼び出されるメソッドは`public`にする
- 同じクラス内で`public`なメソッドから呼び出されるだけのメソッドは`private`にする
- フィールドはすべて`private`にする
- 定数はすべて`public`にする

<br>

`protecteed`なフィールドはクラス間の結合を強め、カプセル化の妨げになります

継承を疎結合で実現したい場合はには、クラス間のインタフェースに`protected`なフィールドを使いません。メソッドのみでクラス間のインタフェースを定義しましょう。

<br>

冗長な修飾子は避けましょう。

- インタフェースで定義するメソッドには`public`、`abstract`を付けません。
- インタフェースで定義するフィールドには`public`、`static`、`final`を付けません。

### ラッパークラス型よりもプリミティブ型を使う

- ラッパークラス型(Integer、Double、Boolean)よりプリミティブ型(int、double、boolean)を使います。

- コレクションの要素として使用する場合には、ラッパークラス型を使用します。

**Good**

    while (reader.hasNext()) {
      long xmlType = reader.next();
      if (xmlType == XMLStreamReader.START_ELEMENT) {
        File sourceCodeFile = new File(reader.getAttributeValue(0));
        String sourceCodePath = replacePath(sourceCodeFile, getTempPath());
      }
    }

**Bad**

    while (reader.hasNext()) {
      Long xmlType = reader.next();
      if (xmlType == XMLStreamReader.START_ELEMENT) { // ラッパークラス型のLongを使用している。繰り返し実行のたびに、Long型のオブジェクトの生成と破棄が起こる。
        File sourceCodeFile = new File(reader.getAttributeValue(0));
        String sourceCodePath = replacePath(sourceCodeFile, getTempPath());
      }
    }

ラッパークラス型でもプリミティブ型でも実行可能な利用シーンでは、必ずプリミティブ型を使用します。(プリミティブ型の方が、消費メモリ、処理性能のいずれにおいても有利です。)

コレクションに保持するなどの目的でラッパークラス型の使用を選択する場合は、以下の点で注意が必要です。

- `==`演算子で同値比較ができません。(物理的に同じオブジェクトの参照間での比較のみ`true`になる)
- 数値型のラッパークラスのオブジェクト間でも、不等号「<」、「<=」、「>=」、「>」による比較ができます。
- 数値型のラッパークラスのオブジェクト間でも、四則演算ができます。
- ラッパークラス型のオブジェクトは値として`null`になりえるため、上記の不等号や四則演算を適用すると、`Null PointerException`がスルーされる場合があります。

### 比較演算子は「<」か「<=」を使う

「>」と「>=」は使いません。

**Good**

    if (0 < size && size < 10) {
      :
    } else if (10 <= size && size < 20) {
      :
    } else if (20 <= size) {
      :
    }

### オートボクシングを使用しない


### 継承させたくないクラスには`final`宣言をする

`final`宣言をすることで、そのクラスのサブクラスを他者が定義できなくなります。

### クラスにtoStringメソッドを装備する

`toString`が返す文字列は、オブジェクトの属性値や内部的な状態を組み合わせて作成します。アプリケーションの動作に特に重要なものを絞って埋め込みます。

**Good**

    public class DataStyle {
      private int type;

      /**
       * 文字列を返します
       * @return 日本語文字列
       */
      public String toString() {
        switch (type) {
          case 1:
            return "業務";
          case 2:
            return "行程";
          default:
            return "";
        }
      }
    }

要のクラスに`toString`メソッドを常備するメリットは、`System.out.println(object)`と書くだけでいつでもオブジェクトの中身をプリントできることです。

`toString`メソッドを新たに実装しなかった場合は、`Object.toString()`が継承されます。

### メソッドのないインタフェースを定義せず定数クラスを使う

- 複数のクラスで定数の定義を共有したい場合は、定数クラスを作成し、そこで定数の定義を集めます。定数は定数クラスのフィールドで実現します。
- クラス変数を値の書き換えができない定数として宣言するためにフィールドを`static`と`final`で修飾します。
- 定数クラスはインタンス化をさせないようにデフォルトコンストラクタを`private`で宣言しておきます。

**Good**

    package constant;

    class FDConstants {

      public static final String DEFAULT_DATE_FORMAT = "yyyyMMddHHmmss";

      public static final String CUSTOM_DATE_FORAMT = "yyyy'/'MM'/'dd' 'HH':'mm':'ss";
      public static final String CUSTOM_DATE_FORAMT_MMDD = "MM'/'dd' 'HH':'mm':'ss";

    }

    -------

    import constant.FDConstants.DEFAULT_DATE_FORMAT
    import constant.FDConstants.CUSTOM_DATE_FORAMT
    import constant.FDConstants.CUSTOM_DATE_FORAMT_MMDD

    class FDOrderPage {

      // デフォルトコンストラクタ
      public FDOrderPage() {
        String defaultDateFormat = FDConstants.DEFAULT_DATE_FORMAT;
      }
    }

### 定数クラスには`static`イニシャライズを使う

- 定数クラスの`static`メンバ変数は`static`イニシャライズで初期値を設定します。
- メンバ変数の宣言時に記述したコメントと同じものを`static`ブロックの中にも記述します。

**Good**

    class FDConstants {

      public static final String DEFAULT_DATE_FORMAT;
      public static final String CUSTOM_DATE_FORAMT;
      public static final String CUSTOM_DATE_FORAMT_MMDD;

      static {
        DEFAULT_DATE_FORMAT = "yyyyMMddHHmmss";
        CUSTOM_DATE_FORAMT = "yyyy'/'MM'/'dd' 'HH':'mm':'ss";
        CUSTOM_DATE_FORAMT_MMDD = "MM'/'dd' 'HH':'mm':'ss";
      }
   }


### オーバーライドさせたくないメソッドは`final`宣言をする

### 配列やコレクションを返すメソッドでは`null`を返さない

配列やコレクションを返すメソッドで、処理結果として要素がまったく作られない場合でも、戻り値として`null`を返さずにサイズ0の配列またはコレクションを返します。

**Good**

    public LinkedList<String> getMerticsList(String projectName) {
      throws ProjectDashboardException {

        LinkedList<String> metricsList = new LinkedList<String>();

        if (!existsProperty(key, projectPathMap.get(projectName))) {
          return metricsList;
        }

        metricsList.add(Functions.CheckStyle.getName());
        return metricsList;
      }
    }

**Collectionsを使用する例**

    public LinkedList<String> getMerticsList(String projectName) {
      throws ProjectDashboardException {

        if (!existsProperty(key, projectPathMap.get(projectName))) {
          return Collections.emptyList(); // 予め定数値化されている空のListを返す。
        }

        LinkedList<String> metricsList = new LinkedList<String>();

        metricsList.add(Functions.CheckStyle.getName());
        return metricsList;
      }
    }

### 引数の数は少なめにする

引数の数を少なくしたいがために、複数のオブジェクトを`Collection`や`Map`に格納して引数渡しすることは推奨されません。このような場合は`Bean`を使うべきです。

### 引数の正当性を検査する

- 引数の整数がインデックス値の場合は、負でないことをチェックします。あるいは上限、下限のチェックをします。
- `public`メソッドの引数は、メソッド本体の先頭で正当性を検査する。その後にメソッド内部の処理に引き継ぎます。コンストラクタの引数も同様です。
- 引数がオブジェクトの参照である場合は、`null`でないことをチェックします。
- 引数のチェックの結果、正当性が否定された場合は、例外をスローします。
- `protected`メソッド、`private`メソッドの場合は、呼び出し元を限定できるので、冗長な検査は行いません。

### クラスメソッドを実行するときはクラス名を使って呼び出す

- 自クラスのクラスメソッドを実行する場合はクラス名を省略しまｓ。

**Good**

    private DataBox firstLaunch() throws ProjectDashboardException {
      DataBox latestDataBox = null;
      if (FileUtility.existsDocument(getTempDir())) {
        runDocumentTool();
        latestDataBox = createDocumentBox();
      } else {
        :
      }
    }

**Bad**

    private DataBox firstLaunch() throws ProjectDashboardException {
      DataBox latestDataBox = null;
      FileUtility fileUtil = new FileUtility;
      if (fileUtil.existsDocument(getTempDir())) {
        runDocumentTool();
        latestDataBox = createDocumentBox();
      } else {
        :
      }
    }

- クラスメソッドは、特殊な場合を除き、状態を伴わない(オブジェクトの状態の影響を受けない)メソッドです。すなわち、引数で渡したパラメータだけから影響を受けて動作します。

### 配列の宣言は型名に「[]」をつけて行う

**Good**

    String[] dataArray = dataCombo.getItems();
    int[] totalCounter = {0, 0, 0, 0, 0};

**Bad**

    String dataArray[] = dataCombo.getItems();
    int totalCounter[] = {0, 0, 0, 0, 0};

Javaでは処理系内部での配列型の扱いがC言語とまったく異なります。そのため配列型を型として意識できるように、規約では型名に「[]」を付ける方法で統一します。

### 2次元以上の配列を宣言しない

配列の使用は、原則として1次元のものだけにします。

- 多次元のデータを扱う場合は、1次元の配列と`ArrayList`や`Map`と組み合わせで代用できないか検討します
- 処理の高速化のために配列の構造が必要な場合は、上記の限りではありません。
- 数学的に行列を扱う場合、CSVや表計算による2次元データなど、行番号、列番号で要素を取り扱う場合に、多次元の配列を使うことがあります。

**Good**

    HashMap<String, int[]> eventName = (HashMap<String, int[]>) table.getData();
    int[] value = (int[]) eventName.get(name);

**例外的に認められる行列形式のデータ**

    int[][] counterMatrix = [{0,0,0}, {0,0,0}, {0,0,0}];

Javaにおける2次元配列は、1次元の配列を要素に持つ1次元の配列です。3次元以上もその考え方の繰り返しです。

基本的な考え方として、オブジェクト型を要素とする1次元配列を実現する仕組みを持ち、この配列を入れ子にして多次元配列を表します。

### 配列のコピーにはarraycopyメソッドを使用します。

配列のコピーには`System.arraycopy()`メソッドを使います。

予めコピー先の配列を用意し、arraycopyメソッドを用いて要素の参照をコピーします。コピー元とコピー先で物理的に同じ要素を共有します(プリミティブ型の配列は値をコピーします)。

配列型に備わっている`clone`メソッドを使用したコピーも推奨します。`clone`メソッドは、コピー元と同じサイズで同じ要素の配列をもつもう一つ作りたいときに使用できます。コピー元とコピー先で物理的に同じ要素を共有します(プリミティブ型の配列は値をコピーします)。

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};

    int length = brand.length;
    String[] brandCopy = new String[length];
    System.arraycopy(brand, 0, branchCopy, 0, length);

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};

    String[] brandCopy = (String[])brand.clone();

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};

    int length = brand.length;
    String[] brandCopy = Arrays.copyOf(brand, length);

**Bad**

    String[] brand = {"Syaro", "Chiya", "Rize"};

    int length = brand.length;
    String[] brandCopy = new String[length];
    for (int i = 0; i < length; i++) {
      brandCopy[i] = brand[i];
    }

for文のループによるコピーよりも、`arraycopy`や`clone`など高速にコピーを実施するメソッドを使用することを推奨します。

arraycopyとcloneのどちらも、要素の新しい複製を作るのではなく、要素への参照をコピーをします。 (shallow copy)

**ディープコピーの例**

    int length = numberList.length;
    BigDecimal[] numberCopy = new BigDecimal[length];
    for (int i = 0; i < length; i++) {
      numberCopy[i] = new BigDecimal(numberList[i].toPlainString());
    }

一次元配列に限れば、プリミティブ型の配列は要素を参照ではなく値で持つので、シャローコピーでも要素を共有しない複製になります。

String型、Integer型やDouble型などのラッパークラスの1次元配列は、値を更新できないオブジェクトへの参照が要素ですから、シャローコピーとディープコピーを使い分ける必要がありません。

ディープコピーを行うには、for文でループして要素の一つ一つを、newしながら値をコピーします。

2次元以上の配列では、要素以外に要素を格納する配列もnewする必要があります。例えば、2次元配列(M行×N列の行列)は行を成す配列について行ごとにnewして生成します。

### インスタント変数はか必ず初期化する

インスタント変数の初期化には以下の方法があります。最も優先される初期化方法はコンストラクタの中での初期化です。

- コンストラクタの中で初期化する。
- インスタント変数の宣言時に初期化する
- イニシャライズで初期化する。
- ゲッターにより初めて値が参照されるときに初期化する

### むやみにアクセサを定義しない

**Good**

    private class Profile {
      private String zipCode = "";
      private String addressCity = "";
      private String addressNumber = "";
      :

      // 郵便番号と番地をパラメータから設定し、都道府県市町村名は郵便番号から検索して設定する
      public void setAddressSet(String aZipCode, String addressNum) {
        :
      }
    }

**Bad**

    private class Profile {
      private String zipCode = "";
      private String addressCity = "";
      private String addressNumber = "";
      :

      public void setZipCode(String aZipCode) {

      }

      public void setAddressCity(String anAddressCity) {
        :
      }

      public void setAddressSetString addressNum) {
        :
      }
    }

なんでもかんでもpublicなセッターおよびゲッターを定義してしまうと、インスタント変数の整合性が保てなくなる場合もあり、オブジェクトがカプセル化できているとは言えません。

### クラス変数にpublic static final宣言した配列を利用しない

各々の要素を更新不可として保持する配列(リスト)が必要なときに、`public static final`宣言した配列を用いません。

`*final`で修飾した配列型は要素の書き換えができてしまうため`public`にすると安全ではありません。つまり`public static final`宣言で要素を定数化した配列は定義できません。

可視性を`public`にしたい場合は、配列を使わず`Collections`クラスの`unmodifiableList`メソッドを使用して、読み取り専用のコレクションを生成します。

保持するデータは、String、Integer、Doubleなどのラッパークラスのオブジェクトとします。(これらは属性を更新できないから)

**Good**

    private static final String[] DAY_OF_WEEK_STR = {"SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"};
    public static final List DAY_OF_WEEK = Collections.unmodifiableList(Arrays.asList(DAY_OF_WEEK_STR));

**Bad**

    public static final String[] DAY_OF_WEEK = {"SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"};

### クラス変数はクラス名を使ってアクセスする

**Good**

    public class GraphParts {
      public static final List DAY_OF_WEEK = Collections.unmodifiableList(Arrays.asList(DAY_OF_WEEK_STR));
    }

    ------

    public class DisplayGraph {
      public void draw() {
        String dayOfWeek = GraphParts.DAY_OF_WEEK.get(i);
      }
    }

**Bad**

    public class GraphParts {
      public static final List DAY_OF_WEEK = Collections.unmodifiableList(Arrays.asList(DAY_OF_WEEK_STR));
    }

    ------

    public class DisplayGraph {
      GraphParts graphParts = new GraphParts();

      public void draw() {
        String dayOfWeek = graphParts.DAY_OF_WEEK.get(i);
      }
    }

### ローカル変数は安易に再利用しない

### 更新される文字列には`StringBuilder`を使用する

更新が想定される文字列は`StringBuilder`型で宣言します。`StringBuilder`型は`StringBuffer`型と同じ使用方法です。

**Good**

    public void insert(DBConnection connection) throws SQLException {
      StringBuilder sql = new StringBuilder(1024);
      sql.append("INSERT INTO STEPCOUNT VALUES(");
      sql.append(classId);
      sql.append(".");
      sql.append(totalStep);
      sql.append(".");
      sql.append(handwriteingSqlStep);
      sql.append(")");
      connection.executeUpdate(sql.toString());
    }

**Bad**

    public void insert(DBConnection connection) throws SQLException {
      String sql = "INSERT INTO STEPCOUNT VALUES (";
      sql += classId + ",";
      :
      connection.executeUpdate(sql);
    }

**Bad**

    // 最も非効率
    public void insert(DBConnection connection) throws SQLException {
      StringBuilder sql = new StringBuilder(1024);
      sql.append("INSERT INTO STEPCOUNT VALUES(");
      sql.append(classId + ",");
      sql.append(totalStep + ",");
      sql.append(handwriteingSqlStep + ")");
      connection.executeUpdate(sql.toString());
    }

`String`のように、euqalsメソッドを使って`StringBuilder`文字列の内容を比較できません。双方が物理的に同じバッファを共有しているときだけ`true`になります。


### 更新されない文字列には`String`を使用する

### プリミティブ型とStringオブジェクトとの変換には変換メソッドを使用する

int、doubleなどのプリミティブ型の数値はString型の文字列に変換するには`String`クラスの`valueOf`メソッドを使用します。

String型の文字列で表記された数値をint、doubleなどのプリミティブ型に変換するには、`Integer`クラスの`parseInt`メソッドや`Double`クラスの`parseDouble`メソッドを使用します。

**Good**

    public void convertString(int intInput, String stringNumber) {
      String intString = String.valueOf(intInput);
      int intValue = Integer.parseInt(stringNumber);
    }

### 4桁以上の数値リテラルには3桁ごとにアンダースコアを挿入する

**Good**

    static long LOWER_LIMIT_VALUE = 200L;  // 200
    static long UPPER_LIMIT_VALUE = 80_000_000_000L;  // 80億

### 配列やコレクションを処理するループに拡張for文を使う

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};
    String[] size = {"S", "M", "L"};

    List<ItemLabel> items = new ArrayList<ItemLabel>();
    for (String i : brand) {
      for (String j: size) {
        items.add(new ItemLabel(i, j));
      }
    }

###### Java8では...

for文の使用禁止

**StreamAPIとラムダ式**

    ArrayList<Integer> nabeAtsu = new ArrayList<Integer>();
    IntStream.rangeClosed(1, 51);
      .filter(i -> (i % 3 == 0 || String.valueOf(i).matches("[0-9]?3[0-9]?")))
      .forEach(i -> nabeAtsu.add(Integer.valueOf(i)));


### 繰り返し処理中のオブジェクトの生成は要否を考える


### 繰り返し処理のループの中にtry/catchブロックを記述しない

try/catchブロックは、繰り返しループの内側に記述すると、アプリケーションのパフォーマンスを悪化させてしまいます。

try/catchブロックをループの内側に記述するケースは、例外発生時に適切な例外処理を実施して、ループの最後まで処理を続けたい時です。

**Good**

    try {
      for (int i = 0; i < brand.length; i++) {
        for (int j = 0; j < size.length; j++) {
          displayLabel(brand[i], size[j]);
        }
      }
    } catch (ProjectDashboardException e) {
      :
    }

**Bad**

    for (int i = 0; i < brand.length; i++) {
      for (int j = 0; j < size.length; j++) {
        try {
          displayLabel(brand[i], size[j]);
        } catch (ProjectDashboardException e) {
          :
        }
      }
    }

### switch文で文字列による分岐の際に文字列を`null`チェックする

switch文の構文で、swtich(式)～の式に`String`型が使用できます。このときの式の値が`null`にならないように、事前に式の値のチェックを行います。

**Good**

    String chartStyle = style.getChartStyleString();
    if(chartStyle == null) {
      return;
    }
    switch(chartStyle) {
      case "a":
        :
        break;
      case "b":
        :
        break;
      default:
        break;
    }

### CollectionやMapにはジェネリクスを使う

ジェネリクスで変数を宣言する際に、変数の初期化時のnewにおける型パラメータを省略してダイヤモンドのみを記述します。

- String型を格納する`List`インタフェース型は、`List<String>`と宣言します。
- String型を格納する`Collection`インタフェース型は、`Collection<String>`と宣言します。
- String型のコレクション反復子`Iterator`インタフェース型は、`Iterator<String>`と宣言します。
- Integer型を格納し、キーがString型の`Map`インタフェース型は、`Map<String, Integer>`と宣言します。

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};
    String[] size = {"S", "M", "L"};

    List<ItemLabel> items = new ArrayList<ItemLabel>();
    for (int i = 0; i <= brand.length; i++) {
      for (int j = 0; j < size.length; j++) {
        items.add(new ItemLabel(brand[i], size[j]));
      }
    }

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};
    String[] size = {"S", "M", "L"};

    // 型パラメータの省略は、ジェネリクスの宣言と同時に初期化を行うときに限って実施する。
    List<ItemLabel> items = new ArrayList<>();
    for (int i = 0; i <= brand.length; i++) {
      for (int j = 0; j < size.length; j++) {
        items.add(new ItemLabel(brand[i], size[j]));
      }
    }

### オブジェクトの集合の繰り返し処理にはStreamAPIを使用する

**Good**

    String[] brand = {"Syaro", "Chiya", "Rize"};
    String[] size = {"S", "M", "L"};

    List<ItemLabel> items = new ArrayList<ItemLabel>();
    Arrays.stream(brand)
      .forEach(br -> Arrays.stream(size)
        .forEach(sz -> items.add(new ItemLabel(br, sz))));

    Stream<ItemLabel itemsStream = items.stream();
    itemsStream.forEach(e -> System.out.println(e));

### `AutoCloseable`を実装していないストリームを扱うときは`finally`ブロックでクローズ処理をする

ストリームに関する処理は、必ずtryブロックの中にコーディングします。

- ストリームに関する処理のうち、オープン、アクセスは必ずtryブロックの中にコーディングします。
- ストリームの宣言は、try/catchブロックの外側で行います。

**Good**

    あー

### AutoCloseableを実装しているストリームを扱うときはリソース付きtry文を使用する


### ストリームの操作でバッファ入出力を使う

- 文字ストリームを扱うとき
-- `BufferReader`クラスおよび`BufferWriter`クラスを使う
- バイトストリームを扱うとき
-- `BufferedInputStream`クラスおよび`BufferedOutputStream`クラスを使います。

**Good**

  あー

### catch文でキャッチする例外は詳細な例外クラスでキャッチする

catch文では、`Exception`、`RuntimeException`、`Throwable`をキャッチしません。これらのサブクラスをキャッチするように記述します。

    try {
      for (int i = 0; i < contents.length; i++) {
        objectStream.writeObject(contents[i]);
      }
      objectStream.flush();
      objectStream.reset();
    } catch (InvalidClassException e) {
      throw new ProjectDashboardException(serverity.ERROR, "0002", e);
    } catch (IOException e) {
      throw new ProjectDashboardException(serverity.WARNING, "0005", e);
    }

### Exceptionクラスのオブジェクトを生成してスローしない

**Bad**

    String sourcePath = sourceFile.getAbsolutePath().replace(getTempPath().getAbsolutePath(), "");

    if (sourceFile.getAbsolutePath().equals(sourcePath)) {
      throw new Exception();  // Exceptionクラスのオブジェクトをスローしている
    }

### finallyブロックには戻り値に影響がある記述をしない

- finallyブロックの中にはreturn文を記述しません。
- finallyブロックの中にはthrow文を記述しません。
  - finallyから新たな例外をスローしません。finallyブロックの中でthrows節が宣言されているメソッドを呼び出す場合は、finallyブロック内にtry/catch文を記述して例外をキャチします。

**Bad**

    boolean jobStatus = false;
    try {
      jobStatus = true;
      return jobStatus; // このreturn文はfalseを返す
    } catch (FileNotFoundException e) {
      setStatus(serverity.WARNING, "0012", e, url);
      jobStatus = false;
    } finally {
      jobStatus = false;
    }
    return jobStatus;

finallyブロックは必ず実行されます。tryブロックまたはcatchブロックでreturn文が記述されていても、そのreturn文の実行後にfinallyブロックが実行されます。

もしfinallyブロックにもreturn文が記述されていると、finallyブロックのreturn文が最終的にメソッドの戻り値を返します。

### catchブロックでは必ず処理をする

- catchブロックは空にせず必ず例外処理を記述します。

### Error、Throwableクラスを継承しない

アプリケーション独自の例外クラスは`Exception`クラスを継承します。

**Good**

    public class ProjectDashboardException extends Exception {
      :
    }

`Error`クラスは、アプリケーションでキャッチすべきではない重大な異常(起きてはならない異常)を表しています。

当然、throws節やcatch文にも`Error`クラスは記述しません。

`Throwable`クラスは、`Error`クラスと`Exception`クラス共通のスーパークラスです。

`Throwable`クラスからアプリケーションの例外を継承すると、エラーと例外の区別がつかず、意味が曖昧になります。

### Javadocの記述を揃える

**Good**

/**
 * <pre>
 * 前回の実行結果ファイルを読み込み、ディレクトリに存在するファイルの最終更新日が
 * 前回から変更なく、チェックの対象外となったファイルを削除します。
 * </pre>
 *
 * @params oldDataBox
 *   前回の実行結果データ
 * @params documentNameList
 *   ディレクトリに存在するファイル名一覧
 * @throws ProjectDashboardException
 *   ファイルが削除できないときの例外
 */
public static void removeNoUpdatedFile(DataBox oldDataBox, List<File> documentNameList) throws ProjectDashboardException {
  :
}


