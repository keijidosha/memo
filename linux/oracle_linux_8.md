# Oracle Linux 8.x


* EPEL リポジトリを追加  
/etc/yum.repos.d/ol8-epel.repo  
  ```
  [ol8_developer_EPEL]
  name= Oracle Linux $releasever EPEL ($basearch)
  baseurl=https://yum.oracle.com/repo/OracleLinux/OL8/developer/EPEL/$basearch/
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
  gpgcheck=1
  enabled=1
  ```
