# JavaScript

* ID からエレメントを取得する  
`var obj = document.getElementById( "hoge" );`
* エレメントのタグに挟まれた文字列をセットする  
`obj.innerHTML = "hoge";`
* エレメントの属性を取得する  
`var text = obj.getAttribute( 'elementname' );`
* エレメントの属性を設定する  
`var text = obj.setAttribute( 'elementname', 'hoge' );`
* エレメントにクラス名をセットする  
`obj.className = "hoge";`
* エレメントの配下にタグを追加する  
  ```
  var table = document.getElementById( "html" );
  var tr = document.createElement( 'tr' );
  var td = document.createElement( 'td' );
  td.innerHTML = "hoge";
  td.className = "td_hoge";
  tr.appendChild( td );
  table.appendChild( tr );
  ```
* エレメントの最初の要素を削除する  
`obj.removeChild( obj.firstChild );`
* エレメントの一番最後にスクロールする  
`obj.scrollTop = obj.scrollHeight;`
* 接続URLのプロトコルを取得・チェックする  
  ```
  if (window.location.protocol == 'http:') {
      // 処理
  }
  ```

## npm

* npm パッケージの一覧を表示
  ```
  npm list
  ```
* npm パッケージがどのパッケージから依存・参照されているかを表示
  ```
  npm ls <package name>
  ```

