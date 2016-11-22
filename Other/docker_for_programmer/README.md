### Bootstrapping

OSの起動を自動化する(Vagrant, Docker)

- OSインストール
- 仮想環境の設定
- ネットワーク構成の設定

### Configuration

OSやミドルウェアの設定を自動化するツール(Chef, Ansible)

- OSの設定(セキュリティ / サービス起動など)
- ミドルウェア(各種サーバ)のインストール / 設定

### Orchestration

複数サーバの管理を自動化するツール(Capstrano, Serf, Kubernates)

- アプリケーションのデプロイ
- サーバ群のオーケストレーション


------

# 第二章 コンテナ仮想化技術とDocker

### Dockerコンポーネント

#### Dockerイメージを作る

- Docker Engine
--

#### Dockerコンテナを動かす

- Docker Compose
- Docker Kitematic
- Docker Machine
- Docker Swarm

### Dockerイメージを公開/共有する機能

- Docker Registry


------

## Dockerイメージの操作

イメージのダウンロード

    docker pull
    docker pull centos:7
    docker pull registry.hub.docker.com/centos:7

イメージの一覧表示

    docker images
    docker images --digests eiurur/dockersample

イメージの詳細確認

    docker inspect
    docker inspect centos

イメージのタグ設定

    docker tag http:2.4 eiurur/webserver:1.0
    // => http:2.4という名前のイメージに対して、ユーザ名がeiurur、イメージ名がwebserver、タグにバージョン情報である1.0というタグをつける。

イメージの検索

    docker search
    docker search centos
    docker search --stars=30 centos

イメージの削除

    docker rmi
    docker rmi nginx
    docker rmi 3f72
    // IMAGE IDでもよい

DockerHub関連

    docker login
    docker push eiurur/webserver:1.0
    docker logout


## Dockerコンテナの生成/起動/停止

イメージが出来上がったら、イメージをもとにコンテナを生成できる。

### ライフサイクル


コンテナの生成

    docker run
    docker run -it --name "test" centos /bin/cal
    //=> コンテナを作成/実行 コンソールに結果を出すオプション コンテナ名 イメージ名 コンテナで実行するコマンド
    docker run -it --name "test2" centos /bin/bash

コンテナのバックグランド実行

    docker run
    docker run -d centos /bin/ping localhost

    // バックグランドで実行されているかどうか確認するときは、docker logsコマンドを使います。
    docker logs -t 4bd7e
    // タイムスタンプを表示 コンテナ識別子を指定

    docker run -it --restart=always centos /bin/bash
    // => コンテナの常時再起動

コンテナのネットワーク設定

    docker run -d -p 8080:80 https
    // => ホストのポート番号8080とコンテナのポート番号80をマッピングする。
    // => ホストの8080ポートにアクセスすると、コンテナ上で動作しているhttpd(80番ポート)ｍｐサービスにアクセスします

    docker run --dns=192.168.1.1 httpd
    // => コンテナのDNSサーバ指定

    docker run -it --add-host=test.com:192.168.1.1 centos
    // => ホスト名とIPアドレス定義

    docker run -it --hostname=www.test.com --add-host=node1.test.com:192.168.1.1 centos
    // => コンテナ自身のホスト名を指定するにはhostnameオプションを使用する

リソースを指定してコンテナを生成/実行

    docker run -c=512 -m=512m centos
    // => CPU時間の相対割合とメモリの使用量を指定

    docker run -v /c/Users/eiurur/webpage:/var/www/html httpd
    // => ホストOSとコンテナ内のディレクトリを指定したいときは-vオプションを使用する。
    // => ホストのCドライブの/Users/eiurur/webpageディレクトリを、コンテナの/var/www/htmlディレクトリと共有するとき

コンテナを生成/起動する環境を指定

    docker run -it -e foo=bar centos /bin/bash
    // => コンテナの起動時に環境変数を設定する

    docker run -it --env-file=env_list centos /bin/bash
    // > ファイルから一括登録

    docker run -it -w=/tmp/work centos /bin/bash
    // => コンテナの作業ディレクトリ(pwd)を指定して実行したいとき。コンテナの/tmp/workを作業ディレクトリにしている。

稼働コンテナの一覧表示

    docker ps
    docker ps -a
    // => 停止いしているコンテナも表示
    docker ps -a -f 'name=test1'
    // => フィルタリング

コンテナの稼働確認

    docker stats
    docker stats apatche nginx

コンテナの起動(停止しているコンテナを起動する)

    docker start
    docker start コンテナID

コンテナの停止

    docker stop
    docker stop -t 2 コンテナID
    // => 2秒後にコンテナを停止する。

コンテナの再起動

    docker restart
    docker restart コンテナID

コンテナの削除

    docker rm
    docker rm コンテナID
    docker rm -f 'docker ps -a -q'
    // => 起動中のコンテナもすべて削除

## 稼働しているコンテナの操作

