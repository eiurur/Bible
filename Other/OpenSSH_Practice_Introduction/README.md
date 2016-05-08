OpenSSH実践入門
======

<a href="http://www.amazon.co.jp/dp/4774168076" target="_blank">Amazon.co.jp： OpenSSH[実践]入門 (Software Design plus): 川本 安武: 本</a>

# OpenSSHの基本的な使い方

### OpenSSHのおもな機能、特徴

OpenSSHは「それ自身が動作するすべての環境をいっさい信用しない」という非常に興味深い特徴を持つアプリです。

### ホスト認証

ホスト認証では、クライアントが「ログイン先のサーバが正しいかどうか」を確認します。

ホスト認証は公開鍵暗号方式を利用して行います。認証には次の2種類のホスト鍵を使用します。

- ホスト秘密鍵
  - サーバ(sshdが実行されているホスト)の秘密鍵。
- ホスト公開鍵
  - サーバ(sshdが実行されているホスト)の公開鍵。
  - サーバで生成し、クライアントに提示する。
  - 初めてサーバに接続した際、この鍵の内容は、クライアントのホスト公開鍵データベースファイル(`~/.ssh/known_hosts`)に記録される。


### 公開鍵の登録

作成した公開鍵は、ログインしたいサーバの、ログインするアカウントのホームディレクトリにある`.ssh/authorized_keys`に保存しておきます。

`ssh-copy-id`というツールを使用するのが最も手間が省ける。

### ssh-copy-id

ユーザの公開鍵をリモートホストに登録するツールです。

公開鍵認証以外の方法でリモートホストに接続できる必要があります。

`~/.ssh/authorized_keys`の作成、パーミッションの設定を行ってくれます。

### 認証エージェントの利用

認証エージェントは、ユーザに変わって公開鍵認証を行うプログラムです。

ユーザは認証エージェントに認証情報を渡すことで、認証処理を肩代わりさせることができます。

**認証エージェントの起動**

新しいシェルを起動して使用する

    $ ssh-agent bash

**認証エージェントの終了**

    $ ssh-agent -k

実際の運用では、`.bashrc`などで`eval`を使って認証エージェントを起動させ、ログインシェルの中で常に使用できるようにします。

また、`.logout`に`ssh-agent -k 1>&2 /dev/null`と記述しておくことで、ログアウト時に認証エージェントの終了忘れを防ぐことができます。


<br>

認証エージェントへの秘密鍵の登録や削除は、`ssh-add`で行います。

**秘密鍵の登録**

    $ ssh-add .ssh/id_rsa

**登録されている秘密鍵の一覧表示**

    $ ssh-add -l

**登録された秘密鍵の削除**

    $ ssh-add -D

### うまくつながらないときの対処法

- ログイン先においてある公開鍵(`~/.ssh/authorized_keys`)が、ログインユーザ以外で書き込み可能なパーミッションになっていると、`sshd`は危険な認証とみなして接続を拒否します。
- クライアントマシンに保存されている秘密鍵もログインユーザ以外に書き込み権があると、`ssh`が接続を中止します。
- 過去にログインしたサーバのホスト公開鍵が、クライアントマシンに保存されているものから変更されている場合は、SSHクライアントが接続を拒否します。
  - 接続するためには、`~/.ssh/known_hosts`からこの公開鍵を削除する必要があります。
  - 手作業はもちろん、`ssh-keygen`コマンドでも削除できる。

    $ ssh-keygen -R xxx.xxx.xxx.xxx

### OpenSSHに付属しているコマンド

**ssh-agent**

秘密鍵を保持するための認証エージェントです。

公開鍵認証では、秘密鍵を使用するたび(リモートホストにログインしようとするたび)にパスフレーズの入力を求めますが、認証エージェントにおいては、一度認証されたものを再利用するときには、認証済みとして扱われるため無駄な認証を何回も行うことはありません。



# 一歩進んだOpenSSHの使い方

OpenSSH-6.6では次の3種類のポートフォワード機能をサポートしています。

