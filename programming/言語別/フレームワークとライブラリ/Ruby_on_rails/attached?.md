### attached?

Ruby on RailsのActive Storageでは、アプリケーションのモデルにファイルを添付するためのAPIを提供しています。`has_one_attached` や `has_many_attached` メソッドを使用してモデルにファイル添付機能を追加できます。

`attached?` メソッドは、特定のモデルインスタンスにファイルが添付されているかどうかを確認するために使用されます。これは、添付ファイルがあるかどうかをブール値（trueまたはfalse）で返します。

例えば、`User` モデルがあるとして、ユーザーのプロフィール画像を添付する場合は、モデルに以下のように記述します：

```ruby

class User < ApplicationRecord
  has_one_attached :profile_picture
end

```

この場合、特定の `User` インスタンスにプロフィール画像が添付されているかどうかをチェックするには、`attached?` メソッドを使用します：

```ruby

user = User.find(params[:id])

if user.profile_picture.attached?
  # プロフィール画像が添付されている場合の処理
else
  # プロフィール画像が添付されていない場合の処理
end

```

`attached?` メソッドは、ファイルが実際に添付されているかどうかのみをチェックします。ファイルの内容やタイプをチェックするためには、さらに別のメソッドやバリデーションを使用する必要があります。

また、セーフナビゲーション演算子 `&.` を使用すると、メソッドを呼び出す前にオブジェクトが `nil` でないことを確認できます。これにより、もし `user` インスタンスが `nil` であったり、`profile_picture` が未定義であったりしてもエラーが発生せず、単に `nil` を返すだけになります。例えば：

```ruby

# セーフナビゲーション演算子を使って `attached?` メソッドを呼び出す
if user&.profile_picture&.attached?
  # プロフィール画像が添付されている場合の処理
else
  # プロフィール画像が添付されていない場合の処理
end

```

この方法で、メソッドが存在しないか、または関連付けられているオブジェクトが `nil` の場合に発生する可能性がある `NoMethodError` を回避できます。