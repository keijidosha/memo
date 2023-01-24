# sudo

### sudo を使って特定のユーザになる権限だけを与える。
1. su でユーザを切り替えるだけの shll script を作成。  
    ```
    cat /usr/local/bin/sufoo.sh
    #!/bin/sh
     
    su - foo;
    ```
1. /etc/group に特定のユーザになる権限を与えたいグループとメンバーを登録。  
    ```
    tail /etc/group
    foousers:x:300:user1,user2,user3
    ```
1. visudo で /etc/sudoers に権限を与えたいグループと、作成した shell script を登録  
    ```
    %foousers  ALL=(root)      NOPASSWD: /usr/local/bin/sufoo.sh
    ```

* sudo を実行する時、元々のユーザーの環境を引き継ぐ  
-i オプションを指定  
`sudo -i rbenv -v`
* 環境変数 PATH を引き継ぐ /etc/sudoers の設定  
`Defaults    env_keep += "PATH"`
* sudo のタイムアウト時間を設定  
/etc/sudoers の timestamp_timeout にタイムアウトを分で設定  
`Defaults timestamp_timeout = 180`