- ローカルポートフォワード
- リモートポートフォワード
- ダイナミックポートフォワード

#### ローカルポートフォワード

コマンドラインから指定する方法

    ssh -L [ローカルマシンで待ち受けるIPアドレス]:転送元ポート:転送先ホスト:転送先ポート ログインホスト

コンフィグファイル(~/.ssh/config)に記述する方法

    Host ログインホスト
      GatewayPorts no
      LocalForward [ローカルマシンで待ち受けるIPアドレス]:転送元ポート 転送先ホスト:転送先ポート


- どちらもローカルマシンで待ち受けるIPアドレスは省略可。その場合はlocalhostが指定される。

**実際の使用例**

localhost:1389へアクセスすると、ログイン先のlocalhost:389(つまり、192.168.100.100のホスト自身)に接続されます。

    $ ssh -L 1389:localhost:389 192.168.100.100

<br>

`-g`オプションが指定されていますので、SSHクライアントを実行したホストのすべてのIPアドレスの3389/TCPが転送先ポートになります。ファイアウォールなどで制限されていなければ、別のマシンからもポートフォワードが利用できます。3389/TCPへアクセスすると、ログイン先からアクセスできる192.168.100.200:3389へ接続されます。

    $ ssh -g -L 3389:192.168.100.200:3389 192.168.100.100

#### リモートポートフォワード

コマンドラインから指定する方法

    ssh -R [リモートマシンで待ち受けるIPアドレス]:転送元ポート:転送先ホスト:転送先ポート ログインホスト

コンフィグファイル(~/.ssh/config)に記述する方法

    Host ログインホスト
      RemoteForward [リモートマシンで待ち受けるIPアドレス]:転送元ポート 転送先ホスト:転送先ポート


**実際の使用例**

リモートホストで待ち受けるIPアドレスが省略されていますので、192.168.100.100のlocalhost:10022で待ち受けます。
このままでは直接接続はできませんが、このポートへ接続すると、SSHクライアントを動かしているホストからアクセスできる192.168.10.10:22へ転送されます。
192.168.10.10で起動しているsshdが外部からの接続ができない環境にあったとしても、リモートポートフォワードを使用すれば192.168.100.100のホストを経由して接続が可能になります。

別ホストのsshd(ポート22)へ接続を転送する。

    $ ssh -R 10022:192.168.10.10:22 192.168.100.100


#### ダイナミックポートフォワード

コマンドラインから指定する方法

    ssh -D [ローカルホストで待ち受けるIPアドレス]:転送元ポート ログインホスト

コンフィグファイル(~/.ssh/config)に記述する方法

    Host ログインホスト
      DynamicForward [ローカルホストで待ち受けるIPアドレス]:転送元ポート

**実際の使用例 (localhost以外のApache server statusを参照する)**

ダイナミックポートフォワードを使用すれば、localhostからしかアクセスできないWebUIを閲覧することができます。
一例として、Apache httpdのserver infoやserver statusを閲覧する例を見てみましょう。ここでは、SSHでログインできるホスト(192.168.100.100)のApache server statusを閲覧します。
192.168.100.100のhttpd.confは以下のようになっており、localhost以外からのアクセスを拒否しています。

SSHでログインできるホスト(192.168.100.100)のhttpd.conf

    <Location /server-status>
      SetHandler server-status
      Order deny,allow
      Deny from all
      Allow from localhost
    </Location>


ダイナミックポートフォワードを有効にしてログインします。

    [localhost]$ ssh -D 1080 192.168.100.100

次に、クライアントのブラウザのプロキシを「種類: socks、アドレス: localhost、ポート: 1080」に設定します。

その後、ブラウザから「http://localhost/server-status」へアクセスします。するとserver statusの画面が表示されます(図略)

## プロキシと多段SSH

### プロキシを経由してSSH接続を行う

#### HTTPプロキシを利用する

