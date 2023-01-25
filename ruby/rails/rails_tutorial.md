# Rails: tutorial

esqlite3 --version  
gem install rails  
rails --version  

* 新規アプリケーションを作成  
rails new blog  
cd blog  
* サーバー起動  
rails server  
* Controller 作成  
rails generate controller welcome index  
* View 編集  
  vi app/views/welcome/index.html.erb  
  ```
  <h1>Hello, Rails!</h1>
  <%= link_to 'My Blog', controller: 'articles' %>
  <%= link_to 'New article', new_article_path %>
  ```
* ルート編集  
  vi config/routes.rb  
  ```
  Rails.application.routes.draw do
    resources :articles
    root 'welcome#index'
  end
  ```
* ルートを確認  
rake routes  
* Controller 作成  
rails g controller articles
* 入力画面作成  
  vi app/views/articles/new.html.erb  
  ```
  <%= form_for :article, url: articles_path do |f| %>
    <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@article.errors.count, "error") %> prohibited
        this article from being saved:</h2>
      <ul>
      <% @article.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
    <% end %>
    <p>
      <%= f.label :title %><br>
      <%= f.text_field :title %>
    </p>
   
    <p>
      <%= f.label :text %><br>
      <%= f.text_area :text %>
    </p>
   
    <p>
      <%= f.submit %>
    </p>
  <% end %>
   
  <%= link_to 'Back', articles_path %>
  ```
* フォームに入力された値を表示  
  vi app/controllers/articles_controller.rb  
  ```
  class ArticlesController < ApplicationController
    def create
      render plain: params[:article].inspect
    end
  end
  ```
* モデルを作成  
rails generate model Article title:string text:text
* テーブルを作成  
rake db:migrate
* productioin環境で実行する場合  
rake db:migrate RAILS_ENV=production
* Controller  
  vi app/controllers/articles_controller.rb  
  ```
  class ArticlesController < ApplicationController
  
  def index
    @articles = Article.all
  end
  
  def new
    @article = Article.new
  end
  
  def create
    @article = Article.new(params[:article])
  
    if @article.save
      redirect_to @article
    else
      render 'new'
    end
  end
  
  def show
    @article = Article.find(params[:id])
  end
  
  def edit
    @article = Article.find(params[:id])
  end
  
  def update
    @article = Article.find(params[:id])
  
    if @article.update(article_params)
      redirect_to @article
    else
      render 'edit'
    end
  end
  
  def destroy
    @article = Article.find(params[:id])
    @article.destroy
  
    redirect_to articles_path
  end
  
  private
    # 更新時必須: article, 許可: title, 宣言していないパラメーターは無視される
    def article_params
      params.require(:article).permit(:title, :text)
  end
  
  
  end
  ```
* show のフォーム  
  vi app/views/articles/show.html.erb  
  ```
  <p>
    <strong>Title:</strong>
    <%= @article.title %>
  </p>
   
  <p>
    <strong>Text:</strong>
    <%= @article.text %>
  </p>
  
  <%= link_to 'Back', articles_path %>
  | <%= link_to 'Edit', edit_article_path(@article) %>
  ```
* 一覧表示  
  vi app/views/articles/index.html.erb  
  ```
  <h1>Listing articles</h1>
   
  <table>
    <tr>
      <th>Title</th>
      <th>Text</th>
      <th colspan="2"></th>
    </tr>
   
  <% @articles.each do |article| %>
    <tr>
      <td><%= article.title %></td>
      <td><%= article.text %></td>
      <td><%= link_to 'Show', article_path(article) %></td>
      <td><%= link_to 'Edit', edit_article_path(article) %></td>
      <td><%= link_to 'Destroy', article_path(article),
                      method: :delete, data: { confirm: 'Are you sure?' }
    </tr>
  <% end %>
  </table>
  
  <%= link_to 'Back', articles_path %>
  ```
* モデル  
  vi app/models/article.rb  
  ```
  class Article < ActiveRecord::Base
    # バリデーション
    validates :title, presence: true,
                      length: { minimum: 5 }
  end
  ```
* vi app/views/articles/edit.html.erb  
  ```
  <h1>Editing article</h1>
   
  <%= form_for :article, url: article_path(@article), method: :patch do |f| %>
    <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@article.errors.count, "error") %> prohibited
        this article from being saved:</h2>
      <ul>
      <% @article.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
    <% end %>
    <p>
      <%= f.label :title %><br>
      <%= f.text_field :title %>
    </p>
   
    <p>
      <%= f.label :text %><br>
      <%= f.text_area :text %>
    </p>
   
    <p>
      <%= f.submit %>
    </p>
  <% end %>
   
  <%= link_to 'Back', articles_path %>
  ```

