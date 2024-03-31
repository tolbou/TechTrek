# **AWS でアプリを動かしてみよう**

## **概要**

EC2 インスタンス内に Rails アプリを動かす環境を作っていきます。

最初に PC を購入した際に Rails が動かせるように環境構築をしたことと同様にインスタンス内も同じく Rails が動くように環境構築をしていきます。

EC2 にログインして、その中のターミナルでコマンドを実行していきます。

## **EC2 内の環境構築**

### **利用パッケージの追加**

まずはパッケージをアップデートします。下記のコマンドを打ちます。

```
[ec2-user@ip... ~]$ sudo yum -y update

```

EC2 の初期状態では最低限のパッケージしかインストールされていないので、利用するパッケージをインストールします。

下記のコマンドを 1 行ずつ実行してください

```
[ec2-user@ip... ~]$ sudo yum remove mariadb-libs
[ec2-user@ip... ~]$ sudo rm -rf /var/lib/mysql
[ec2-user@ip... ~]$ sudo yum install -y https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
[ec2-user@ip... ~]$ sudo amazon-linux-extras install epel
[ec2-user@ip... ~]$ sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023

```

下記のコマンドを`\`を含めて全て実行してください。

```
[ec2-user@ip... ~]$ sudo yum -y install \
git make gcc-c++ patch curl \
openssl-devel \
libcurl-devel libyaml-devel libffi-devel libicu-devel \
libxml2 libxslt libxml2-devel libxslt-devel \
zlib-devel readline-devel \
mysql-community-server mysql-community-client mysql-devel \
ImageMagick ImageMagick-devel \
epel-release

```

### **nodejs インストール**

次に node.js のインストールをします。

まずは node.js のパッケージをダウンロードします。

```
[ec2-user@ip... ~]$ curl -sL https://rpm.nodesource.com/setup_16.x | sudo bash -

```

次に node.js をインストールします。

```
[ec2-user@ip... ~]$ sudo yum install -y nodejs

```

※上記実行時に別のバージョンの node.js をインストールしようとしているログが出てしまっている場合はキャッシュが残っている可能性があるので下記のコマンドを実行してから再度 nodejs インストールの項目を実行してください

- ```

  ```

下記コマンドでインストールされたか確認しましょう！

```
[ec2-user@ip... ~]$ which node
/usr/bin/node

[ec2-user@ip... ~]$ node -v
v16.20.2

```

### **yarn インストール**

yarn も利用するのでインストールします。

nodejs と同じように yarn のパッケージをダウンロードします。

```
[ec2-user@ip... ~]$ curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo

```

yarn をインストールします。

```
[ec2-user@ip... ~]$ sudo yum -y install yarn

```

### **ruby インストール**

ruby を利用できるように、rbenv と ruby-build をインストールしていきます。

```
# rbenvインストール
[ec2-user@ip... ~]$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
[ec2-user@ip... ~]$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
[ec2-user@ip... ~]$ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
[ec2-user@ip... ~]$ source ~/.bash_profile

# ruby-buildインストールしてrubyをインストール
[ec2-user@ip... ~]$ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
[ec2-user@ip... ~]$ rbenv install -v 3.1.4
[ec2-user@ip... ~]$ rbenv global 3.1.4
[ec2-user@ip... ~]$ rbenv rehash
[ec2-user@ip... ~]$ ruby -v
ruby 3.1.4p223 (2023-03-30 revision 957bb7cb81) [x86_64-linux]

```

## **アプリデプロイ**

EC2 内にアプリのコードを送ります。

GitHub 利用をしてコードをクローンできるようにするため、EC2 上で公開鍵と秘密鍵のペアを作成します。

### **鍵作成**

下記のコマンドを参考に公開鍵と秘密鍵を作成します。

```
[ec2-user@ip... ~]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ec2-user/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
# 何も入力せずそのままエンター
Enter same passphrase again:
# 何も入力せずそのままエンター
Your identification has been saved in /home/ec2-user/.ssh/id_rsa.
Your public key has been saved in /home/ec2-user/.ssh/id_rsa.pub.
The key fingerprint is:
~~省略~~

```

### **GitHub に公開鍵を登録**

GitHub と接続できるように作成した公開鍵を登録します。

```
[ec2-user@ip... ~.]$ cd .ssh
[ec2-user@ip... .ssh]$ cat id_rsa.pub
# ここに表示された文字列を全てコピーする(ssh-rsaから)