プロキシを利用するために用いる`netcat`は、ブラウザなどで利用されるHTTPプロキシにも対応しています。`netcat`の接続先をHTTPプロキシにすることで、sshがHTTPプロキシを経由してリモートホストへ接続できるようになります。
`<proxy-server>`と`<proxy-port>'にはそれぞれプロキシサーバのホスト名/IPアドレスとポート番号を指定します。
%hはSSH接続先のホスト、%pにはSSH接続先のポート番号に展開されます。

HTTPプロキシを経由してリモートホストへ接続する方法

    [localhost]$ ssh -o proxyCommand='nc -X connect -X <proxy-server>:<proxy-port> %h %p' <target-host>

#### ユーザコンフィグファイルの利用

コンフィグファイル(~/.ssh/config)に記述する方法

    Host <target-host>
      proxyCommand nc -X connect -X <proxy-server>:<proxy-port> %h %p

設定時のコマンドラインからの実行方法

    [localhost]$ ssh <target-host>

### 多段SSH

#### リモートホストでsshを実行してログインする

この方法は、リモートホストでコマンドが実行できることを利用し、リモートホストでsshを実行することにより多段にログインを行います。
「リモートホストにログインしてコマンドを実行」するかわりに、手元のクライアントマシンから一気にコマンドを実行します。
仮想端末の割り当てが必要なことを除けば、とくにクライアントにもサーバにも制限はなく、手軽に使えるのが特徴です。

リモートホストでsshを実行してログインする方法

    [localhost]$ ssh -t <ssh-gateway> ssh <target-host>


#### ダイナミックポートフォワードを利用する

ダイナミックポートフォワードを利用し、リモートホストのsshdをSOCKSのプロキシにして接続します。
ログインしているリモートホストから接続できるホストであれば、手許の端末から直接接続できるようになります。

この方法は、ログイン元の端末で`netcat`が利用でき、ポートフォワードに制限がないことが条件になります。以下の例の通り、プロキシ接続用のセッションと、実際にログインするセッションの2つのsshを動作させる必要があります。

「ProxyCommand」で使用されている%hと%pはそれぞれログイン先のアドレスとポート番号に置き換わります。

このように特定のサーバを経由してSSH接続を行う場合、経由するサーバは「SSHゲートウェイ」と呼ばれることがあります。



ダイナミックポートフォワードを利用する方法

    [local#1]$ ssh -D 1080 <ssh-agent>

    [local#2]$ ssh -o proxtCommand='nc -x localhost:1080 %h %p' <target-host>

## 簡易VPN

簡易VPNは、通常のVPNに比べると効率も非常に悪くなっています。

VPNを常用する必要がある場合は、OpenVPNなどの別の手段を検討したほうが賢明です。

この本にはOpenVPNについて書かれていないので、ググろう。

## ほかのアプリケーションとの連携

### rsync

ホスト間でコピーを行う場合は、リモートホストにもrsyncがインストールされている必要があります。

rsyncの`-e`オプションを使用してsshにオプションを渡してファイルに転送する

    $ rsync -avz -e 'ssh -l root -p 2022' /home backupsv:/mnt/backup

上の例は、ローカルディスクにある`/home`ををbackupsvというリモートホストの`/mnt/backup`に2022ポートでrootユーザとして接続し、ファイルの転送をしています。

コマンドラインで指定している「backupsv」がsshで接続するホストになります。

ssh_configに次のように記述しておくことで、`-e`オプションをすべて省略できます。

ssh_configに`-e`オプションの指定を記述する例

    Host backupsv
      Port 2022
      User root


# SSHクライアントの設定


![](https://lh3.googleusercontent.com/VaUhK2s8Pz8Sk31SgCjTf8HBzrqpZHYnKJwldJ4tJzNPAMJpqHjayH9mENWZAQ8RCEM3hDS1HXay25bcfh_WZGqdNeDpzFQ6zzxhA5_FlLCFXj-5biZQ59kqNnYkSWyq1FIoX5XVHP9_tmM5arm9H274jZDX8dKQQ3oVdYLsCUEYXs2sjq0KnarDAfTUV3FjRIlPU4dt1nnIr_44Wb9xQOP2KVKZ21NDzzmQ88qrgiwzUYBAqE7gFdO7n5UVLNhOhIehBBddLccAi61sALX7UAoOgfnzuEOzL9r51UfVyummYJR8XY4d-Vk4IlqZGhsw3RZPjWko588aSGzdl0-BcoRJ-XazdmOpEDdsQfnhLizp1dEmobiuwg-YUZrNhlyfXki0U_WarPm8fdzBuvj_pRWZ0NuIPL0Fvkjz7ZJAxaKewjk8OrickSlvLV7XC-06JstP7EH5P_Ys4lRSDOOwiWpOkeLiJdbgW0S5a4M63vpTT66KwRIwBuB6NKJ4YxIF5f4-ml90dX35MhbYmiR1W7KkmP4uEEzZb775xPhPdEzF=w600-h800-no)
　

# OpenSSHの落とし穴

ここに記述
　

## 運用方法の再考

OpenSSHを使用することで「できることは何か？」を正しく把握し、許可するものは許可し、禁止するものは禁止するべきです。中にはOpenSSHの設定や機能だけでは対処できないものもあります。それらをふまえてセキュリティの観点から運用方法を見直すことも重要です。

### TCPポートフォワードの利用

TCPポートフォワードを使用すると、ファイアウォールのポリシーに関係なく、SSHの暗号通信を通じてトンネリングを行えます。

「ファイアウォールのポリシー」という視点で見れば、外部からのアクセスはフィルタされているにもかかわらず、SSHを通して外部からアクセスできるようになります。この視点でのTCPポートフォワードは悪であり、禁止すべき項目の一つです。

![](https://lh3.googleusercontent.com/kJU4mJEW-75aBZr2pJzfvJ5OoCgBcIj3wXBdY0lm3PqwGXfAHioyDdvDZ9-lnze2oB5LnDn5AbDk9anmtqX3g2Tjzen7MThZWyBClQbkmHBfVNyowVsg-A_5jr6mFayRE1nxA6ETIs5F1ve-ZVUtTq1lKCjJFRWYtjPgQysajIVizm7Gf8Q89ovm4MreskrmFBiG5SkHIukGxcOV6HL0osC1Wo9jZvt94DlsNkbULuxgs1D9jSJmd6BOha2EX7qfW8Y7_09lI2HrASkd5WL5egvjP4ZTkORXzBRLVySBjkgx_rw105nyg3nDdDFaIHlOUxYhmiwPcCK61sAN9dgyS5GmeBJY0zy52SLw_jq1ofblH3bT4D_cX6z_ALs7d9cju0AydfzTdpTNqAgtCZavQqNs-SvLxntLS-06QN5T1ola7yMKGo6oFaPQpSijfDMeasV7EHvkB8_gT7DJ_-0QoYT0R88tPd1yYEsUslvpu58ID4T6dzqHtv2rJRTXpb0DIlp_1kvufOA_9jjGQ3WmmNaMbEonlVLtk18aeFPqMWdX=w600-h800-no)

### プロキシの利用

### 多重防御と多層防御

#### 多重防御

#### 多層防御

![](https://lh3.googleusercontent.com/GZOx6OlrBeu75HyLypei0RcDebdMOYW1NO_c7eLrBQsUpxjd_aNKmbf3tzI3YmE9h_FQSFcKrJW-kGy9Q18pv-3pRNHhyybnAdYt91H5KdIoVuHpv6yrz5pmCkFq_Um_4wcFaBdUAgY1pxCVI4gtl55tTiWbzuR7EylLI1SuI8moic7C6oWlRU7yMIao52zeV6zATKm1GCow0X3tygoY_ClCutrznsplTu0OMx1OdkoqRNvjmrh1Aq7Sa-_gHqlR8u55FY4Z-hi1PAcvoapJ9zrkxzmiy2brtm8PyD-oJJ9TAbn2FCdp1Yno-EPziGi5EtwCwtmqXcDih9gA3gM_3zmn7fFfZ_xs1oWyl5uefy2BUN-Qattj168207dFL_cxc3hsNf_oy1DpcmqAqqfbQdP1e1Z5qKIMyIhHEaTUwRYxjAEOjOcSKAPhdfbH4C4a8cTnqJZ2V6tBMuNRYj1C7xH3HcHGUlqHI8Xk89vrNkWYdGrk1Y9V_TbYQVaxKT0wbRQb7QAXLMEhAUC1WL6GJR6bqWUbQbfAJxzA_qgDG2SY=w600-h800-no)


![](https://lh3.googleusercontent.com/poQQsf6YvDf69zeVwXY-GFkR9plG5VzRkBRBdpV4WHWKz9JgJ7hRMFdff0Mti4vNvVyH-Y96vk7eRpUTqpGHfHt3y4AQakutyarA57FlfBGkzejkga-qJ3pYvaYUPc2zw-kjk2ydczLjZDYOS3Fo7bI9vaoEUN8-zd4FZ9uOpHPTzuJm95RXz5yUvsS6115OwsmKXRxI5p2ubP3bLqSHwPq-tljIbiGkepWrcpCDl7FOE2zlvOL4-W9DcEY1JmxI2lRlLyNVaUxpeFsTT97UO0CbiH0U5_8uZt-_JhLY1qNstbTlUZHk7Q4R-osfYSE5ygdPo3IzrJyH-TB5yZmP0HeE5C-224xaitVgYRv2QhvBFYKMU7R-FELU1wYWmElliFCoRJHy_HlGn1tx5lIB0k3YNuwuWWp5YiaS39mwGnb8nsBKzH4QGF1ADC9-ASWSaXg4ranlNqcxdzPIM5aSbtaZfPhhucGMRDsFIIq4xF4-Btr75lF7GBCgk477HWl8veQItPsSnUHL6w-9UiUXvz4NK_HSIPVgZi331aE6FRZY=w600-h800-no)

# 設定事例集　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　

## Netfilter(iptables)を使用したアクセス制限

#### 接続元IPアドレスで制限

![](https://lh3.googleusercontent.com/G5_rjOizMuoYncZBNGKbKeL71_DTg9O9xxan87GMlkp0YluLoF8Moa-146lr-bixmqa6ZZ_ua6ZiPiELPl1C54Ld-NdMc6BQIvA59rxlxbHXrzJ0RSE7pc6hOecF5EzdR1n4-fP9PKrH1mAi-avEs_f4X52qHD2XuRiysmzk6QFwGKFu5hWSnk_cMN4s0ccYVpJmDeElpHy7QRkIKrHx-kTi_tX6uAJkH6yea1VpeMoYNHPkMQdwgerO66dkLsaDadlWVq4Rxg6Kd33AQZGgQJiY6ZK5J_Anj0a_4uuSbXVxSOYCpbsG2dhu5SJhRCkLf0Om_WPBSqfl0BYmkoz2Yhq6L2HjXhA9Q3wwn3etj1ieiLMpG7jPoYGenHR9J2j-cmk1HPx5PYqFGza1_BZsBuKBFUy6Qp4yRMsBH_vZ6Stf5rPOhMDYeR0_0GvS9NQ6NfyKHbNAH0iBQPQwDDEZjqFzYE1MwvGHPtsHu7Uah5DcngEqkGeMm75Lwg3-lTib2qG7VDzKh2O6d1COivpHE0l7Awflyq8b5Wd71SpO6DDG=w600-h800-no)

![](https://lh3.googleusercontent.com/YCbEDO-923z7PHj7AFX8MGjypGx2yysE09_3KZZwQqqStKpPaUKdpELCH6KZj22tqre36FQI3YES9m7gJunOguosWI5sk5bK8fYvrKLQtFv1a_DrtlVn8fC8fzs1Fe2xltA7hWfFapV2ZexZ0gZHKSnMUFQn5ucCD4X3XixSKUQcg0ThHzNtGDOcwsK3aNDszjzghwjJvyurMXIMWunJeQYF_55PatWfnCmFrZjlXlimyuKDr9_ncNQzjikyjOdmZcxjw43zshc8hm0JrlHThN2ipjecXXCawVs1j_oTiUCZrX-k6n4SRSdTsI6kCditzKOKLyVShilAOjRAhsy6WcGNXZMyTiSe0eu62EMOWpi96nFENoED5NAEq6FCqBdsFt6XMRX3EkEnxUCdE_FFJKyeViSNlbErXGLRVvF864SoP-cPVPpqHzwr_G6y5FwGvBI_tbDwOYOOwixSEXnQw4QP8Bv6deP2b2tiYgnw1CSMpnhjJ8O6H5QBDAiD9ijT_V90ZLXrKOfNptS0XgxRySbT71yVdYe-_FA79Ij02RRL=w600-h800-no)

![](https://lh3.googleusercontent.com/ZeDFkprmb2o_v2eCZTSfSGp_ZuVmFq05Y8LdeCZuSHF8YMVt3_lgiUh9-qR4tk6dXtpQgg_B3OqyyseL6k1QGNGTxGTIIxSU3cLkBifxGSLma2XwYU2DZxy4dP3zKZbckX0Fizr06tIAaKjLWRyCKzMUJ1-PN1riWj_mxrc81ZLrqc7bJOTINtOoFz6rR2Gjam2EGzjf1Kr7ivGWaO2aE-sHRQ934QSlr1Y1c1c1WcPN0Ec1zyRdQwZrDJcujExQfSBSuh6MYjglIvhv3e5l-3RPpgdBST_RKOk--0bGZiKEZi7TxObffABxNXNQq2KRmhLK0IYD0L5OuFvCDeUlFoiiPdnqeGAQypacQvG8Bp-Z0dZQyH9sFgCkqMv6139Ap8d5uMETO_eo9VeN4K0m4mrbV0UOa9G6HB9C4y_UkdlGZrzLOxpfVMRJ9sr60mQXZzxftXTvnMmifAf6fSGY3iSXtb5YdruKRb0FOC9IyEhgG5hTQQQ3FQxnBJV0hYixCVQ4STEnXyjCBh4mYf-b89A796huZPHAcWl9juq-uYpD=w600-h800-no)

#### NetFilterを利用した制限の弱点

![](https://lh3.googleusercontent.com/PQ0c1eCCZI7P9X4JSaVxusmQ0bvPRecyvj2MGIQZfGJIaImEhWyK3I6a1xC_JavZY2WWlb78iaqyCgVskRRFRSFRNY2thUi3eFO0wXo_ggeLx8sScM0Ms0BI6ztGbZ1XCuoe9ikX9V97Zaefgve8SIkiqc0P3JoU19plkKBuyzT5alj-2RdbBoLUyduLKQD3C4247orYHBQbPaAG2-yDuEGz6L8CMlkDqeGmGop0rFbEGc6RgPsiUbIBdpzK9Lyp-8U1Afp2csDbx6nwsm7asSohs8T5K1C-8fBCC6mftk6ZZ3lU1aEn4Ixmx8jr4qOOgyuQ9Ci0pDgaj65CjP5NMyZr33MfH3A5lR6ymViEm5aZF1td6g0Eoi26h8OkSNQaNw3AfNY0T6sMFX1iSqFUfriThOldkQCuUtdORhEfD0-UaOmJ0LiWeo1QIPzu4Y5z44YoEv8WtRrH0cCBg2JiJ3RVpnGnjeIG67-48X6D06V1ZMbwKQX5JADWaGVxRG_rC1OaTDrb4FmBmIYWHrDvecMDy1Dp6Ujh7GLrjo36rgq2=w600-h800-no)


### 必要最低限の設定例

リモートホストへログインするために必要な最低限のsshd_config設定を記述しています。

- rootユーザのログイン拒否
- 公開鍵認証のみを許可
- 転送機能をすべて禁止
- 暗号方式をAESのみに限定
- TCP Keep AliveではなくClientAliveを使用

![](https://lh3.googleusercontent.com/sc1YPyj2NqFq6diNK5NJslZSBiJnVmm27yWgttF-GP94uIdSGgTgU-5JQR4-FkE9tSj_Z8RPO8FFvBK__x7tvBhD95lQmgCwq9BF6ug0Lt39gE6zxa5TWSlnztvjjszWYHUwQ2-kIlRDp8eMQ2MGHie3_o8QzclmvVdDXW-mtilaRwfEXCuEBKG3UDOD-4b2xpDJ6uKqzJaOKdK_DxnX1ddkO0HK7xVyyyB5IiiBWk2whpvb2bLe2EOHzaHI-D9WvwNRreVub7aqVbyFv22bV89I1srAmOvhL3wjFq3dQstErzffS8za97RW2Vb3oJqVl14aPzBqyfV0bkh1yHgbrB7kDqTqbVfdXrLXbPIz6NgiJbJZ8O0ROs2knH6xMwrXjo9Q2wgRuPpfOqui2YdkIaQdwb8CgEHE7XAi8Ej9cg7ZLutFBjC4euqUYAY74Omi09I7JVQ6yHfHk2oV9H1F2mWJKzP9Jvg6Jxw64w2oLQ6JclLL4_MW5mo3ji0yE8ybPK5N37hEOfuMAsAyvoddFObrI_ZA07fbLUMaLeNpz-H6=w600-h800-no)

![](https://lh3.googleusercontent.com/83OpL7aXbi0l7oKu2Y5PNOXp5yIHP1s-NRL-9FtRjRW2w4hw8EGua-Z4ToOnoVsHYxa-roJATzWeBKZdnsCIOW-kwXR3QsbhVr9Pn2jKo-HJRGgfN_wWhcP7nEGbFARbZvq0XcNY4S7FWnRdeOAfidqLlRQgV11MbCRclRi_elkplYdDFPJZCVnitJN-FGkj6Cu5mvJr2n0p2bvGqnQElbMaCilDyiEkFrUwZEVJfkbzdxta3rt-PKeE3NTzuDVi7N8ealSWJgsmRbr7gGXOIk9FpVP4fdKDBTX6waBp1DihkuxtJtxI2PYVa7lbr-WsJ_5DwNl8paHF9bFDFTWTA6ochvZjjTW0TFRWO4DGRjYa26tjRbYalvat5M1Coopx-Vm5dOsqdRR1rwN1zpL1LG9q-bfT_U2u4ImKId9YRLlmSextWgRa556taijYG0_F2kZqeYschb5FX44saAjeKgGAwzGo_S5SAsqBmzX4LwFxLJoqKjfalGWbPtehm0qbESycLqusVLnYnCPMIX-lKyIcmAxmIBJCA8o07xav8ZEc=w600-h800-no)

![](https://lh3.googleusercontent.com/FAO_Phl1ulrmW4n_DQz7i2fGgWNFHvDglLTfotyj0Oh6BSJeXfhl6yI8r6YTayg4mZ7JFF6DQlagc1fYA4vdSAA9CqK0aQYaxkNNx0gxZy_RQwBdKFn5zQ9QmqYC7c4wz-nfJHPrz4TM3XfKgJm3_9yKKeozsiAnIaIejPO3b10I_G2jSTgy5HX0DeM8_npbgmQwTgeKO7uXFPMP5vauLuHdtjNZ2o1XWMc8Qvo81jDzwflSnwB3r843TJR3EtNphT7VDVY_VEPKYGqL_mKSSutB1MeKR01uIbeesu8sD0xXP8FmnLGYKU9d66QEM9miWu3ZMxxBtkD5Bhf5g5Zqu55x7ej_9mqdN7V5LUee2wJEKo8Qb4zF2WU3bgql5Ym10oepFdjYHm3p7l6-qWOBbitLqAttM6EDCF4SkgXCO_bzDJ9qyxyywqGk-AZjWV17-Ve2_GQMsZ7K_2QsoeutKB_OdKN9Cgg_hxWHGu74I8Jnd6qPNHYKDvx28NfjSloAM0Q1H4AZ2nczpP5vAbpGAVSxaa-oXVlQPEkkfoR5Q4pH=w600-h800-no)