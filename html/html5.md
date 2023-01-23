# HTML5

* vertical-align
  * \<!DOCTYPE html> を指定すると HTML5 の標準モードで表示される。
  * HTML5 の標準モードで表示すると、img タグの vertical-align が baseline になる(HTML4 では bottom)。
  * HTML5 では img タグの align="bottom" が無視される。  
=> 結果的に \<!DOCTYPE html> を付けると見た目ができる(隙間ができる)。
  * HTML5 では style="vertical-align: bottom;" を指定すると、隙間なく表示される。  
=> 最下行は style="vertical-align: top;"を指定する。
