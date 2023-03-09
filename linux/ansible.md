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
  <pre>
  － name: find rpm files
    find:
      paths: /tmp/rpms
      patterns: "*.rpm"
    register: rpm_files
  － name: create rpm file list
    set_fact:
      rpm_file_list: "\{{ rpm_files.files ｜ map(attribute='path') ｜ list }}"
  － name: install rpm files
    dnf:
      disablerepo: "\\*"
      disable_gpg_check: true
      name: "{{ rpm_file_list }}"
      state: present
  </pre>
