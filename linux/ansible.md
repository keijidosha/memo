# Ansible

- Table of Content  
{:toc}

## playbook

* コマンドライン・オプション  
  [https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)
  * -C(--check): dry run, 変更はしない
  * -D(--diff): 変更差分を表示, --check と組み合わせ可
  * --step: ステップ実行, プロンプトが表示される
  * --start-at-task="開始するタスク名": 指定したタスクから開始
  * -l "host or group"(--limit): 指定したホストまたはグループだけを実行
  * -t <tag_name>(--tags): 指定したタグだけを実行
  * --skip-tags <tag_name>: 指定したタグを除外して実行
* 変数の内容を表示
  ```yaml
  - name: echo hoge
    debug:
      msg: "{{ hoge }}"
  ```
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

## ファイル

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
  (参考) [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html)
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
* ターゲット間でファイルをコピー
  ```yaml
  {% raw %}
  - name: copy hoge.txt
    copy:
      src: /home/hoge/hoge.txt
      dest: /tmp/
      remote_src: true
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
* ファイルを読み込んで変数にセット  
  ```yaml
  {% raw %}
  - name: read file(Base64 encoded).
    slurp:
      src:  /hoge/hoge/hoge.txt
    register: hoge
  - name: show hoge.txt
    debug:
      msg: "{{ hoge.content | b64decode }}"
  {% endraw %}
  ```  
  ※読み込んだ内容は Base64 化される。
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
* ファイルに文字列を書き込む  
  ```yaml
  {% raw %}
  - name: write string to text file
    copy:
      dest: /home/hoge/hoge.txt
      content: "hoge is {{ hoge }}\n"
  {% endraw %}
  ```  
  * 複数の行を書き込む場合は "|" を使用
    ```yaml
    {% raw %}
    - name: write string to text file
      copy:
        dest: /home/hoge/hoge.txt
        content: |
          hoge is {{ hoge }}
          fuga is {{ fuga }}
    {% endraw %}
    ```  
    ※"|" で複数行に分ける場合は、変数を使っていても文字列はダブルクォートで囲まなくてよい。
* ファイルの文字列を置換
  ```yaml
  {% raw %}
  - name: replace string in text file
    replace:
      path: /home/hoge/hoge.txt
      regexp: '正規表現'
      replace:   '置換文字列'
      encoding: UTF-8
      owner: hoge
      group: hoge
      mode: 0644
      backup: true
  {% endraw %}
  ```  
  変換対象が複数の場合  
  ```yaml
  {% raw %}
  - name: replace string in text file
    replace:
      path: /home/hoge/hoge.txt
      regexp:  "{{ item.regexp }}"
      replace: "{{ item.replace }}"
    with_items:
      - regexp: hoge
        replace: fuga
      - regexp: foo
        replace: bar
  {% endraw %}
  ```  
* ファイルの内容を行単位で置換
  ```yaml
  {% raw %}
  - name: replace by line in text file
    lineinfile:
      path: /home/hoge/hoge.txt
      regexp: '正規表現'
      line:   '置換文字列'
      backrefs: true
  {% endraw %}
  ```  
  デフォルトでは regexp にマッチする行がない場合、lineinfile はファイルの末尾に line を追加する。  
  backrefs: true を指定すると、regexp にマッチする行がない場合はファイルを更新しなくなる。  
  変換対象が複数の場合  
  ```yaml
  {% raw %}
  - name: replace by line in text file
    lineinfile:
      path: /home/hoge/hoge.txt
      regexp: "{{ item.regexp }}"
      line:   "{{ item.line }}"
    with_items:
      - regexp: '^hoge'
        line: fuga
      - regexp: '^foo'
        line: bar
  {% endraw %}
  ```  
  * 正規表現
    * 正規表現文字列をダブルクォートで囲った中でエスケープ用のバックスラッシュを使う場合は、2重に記述  
      (例) "^\\\\(hoge\\\\)"
    * \w などは使えない  
      (例) "\\w" の代わりに "( |\t)" と指定
  * 正規表現で置換パラメーターを使う場合、backrefs を指定。  
    ```yaml
    {% raw %}
    - name: replace by line in text file
      lineinfile:
        path: /home/hoge/hoge.txt
        backrefs: true
        regexp: 'xxx1=(\d+);'
        line:   'xxx2=\1;'
    {% endraw %}
    ```  
* 置換後の文字列に変数を含めるとうまくいかない場合  
  (例)  
  max_connections = 100			# (change requires restart)  
  となっている行の 100 を pgsql_max_connections の変数にセットした値に置換  
  max_connections = 200			# (change requires restart)  
  ```yaml
  {% raw %}
  - name: set variable
    set_fact:
      pgsql_max_connections: 200
  - name: edit postgresql.conf
    ansible.builtin.lineinfile:
      path: /var/lib/pgsql/data/postgresql.conf
      regexp: '^(max_connections *= *)\d+([^\d]*.*)$'
      line: \g<1>{{ pgsql_max_connections }}\g<2>
      backrefs: true
      backup: true
  {% endraw %}
  ```  
  "\1&#x7b;&#x7b; pgsql_max_connections &#x7d;&#x7d;\2" のように、`\1` の直後に &#x7b;&#x7b; がくるとうまくいかないので  
  `\1, \2` の代わりに `\g<1>, \g<2>` を使用

* lineinfile で regexp, insertafter, line を指定した場合  
  * regexp で指定した行が見つかった場合  
    regexp を line で置換
  * regexp で指定した行が見つからず、insertafter で指定した行が見つかった場合  
    insertafter の次の行に line を追加
  * regexp で指定した行も、insertafter で指定した行も見つからない場合  
    ファイルの末尾に line を追加    

* ファイルの内容を行単位で置換
  ```yaml
  {% raw %}
  - name: add [source ~/.nxs_env] to .bashrc
    replace:
      path: "{{ NEXTGEN_HOME }}/.bashrc"
      regexp: |
        hoge

        fuge
      replace: |
        hoge

        hai!

        fuga
      backup: true
  {% endraw %}
  ```
  ⬇︎
  ```
  hoge
  
  fuga
  ```
  が
  ```
  hoge
  
  hai!
  
  fuga
  ```
  に置換される。

* ファイルを検索(find)
  ```yaml
  {% raw %}
  - name: find
    find:
      paths: "/tmp"
      patterns: "hoge_\\d+.txt"
      file_type: file
      depth: 1
      use_regex: true
      recurse: true
    register: result_find
  - name: result
    debug:
      msg: "matched count={{ result_find.matched }}, 1st matched path={{ result_find.files[0].path }}"
    when: result_find.matched > 0
  {% endraw %}
  ```

## dnf

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
  **ansible 2.9 で、上の方法では RPM インストールチェックがうまくいかないので、ファイルの存在チェックで判断した方が良さそう**  
  ```yaml
  {% raw %}
  - name: check httpd is installed?
    stat:
      path: /usr/sbin/httpd
    register: check_httpd_command
  - name: install rpm files
      dnf:
        name: httpd
        state: present
     when: not check_httpd_command.stat.exists
  {% endraw %}
  ```  

## systemctl

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
* 特定のポートがリスン状態になるまで待機(サービス起動直後など)  
  ```yaml
  {% raw %}
  - name: wait port 80 is listening
    wait_for:
      port: 80
      host: 127.0.0.1
  {% endraw %}
  ```  
  ※host は、ansible 実行対象から見たホストになる模様。  
  (例) ansible のターゲットホストが 192.168.1.1 で host に 127.0.0.1 を指定した場合は、192.168.1.1 自身の 80 ポートを待機。
  (参考)  
  [ansible.builtin.wait_for module – Waits for a condition before continuing](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/wait_for_module.html)

## firewalld

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
  [https://docs.ansible.com/ansible/latest/collections/ansible/posix/firewalld_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/posix/firewalld_module.html)
* firewalld でポートを通す
  ```yaml
  {% raw %}
  - name: permit port 8080 to firewalld
    firewalld:
      port: 8080/tcp
      state: enabled
      permanent: true
      immediate: true
  {% endraw %}
  ```  

## コマンド実行

* コマンド実行
  ```yaml
  {% raw %}
  - name: exec hoge.sh
    command: /home/hoge/hoge.sh
  {% endraw %}
  ```
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
  * コマンドの実行結果をフィルタで判定して、次の処理実行するかを決定  
    ```yaml
    {% raw %}
    - name: grep
      shell:
        cmd: grep -L hoge *.txt
      register: grep_hoge
    - name: set_fact
      set_fact:
        exists_hoge: '{{ grep_hoge.stdout_lines | select("regex", "^[0-9]+hoge\.txt$") | list | count > 0 }}'
        exists_fuga: '{{ grep_hoge.stdout_lines | select("regex", "^[0-9]+fuga\.txt$") | list | count > 0 }}'
    - name: message hoge
      debug:
        msg: hoge exits
        when: exists_hoge
    - name: message fuga
      debug:
        msg: fuga exits
        when: exists_fuga
    {% endraw %}
    ```  
    hoge を含む拡張子 txt のファイルのうち、ファイル名が数字で始まり、hoge.txt で終わるファイルと、fuga.txt で終わるファイルが存在するかどうかをチェック。  
    (他にも良い方法がありそうですがフィルタを使ったケースとして)

## その他

* タスク実行しても changed のカウントに含めない  
(例) shell 実行すると、実際には変更が発生していなくても changed にカウントされる。  
これをカウントされないようにするには changed_when: false を指定。  
  ```yaml
  {% raw %}
  - name: exec hoge.sh
    shell:
      cmd: /home/hoge/hoge.sh
      chdir: /home/hoge
    changed_when: false
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
  「{\%-」で始まる行は、変数名の定義からインデントさせないと「Syntax Error」が発生。
* 変数を入力するプロンプトを表示  
  ```yaml
  {% raw %}
  - hosts: hoge
    vars_prompt:
      - name: hoge_value
        prompt: "input value of hoge"
        private: no
    roles:
      - hoge
  {% endraw %}
  ```  
  入力された文字列が name に指定した hoge_value にセットされる。
* タスク定義の中で変数を入力  
vars_prompt はタスクと一緒に定義できない  
=> vars_prompt だと、タグ指定により指定されたロールでなくても、入力を求められてしまう。  
  ```yaml
  {% raw %}
  - name: input about hoge
    pause:
      echo: true
      prompt: "input about hoge"
    register: about_hoge
  - name: debug
    debug:
      msg: "input is {{ about_hoge.user_input }}"
  {% endraw %}
  ```  
  入力した内容は .user_input で参照できる。
* ある条件でエラーにする  
  ```yaml
  {% raw %}
  - name: exec hoge.sh
    shell:
      cmd: /home/hoge/hoge.sh
      chdir: /home/hoge
    register: exec_hoge
    failed_when: exec_hoge.stderr is defined and exec_hoge.stderr != ''
  {% endraw %}
  ```  
  シェルの実行結果が 0 であっても、エラー出力が空でない場合はエラーにする。  
  => register に指定した変数の内容が画面に出力される。

* ansible の実行を途中で止めて Enter キーを待つ  
  pause を使う。
  ```
  - name: breakpoint
    pause:
      prompt: "Continue with Enter key"
  ```


## inventory

### ansible-inventory コマンド

* ansible-inventory -i hosts --graph  
  グループとホストの関連をツリー表示  
* ansible-inventory -i hosts --host <ホスト名>  
  指定したホストを実行した時に定義される変数の一覧を表示  
* ansible-inventory -i hosts --list  
  すべてのホストの情報を表示  

## 変数

* tasks の中で変数を定義  
  set_fact を使用。
  ```yaml
  {% raw %}
  - name: hoge
    set_fact:
      url: https://example.com/
    when: ansible_distribution == "xxx"
  {% endraw %}
  ```
* 状況に応じて変数を定義し、その変数が true になっている場合だけ処理を実行(ここでは nginx をインストール)  
  ```
  - name: whether nginx is required?
    set_fact:
      install_nginx: true
    when: deploy_web_app

  - name: install nginx 1.26
    dnf:
      name: '@nginx:1.26'
      state: present
    when: install_nginx is defined and install_nginx
  ```  
  deploy_web_app が false の場合、install_nginx は未定義になるので、`is defined` で定期が済みかチェックした上で install_nginx が true かを判定。
* when で変数を文字列結合して比較  
  when で {{ }} は使えないので () で囲って、~ で文字列結合
  ```
  - name: exec if differrent url
    command: curl http://{{ ip }}/
    when: url != ("http://" ~ ip ~ "/")
* OS ごとに分けて変数をファイルに定義  
  (例) CentOS、RHEL と Ubuntu でファイルを分ける場合  
  1. roles/<ロール名>/vars ディレクトリ配下に CentOS.yml, RedHat.yml, Ubuntu.yml を作成して変数を定義。  
  1. タスク内で次のようにして変数を定義したファイルを読み込み。  
     ```
     {% raw %}
     - name: include var file
       include_vars: "{{ ansible_distribution }}.yml"
     {% endraw %}
     ```
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

## コレクション

* インストール済みコレクションの一覧  
  ansible-galaxy collection list  
* コレクションをインストール  
  ansible-galaxy collection install <コレクション名>  
  (例)  
  `ansible-galaxy collection install community.general`  

## ansible.utils.validate

スキーマ定義を用意して変数をチェック。

* schema.yml
  ```
  common_port_def: &portdef
    type: integer
    minimum: 1
    maximum: 65535

  required: [
    server_ip,
    server_port
  ]

  properties:
    server_port: *portdef
    ip_private_ash_data:
      format: ipv4
    server_name:
      type: string
      enum: [ "taro", "jiro" ]
    s3_region:
      type: string
      pattern: "(us|ap|ca|eu|sa)-(west|east|north|south|central|northeast|southeast)-[0-9]"
  ```
* main.yml
  ```
  - name: check the variable
    ansible.utils.validate:
      data: "{{ vars }}"
      criteria: "{{ lookup('file', 'schema.yml') | from_yaml }}"
  ```

## トラブルシューティング

* VS Code で schema.yml を開くと大量の警告が表示される。  
  ansible のスキーマは python3 の jsonschema とは異なる「純粋な JSON SChema?」ではなく?、有効なスキーマチェックができないので?、次の手順でスキーマチェックを無効にする。
  1. schema.yml を ansible-schema.yml にリネーム。
  2. /Users/xxx/.vscode/yaml-any.schema.json を次の内容で作成。
  ```
  {
    "$schema": "http://json-schema.org/draft-07/schema#",
    "additionalProperties": true
  }
  ```  
  3. /Users/xxx/Library/Application Support/Code/User/settings.json に次の内容を追加。
  ```
	"yaml.schemas": {
		"file:///Users/xxx//.vscode/yaml-any.schema.json": [
		  "**/ansible_schema.yml"
		],
		"file:///Users/xxx/.vscode/extensions/atlassian.atlascode-4.0.12/resources/schemas/pipelines-schema.json": "bitbucket-pipelines.yml"
	},
  ```


