# Ansible

* グループ作成
  ```
  - name: create group
    group:
      name: hoge
      gid: 999
  ```
* ユーザー作成
  ```
  - name: create user
    user:
      name: hoge
      uid: 999
      groups: hoge
  ```
* selinux を無効化
  ```
  - name: disable selinux
    selinux:
      state: disabled
      policy: targeted
  ```
* タイムゾーン設定
  ```
  - name: set timezone
    timezone:
      name: Asia/Tokyo
  ```
* ロケール設定
  ```
  - name: set locale to Japanese
    shell: localectl set-locale LANG=ja_JP.utf8
  ```
* ファイルをローカルからターゲットにコピー
  ```
  - name: copy hoge.txt
    copy:
      src: /home/hoge/hoge.txt
      dest: /tmp/
  ```
* コピーするファイルをワイルドカード指定
  ```
  - name: copy *.txt
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
  ```
  - name: hoge template
    template:
      src: hoge.conf.j2
      dest: /etc/hoge.conf
      mode: 0644
    vars:
      hoge: fuga
  ```
* tar.gz をリモートで解凍
  ```
  - name: unarchive xxx.tar.gz
    unarchive:
      src: /home/foo/hoge.tar.gz
      dest: /usr/local
  ```
* ディレクトリ作成
  ```
  - name: create directory
    file:
      path: /tmp/hoge
      state: directory
      owner: hoge
      group: hoge
      mode: 0755
  ```
* 複数のファイル・ディレクトリを削除
  ```
  - name: delete tar.gz + extracted directory
    file:
      path: /tmp/{{ item }}
      state: absent
    with_items:
      - hoge.tar.gz
      - hoge
  ```
* RPMファイル群のパスをリスト化して dnf でインストール
  ```
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
  ```
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
  ```
  - name: enable hoge service
    systemd:
      name: hoge.service
      daemon_reload: yes
      enabled: yes
      state: started
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_module.html)
* firewalld で https を通す
  ```
  - name: permit https to firewalld
    firewalld:
      service: https
      state: enabled
      permanent: true
      immediate: true
  ```  
  [https://docs.ansible.com/ansible/2.9_ja/modules/firewalld_module.html](https://docs.ansible.com/ansible/2.9_ja/modules/firewalld_module.html)
