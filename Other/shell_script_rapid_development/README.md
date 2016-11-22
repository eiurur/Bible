- [ ] sysstat

サーバの負荷などを計測して保存するツール

    sudo apt-get install sysstat

    cat /etc/default/sysstat

    ENABLED="true" <- ここが最初はfalseになっているのでtrueにする

    service sysstat start

- [ ] scp

作った公開鍵をリモートに送る

  scp .ssh/id_rsa.pub eiurur@172.1.1.1:~/


リモートマシンにログインして、所定の場所に鍵のリスト（authoruzed_keys）を作る

  ssh eiurur@172.1.1.1

  mkdir .ssh

  chmod 700 .ssh/

  cat id_rsa.pub >> .ssh/authorized_keys

  chmod 600 .ssh/authorized_keys


- [ ] nmap

ポートスキャン

    sudo nmap -sT -P0 -p 1-65535 172.1.1.1


- [ ] nslookup

    nslookup test.eiurur.xyz

# 2章

- [ ] gawk

GNU awk。ローカル、リモートともにGNU Awkで日本語を扱うことがでいます。逆に日本語を特別扱いしてほしくないばあいは、コマンドの頭にLANG=Cをとつける。

    gawk '{sub(/./, "川"); print}'

    LANG=C gawk '{sub(/./, "川"); print}'

- [ ] curl

- [ ] wget

curlと違て、デフォルトで得たファイルを標準出力ではなくファイルに出力してしまう。

- [ ] nkf

ネットワークに流れるいろんな文字コードの日本語を変換するためのコマンド

    echo ア イ ウ エ オ | nkf --hiragana


-----

- [ ] date

    date +%Y%m%d%H%M%S

- [ ] dirname, basename

dirname ・・・ ファイル名をパスから除去してディレクトリのパスを標準出力に出力する

    dirname /dir1/dir2/file1
    > /dir1/dir2

    dirname file1
    > /

    dirname ../var/dir1/dir2/file1
    > ../var/dir1/dir2

basename ・・・ パスで指定されたファイル名からファイル名だけを取り出すときに使う

    basename /a/b/c
    > c

    basename ~/b/c
    > c

    basename c
    > c

- [ ] rsync

