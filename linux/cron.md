# cron

* 番号と曜日の対応  
0:Sun,1:Mon,2:Tue,3:Wed,4:Thu,5:Fri,6:Sat,7:Sun  
0と7が日曜日になる
* 一定間隔で実行(5分ごとに実行)  
    ```
    #minute hour mday month wday command
    */5     *    *    *     *    /bin/touch xxx
    ```
  「0,5,10,15,20,25,30,35,40,45,50,55」と同じ。
* ある期間だけ実行(5分から10分の間だけ毎分実行)  
    ```
    #minute hour mday month wday command
    5-10    *    *    *     *    /bin/touch xxx
    ```
* 実行する日時の条件に、年を追加  
  ```
  #minute hour mday month wday command
  0       0    1    3     *    [ `/bin/date +\%Y` -eq 2018 ] && touch /tmp/touch.txt
  ```


