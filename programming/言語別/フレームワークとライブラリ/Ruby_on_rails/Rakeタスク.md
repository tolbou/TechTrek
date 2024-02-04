# **【Rails 】Rakeタスクとは**

- [Rakeタスク](https://qiita.com/tags/rake%e3%82%bf%e3%82%b9%e3%82%af)

最終更新日 2022年2月17日 17:29投稿日 2022年02月17日

## **Rakeタスクとは**

`Rake`とは、`Ruby`で書かれたコードを`タスク`として作成しておき必要に応じて呼び出し実行する事が出来る機能。

この`Rake`が実行する処理内容を`Rakeタスク`と呼び、定義する場所を`Rakefile`と呼ぶ。

`rakeタスク`を利用する場面として、

・何かしらのデータの連携

・データベースのバックアップ

・定期的にデータを更新、削除する

などが挙げられる。

また`whennever`と組み合わせることにより、任意のタイミングで処理を実行することができる。

▷[【Rails】wheneverでcronを設定](https://qiita.com/mmaumtjgj/items/19e866f31541abb6c614)

## **Rakeタスクの作り方**

`Hello`と簡単な挨拶を表示するタスクを作成。

まず最初にタスクファイルを生成する。

`$ rails g task ファイル名
↓
$ rails g task greet`

上記のコマンドで、`lib/tasks`配下に`greet.rake`というファイルが生成されていて、ファイルの中身は以下のようになっている。

lib/tasks/greet.rake

`namespace :greet do
end`

この中に処理を記入していくことで、定義が完了する。

`#基本形
namespace :greet do
  desc "タスクの説明" #desc → description（説明）
  task task_name: :environment do #task_nameは自由につけられる
    # 実行したい処理を記述する場所
  end
end

#lib/tasks/greet.rake
namespace :greet do
  desc "Helloを表示するタスク"
  task say_hello: :environment do
    puts "Hello"
  end
end`

記入し終えたら、タスクが表示されるか確認をする。

`$ rake -T   #←現在のアプリに定義されているタスク一覧が表示されるコマンド

rake greet:say_hello                    # Helloを表示するタスク`

今度はタスクを実行して確認。`Hello`が返ってきたら成功。

`$ rake greet:say_hello
  Hello`