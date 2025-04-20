
---

# 🌐 Deploy and Configure Nginx Web Server using Ansible

## 📘 Introduction

**Nginx** is a high-performance web server used widely across the internet. Manually installing and configuring it on multiple servers can be repetitive and error-prone. With **Ansible**, you can automate this process to deploy and configure Nginx quickly and consistently.

---

## 🎯 Objectives

- Learn how Ansible simplifies application deployment.
- Set up an Ansible environment for Linux server management.
- Create and execute a playbook to install Nginx.
- Configure a basic website hosted by Nginx.
- Validate the deployment and test the web server.

---

## 🧾 Prerequisites

- **Linux Servers**: One or more target machines (Ubuntu/Debian recommended).
- **Ansible Installed** on the control machine.
- **SSH Access** with public key authentication between control and target nodes.
- A text/code editor (e.g., `nano`, `vim`, VS Code).

⏱️ **Estimated Time**: 2–3 hours

---

## 📋 Tasks Outline

1. Install and configure Ansible.
2. Create an inventory file for the target server(s).
3. Write and execute a playbook to install Nginx.
4. Write a second playbook to configure a custom website.
5. Verify deployment and access the web server.

---

## 🚀 Project Tasks

### ✅ Task 1: Install and Configure Ansible

Install Ansible (Ubuntu):

```bash
sudo apt update
sudo apt install ansible -y
```

Verify the installation:

```bash
ansible --version
```

Set up SSH key authentication:

```bash
ssh-keygen -t rsa
ssh-copy-id user@<target-server-ip>
```

---

### 🗂️ Task 2: Set Up the Ansible Inventory File

Create the inventory file:

```bash
nano inventory.ini
```

Add your server:

```ini
[web_servers]
target ansible_host=<target-server-ip> ansible_user=<user>
```

---

### 🛠️ Task 3: Create a Playbook to Install Nginx

Create the playbook:

```bash
nano install_nginx.yml
```

Add the following content:

```yaml
- name: Install Nginx on the server
  hosts: web_servers
  become: yes
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Ensure Nginx is running
      service:
        name: nginx
        state: started
        enabled: yes
```

---

### 🌍 Task 4: Configure a Custom Nginx Website Using Ansible

Create a second playbook:

```bash
nano configure_nginx.yml
```

Add the following configuration:

```yaml
- name: Configure Nginx website
  hosts: web_servers
  become: yes
  tasks:
    - name: Create website root directory
      file:
        path: /var/www/mywebsite
        state: directory
        mode: '0755'

    - name: Deploy HTML content
      copy:
        content: |
          <html>
          <head><title>Welcome to My Website</title></head>
          <body>
          <h1>Hello from Nginx!</h1>
          </body>
          </html>
        dest: /var/www/mywebsite/index.html

    - name: Configure Nginx server block
      copy:
        content: |
          server {
              listen 80;
              server_name _;
              root /var/www/mywebsite;
              index index.html;

              location / {
                  try_files $uri $uri/ =404;
              }
          }
        dest: /etc/nginx/sites-available/mywebsite

    - name: Enable the Nginx server block
      file:
        src: /etc/nginx/sites-available/mywebsite
        dest: /etc/nginx/sites-enabled/mywebsite
        state: link

    - name: Remove default Nginx server block
      file:
        path: /etc/nginx/sites-enabled/default
        state: absent

    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded
```

---

### 🔍 Task 5: Verify Nginx Deployment

Run the playbooks:

```bash
ansible-playbook -i inventory.ini install_nginx.yml
ansible-playbook -i inventory.ini configure_nginx.yml
```

Test with `curl`:

```bash
curl http://<target-server-ip>
```

Or open `<target-server-ip>` in a browser. You should see:

```
Hello from Nginx!
```

---