```

自分の GitHub にログインし`Settings`->`SSH and GPG keys`->`New SSH key`に進んで登録します。

- Title：わかりやすい名前
- Key：先ほどコピーした公開鍵

これで EC2 と GitHub で SSH 通信ができるようになりました。

確認してみましょう！

```
[ec2-user@ip... ~]$ ssh -T git@github.com
# 下記メッセージが出たらyesを入力する
Are you sure you want to continue connecting (yes/no)?

```

「Hi `GitHubアカウント名`....」が出れば接続完了です。

### **デプロイするアプリの準備**

今回は[こちら](https://github.com/runteq/v3_deploy_sample_app)のアプリケーション利用します。

上記アプリケーションを GitHub 上で、ご自身のリモートリポジトリにフォークしてください。

### **GitHub からコードクローン**

サーバー上の操作に戻りまして、コードの保存ディレクトリを作成します。

```
[ec2-user@ip... ~]$ cd /
[ec2-user@ip... /]$ sudo mkdir /var/www/
[ec2-user@ip... /]$ sudo chown ec2-user /var/www/
[ec2-user@ip... /]$ cd /var/www
[ec2-user@ip... www]$ git clone git@github.com:アカウント名/アプリ名.git

※ git@github.com:アカウント名/アプリ名.gitのところは先程のフォークしたリモートリポジトリを参照して適宜修正してください。

```

## **本番用のアプリの設定**

### **master.key 設定**

本カリキュラムで使用するアプリケーションでは特に操作は必要ありません。

（＊本来であれば秘匿情報である maskter.key を手動で設定する必要があります。通常 master.key などの秘匿情報は GitHub 上に掲載しません）

以下、master.key の設定の一例になります。

**※ 本カリキュラムでは操作不要です**

```
- ローカルのmaster.keyの値を習得
- EC2のサーバーに接続して以下のコマンドを実行
  - cd /var/www/アプリ名
  - vim config/master.key
    - ローカルのmaster.keyの値を入力・保存

```

### **database.yml の設定**

RDS 作成した際の database 接続設定をします。

下記コマンドで database.yml を編集しましょう！

`vim config/database.yml`

```
default: &default
  adapter: mysql2
  encoding: utf8mb4
  charset: utf8mb4
  collation: utf8mb4_general_ci
  username: 設定したユーザ名
  password: 設定したパスワード
  host: RDSのエンドポイント
  pool: 5
  timeout: 5000

production:
  <<: *default
  database: runteq_production # なんでもOK

```

### **gem のインストール**

`gem list bundler`と打つと bundler のバージョンが確認できるので打ち込んでみましょう。

```
[ec2-user@ip... `アプリ名`]$ gem list bundler

***LOCAL GEMS***

bundler (default: 2.3.17)

```

デプロイで必要なローカルと同じ bundler のバージョンをインストールする必要があります。

```
[ec2-user@ip... `アプリ名`]$ gem install bundler -v x.x.x
x.x.xにはローカルと同じバージョンを指定してください

```

準備ができたので gem をインストールします。

```
[ec2-user@ip... `アプリ名`]$ bundle install

```

※ここでエラーとなりファイルの編集や gem の追加が必要になった場合は、EC2 上で作業せずにローカルで作業して push したものを EC2 上で pull してください。

リポジトリのソースコードを更新しないと、別環境でデプロイした際に同じ内容のエラーが再発するため注意しましょう！

### **DB 作成**

```
# DBを作成
[ec2-user@ip... `アプリ名`]$ rails db:create RAILS_ENV=production
# テーブル作成
[ec2-user@ip... `アプリ名`]$ rails db:migrate RAILS_ENV=production

```

※デフォルトでは develop 環境への実施になるので、production 環境に対して実施してください

### **Nginx のインストール**

本番環境は開発環境とは異なり、複数のユーザーから大量のアクセスが発生するので、アプリケーションサーバーだけではリクエストを処理しきれません。

そのために`リクエストごとに発生する処理をさばいてくれる`web サーバーが必要になります。

今回は web サーバーの nginx を利用します。

まずはインストールします。

```
[ec2-user@ip... ~]$ sudo amazon-linux-extras install nginx1

```

`Is this ok [y/d/N]:`を聞かれた場合は`y`を入力して進んでください。

### **Nginx の起動**

下記コマンドで nginx を起動して、ブラウザで nginx が起動しているか確認します。

```
[ec2-user@ip... ~]$ sudo systemctl start nginx

