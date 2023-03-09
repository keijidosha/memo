# Ansible

## playbook

* グループ作成  
  ```yaml
    - name: create group
      group:
        name: hoge
        gid: 999
  ```  
  [https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html)
