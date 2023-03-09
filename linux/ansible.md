# Ansible

## playbook

* グループ作成  
  ```yaml
    group:
      name: hoge
      gid: 999
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html)
* ユーザー作成
  ```yaml
    user:
      uid: 999
      groups: hoge
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html)e
* selinux を無効化
  ```yaml
    selinux:
      state: disabled
      policy: targeted
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/posix/selinux_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/posix/selinux_module.html)
* タイムゾーン設定
  ```yaml
    timezone:
      name: Asia/Tokyo
  ```
* ロケール設定
  ```yaml
    shell: localectl set-locale LANG=ja_JP.utf8
  ```
* ファイルをローカルからターゲットにコピー
  ```yaml
    copy:
      src: /home/hoge/hoge.txt
      dest: /tmp/
  ```
* コピーするファイルをワイルドカード指定
  ```yaml
    copy:
      src: "{{ item }}"
      dest: /tmp/
      owner: hoge
      group: hoge
      mode: 0644
    with_fileglob:
      - "/home/hoge/*.txt"
  ```
* テンプレート
  ```yaml
    template:
      src: hoge.conf.j2
      dest: /etc/hoge.conf
      mode: 0644
    vars:
      hoge: fuga
  ```
* tar.gz をリモートで解凍
  ```yaml
    unarchive:
      src: /home/foo/hoge.tar.gz
      dest: /usr/local
  ```
* ディレクトリ作成
  ```yaml
    file:
      path: /tmp/hoge
      state: directory
      owner: hoge
      group: hoge
      mode: 0755
  ```
* 複数のファイル・ディレクトリを削除
  ```yaml
    file:
      path: /tmp/{{ item }}
      state: absent
    with_items:
      - hoge.tar.gz
      - hoge
  ```
* RPMファイル群のパスをリスト化して dnf でインストール
  ```yaml

  - name: find rpm files
    find:
      paths: /tmp/rpms
      patterns: "*.rpm"
    register: rpm_files
  - name: create rpm file list
    set_fact:
      rpm_file_list: "{{ rpm_files.files | map(attribute='path') | list }}"
  - name: install rpm files
    dnf:
      disablerepo: "\\*"
      disable_gpg_check: true
      name: "{{ rpm_file_list }}"
      state: present
  ```
* RPM がインストールされているチェックして、インストールされてなかったらインストール
  ```yaml

  - name: check hoge is installed?
    dnf:
      name: hoge
      state: installed
    check_mode: true
    register: hoge_is_insatalled
  - name: install rpm files
      dnf:
        disablerepo: "\\*"
        disable_gpg_check: true
        name: hoge.rpm
        state: present
     when: not hoge_is_insatalled
  ```
* サービスの有効化 + 開始
  ```yaml
    systemd:
      name: hoge.service
      daemon_reload: yes
      enabled: yes
      state: started
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html)
* firewalld で https を通す
  ```yaml
    firewalld:
      service: https
      state: enabled
      permanent: true
      immediate: true
  ```  
  [https://docs.ansible.com/ansible/2.9_ja/modules/firewalld_module.html](https://docs.ansible.com/ansible/2.9_ja/modules/firewalld_module.html)

## 変数

* Ansible Facts 変数の確認
  ```
  ansible <ホスト名> -m setup
  ```  
  ※ホスト名: Ansible の hosts ファイルに指定したターゲットホスト名
  * 結果の抜粋
    ```
    {
        "ansible_facts": {
            "ansible_architecture": "x86_64",
            "ansible_default_ipv4": {
                "address": "172.21.xx.xx",
                "alias": "eth1",
                "broadcast": "172.xx.xx.255",
                "gateway": "172.21.xx.xx",
                "interface": "eth1",
                "macaddress": "00:16:3e:xx:xx:xx",
                "mtu": 1500,
                "netmask": "255.xx.xx.0",
                "network": "172.xx.xx.0",
                "type": "ether"
            },
            "ansible_distribution": "OracleLinux",
            "ansible_distribution_file_search_string": "Oracle Linux",
            "ansible_distribution_file_variety": "OracleLinux",
            "ansible_distribution_major_version": "8",
            "ansible_distribution_release": "NA",
            "ansible_distribution_version": "8.4",
            "ansible_dns": {
                "nameservers": [
                    "172.21.xx.xx"
                ]
            },
            "ansible_domain": "",
            "ansible_env": {
                "LANG": "C",
                "LC_CTYPE": "C.UTF-8",
                "USER": "root",
            },
            "ansible_eth0": {
                "active": true,
                "device": "eth0",
                "ipv4": {
                    "address": "10.xx.xx.xx",
                    "broadcast": "10.xx.xx.xx",
                    "netmask": "255.xx.xx.0",
                    "network": "10.xx.xx.0"
                },
                "macaddress": "00:16:3e:xx:xx:xx",
                "type": "ether"
            },
            "ansible_eth1": {
                "active": true,
                "device": "eth1",
                "ipv4": {
                    "address": "172.xx.xx.xx",
                    "broadcast": "172.xx.xx.255",
                    "netmask": "255.xx.xx.0",
                    "network": "172.xx.xx.0"
                },
                "macaddress": "00:16:3e:d7:14:e2",
                "mtu": 1500,
                "promisc": false,
                "type": "ether"
            },
            "ansible_hostname": "myserver",
            "ansible_interfaces": [
                "eth0",
                "eth1",
                "lo"
            ],
            "ansible_machine": "x86_64",
            "ansible_memfree_mb": 6516,
            "ansible_memory_mb": {
                "nocache": {
                    "free": 6614,
                    "used": 751
                },
                "real": {
                    "free": 6516,
                    "total": 7365,
                    "used": 849
                },
                "swap": {
                    "cached": 0,
                    "free": 0,
                    "total": 0,
                    "used": 0
                }
            },
            "ansible_memtotal_mb": 7365,
            "ansible_nodename": "myserver",
            "ansible_os_family": "RedHat",
            "ansible_pkg_mgr": "dnf",
            "ansible_processor": [
                "0",
                "GenuineIntel",
                "Intel(R) Core(TM) ix-xxxxxx CPU @ 2.xxGHz",
                "1",
                "GenuineIntel",
                "Intel(R) Core(TM) ix-xxxxxx CPU @ 2.xxGHz",
                "2",
                "GenuineIntel",
                "Intel(R) Core(TM) ix-xxxxxx CPU @ 2.xxGHz",
                "3",
                "GenuineIntel",
                "Intel(R) Core(TM) ix-xxxxxx CPU @ 2.xxGHz"
            ],
            "ansible_processor_cores": 4,
            "ansible_processor_count": 1,
            "ansible_processor_threads_per_core": 1,
            "ansible_processor_vcpus": 4,
            "ansible_product_name": "VirtualBox",
            "ansible_selinux": {
                "status": "disabled"
            },
            "ansible_service_mgr": "systemd",
            "ansible_system": "Linux",
            "ansible_user_dir": "/root",
            "ansible_user_gecos": "root",
            "ansible_user_gid": 0,
            "ansible_user_id": "root",
            "ansible_user_shell": "/bin/bash",
            "ansible_user_uid": 0,
            "ansible_userspace_architecture": "x86_64",
            "ansible_userspace_bits": "64",
            "ansible_virtualization_role": "guest",
            "ansible_virtualization_type": "lxc",
        }
    }
    ```

