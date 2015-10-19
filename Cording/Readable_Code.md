- 「理解するまでにかかる時間」を短くする。

- 明確な単語を選ぶ(名前に情報を詰め込む)
  - Size() -> Height(), NumNodes(), MemoryBytes()
  - Stop() -> Kill(), Pause()
  - send -> deliver, dispatch, announce, distribute, route
  - find -> search, extract, locate, recover
  - start -> launch, create, begin, open
  - make -> craete, set up, build, generate, compose, add, new

- 汎用的な名前を避ける
  - tmp, retval -> vの二乗の合計を表す変数なら sum_squares
  - ただし、情報の一時保管(変数の入れ替えとか)には、tmpなどを用いてよい
  - ループイテレータも、i,j,kではなく、club_iとか、members_iとか、users_iとかにする。

- 値に単位
  - 変数名に _ms をつける。
  - delay -> delay_secs
  - size -> size_mb
  - limit -> max_kbps
  - angle -> degrees_cw

    var start_ms = (new Date()).getTime(); // ページの上部
    ...
    var elapsed_ms = (new Date()).getTime() - start_ms; // ページの下部
    ducument.write("読み込み時間： " + elapsed_ms / 1000 + " 秒");

- その他重要な属性を追加する
  - password -> plaintext_password (passwordはプレインテキストなので、処理をする前に暗号化すべきである。)
  - comment -> unescaped_comment (ユーザが入力したcommentは表示する前にエスケープする必要がある。)
  - html -> html_utf8 (htmlの文字コードをutf-8に変えた。)
  - data -> data_urlenc (入力したdataをURLエンコードした。)

- 限界値を含めるときは `max_`か`min_`をつける

    MAX_ITEMS_IN_CART = 10;
    if shopping_cart.num_items() > MAX_ITEMS_IN_CART
      ERROR

- 範囲を指定するときは`first`と`last`を使う
  - 包含的な範囲を表すのであれば(終端を範囲に含めるのであれば)、`first`と`last`を使うのがいい。

- 包含 / 排他的範囲には`begin`と`end`を使う

    printEventsInRange(begin, end){}

    printEventsInRange("OCT 16 12:00am", "OCT 17 12:00am")

- ブール値の名前には`is`、`has`、`can`、`should`などをつけてわかりやすくなる。
  - SpaceLeft()だと数値を返すように聞こえる。ブール値を返したいのであれば、HasSpaceLeft()という名前にしたほうがいい。

- 名前を否定形にするのは避ける。

    bool disable_ssl = false;

より

    bool use_ssl = true;

- 一貫性と意味のある並び
  - 対応するHTMLフォームの`<input>`フィールドと同じ並び順にする
  - アルファベット順に並べる。

# コメント

- コメントに書かないこと
  - コードからすぐにわかることをコメントに書かない。
  - コメントのためのコメントをしない。(ひどい名前の関数を補う「補助的なコメント」。コードの方を直せ。)

- 自分の考えを記録する
  - なぜコードが他のやり方ではなくこうなっているのか。
  - (ex) // このデータだとハッシュテーブルよりもバイナリツリーのほうが40%速かった。
  - コードが汚い理由をコメントに書いてもよい。 (ex) // このクラスは汚くなってきている。サブクラス 'ResourceNode'を作って整理したほうがいいかもしれない。

- コードの欠陥にコメントをつける
  - TODO: もっと高速なアルゴリズムを使う
  - TODO: あとで手をつける
  - todo: 小さい問題
  - FIXME: 既知の不具合があるコード
  - HACK: あまり綺麗じゃない解決策
  - XXX: 危険！大きな問題がある

- 定数にコメントをつける
  - 定数の値にまつわる「背景」。

    // 合理的な限界値。人間はこんなに読めない。
    const int MAX_RSS_SUBSCRIPTION = 1000;

- はまりそうな罠を告知する。

    // メールを送信する外部サービスを呼び出している(1分でタイムアウト)
    void SendEmail(string to, string subject, string body);

- 全体像のコメント
  - // このファイルには、ファイルシステムに関する便利なインターフェースを提供
  - // するヘルパー関数が含まれています。ファイルのパーミッションなどを扱います。

- あいまいな代名詞を避ける
  -　「その」が指しているのは、「データ」？　「キャッシュ」？
  - // データをキャッシュに入れる。ただし、先にデータのサイズをチェックする。

- 歯切れの悪い文章を磨く
  - Before: // これまでにクロールしたURLどうかによって優先度を変える。
  - After: // これまでにクロールしていないURLの優先度を高くする。