本番環境での運用のときなど、すでに稼働しているコンテナの状態を確認したり、任意のプロセスを実行させたりするときの操作について説明

稼働コンテナへの接続

    docker attach
    docker attatch test
    // => /bin/bashが稼働しているtestという名前のコンテナに接続する
    // => 接続したコンテナごと終了させるときはCtrl + C、
    // => コンテナを起動したままコンテナ内で動くプロセスのみを終了させるときはCtrl + q

稼働コンテナでプロセス実行

    docker exec
    docker exec -it nginx1 /bin/bash
    // => nginx1という名前の稼働中のコンテナで/bin/bashを実行する。
    // => Webサーバのようにバックグラウンドで実行しているコンテナにアクセスしたいとき、docker attatchコマンドで接続しても、シェルが動作していない場合は、コマンドを受け付けることができない。そのため、docker execコマンドを使う。

    docker exec -it nginx1 /bin/ping localhost
    // => コマンドを直接実行する。

コンテナ内のファイルをコピー

    docker cp test:/etc/passws /tmp/etc
    // => testという名前のコンテナ内の/etc/passwdファイルを、ホストの/tmp/etcにコピーするとき

    docker cp ./localhost.txt test:/tmp/test.txt
    // => ホストからコンテナへのファイルコピー

## コンテナからイメージの作成

コンテナからイメージを作成

    docker commit -a "eiurur" nginx1 eiurur/webfront:1.0
    // => nginx1という名前のコンテナをeiurur/webfrontという名前でタグ名を1.0に指定して、新しいイメージを作成する。

コンテナをtarファイル出力

    docker export
    docker export webap > latest.tar
    // => webapという名前のコンテナを、latest.tarという名前でtarファイルに出力する。。

tarファイルからイメージ作成

    docker import ファイルまたはURL
    cat latest.tar | docker import - webap:1.0
    // => latest.tarにまとめられたディレクトリやgファイルをもとにwebapとぃう名前でタグ名が1.0のイメージを作成する

イメージの保存



    docker save
    dokcer save -o export.tar mongo
    // => mongoという名前のイメージを、export.tarに保存する。保存するファイル名の指定には-oオプションを指定する。

イメージの読み込み

dokcer saveしコマンドで保存したtarファイルからイメージを生成できる。

    docker load -i export.tar

**export / import, save / loadの違い**

コンテナをexportすると、コンテナを動かすのに必要なファイルが、すべて圧縮アーカイブにまとめられる。

そのため、このtarファイルを展開すると、コンテナのルートファイルシステムがそのまま取り出せる。

イメージをsaveすると、イメージのレイヤー構造も含めた形で圧縮アーカイブにまとめられる。

もととなるイメージは同じであっても、内部的なディレクトリ/ファイルの構造が違う。それぞれ対になるコマンドを使うようにしてなくてはいけない

## Dockerfileの基本

DockerfileからDockerイメージの作成

    docker build -t [生成するイメージ名]:[タグ名] [Dockerfileの場所]
    docker build -t sample:1.0 /home/dokcer/sample

### コマンド/デーモンの実行

コマンドの実行

    RUN echo こんにちはShell形式
    RUN ["echo", "こんにちはExec形式"]
    RUN ["/bin/bash", "-c", "echo 'こんにちはExec形式でbashを使ってみました'"]

デーモンの実行

RUN命令はイメージを作成するために実行するコマンドを記述する。

イメージをもとに生成したコンテナ内でコマンドを実行するには、CMD命令を使う。

Dockerfileには、1つのCMD命令を記述することができます。もし複数指定したときは、最後のコマンドのみ有効になります。

    CMD /usr/sbin/httpd -D FOREGROUND
    CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]

デーモンの実行(ENTRYPOINT命令)

ENTRYPOINT命令で指定したコマンドは、DockerfileからビルドしたイメージからDockerコンテナを起動するため、docker runコマンドを実行したときに実行されます。

    ENTRYPOINT [実行したいコマンド]
    ENTRYPOINT /usr/sbin/httpd -D FOREGROUND
    ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]

ENTRYPOINT命令とCMD命令の違いは、docker runコマンド実行時の動作です。

CMD命令 ・・・ コンテナ起動時に実行したいコマンドを定義しても、docker runコマンド実行時に引数で新たなコマンドを指定した場合、そちらを優先実行する。

=> コンテナ実行時にDockerfileの作成者が意図しないデーモンが実行されうｒこともある。


**コンテナ実行時にコマンド引数を任意に指定したいとき**

Dockerfile

    FROM centos:latest

    ENTRYPOINT ["top"]
    CMD ["-d", "10"]

実行

    docker run -it sample
    // => CMD命令で指定した10秒ごとに更新する場合

    docker run -it sample -d 2
    // => 2秒ごとに更新する場合

CMD命令はdocker runコマンド実行時に上書きできる仕様を逆手にとった使い方