```

EC2 に設定した ElasticIP をブラウザのアドレスバーに打って以下の nginx のページが表示がされたら ok です。

!https://img.esa.io/uploads/production/attachments/2315/2019/10/02/53778/3161988b-bbfa-4157-899b-6a130cd05e5e.png

今は Nginx デフォルトのページに飛ばされているのでこれを Rails アプリに飛ぶように設定をします。

### **Nginx の設定ファイルを作成**

Nginx の設定ファイルはインストールした時に生成されています。

`/etc/nginx/nginx.conf`がデフォルトの Nginx の設定ファイルです。

新しく設定ファイルを作成して`/etc/nginx/nginx.conf`に読み込ませて設定します。

```
[ec2-user@ip... ~]$ cd /etc/nginx/conf.d
[ec2-user@ip... conf.d]$ sudo vi runteq-infra.conf

```

下記を参考にファイルを作成します。

※複数箇所にディレクトリ名を指定する箇所があるので自分のアプリと合わせる必要があるので注意。

```
error_log  /var/www/アプリ名/log/nginx.error.log;
access_log /var/www/アプリ名/log/nginx.access.log;

client_max_body_size 2G;
upstream runteq-app {
  server unix:///var/www/アプリ名/tmp/sockets/puma.sock fail_timeout=0;
}
server {
  listen 80;
  server_name xxx.xxx.xxx.xxx; # 作成したEC2の ElasticIPアドレス。
  keepalive_timeout 5;
  root /var/www/アプリ名/public;
  try_files $uri/index.html $uri.html $uri @app;
  location @app {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://runteq-app;
  }

  error_page 500 502 503 504 /500.html;
  location = /500.html {
      root /var/www/アプリ名/public;
  }
}

```

### **Puma を起動する**

puma の本番設定ファイルを確認します(ない場合は作成しておきましょう)。

config/puma/production.rb

```ruby
environment "production"

tmp_path = "#{File.expand_path("../../..", __FILE__)}/tmp"
bind "unix://#{tmp_path}/sockets/puma.sock"

threads 3, 3
workers 2
preload_app!

pidfile "#{tmp_path}/pids/puma.pid"# stdout_redirect "#{tmp_path}/logs/puma.stdout.log", "#{tmp_path}/logs/puma.stderr.log", true

plugin :tmp_restart

```

下記で起動させます。

```
[ec2-user@ip... ~]$ cd /var/www/アプリ名/
[ec2-user@ip... アプリ名]$ RAILS_ENV=production rails assets:precompile
[ec2-user@ip... アプリ名]$ bundle exec puma -C config/puma/production.rb -e production

```

puma 起動時に下記のエラーが起きてしまったら rails ディレクトリの tmp 配下に`sockets`というディレクトリを作成してみましょう(`mkdir /var/www/アプリ名/tmp/sockets`)

```
bundler: failed to load command: puma (/home/ec2-user/.rbenv/versions/3.1.4/bin/puma)
/home/ec2-user/.rbenv/versions/3.1.4/lib/ruby/gems/3.1.0/gems/puma-5.6.4/lib/puma/binder.rb:196:in `realdirpath': No such file or directory @ realpath_rec - /var/www/v3_deploy_sample_app/tmp/sockets (Errno::ENOENT)

```

### **Nginx を再起動する**

Puma がうまく起動できたら Nginx を再起動する。

（※Puma は起動状態にしておく必要があるので、Puma 起動中のターミナルとは別に新しくターミナルを立ち上げて以下を実行してください。）

```
[ec2-user@ip... アプリ名]$ sudo nginx -s stop
[ec2-user@ip... アプリ名]$ sudo service nginx start

```

### **アクセス**

ElasticIP のアドレスにアクセスしてみましょう。

サンプルアプリの場合はルートディレクトリが定義されていないので`ElasticIPのアドレス/posts`でアクセスすると良いでしょう。

もしエラーが発生していた場合はログを確認して修正してみてください。

Rails のログはどこにあるでしょうか、探してみましょう！

そのエラーを解消すればアプリが使えるようになっているはずですね！！

!https://t.gyazo.com/teams/startup-technology/b8c6b22326e93b09c10c02a9da21c416.png

## **お片付け**

クラウドサービスなので、構築内容によっては起動していなくても金額が発生してしまいます。
d
完了したら、作成した環境を削除しておきましょう。

- EC2 は停止して削除する。
- ElasticIP を「アドレスの関連付けの解除」して、ElasticIP アドレスを解放する。
- RDS は停止と削除する。RDS は停止をしても 7 日後には自動的に起動する仕様なので「削除」までやりましょう。
- その他の VPC やらサブネットやらは残したままでも OK です。
