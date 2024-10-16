# Oracle Linux 8.x


* EPEL リポジトリを追加  
  `dnf install oracle-epel-release-el8`  
  base リポジトリの latest が enabled になっている状態で実行。  
  または  
  vi /etc/yum.repos.d/ol8-epel.repo  
  ```
  [ol8_developer_EPEL]
  name= Oracle Linux $releasever EPEL ($basearch)
  baseurl=https://yum.oracle.com/repo/OracleLinux/OL8/developer/EPEL/$basearch/
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
  gpgcheck=1
  enabled=1
  ```
