## Punditとは

PunditとはRubyのgemであり､｢認可｣の仕組みを提供するものです｡

｢ユーザーによってページの表示の許可・拒否をしたり､表示情報の範囲を変えられるgem｣です｡

Punditはcurrent_user(ログイン中のユーザー)メソッドを扱うので､sorcery gem などが｢認証｣の仕組みありきです｡認可は認証に依存しています｡

似ているgemで`cancancan`がありますが､ちょっと違います｡

# 認証と認可とは

認証と認可は似ているようで全く別の概念です｡

## 認証 Authentication

通信の相手が誰(何)であるかを確認することが｢認証｣

純粋な認証には｢リソース｣やそれに対する｢権限｣という概念はありません｡

いわゆる｢証明書の確認｣のようなものです｡

マイナンバーカードで身元を確認するようなことです｡

ただ､〇〇さんというのは確認できても､何かが許されるといった話は関係ないです｡

つまり､相手が誰なのか確認する｡それだけです｡

## 認可 Authorization

とある特定の条件に対して､リソースアクセスの権限をあたえることが｢認可｣

純粋な｢認可｣には､｢誰｣という考え方はありません｡

いわゆる｢鍵の発行｣や｢チケットの発行｣のようなものです｡

チケットがあるからといって誰かの身元が明らかになる話とは関係ないです｡

電車のチケットなら､乗車の許可があるだけで､誰かというのは関係ありません｡

認可はそれを持っているだけで何か(リソースへのアクセス)が許可されます｡

鍵があればドアが開くし､持っていなければ開きません｡

つまり､ただの許可章の発行だけです｡

ただ多くの場合､認証できないと認可できないので､認可は認証に依存していると言えます｡

# 認可の仕組み

`Pundit`はコントローラの各アクションで`authorize` リソースオブジェクトを呼ぶと､対象のリソースに対して権限があるかどうかを確認してくれます｡その設定を`app/policies`にあるポリシーファイルで細かく定義できます｡

# 導入方法&使用例

## インストール

`gem "pundit"`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

`bundle install`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

## Punditをinclude

使いたいコントローラでPunditをincludeします｡

`class ApplicationController < ActionController::Base
  include Pundit
end`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

## 認可のルールを記述するファイルを作成

generatorで作成します｡

`rails g pudit:install`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

上記のコマンドを実行すると､`app/policies/application_policy.rb`というファイルが生成されます｡

app/policies/applicaiton_policy.rb

`class ApplicationPolicy
	# 読み取りの属性を定義している
  attr_reader :user, :record

  def initialize(user, record)
    @user = user
    @record = record
  end

  def index?
    false
  end
end`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

このファイルで定義している`ApplicationPolicy`クラスを継承して､他のコントローラごとの認可ルールを記述していきます｡

`initialize`で定義される最初の引数は、`user`です。Punditは`current_user`メソッドを呼び出して、この引数に送ります｡

第二引数の`record`は認可をチェックしたいモデルオブジェクトです。対応するモデルのインスタンスを手動で割り当てます｡

これを利用することで､アクセスしているユーザーオブジェクトと､対象のリソースオブジェクトを知ることができます｡

## 認可ファイルを作成してみる

例としてPostという名前のモデルに対してpolicyを作成してみます｡

app/policies/post_policy.rb

`class PostPolicy < ApplicationPolicy

  def create?
    # アクセスユーザー権限がadminまたはeditorのときのみ認可
    user.admin? || user.editor?
  end

end`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

- `モデル名_policy.rb`でファイル作成
- `モデル名Policy`でクラス名定義
- `def アクション名?`で認可ルール(policy)を記述
- `*def アクション名?`の返り値によって､認可するか否かを判断しています｡

例えば､上記の`def create?`で`false`が返ってくれば､

PostControllerの`create`アクションは拒否されて､`Pundit::NotAuthorizedError`が発生します｡

## コントローラ側からPundit呼び出し

先程のpolicyファイルを適用するために､コントローラからPunditを呼び出します｡

app/controller/posts_controller.rb

`def create@tag = Tag.find(params[:id])

  authorize(Tag)

- 省略 -

end`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

- `authorize`メソッドで､先程のpolicyファイルに記述された`def create?`が処理されます｡
- 引数にはモデル(リソース)オブジェクトを入れます｡
- インスタンスかモデルか認可状況を確認します｡

# Pundit用静的403エラー画面の作成

## `Pundit::NotAuthorizedError`を捕捉してエラーページを表示させる

各認可がfalseだった場合､`authorize`が`Pundit::NotAuthorizedError`を`raise`するので、エラーを拾って403を返す仕組みを作っておく必要があります｡

実装の観点として下記2つを意識します｡

- ヘッダーやフッターなど共通のページレイアウトをエラーページに表示させるかどうか
- 開発環境で403エラーページの表示させるかどうか

今回は､共通レイアウトは表示させずに､自作のエラー画面`public/403.html`を表示させます｡

また､上記のエラー画面は､本番環境では表示されますが､開発環境ではデフォルトで非表示となっています｡正しく表示されるか開発環境で確認する方法も後で記述します｡

## エラー画面のテンプレートを作成

まずはエラー画面の`public/403.html`を作成しておきます｡

`<!DOCTYPE html><html><head><title>権限がありません(401)</title><meta name="viewport" content="width=device-width,initial-scale=1"></head><body><p>権限がありません。</p></body></html>`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

## 本番環境でユーザー向けの403エラー画面を表示させる設定

Production環境でユーザー向けの403エラー画面を表示させるには下記の通り､`config/application.rb`に設定を記述して､サーバを再起動させます｡

config/application.rb

`#例外を403HTTPステータスにします。これを付けないと500になる｡
# :forbiddenというシンボルはステータスコード403と定義されている｡
config.action_dispatch.rescue_responses["Pundit::NotAuthorizedError"] = :forbidden`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

`Pundit::NotAuthorizedError`を補足させ､`:forbidden`を指定することで､HTTPステータスコードが`403`になります｡このシンボルはで定義されています｡

## 開発環境で403エラー画面を確認する

上記の設定で`Pundit::NotAuthorizedError`が発生した場合､本番環境では､`public/403.html`が表示されるようになりました｡これを開発環境で確認するには､`config/environments/development.rb`の `config.consider_all_requests_local`を`false`にして､サーバを再起動することで可能となります｡

config/environments/development.rb

`# エラー画面404と403をデバッグ用か本番用か切り替えられる
  # config.consider_all_requests_local = true
  config.consider_all_requests_local = false`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

確認ができたら`true`に戻しておきましょう｡

上記の設定で確認したのが下図になります｡

`Pundit::NotAuthorizedError`が発生するパスにアクセスすると`public/403.html`が表示されていることが確認できました｡また､ステータスコードも`403`になっております｡

以上｡

## ※ 共通レイアウトも表示させる場合

共通レイアウトも表示させる場合は､ApplicationControllerで`rescue`処理を記載して､`app/view`以下のテンプレートファイルを用意して`render`させます｡

`class ApplicationController < ActionController::Base
  include Pundit

  rescue_from Pundit::NotAuthorizedError, with: :user_not_authorized

  private

  def user_not_authorized
    render 'error/403', status: :forbidden
  end
end`

!https://static.zenn.studio/images/copy-icon.svg

!https://static.zenn.studio/images/wrap-icon.svg

# 参考

https://github.com/varvet/pundit)