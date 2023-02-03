# ln(リンク)

* ハードリンクされたファイルを探す  
  * inode を確認する  
  `ls -lh <ファイル名>`  
  * inode でファイルを検索する  
  `find <検索PATH> -type f -inum <inode>`
