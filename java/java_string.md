# Java:文字列

* 整数をカンマ区切りでフォーマットする  
  * String#format() を使う  
`System.out.prinln( String.format( "%,d", 123456 ));`
  * DecimalFormat を使う  
    ```
    DecimalFormat df = new DecimalFormat("#,##0");
    System.out.println( df.format( 123456 ));
    ````
    DecimalFormat は再入可能ではない(複数スレッドから同時に使うことはできない)。
