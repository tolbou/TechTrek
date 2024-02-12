## ActionMailer

Action Mailerを使用することで、Ruby on Railsアプリケーションからメールを送信する機能を簡単に統合し、管理できます。Action Mailerを詳しく説明するために、その設定、メーラーの作成、ビューの利用、メール送信のプロセスについて、より詳細に見ていきましょう。

### **Action Mailerの設定**

Action Mailerの設定は、アプリケーションの**`config/environments`**ディレクトリ内の環境別の設定ファイル（例：**`development.rb`**、**`production.rb`**）で行います。メール送信方法（SMTP、Sendmailなど）、送信元のメールアドレス、SMTPサーバーの設定などを指定できます。

例えば、開発環境でのSMTP設定は以下のようになります：

```ruby
rubyCopy code
# config/environments/development.rb
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = {
  address:              'smtp.example.com',
  port:                 587,
  domain:               'example.com',
  user_name:            '<ユーザー名>',
  password:             '<パスワード>',
  authentication:       'plain',
  enable_starttls_auto: true
}

```

### **メーラーの作成**

メーラーはメール送信に関するロジックをカプセル化するクラスです。**`rails generate mailer`**コマンドを使ってメーラーを生成できます。例えば、ユーザーへのウェルカムメールを送信するメーラーを生成するには、以下のコマンドを実行します：

```bash
bashCopy code
rails generate mailer UserMailer

```

このコマンドは、**`app/mailers/user_mailer.rb`**にメーラークラスを作成し、関連するビューファイル用のディレクトリを**`app/views/user_mailer`**に生成します。

### **メーラークラス内でのアクションの定義**

メーラークラス内でメールを送信するためのメソッド（アクション）を定義します。例えば、ウェルカムメールを送信するメソッドは以下のようになります：

```ruby
rubyCopy code
class UserMailer < ApplicationMailer
  default from: 'no-reply@example.com'

  def welcome_email(user)
    @user = user
    @url  = 'http://example.com/login'
    mail(to: @user.email, subject: 'Welcome to My Awesome Site')
  end
end

```

### **ビューの利用**

メールの本文はビューテンプレートを通じて定義します。メーラーのアクション名に対応するビューファイルを**`app/views/user_mailer`**ディレクトリ内に作成します。例えば、**`welcome_email`**アクションに対するビューは、**`welcome_email.html.erb`**（HTML形式のメール）と**`welcome_email.text.erb`**（テキスト形式のメール）の2つを作成できます。

### **メール送信のプロセス**

メール送信は、アプリケーションのどこからでもメーラークラスのインスタンスを作成し、定義したメソッドを呼び出すことで行います。メールを即時に送信する場合は**`deliver_now`**メソッドを、バックグラウンドで送信する場合は**`deliver_later`**メソッドを使用します。

```ruby
rubyCopy code
user = User.find(1)
UserMailer.welcome_email(user).deliver_now

```

Action Mailerを使うことで、メール送信のためのロジックをクリーンに保ちながら、メールの内容や送信プロセスを柔軟にカスタマイズできます。また、開発環境でメールのプレビュー機能を利用することで、実際のメール送信を行わずにメールのレイアウトや内容を確認できるのも大きな利点です。