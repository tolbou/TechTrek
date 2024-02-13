# 概要

実装したコードの品質を確保し、バグを見つけて修正するための重要な手法として、自動テストがあります。

Railsアプリケーションにおいて主要なテストフレームワークであるRSpecについて学んでいきましょう。

またGitHubを利用してコードを管理する方法についても学んでいきます。

## 学習の意識ポイント

- 自動テストであるRSpecは何か
- RSpec利用するための準備
- GitHubでのPullRequestの作成方法

## ブランチ名

01_setup

## インプットフェーズ

### 自動テストとは

プログラミングにおいて、自動テストはWebサービスの品質を確保するための重要な手法です。

自動テストを使うと、プログラムの動作を自動的にチェックするテストコードを書くことができます。

プログラムに変更を加えた後でも、以前の機能が正しく動作するかどうかを素早く確認することができます。自動テストにより下記のようなメリットがあります。

- プログラムの品質向上：テストコードにより、バグやエラーを早期に発見できます。また、プログラムの機能が正しく動作することを確認できるため、品質向上につながります。
- リファクタリングの安全性向上：ソフトウェアの改善や修正を行う際に、自動テストがあると変更が予期せずに問題を引き起こす可能性を減らすことができます。
- チームの効率化：複数の開発者が同じプロジェクトで作業する場合、自動テストはコードの一貫性を保ち、互いの変更が互換性を持つことを確認するのに役立ちます。

### RSpecとは

RSpecは、Rubyプログラミング言語用のテストフレームワークです。Railsと組み合わせて使用することが多く、Railsアプリケーションの自動テストを作成するために便利です。

RSpecはプログラミング初学者にも利用しやすいテストフレームワークです。自然言語に近い構文を使用するため、テストコードの読み書きがしやすくなっています。

また、RSpecの豊富な機能を活用することで、初学者でも効果的なテストコードを作成することができます。

初めてのテストコード作成においても、RSpecを利用することで品質の高いコードを開発する手助けになります。

## 実践フェーズ

1. 環境構築
2. RSpecのセットアップ
3. PullRequestの作成
4. 解答から自分のコードを確認&修正
5. PullRequestのマージ
6. 課題完了、次のchapterへ進む

### 環境構築

### GitHubのリポジトリをforkする

課題で利用する環境はGitHubに準備されていますので、自分のアカウントのリポジトリにコピーします。

GitHubでは、このコピー操作を「fork」という機能を使って行います。

以下の手順に従ってforkしてください。

1. [こちら](https://github.com/runteq/basic_rspec_app)をクリックしてGitHubの該当のリポジトリに移動します。
2. forkボタンをクリックします。
    
    !https://t.gyazo.com/teams/startup-technology/9a1056cfe66b28b4955b0209cf381209.png
    
3. Ownerが自分のGitHubアカウントであることを確認し、Create forkボタンをクリックします。
    
    ※もしクリック後に500エラーページが表示された場合は、ブラウザをリロードしてみてください。
    
    !https://t.gyazo.com/teams/startup-technology/66490dbef1dca846785ea1f40ddd9b39.png
    
4. 下記の赤枠内が自分のGitHubアカウント名であればfork完了です。
    
    !https://t.gyazo.com/teams/startup-technology/61f9e12966c00fa3f06a3f160c17f376.png
    

### cloneしてローカルに環境構築する

forkが成功したので、git cloneコマンドでローカルの環境にコードをcloneします。

1. リポジトリのURLをコピーする
    
    先ほどforkした自分のリポジトリからcloneするURLを取得します。
    
    codeをクリックし、表示されたURLをコピーします。
    
    !https://t.gyazo.com/teams/startup-technology/5b3914d50cd362e7363d794edb6f239d.png
    
2. ローカルにcloneする
    
    ターミナルで作業ディレクトリに下記のコマンドでコードをcloneします。
    

```
git clone コピーしたリポジトリURL

```

ローカルにRailsのプロジェクトがcloneされたら完了です。

### RSpecのセットアップ

作業ブランチを作成して、RSpecを設定していきます。

以下の流れに従って、必要なGemのインストールし、RSpecが実行できるところまで進めていきます。

### Gemのインストール

下記を参考にGemfileのdevelopmentとtestの環境にgemを追記し、bundle installをします。

```
...
group :development, :test
  gem 'rspec-rails'
  gem 'factory_bot_rails'
end
...

```

### 設定ファイルの作成

gemのインストールが完了したら、下記のコマンドでRSpecの初期設定をします。

```
bundle exec rails g rspec:install

```

### テストファイルの作成

今回はTaskのmodelのテストファイルを作成してテストを実行します。下記のコマンドでテストファイルを作成します。

```
bundle exec rails generate rspec:model Task

```

### テストの実行

最後に下記のコマンドで、作成したテストファイルを実行します。

```
bundle exec rspec spec/models/task_spec.rb

```

下記のようにテストが正常に実行されるか確認してください。

!https://user-images.githubusercontent.com/28836274/67480376-bc88f700-f69a-11e9-80e0-40af0b543b94.png

正常に実行されたことを確認して、この時点での修正内容をcommitして、GitHubにpushします。

次はPullRequestを作成します。

### PullRequestの作成

GitHub上でPullRequestを作成します。

- PullRequestを作る
    
    forkした自分のリポジトリの画面から、赤枠のPull Requestタブをクリックし、New pull requestボタンをクリックします。
    
    !https://t.gyazo.com/teams/startup-technology/8237a9f574aed1368037d7b194f4e3a1.png
    
- 対象のリポジトリを選択する
    
    PullRequest設定画面に遷移したら、赤枠のbase repositoryをクリックし、自分のアカウントのリポジトリを選択します。
    
    !https://t.gyazo.com/teams/startup-technology/d8b3d01b11bc1f19b270009a58bb70ba.png
    
- 対象のブランチを選択する
    
    赤枠のcompareをクリックして、今回実装した01_setupのブランチを選択します。
    
    !https://t.gyazo.com/teams/startup-technology/45296ba8601b3587a57bcc480a0d0069.png
    
- PullRequestの概要を作成する
    
    Create pull requestボタンが出てくるのでクリックします。
    
    !https://t.gyazo.com/teams/startup-technology/82be696e663308f3989de99acf908488.png
    

次に今回実装した内容を記載するフォームが出るので、下記の画像を参考に記載してください。

記載できたら、Create pull requestボタンをクリックして完了です。

!https://t.gyazo.com/teams/startup-technology/88d63926008a0ee2ebb58c280f7d7933.png

### 解答例を確認してPullRequestのマージ

最後に解答例を確認して、修正等なければGitHub上からPullRequestをマージします。

### PullRequestのマージ

最後にブランチをdevelopにマージして完了です。

下記を参考にGitHub上でマージしてください。

作成したPullRequestの画面の下部にMerge pull requestボタンをクリックします、これでマージ完了です。

!https://t.gyazo.com/teams/startup-technology/fe697897eac77364f09d0bd4e0cdaed5.png

### 課題完了！次のchapterへ

GitHubのdevelopブランチに実装した内容が反映されているかと思います。

自分のローカルにもdevelopのコードをgit pullコマンドで反映させておきます。

次のchapterからもこのような流れで進めていきますので、PullRequestの作成に困ったらこちらの流れを見返すようにしてください。