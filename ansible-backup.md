
---

# 🗂️ Backup and Restore Files on a Linux Server using Ansible

## 📘 Introduction

Data backup and restoration are critical tasks to ensure the safety and continuity of your data in Linux server environments. Manually managing backups can be time-consuming, but with **Ansible**, you can automate this process efficiently. This guide will walk you through creating Ansible playbooks to automate file backup and restoration on a Linux server.

---

## 🎯 Objectives

- Understand Ansible's role in automating system administration tasks.
- Set up an Ansible environment to manage Linux servers.
- Create an Ansible playbook to back up files to a local or remote directory.
- Develop a playbook to restore files from a backup.
- Test and verify the backup and restore processes.

---

## 🧾 Prerequisites

- **Linux Servers**: A target server to manage backup and restore tasks, plus an optional control machine for Ansible.
- **Ansible Installed**: Ensure Ansible is installed on your control machine (follow Ansible's installation guide).
- **SSH Access**: SSH keys must be set up for passwordless authentication between the control and target machine.
- A text/code editor for editing your Ansible playbooks.

⏱️ **Estimated Time**: 2–3 hours

---

## 📋 Tasks Outline

1. Install and configure Ansible.
2. Set up an inventory file for target server(s).
3. Write a playbook for backing up files.
4. Write a playbook for restoring files.
5. Test and verify backup and restore functionalities.

---

## 🚀 Project Tasks

### ✅ Task 1: Install and Configure Ansible

1. Install Ansible on the control machine (Ubuntu):

    ```bash
    sudo apt update
    sudo apt install ansible -y
    ```

2. Verify installation:

    ```bash
    ansible --version
    ```

3. Set up SSH key authentication between the control and target servers:

    ```bash
    ssh-keygen -t rsa
    ssh-copy-id user@<target-server-ip>
    ```

---

### 🗂️ Task 2: Set Up the Ansible Inventory File

Create the inventory file to define the target server:

```bash
nano inventory.ini
```

Add the following:

```ini
[linux_servers]
target ansible_host=<target-server-ip> ansible_user=<user>
```

---

### 🛠️ Task 3: Create an Ansible Playbook to Back Up Files

Create a playbook file to automate the backup:

```bash
nano backup.yml
```

Add the following content:

```yaml
- name: Backup files on the server
  hosts: linux_servers
  tasks:
    - name: Create backup directory
      file:
        path: /backup
        state: directory
        mode: '0755'

    - name: Copy files to backup directory
      copy:
        src: /path/to/files
        dest: /backup/
        remote_src: yes
```

Replace `/path/to/files` with the actual file or directory you wish to back up.

---

### 🔄 Task 4: Create an Ansible Playbook to Restore Files

Create a playbook file for file restoration:

```bash
nano restore.yml
```

Add the following content:

```yaml
- name: Restore files from backup
  hosts: linux_servers
  tasks:
    - name: Copy files back to original location
      copy:
        src: /backup/
        dest: /path/to/files
        remote_src: yes
```

Replace `/path/to/files` with the original file or directory path where files need to be restored.

---

### 🔍 Task 5: Test the Backup and Restore Functionality

1. **Run the backup playbook**:

    ```bash
    ansible-playbook -i inventory.ini backup.yml
    ```

2. **Verify backup directory and files** on the target server:

    ```bash
    ls /backup
    ```

3. **Run the restore playbook**:

    ```bash
    ansible-playbook -i inventory.ini restore.yml
    ```

4. **Verify the restored files** in the original location:

    ```bash
    ls /path/to/files
    ```

---
