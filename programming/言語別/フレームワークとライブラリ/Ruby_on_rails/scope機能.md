## **モデルのscope機能とは**

モデル側であらかじめ特定の条件式に対して名前をつけて定義し、その名前でメソッドの様に条件式を呼び出すことが出来る仕組みのこと。

`実装例`

現在時刻よりも過去の記事を取得したい場合、過去のように記載する。

model.rb

`scope :past_published, -> { where('published_at <= ?', Time.current) }`

定義したスコープは以下のように呼び出す事が出来る。

`Article.past_published`

## **scope機能のメリット**

1.条件式に名前を付けられるので、直感的なコードになる

2.修正箇所を限定することが出来る

3.コードが短くなる

## **scope機能の基本的な使い方**

`scopeメソッド`の第一引数に条件式を呼び出すための名前、第二引数に条件式を実装するlambdaを渡す。

`class モデル名 < ApplicationRecord
  scope :スコープの名前, -> { 条件式 }
end`

冒頭での例がこれに該当する。

第一引数は、シンボル`:`を使ってスコープの名前を指定。

第二引数の`->{条件式}`の`lambda`は、処理の中でメソッドを定義してくれる手法。

※第二引数の`lambda`は、`->{}`の中に条件式を記述することが出来るとだけ覚えておく。

---

### **引数を使う方法**

`scopeメソッド`は、下記の様に`->`の後に引数を定義することで、引数を渡すことが出来る。

`class モデル名 < ApplicationRecord
  scope :スコープの名前, -> (引数){ 条件式 } 
end`

公開記事を取得する上限を2件までにしたい場合、下記のように記載できる。

取得する記事の上限をその都度変更したい場合は、下記の様に引数`count`を`scopeメソッド`に定義する

`# 引数を使用しない場合
class Blog < ApplicationRecord  
  scope :published, -> { where(published: true).limit(2) } 
end

# 引数を使用した場合
# 取得する記事の上限をその都度変更したい場合はこっち
class Blog < ApplicationRecord 
  scope :published, -> (count){ where(published: true).limit(count) }
end`

---

### **条件分を使う方法**

`scopeメソッド`は、`if`文を使って処理を分岐することが出来る。

下記は、引数で渡される値が5未満の場合はその値の記事数を取得する事が出来て、５を超える場合は５件以上はないので全件(5件)取得する例。

`class Blog < ApplicationRecord
  scope :published, -> (count){ where(published: true).limit(count) if count < 5 }
end`

## **scope機能の応用**

### **nilの場合について**

`scopeメソッド`は、`scopeメソッド`で定義した条件式が`nil`を返す場合は、`allメソッド`を実行する。

また`allメソッド`の返り値は`ActiveRecord::Relationオブジェクト`なので、`scopeメソッド`で定義した条件式が`nil`を返す場合でも、`ActiveRecord::Relationオブジェクト`を返す。

`#blogsテーブルの全レコードが取得される
class Blog < ApplicationRecord
  scope :published, -> { nil } # 条件式をnilに変更
end`

---

### **スコープとクラスメソッドの違い**

クラスメソッドでも同じように定義可能。`対象モデル.クラスメソッド名`で定義した条件式を呼び出す事が出来る。

`class モデル名 < ApplicationRecord
  def self.メソッド名
    条件式
  end
end`

相違点は、**`scopeメソッド`と`クラスメソッド`は、`nil`を返す時の挙動が違う。**

**`・scopeメソッド`**

`nil`の場合に`allメソッド`が実行されるので、メソッドチェーンを使う事ができる。

**`・クラスメソッド`**

`nil`の場合は`nil`が返るので、メソッドチェーンを使う事が出来ない。

`scopeメソッド`の引数は、`クラスメソッド`の機能を複製させたものなので、引数を使う場合は`クラスメソッド`を使う方が推奨される。