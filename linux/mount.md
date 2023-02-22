# mount

* 使用可能なファイルシステムの一覧を表示  
`cat /proc/filesystems`
* CD のマウント指定を /etc/fstab に記述(CentOS 4.4を参考)  
  1. マウントポイント作成  
  `mkdir /cdrom`
  1. 下記内容を /etc/fstab に追記。
      ```
      /dev/cdrom /cdrom auto pamconsole,fscontext=system_u:object_r:removable_t,exec,user,ro,noauto,managed 0 0
      ```
      * user  
        一般ユーザもマウント可能
      * ro  
        読み込み専用でマウント  
        read-only のメッセージが表示されなくなる。
      * noauto  
        OS 起動時にはマウントしない
      * CD をマウントする時、大文字を小文字にマップ(変換されないようにする)。  
        map=off というオプションを指定。
        ```
        mount -o map=off /mnt/cdrom
        ```
