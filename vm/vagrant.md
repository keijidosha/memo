- Table of Content  
{:toc}

# vagrant

## ゲストOS構築
1. mkdir ~/vagrant
1. cd ~/vagrant
1. mkdir centos7
1. cd centos7
1. vagrant init bento/centos-7.2
1. 必要に応じて Vagrantfile 編集
1. vagrant up
1. vagrant ssh

bento で利用可能な CentOS を確認  
[https://app.vagrantup.com/bento](https://app.vagrantup.com/bento)

## Vagrantfile の書き方
- ブリッジネットアダプタを設定する
  - en0 にブリッジする  
`config.vm.network :public_network, :bridge => "en0: Ethernet"`
  - 固定IPアドレスを割り当てる  
`config.vm.network :public_network, :bridge => "en0: Ethernet", :ip => "192.168.1.20"`
- ホストオンリーアダプタを設定する
  - ホストオンリーアダプタに固定IPを設定する  
`config.vm.network "private_network", ip: "192.168.56.31"`
  - ホストオンリーアダプタに MACアドレスを指定する  
`config.vm.network :private_network, virtualbox__intnet: "test_net", :mac => "010203040506"`
- メモリー割り当てを設定する  
    ```
    config.vm.provider "virtualbox" do |v|
      v.memory = "2048"
    end
    ```
- メモリー割り当てとCPU使用率制限を設定する  
    ```
    config.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--cpuexecutioncap", "75", "--memory", "2048"]
    end
    ```
- Linked Clone を指定する  
    ```
    config.vm.provider "virtualbox" do |v|
      v.linked_clone = true
    end
    ```
* よく使う Vagrantfile  
  ```
  Vagrant.configure("2") do |config|
    config.vm.network :public_network, :bridge => "en0: Ethernet", :ip => "192.168.1.1"
  
    config.vm.provider "virtualbox" do |v|
      v.linked_clone = true
      v.memory = "1024"
      v.cpus = 2
    end
  
  end
  ```

### ディスクサイズ指定・拡張

* プラグインインストール  
`vagrant plugin install vagrant-disksize`
* Vagrantfile に設定を追記  
`config.disksize.size = '32GB'`

(参考) [Vagrantfileに一行書くだけでVMのディスク容量を増やす方法](https://qiita.com/yut_h1979/items/c84c490053877beee5c1)

物理ディスク拡張後の LVM 設定は [LVM](..//linux/lvm.md#vagrant--virtulbox--ubuntu-2204-%E3%81%AE-ext4-%E3%81%A7%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%83%E3%83%88%E3%81%95%E3%82%8C%E3%81%9F%E3%83%91%E3%83%BC%E3%83%86%E3%82%A3%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E6%8B%A1%E5%BC%B5) を参照。

## VMware(Fusion)

### Vagrantfile

VMware を使う場合は `config.vm.provider` に `vmware_desktop` を指定。

```
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-24.04"
  config.vm.disk :disk, size: "64GB", primary: true

  config.vm.provider :vmware_desktop do |v|
    v.linked_clone = false
    v.gui = false
    v.memory = "4096"
    v.cpus = "4"
  end

  config.vm.network :public_network, :bridge => "en0: Ethernet"
  config.vm.synced_folder "/Users/hoge/tmp/",      "/home/hoge/tmp",  owner: "vagrant", group: "vagrant"
end
```

VMware と連携する場合は、Vagrantfile のあるディレクトリ配下に仮想マシンイメージファイルが作成される。
=> VirtualBox の場合は、普通に作成した VirtualBox の仮想マシン同様 ~/VirtualBox VMs ディレクトリ配下に作成される。

### ディスク拡張

ディスク拡張は config.vm.disk で指定したサイズを変更

```
config.vm.disk :disk, size: "64GB", primary: true
⬇︎
config.vm.disk :disk, size: "128GB", primary: true
```

## 設定

## CentOS のタイムゾーンを Asia/Tokyo に設定
Vagrant でインストールした CentOS は、タイムゾーンが UTC になっているので JST に変更

- CentOS7  
  sudo timedatectl set-timezone Asia/Tokyo
- CentOS6  
  sudo vim /etc/sysconfig/clock
  ```
  ZONE="Asia/Tokyo"
  UTC=fales
  ```
  sudo ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
- Ubuntu  
  sudo timedatectl set-timezone Asia/Tokyo

## スナップショット

* スナップショットをとる  
vagrant snapshort save <スナップショット名>
* スナップショット一覧  
vagrant snapshort list
* スナップショットに戻す  
vagrant snapshort restore <スナップショット名>
* スナップショットを削除  
vagrant snapshort delete <スナップショット名>


## ゲストOS削除
`vagrant destroy`
* Vagrantファイルのあるディレクトリで実行
* VirtualBox の仮想イメージも削除される
* Vagrant ファイルは残るので、再利用可能
* 不要な場合は、ディレクトリごと削除する  
cd ..  
rm -rf hoge

## Box バックアップ/復元

* バックアップ
  ディレクトリ移動
  ```
  cd OracleLinux8.10bento/
  ```  
  状態を確認
  ```
  vagrant status
  ```
  表示内容
  ```
  default                   running (virtualbox)
  ^^^^^^^
  ```
  バックアップ  
  表示された default を VM 名に指定。
  ```
  vagrant package default --output ../OracleLinux8.10bento.box
  ```
* 削除
  ```
  vagrant destroy
  ```
* 復元
  復元用ディレクトリ作成して、ディレクトリ移動
  ```
  mkdir OracleLinux8.10bento  
  cd OracleLinux8.10bento/
  ```
  バックアップしておいた Box を復元
  ```
  vagrant box add OracleLinux8.10bento ../OracleLinux8.10bento.box
  ```
  追加されていることを確認
  ```
  vagrant box list
  ```
  表示内容
  ```
  OracleLinux8.10bento      (virtualbox, 0)
  ```
  復元した Box を初期化
  ```
  vagrant init OracleLinux8.10bento
  ```
  起動
  ```
  vagrant up
  ```

(参考) [vagrant環境のboxをバックアップする方法](https://qiita.com/rakuraku0615/items/5fcd59b1c6f263142ec5)

## Box を構築

### Oracle Linux 8.x

* 最新の Oracle Linux 8.x の Box を構築
  1. git clone https://github.com/oracle/vagrant-projects
  1. vagrant-projects/OracleLinux/8/
  1. vagrant up
  1. vagrant ssh
  1. cat /etc/oracle-release
     ```
     Oracle Linux Server release 8.10
     ```
* 特定のバージョンの Oracle Linux 8.x の Box を構築
  1. git clone https://github.com/oracle/vagrant-projects
  1. vagrant-projects/OracleLinux/8/
  1. vi Vagrantfile  
     次の行を追記
     ```
     config.vm.box_version = "8.4.256"
     ```  
     選択肢にないバージョンを指定すると、指定可能なバージョンのリストが表示される。
     ```
     Available versions: 8.0.20, 8.1.25, 8.1.101, 8.2.125, 8.2.126, 8.2.134, 8.2.144, 8.2.145, 8.3.182, 8.3.183, 8.3.195, 8.3.198, 8.4.220, 8.4.221, 8.4.256, 8.4.257, 8.5.285, 8.5.286, 8.5.311, 8.5.320, 8.6.331, 8.6.332, 8.6.359, 8.6.360, 8.7.377, 8.7.378, 8.7.405, 8.7.411, 8.8.485, 8.8.487, 8.9.491, 8.9.511, 8.10.621, 8.10.623
     ```
  1. vagrant up
  1. vagrant ssh
  1. cat /etc/oracle-release
     ```
     Oracle Linux Server release 8.10
     ```
     カーネルのバージョンは 8.4 のものになっているが、なぜかバージョン表記は 8.10。

## Tips

* SSHポートフォワード  
  "--" に続けて ssh コマンドのオプションを指定  
  (例) 8080 への接続をゲストから見た 10.1.2.3 のポート 80 に転送  
  `vagrant ssh -- -L 8080:10.1.2.3:80`  
  次のコマンドと同様の動作  
  `ssh -p 2222 vagrant@127.0.0.1 -L 8080:10.1.2.3:80`
* ストレージの拡張
  * Vagrantfile で拡張して
    ```
    config.disksize.size = '64GB'
    ```
  * [Vagrant + VirtulBox + Ubuntu 22.04 の ext4 でフォーマットされたパーティションを拡張](/linux/lvm.md#vagrant--virtulbox--ubuntu-2204-の-ext4-でフォーマットされたパーティションを拡張) を実施
  * Vagrant で VirtualBox を使っている場合、VirtualBox アプリでのストレージ拡張はしない方がよいかも。

## Troubel Shooting

### VirtualBox を更新すると、Vagrant 起動時にエラーメッセージが出て拡張機能(ホストディレクトリのマウント)ができなくなる。

* vagrant up すると、次のメッセージが表示される。  
  今回は Ubuntu 22.04
  ```
  Package linux-headers-5.15.0-56-generic is not available, but is referred to by another package.
  This may mean that the package is missing, has been obsoleted, or
  is only available from another source

  E: Package 'linux-headers-5.15.0-56-generic' has no installation candidate
  ```
* 次のコマンドを実行
  * sudo apt-get update
  * sudo apt-get upgrade
  * sudo apt-get dist-upgrade
* ゲストOS 再起動
  ```
  sudo shutdown -r now
  ```
* Vagrant 起動時に表示されていたコマンドを実行して、カーネルソースをイストール
  ```
  DEBIAN_FRONTEND=noninteractive sudo apt-get install -y linux-headers-`uname -r` build-essential dkms
  ```
* ゲストOS 再起動
  ```
  exit
  # vagrant reload でも OK
  vagrant halt
  vagrnt up
  vagrant ssh
  ```
* ls /vagrant/ でホストディレクトリがマウントされていることが確認できる。

