## human_attribute_name

## **はじめに**

業務で[Rails](http://d.hatena.ne.jp/keyword/Rails)をいじっているものの、1からセットアップするとなるとよく分かってないで使っている部分の多さに毎回驚きます...

今回もその一つの[Rails](http://d.hatena.ne.jp/keyword/Rails)の日本語化の部分を備忘録として投稿します。

## **[Rails](http://d.hatena.ne.jp/keyword/Rails)のモデル名.human_attribute_name(:[カラム名](http://d.hatena.ne.jp/keyword/%A5%AB%A5%E9%A5%E0%CC%BE))ってなんだ？**

[ActiveRecord](http://d.hatena.ne.jp/keyword/ActiveRecord)::Base のクラスメソッドであり、内部的に [I18n](http://d.hatena.ne.jp/keyword/I18n) モジュールを利用してくれるシロモノらしいです！

つまり `config/locales/ja.yml` に定義してある翻訳内容を良しなに解釈してくれるメソッドってなわけ。

## **定義方法**

`config/locales/ja.yml` に以下のように記述をしておきます。

```
---
ja:
  activerecord:
    errors:
      messages:
        record_invalid: "バリデーションに失敗しました: %{errors}"
        restrict_dependent_destroy:
          has_one: "%{record}が存在しているので削除できません"
          has_many: "%{record}が存在しているので削除できません"
    models:
      tasks: タスク
    attributes:
      task:
        id: ID
        name: 名称
        description: 内容
        created_at: 登録日時
        updated_at: 更新日時

```

## **使い方**

viewで翻訳したい部分に以下のような記載をします。

```
# t("activerecord.attributes.task.name")と同じ
Task.human_attribute_name(:name)
=> "名称"

t("activerecord.attributes.task.name")
=> "名称"

Task.human_attribute_name(:created_at)
=> "登録日時"

t("activerecord.attributes.task.created_at")
=> "登録日時"

```

**`human_attribute_name`** は、Ruby on RailsのActiveModelに定義されているクラスメソッドで、モデルの属性名を人間が読みやすい形式に変換するために使用されます。このメソッドは、主にエラーメッセージやフォームのラベルで、属性名を表示する際に役立ちます。

### **使用方法**

**`human_attribute_name`** メソッドは、以下のようにして使用します。

```ruby
rubyCopy code
ModelName.human_attribute_name('attribute_name')

```

ここで、**`ModelName`** はモデルのクラス名、**`'attribute_name'`** は人間が読みやすい形式に変換したい属性名の文字列です。

### **I18nとの統合**

**`human_attribute_name`** は、国際化（I18n）機能と統合されていて、異なる言語での属性名の表示をサポートしています。属性名の翻訳は、**`config/locales`** ディレクトリ内のロケールファイルに定義することができます。

例えば、**`config/locales/ja.yml`** に以下のように定義することで、属性名を日本語に翻訳できます。

```yaml
yamlCopy code
ja:
  activerecord:
    attributes:
      model_name:
        attribute_name: "属性名の日本語訳"

```

### **実際の使用例**

例えば、Userモデルがあり、その**`email`**属性に対して人間が読みやすい名前を表示したい場合は、以下のようになります。

```ruby
rubyCopy code
User.human_attribute_name('email') # => "Email" or "メールアドレス"（ロケールに依存）

```

このメソッドを使用することで、アプリケーションのユーザーインターフェイスをより親しみやすく、国際化対応も簡単にすることができます。