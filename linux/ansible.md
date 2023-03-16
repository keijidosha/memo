# Ansible

## playbook

* グループ作成
  ```yaml
  {% raw %}
  - name: create group
    group:
      name: hoge
      gid: 999
  {% endraw %}
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html)
* ユーザー作成
  ```yaml
  {% raw %}
  - name: create user
    user:
      name: hoge
      uid: 999
      groups: hoge
  {% endraw %}
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html)e
* selinux を無効化
  ```yaml
  {% raw %}
  - name: disable selinux
    selinux:
      state: disabled
      policy: targeted
  {% endraw %}
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/posix/selinux_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/posix/selinux_module.html)
* タイムゾーン設定
  ```yaml
  {% raw %}
  - name: set timezone
    timezone:
      name: Asia/Tokyo
  {% endraw %}
  ```
* ロケール設定
  ```yaml
  {% raw %}
  - name: set locale to Japanese
    shell: localectl set-locale LANG=ja_JP.utf8
  {% endraw %}
  ```
* ファイルの存在をチェックして実行するか判断
  ```yaml
  {% raw %}
  - name: is exists tar command
    stat:
      path: /usr/bin/tar
    register: check_tar
  - name: install tar
    dnf:
      name: tar
      state: installed
    when: not check_tar.stat.exists
  {% endraw %}
  ```
* ファイルをローカルからターゲットにコピー
  ```yaml
  {% raw %}
  - name: copy hoge.txt
    copy:
      src: /home/hoge/hoge.txt
      dest: /tmp/
  {% endraw %}
  ```
* コピーするファイルをワイルドカード指定
  ```yaml
  {% raw %}
  - name: copy *.txt
    copy:
      src: "{{ item }}"
      dest: /tmp/
      owner: hoge
      group: hoge
      mode: 0644
    with_fileglob:
      - "/home/hoge/*.txt"
  {% endraw %}
  ```
* シンボリックリンクを作成
  ```yaml
  {% raw %}
  - name: create symbolic link
    file:
      src:  /hoge/hoge/hoge.txt
      dest: /hoge/hoge/fuga.txt
      state: link
  {% endraw %}
  ```
* テンプレート
  ```yaml
  {% raw %}
  - name: hoge template
    template:
      src: hoge.conf.j2
      dest: /etc/hoge.conf
      mode: 0644
    vars:
      hoge: fuga
  {% endraw %}
  ```
* tar.gz をリモートで解凍
  ```yaml
  {% raw %}
  - name: unarchive xxx.tar.gz
    unarchive:
      src: /home/foo/hoge.tar.gz
      dest: /usr/local
  {% endraw %}
  ```
* ディレクトリ作成
  ```yaml
  {% raw %}
  - name: create directory
    file:
      path: /tmp/hoge
      state: directory
      owner: hoge
      group: hoge
      mode: 0755
  {% endraw %}
  ```
* 複数のファイル・ディレクトリを削除
  ```yaml
  {% raw %}
  - name: delete tar.gz + extracted directory
    file:
      path: /tmp/{{ item }}
      state: absent
    with_items:
      - hoge.tar.gz
      - hoge
  {% endraw %}
  ```
* RPMファイル群のパスをリスト化して dnf でインストール
  ```yaml
  {% raw %}
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
  {% endraw %}
  ```
* RPM がインストールされているチェックして、インストールされてなかったらインストール
  ```yaml
  {% raw %}
  - name: check hoge is installed?
    dnf:
      name: hoge
      state: installed
    check_mode: true
    ignore_errors: true
    register: check_hoge_installed
  - name: install rpm files
      dnf:
        disablerepo: "\\*"
        disable_gpg_check: true
        name: hoge.rpm
        state: present
     when: check_hoge_installed.failed
  {% endraw %}
  ```  
  対象の RPM がインストールされていない場合 dnf が 1 を返し、そのままではエラーで ansible が中断してしまうので、ignore_errors: true を設定。  
  
  ```yaml
  {% raw %}
  - name: check hoge is installed?
    dnf:
      name: hoge
      state: installed
    check_mode: true
    ignore_errors: true
    register: check_hoge_installed
  - name: install rpm files
      dnf:
        disablerepo: "\\*"
        disable_gpg_check: true
        name: hoge.rpm
        state: present
     when: check_hoge_installed.failed
  {% endraw %}
  ```  

* サービスの有効化 + 開始
  ```yaml
  {% raw %}
  - name: enable service hoge
    systemd:
      name: hoge.service
      daemon_reload: yes
      enabled: yes
      state: started
  {% endraw %}
  ```  
  (参考)  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html)  
  [[Ansible] service モジュールの基本的な使い方（サービスの起動・停止・自動起動の有効化など）](https://tekunabe.hatenablog.jp/entry/2019/02/24/ansible_service_intro)
  * state の設定値  
    * started
    * stopped
    * restarted  
* firewalld で https を通す
  ```yaml
  {% raw %}
  - name: permit https to firewalld
    firewalld:
      service: https
      state: enabled
      permanent: true
      immediate: true
  {% endraw %}
  ```  
  [https://docs.ansible.com/ansible/2.9_ja/modules/firewalld_module.html](https://docs.ansible.com/ansible/2.9_ja/modules/firewalld_module.html)
* ファイルの内容を文字列置換
  ```yaml
  {% raw %}
  - name: replace string in text file
    lineinfile:
      path: /home/hoge/hoge.txt
      regexp: '正規表現'
      line:   '置換文字列'
  {% endraw %}
  ```
  * 正規表現
    * 正規表現文字列をダブルクォートで囲った中でエスケープ用のバックスラッシュを使う場合は、2重に記述  
      (例) "^\\\\(hoge\\\\)"
    * \w などは使えない  
      (例) "\\w" の代わりに "( |\t)" と指定
* コマンドを実行(実行ユーザー、実行ディレクトリを指定して)。
  ```yaml
  {% raw %}
  - name: exec hoge.sh
    shell:
      cmd: /home/hoge/hoge.sh
      chdir: /home/hoge
    become: true
    become_user: hoge
  {% endraw %}
  ```  
* コマンドの実行結果を使って、次の処理を判定・実行
  ```yaml
  {% raw %}
  - name: exec hoge.sh
    shell:
      cmd: /home/hoge/hoge.sh | grep hoge | awk '{print $3;}' | sed -e 's/hoge/fuga/'
      chdir: /home/hoge
    register: exec_hoge
    become: true
    become_user: hoge
  - name: exec if hoge is fuga
    shell:
      # hoge.sh の標準出力を grep/awk/sed した内容をパラメーターで渡す
      cmd: /home/hoge/fuga.sh {{ exec_hoge.stdout }}
    # hoge.sh の実行ステータスが 0 で、grep/awk/sed した後の標準出力が "fuga" の場合に実行
    when: exec_hoge.rc is defind && exec_hoge.rc == 0 && exec_hoge.stdout == "fuga"
  {% endraw %}
  ```  
* 明示的にタグを指定しないと実行されないタスク  
  tags に never を指定。  
  ```yaml
  {% raw %}
  - hosts: hoge_host
    roles:
      - hoge_role
    tags:
      - never
      - hoge
  {% endraw %}
  ```  
  ansible-playbook -t hoge とパラメーター指定した場合だけ実行される。  
  (参考) [[Ansible] 通常時は実行せず、タグが指定されたときのみタスクを実行する](https://tekunabe.hatenablog.jp/entry/2020/06/27/ansible_tags_never)
* 変数を条件分岐で設定する
  ```yaml
  {% raw %}
  - name: copy ash-data.yml template
    shell:
      cmd: /home/hoge/hoge.sh {{ param1 }} {{ param2 }}
  vars:
    param1: >-
      {%- if   user == 'hoge'     -%} 1
      {%- elif user == 'hogehoge' -%} 2
      {%- else -%}                    3
      {%- endif -%}
    param2: >-
      {%- if   target == 'fuga'     -%} 1
      {%- elif target == 'fugafuga' -%} 2
      {%- else -%}                      3
      {%- endif -%}

  {% endraw %}
  ```  


## 変数

* Ansible Facts 変数の確認
  ```
  ansible <ホスト名> -m setup
  ```  
  ※ホスト名: Ansible の hosts ファイルに指定したターゲットホスト名
  * 結果の抜粋
  ```json
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
                "macaddress": "11:22:33:44:55:66",
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
                "macaddress": "11:22:33:44:55:67",
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