- 関数の動作を正確に記述する
  - **Before:** // このファイルに含まれる行数を返す。
  - ""(空のファイル)は、0行なのか1行なのか。
  - "hello"は、0行なのか、1行なのか。
  - "hello\n"は、1行なのか、2行なのか
  - "hello\n\r creul\n world\r"は、2行なのか、3行なのか、4行なのか。
  - **After:** // このファイルに含まれる改行文字('\n')を数える。
  - => 改行文字がない場合は、0を返すことがわかる。また、キャリッジリターン(\r)が無視されることもわかる。

- 入出力のコーナーケースに実例を使う
  - **Before:** // 'src'の先頭や末尾にある'chars'を除去する
  - String Strip(String src, String chars)
  - 問題点:
  - この関数において、charsは、除去する文字列なのか、順序のない文字集合なのか。
  - この関数は、srcの末尾に複数のcharsがあったらどうなるのか？
  - **After:** // 実例: Strip("abba/a/ba", "ab")は"/a"を返す
  - => Strip()のすべての機能を見せている

- コードの意図を書く
  - **Bad:** // listを逆順にイテレートする
  - **Good:** // 値段の高い順に表示する

- 「名前付き引数」コメント
  - (ex) Connect(/* timeout_ms = */ 10, /* use_encryption = */ false);

# ループとロジックの単純化


## 制御フロー

**条件式の引数の並び順**

if文

    if (length >= 10)

変数は左。定数は右。


**if/elseブロックの並び順**

- 条件は否定形よりも肯定系を使う。
- 単純な条件を先に書く。
- 関心を引く条件や目立つ条件を先に書く

**do/whileループは避ける**

ループ条件は前もって書かれているほうが好ましい


## 巨大な式を分割する

**説明変数**

式を表す変数を「説明変数」という。

    username = line.split(':')[0].strip();
    if(username == "root"){}

**要約変数**

大きなコードの塊を小さな名前に置き換えて、管理や把握を簡単にする変数のことを「要約変数」という。

    var user_owns_document = (request.user.id === document.owner_id);

    if (user_owns_document) {
      // ユーザはこの文書を編集できる
    }

    if (!user_owns_document) {
      // 文書は読み取り専用
    }

**ド・モルガンの法則を使う**

not を分配して and/or を反転する

    if (!(a && !b)) return c;

下は上と同意

    if (!a || b) return c;

## 変数と読みやすさ

**変数を削除する**

- 役に立たない一時変数
- 中間結果を削除する
- 制御フロー変数を削除する

    var done = false;

    whiel(/* 条件 */ && !done) {

      if(...) {
        done = true;
        continue;
      }

    }

`done`いらなくね？こうすればよくね？

    while(/* 条件 */) {

      if(...) {
        break;
      }
    }

Q. `break`が使えないようなネストが何段階もあるループはどうするの？

A. コード(ループ内部のコードやループ全体)を新しい関数に移動するといい。

**変数のスコープを縮める**

- ローカル変数に格下げする
- メソッドをstaticにする( = メンバ変数とは関係ないことを表す)
- 大きなクラスを小さなクラスに分割する (お互いのクラスは独立していないと意味ない)

**定義の位置を下げる**

すべての変数を関数の先頭で定義する必要はない。変数の定義は変数を使う直前に移動すればいい。

**変数は一度だけ書き込む**

「永続的に変更されない」変数は扱いやすい。


Before:

    var setFirsetEmptyInput = function(new_value) {
      var found = false;
      var i = 1;
      var elem = document.getElementById('input' + i);
      while (elem !== null) {
        if (elem.value === '') {
          found = true;
          break;
        }
        i++;
        elem = document.getElementById('input' + i);
      }
      if (founf) elem.value = new_value;
      return elem;
    };

After:

    var setFirsetEmptyInput = function(new_value) {
      for (var i = i; true; i++) {
        var elem = document.getElementById('input' + i);
        if (elem === null) {
          return null;
        }

        if (elem.value === '') {
          elem.value = new_value;
          return elem;
        }
      }
    };

<br><br>

# コードの再構成

具体的に、コードを再構成する3つの方法

- プログラムの主目的と関係のない「無関係の下位問題」を抽出する。
- コードを再構築して、一度に1つのことをやるようにする。
- 最初にコードを言葉で説明する。その説明を元にしてきれいな解決策を作る。

***

**一度に1つのことを**

**短いコードを書く**

*最も読みやすいコードは。何も書かれていないコードだ。*

本当に必要な機能なの？実装しなきゃいけないの？

まとめ

- 不必要な機能をプロダクトから削除する。過剰な機能を持たせない。
- 最も簡単に問題を解決できるような要求を考える。
- 定期的にすべてのAPIを読んで、標準ライブラリに慣れ親しんでおく
