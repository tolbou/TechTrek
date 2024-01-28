# resources

[Rails のルーティング - Railsガイド](https://railsguides.jp/routing.html#「浅い」ネスト)

resources

Railsのルーティングシステムでは、**`resources`** メソッドを使用することで、標準的なRESTfulアクションに対するルートと、それに関連する名前付きヘルパー（**`posts_path`**、**`new_post_path`** など）を自動的に生成します。しかし、追加のカスタムルートについては、**`as`** オプションを使用して名前付きヘルパーを明示的に指定する必要があります。

例えば、**`resources :posts`** というルーティングがある場合、以下の標準的なアクションと名前付きルートが自動的に提供されます：

- **`index`** - **`posts_path`** に対応
- **`new`** - **`new_post_path`** に対応
- **`edit`** - **`edit_post_path(post)`** に対応
- **`show`** - **`post_path(post)`** に対応
- **`create`** - **`posts_path`** に対応
- **`update`** - **`post_path(post)`** に対応
- **`destroy`** - **`post_path(post)`** に対応

**`resources`** で生成されたこれらのルート以外に、例えば **`search`** のようなカスタムルートを追加する場合は、**`as`** オプションを使って名前付きヘルパーを定義する必要があります。これにより、ビューやコントローラー内でパスを簡単に参照できるようになります。

```ruby
rubyCopy code
get 'search', to: 'posts#search', as: :search_posts

```

上記のコードは **`search_posts_path`** という名前付きヘルパーを生成します。これを指定しない場合、**`url`** ヘルパーとして直接パスを指定する必要があります（例：**`url: '/search'`**）。しかし、名前付きヘルパーを使用することは、後からルートのURIパターンを変更する際に便利です。なぜなら、パスの文字列を直接コーディングしている多くの場所を探して変更するのではなく、ルーティングファイルでの変更だけで済むからです。

結局、**`as`** オプションを使用すると、コードの可読性、保守性、柔軟性が向上します。