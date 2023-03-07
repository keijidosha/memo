
* tar.gz を転送して解凍
  ```
  - name: unarchive xxx.tar.gz
    unarchive:
      src: /home/foo/hoge.tar.gz
      dest: /usr/local
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
