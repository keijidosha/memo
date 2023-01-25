# Rails: view

## 部分テンプレート

* 部分テンプレートに変数を渡す
  * 渡す側  
    `<%= render partial: 'common', locals: { key: "value" } %>`  
    key という変数に "value" という文字列をセットする例  
    :partial を指定しないと、部分テンプレートに変数が渡らない  
  * 受け取る側  
    `<%= key %>`  
    key という変数で参照できる
* アプリケーションで共通して使用する部分テンプレートを配置する  
views/application ディレクトリに部分テンプレートを配置すると、自動的に認識される


## ヘルパー

### link_to

link_to <表示名>, {<Railsオプション>}, <HTMLオプション>  
(例)  
「一覧」をクリックすると PostsController の index メソッドを開く。  
<controller>_<method>_path(routes.rb で resources :posts を指定しておく必要がある?)  
`<%= link_to "一覧", posts_index_path %>`  
または(メソッド省略時は index メソッドが選択される?)  
`<%= link_to "一覧", posts_path %>`  
または  
`<%= link_to "一覧", { :controller => "posts", :action => "index"} %>`  
または  
`<%= link_to "一覧", "/posts/index" %>`  

「一覧」をクリックすると PostsController の list メソッドを開く。params[:id] で filter_id を取得できるようにする。  
`<%= link_to "一覧", { :controller => "posts", :action => "list", :id => filter_id }`  

「Google」をクリックすると、別ウィンドウで Goolge を開く。  
`<%= link_to "Google", {"http://www.google.com/"}, {:target => "_blank" } %>`  

### form_for

* form_for に HTML タグを指定する  
:html を指定する  
`<%= form_for @item, html: {id: "item"} do |f| %>`  
* form_for に URL/パスを指定する  
`<%= form_for @item, url: item_path do |f| %>`  
Post にぶら下がるコメントを編集するパスを指定  
`<%= form_for(@comment, :url => edit_post_comments_path(@post, @comment)) do |f| %>`  
* form_tag に name タグを指定する。  
第2引数をかっこでかこむ  
`<%= form_tag items_path, {name: :list_item} %>`  

### select

* select に HTMLタグを指定する  
空のハッシュパラメーターを指定する  
`<%= f.select :ngnivr_ip, @ngnivr_list, {}, {:style => "width: 10em;"} %>`  

## model と連携しない form を作成

```
<%= form_tag('/deli/getallposts', :method => :post) do %>
 <p><label>単位</label><%= text_field_tag 'units', '10' %></p>
 <p><label>間隔</label><%= text_field_tag 'interval', '5' %></p>
 <p><label>フラグ</label><%= check_box_tag 'flag', true, true %></p>
 <p><%= submit_tag( '実行', :id => 'fexec' ) %></p>
<% end %>
```

* 複数行のフォームを作成
```
<%= form_tag item_path, method: :post do %>
  <table>
  <% @items.each_with_index do |item, i| %>
    <tr>
      <td><%= item.name %></td>
      <td>
          <%= select "item[#{i}]", :enabled, [["無効","0"],["有効","1"]] %>
          <label class="check_box">在庫あり<%= check_box_tag "item[#{i}][stock]", item.stock %></label>
          <%= hidden_field :item, :id, index: i, value: item.id %>
          <%= button_tag type: 'submit', name: :delete, index: i, value: item.id, onclick: %(return delete_check('#{item.name}');).html_safe do %>削除
          <% end %>
      </td>
    </tr>
  <% end %>
  <tr><td colspan="3"><%= submit_tag "更新" %></td></tr>
  </table>
<% end %>
```

## Pagenatioin using kaminari

1. vi Gemfile
   `gem 'kaminari'`  
1. bundle install
1. Rails を再起動
1. vi views/hoge/index.html.erb  
   ```
   <%= page_entries_info @hoges %>
   
   <table>
     ...
   </table>
   
   <%= paginate @hoges %>
   ```
1. vi controllers/hoge.rb
   ```
   def index
     @hoges = Hoge.page(params[:page])
   end
   ```

## エスケープ

Rails3 以降は <%= %> の内容を自動的にエスケープ  
エスケープされないようにする  
`<%=raw post.name %>`  
または  
`<%= post.name.html_safe %>`  

HTMLをエスケープするヘルパーメソッド  
`<%= h(post.name) %>`  

(例)  
エスケープした後、改行文字を <br /> に変換して、<br /> はエスケープされないようにする  
`<%= h(post.name).gsub("\n","<br />").html_safe %>`  


## Tips

### HTMLタグ

* ページごとにタイトルを変える
  app/views/layouts/application.html.erb  
  `<title><%= yield(:title) %></title>`  
  各ページ  
  `<% content_for :title, "ページのタイトル" %>`  
* table タグで複数行表示されたデータに削除ボタンを付け、削除前に JavaScript でチェックするサンプル  
  View  
  ```
  <%= form_tag items_path, method: :delete do %>
    <table>
      <% @items.each do |item| %>
        <tr>
          <td><%= item.id %></td>
          <td><%= item.name %></td>
          <td>
            <%= button_tag type: 'submit', name: "item[id]", value: item.id, onclick: %(return check('#{item.id}');).html_safe do %>
              削除
            <% end %>
          </td>
        </tr>
      <% end %>
    </table>
  <% end %>
  ```
  Controller
  ```
  def destroy
    item_id = params[:item][:id]
    # 削除処理
    redirect_to items_path
  end
  ```


