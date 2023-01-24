# Ruby: pry

## よく使うコマンド

* next  
現在の行を実行
* step  
ループに入る、メソッドに入る
* finish  
* continue  
処理を再開する
* break  
  * ブレークポイントを設定する、ブレークポイントを表示する  
    (例) 30行目にブレークポイントを設定  
    break 30  
    (例) item.rb の 30行目にブレークポイントを設定  
    break app/models/item.rb:30  
  * ブレークポイントの一覧  
    break  
    ```
    # Enabled At 
    -------------
    1 Yes  /xxx/item.rb @ 10
    2 Yes  /xxx/item.rb @ 20
    3 Yes  /xxx/item.rb @ 30
    ```
  * ブレークポイントに条件設定  
    (例) 2番目のブレークポイントに条件を設定  
    break --condition 2 id == 3  
    break --condition 2 name == \"hoge\"  
    * 条件解除  
      break --condition 2
  * ブレークポイント詳細表示  
    (例) 2番目のブレークポイントの詳細を表示  
    break --show 2  
  * ブレークポイントの削除  
    break --delete 3  
    (ブレークポイントの一覧に表示されたブレークポイントの番号)  
    * すべてのブレークポイントを解除  
      break --disable-all
* @  
停止中近辺のソースを表示
