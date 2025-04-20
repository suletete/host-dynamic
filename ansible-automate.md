
---

# 👤 Automate User Creation on Linux Server using Ansible

## 📘 Introduction

Managing user accounts on multiple Linux servers manually can be time-consuming. With **Ansible**, you can automate user creation across your infrastructure efficiently using playbooks. This guide will walk you through automating user creation and configuration using Ansible.

---

## 🎯 Objectives

- Understand Ansible’s automation capabilities.
- Set up an Ansible environment.
- Write a playbook to automate user creation.
- Configure user settings like groups and SSH access.
- Verify creation and test user login.

---

## 🧾 Prerequisites

- **Linux Servers**: At least one target server (and a control node if needed).
- **Ansible Installed** on the control node.
- **SSH Access** with public key authentication.
- A text editor (e.g., `nano`, `vim`, or VS Code).

⏱️ **Estimated Time**: 1–2 hours

---

## 📋 Tasks Outline

1. Install and configure Ansible.
2. Set up the inventory file.
3. Create a user creation playbook.
4. Add user group and SSH key settings.
5. Verify user creation and test access.

---

## 🚀 Project Tasks

### ✅ Task 1: Install and Configure Ansible

Install Ansible on the control node:

```bash
sudo apt update
sudo apt install ansible -y
```

Verify installation:

```bash
ansible --version
```

Set up SSH key-based authentication:

```bash
ssh-keygen -t rsa
ssh-copy-id user@<target-server-ip>
```

---

### 🗂️ Task 2: Set Up the Ansible Inventory File

Create an inventory file:

```bash
nano inventory.ini
```

Add your target server:

```ini
[linux_servers]
target ansible_host=<target-server-ip> ansible_user=<user>
```

---

### 🛠️ Task 3: Create an Ansible Playbook to Automate User Creation

Create the playbook:

```bash
nano create_users.yml
```

Paste the following content:

```yaml
- name: Automate user creation
  hosts: linux_servers
  become: yes
  tasks:
    - name: Create a new user
      user:
        name: "{{ item.username }}"
        state: present
        shell: /bin/bash
        create_home: yes
      with_items:
        - { username: "user1" }
        - { username: "user2" }
```

---

### 🔧 Task 4: Configure Additional User Settings (Groups & SSH)

Update your playbook to include group assignment and SSH key setup:

```yaml
- name: Automate user creation with groups and SSH keys
  hosts: linux_servers
  become: yes
  tasks:
    - name: Create a new user with group settings
      user:
        name: "{{ item.username }}"
        state: present
        shell: /bin/bash
        create_home: yes
        groups: "{{ item.groups }}"
      with_items:
        - { username: "user1", groups: "sudo" }
        - { username: "user2", groups: "docker" }

    - name: Add SSH key for the users
      authorized_key:
        user: "{{ item.username }}"
        state: present
        key: "{{ lookup('file', item.ssh_key) }}"
      with_items:
        - { username: "user1", ssh_key: "/path/to/user1.pub" }
        - { username: "user2", ssh_key: "/path/to/user2.pub" }
```

> Replace `/path/to/user1.pub` and `/path/to/user2.pub` with actual paths to each user's public SSH key file.

---

### 🔍 Task 5: Verify User Creation and Test Login

Run the playbook:

```bash
ansible-playbook -i inventory.ini create_users.yml
```

Check the users:

```bash
cat /etc/passwd
ls /home
```

Test SSH access:

```bash
ssh user1@<target-server-ip>
ssh user2@<target-server-ip>
```

---
