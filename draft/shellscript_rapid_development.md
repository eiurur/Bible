

    [vagrant@debian-7] /vagrant/github/shell-script-learn
    % seq 1 10 | awk '/1/{print $1 + 1}'
    2
    11

    [vagrant@debian-7] /vagrant/github/shell-script-learn
    % seq 1 10 | awk '/1/{print $1 "yen"}'
    1yen
    10yen

    [vagrant@debian-7] /vagrant/github/shell-script-learn
    % echo {1..10} | sed 's/5 /5\n/'
    1 2 3 4 5
    6 7 8 9 10

    [vagrant@debian-7] /vagrant/github/shell-script-learn
    % echo {1..10} | sed 's/5 /5\n/' > input

    [vagrant@debian-7] /vagrant/github/shell-script-learn
    % cat input
    1 2 3 4 5
    6 7 8 9 10

    [vagrant@debian-7] /vagrant/github/shell-script-learn
    % cat input | awk '$4==9'
    6 7 8 9 10.1