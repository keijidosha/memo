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

