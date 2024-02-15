## self

インスタンスメソッド内で**`self`**を使用すると、そのメソッドを呼び出しているインスタンス自体を参照します。

```ruby

class Dog
  def bark
    puts "Woof!"
  end

  def speak
    self.bark  # selfはこの場合、speakメソッドを呼び出しているインスタンスを指します
  end
end

fido = Dog.new
fido.speak  # "Woof!"と出力される

```

### **クラスメソッド内での`self`**

クラスメソッド内で**`self`**を使用すると、そのクラス自体を参照します。

```ruby

class Dog
  def self.species
    "Canis familiaris"
  end

  def self.describe
    puts "A dog is a member of the species #{self.species}."
  end
end

Dog.describe  # "A dog is a member of the species Canis familiaris."と出力される

```

### **インスタンス変数との区別**

**`self`**は、特にsetterメソッド（属性に値を設定するメソッド）を使用する際に便利です。ローカル変数とインスタンス変数を区別するために**`self`**を使います。

```ruby

class Dog
  attr_accessor :name

  def initialize(name)
    self.name = name  # selfを使用してインスタンス変数にアクセス
  end
end

```

### **クラス定義内での`self`**

クラス定義内（しかしメソッドの外）で**`self`**を使用すると、そのクラス自体を指します。これはクラスメソッドを定義する際などに使われます。

```ruby

class Dog
  self  # この場合のselfはDogクラス自体を指す
end

```

以上が**`self`**の主な用途と使い方です。**`self`**はRubyのオブジェクト指向プログラミングにおいて非常に重要なキーワードであり、多くの場面で利用されます。

Rubyにおいて、メソッドは主に「インスタンスメソッド」と「クラスメソッド」の2種類に分けられます。これらは、レシーバ（メソッドが呼び出される対象）や**`self`**の参照先が異なります。