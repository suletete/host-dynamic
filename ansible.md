
---

# 🛠️ Setting Up Ansible on a Linux Server

## 📘 Introduction

**Ansible** is a powerful automation tool that simplifies IT infrastructure management. This guide walks you through installing and configuring Ansible on a Linux control node and managing other target nodes.

---

## 🎯 Objectives

- Understand what Ansible is and how it works.
- Install and configure Ansible on a control node.
- Set up SSH key-based authentication for target nodes.
- Create an Ansible inventory file.
- Run basic Ansible ad-hoc commands to verify setup.

---

## 🧾 Prerequisites

- ✅ **Control Node:** A Linux server or VM with internet access.
- ✅ **Target Node(s):** At least one other Linux server or VM.
- ✅ **SSH Access** to all nodes.
- ✅ Basic Linux command line experience.

⏱️ **Estimated Time:** 1–2 hours

---

## 📋 Tasks Outline

1. Install Ansible on the control node
2. Set up SSH key-based authentication
3. Create an inventory file
4. Test Ansible connectivity
5. Run simple Ansible commands

---

## 🚀 Project Tasks

### ✅ Task 1: Install Ansible on the Control Node

Update the package list:

```bash
sudo apt update
```

Install Ansible:

```bash
sudo apt install ansible -y
```

Verify installation:

```bash
ansible --version
```

✅ You should see the installed version of Ansible.

---

### 🔐 Task 2: Configure SSH Key-Based Authentication

Generate SSH key pair:

```bash
ssh-keygen -t rsa
```

> Press Enter to accept defaults.

Copy the public key to target machines:

```bash
ssh-copy-id user@<target-server-ip>
```

Test passwordless SSH access:

```bash
ssh user@<target-server-ip>
```

✅ SSH should connect without a password prompt.

---

### 🗂️ Task 3: Create an Inventory File

Create a new directory for Ansible:

```bash
mkdir ~/ansible
cd ~/ansible
```

Create the inventory file:

```bash
nano inventory.ini
```

Example content:

```ini
[linux_servers]
target1 ansible_host=<target1-ip> ansible_user=<user>
target2 ansible_host=<target2-ip> ansible_user=<user>
```

Save and close the file (`Ctrl + O`, `Enter`, `Ctrl + X`).

---

### 🧪 Task 4: Test Ansible Connectivity

Ping all target machines:

```bash
ansible -i inventory.ini linux_servers -m ping
```

✅ You should get a `pong` response from each target.

---

### 💡 Task 5: Run Simple Ansible Ad-Hoc Commands

Check uptime:

```bash
ansible -i inventory.ini linux_servers -m command -a "uptime"
```

Check disk usage:

```bash
ansible -i inventory.ini linux_servers -m shell -a "df -h"
```

✅ Output should return system stats from all target machines.

---

## 🏁 Conclusion

In this project, you learned how to:

- Install Ansible on a Linux control node
- Set up passwordless SSH to target machines
- Create an inventory file
- Test Ansible connectivity
- Run ad-hoc commands for basic system monitoring


---