## 部分テンプレート

vi app/views/articles/_form.html.erb
```
<%= form_for @article do |f| %>
  <% if @article.errors.any? %>
  <div id="error_explanation">
    <h2><%= pluralize(@article.errors.count, "error") %> prohibited
      this article from being saved:</h2>
    <ul>
    <% @article.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
  <% end %>
  <p>
    <%= f.label :title %><br>
    <%= f.text_field :title %>
  </p>
 
  <p>
    <%= f.label :text %><br>
    <%= f.text_area :text %>
  </p>
 
  <p>
    <%= f.submit %>
  </p>
<% end %>
```

vi app/views/articles/new.html.erb
```
<h1>New article</h1>
 
<%= render 'form' %>
 
<%= link_to 'Back', articles_path %>
```

vi app/views/articles/edit.html.erb
```
<h1>Edit article</h1>
 
<%= render 'form' %>
 
<%= link_to 'Back', articles_path %>
```

## コメント

`rails generate model Comment commenter:string body:text article:references`

vi app/models/comment.rb
```
class Comment < ActiveRecord::Base
  belongs_to :article
end
```

`rake db:migrate`

```
class Article < ActiveRecord::Base
  has_many :comments
  validates :title, presence: true,
                    length: { minimum: 5 }
end
```

vi config/routes.rb
```
resources :articles do
  resources :comments
end
```

`rails generate controller Comments`

vi app/views/articles/show.html.erb
```
```
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>
 
<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>

<h2>Comments</h2>
<% @article.comments.each do |comment| %>
  <p>
    <strong>Commenter:</strong>
    <%= comment.commenter %>
  </p>
 
  <p>
    <strong>Comment:</strong>
    <%= comment.body %>
  </p>
<% end %>

<h2>Add a comment:</h2>
<%= form_for([@article, @article.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br>
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br>
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>
 
<%= link_to 'Back', articles_path %>
| <%= link_to 'Edit', edit_article_path(@article) %>
```

vi app/controllers/comments_controller.rb
```
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to article_path(@article)
  end
 
    private
    def comment_params
      params.require(:comment).permit(:commenter, :body)
    end
end
```

## コメントの部分テンプレート

vi app/views/comments/_comment.html.erb
```
<p>
  <strong>Commenter:</strong>
  <%= comment.commenter %>
</p>
 
<p>
  <strong>Comment:</strong>
  <%= comment.body %>
</p>

<p>
  <%= link_to 'Destroy Comment', [comment.article, comment],
               method: :delete,
               data: { confirm: 'Are you sure?' } %>
</p>vi app/controllers/comments_controller.rb
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to article_path(@article)
  end

  def destroy
    @article = Article.find(params[:article_id])
    @comment = @article.comments.find(params[:id])
    @comment.destroy
    redirect_to article_path(@article)
  end
 
    private
    def comment_params
      params.require(:comment).permit(:commenter, :body)
    end
end
```

vi app/views/comments/_form.html.erb
```
<%= form_for([@article, @article.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br>
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br>
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>

vi app/views/articles/show.html.erb
<p>
<strong>Title:</strong>
<%= @article.title %>
</p>

<p>
<strong>Text:</strong>
<%= @article.text %>
</p>

<h2>Comments</h2>
<%= render @article.comments %>

<h2>Add a comment:</h2>
<%= render "comments/form" %>

<%= link_to 'Edit Article', edit_article_path(@article) %> |
<%= link_to 'Back to Articles', articles_path %>
```


vi app/controllers/comments_controller.rb
```
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to article_path(@article)
  end

  def destroy
    @article = Article.find(params[:article_id])
    @comment = @article.comments.find(params[:id])
    @comment.destroy
    redirect_to article_path(@article)
  end
 
    private
    def comment_params
      params.require(:comment).permit(:commenter, :body)
    end
end
```

vi app/models/article.rb
```
class Article < ActiveRecord::Base
  has_many :comments, dependent: :destroy
  validates :title, presence: true,
                    length: { minimum: 5 }
end
```

Basic認証
```
class ArticlesController < ApplicationController
 
  http_basic_authenticate_with name: "dhh", password: "secret", except: [:index, :show]
 
def index
    @articles = Article.all
  end
```

```
class CommentsController < ApplicationController
 
  http_basic_authenticate_with name: "dhh", password: "secret", only: :destroy
 
  def create
    @article = Article.find(params[:article_id])
    ...
  end
```

出典: http://railsguides.jp/getting_started.html
