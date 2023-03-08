# Ansible-Create-User
in yhis repo i will share my ansible playbook for daily devops tasks

use this command to run playbook
copy playbook in file.yaml then run below command
ansible-playbook -i file.yaml

```
---
- hosts: all
  become: yes
  vars_files:
    - secret.yml # contains user_password variable
    - userlist.yml # contains user_list variable

  tasks:
    - name: Create users
      user:
        name: "{{ item.name }}"
        password: "{{ user_password | password_hash('sha512') }}"
        groups: "{{ item.group }}"
        shell: /bin/bash
      loop: "{{ user_list }}"

    - name: Create .ssh directory for users
      file:
        path: "/home/{{ item.name }}/.ssh"
        state: directory
        owner: "{{ item.name }}"
        group: "{{ item.group }}"
        mode: '0700'
      loop: "{{ user_list }}"

    - name: Copy authorized_keys file for users
      copy:
        src: "files/{{ item.name }}_authorized_keys"
        dest: "/home/{{ item.name }}/.ssh/authorized_keys"
        owner: "{{ item.name }}"
        group: "{{ item.group }}"
        mode: '0600'
      loop: "{{ user_list }}"
```
