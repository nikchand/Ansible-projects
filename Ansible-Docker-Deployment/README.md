# 🐳 Ansible Docker Deployment on AWS EC2

### 🚀 Overview

This project demonstrates how to **automate Docker installation and container deployment** on multiple AWS EC2 instances using **Ansible**.
Ansible is used to remotely configure servers, install Docker and Docker Compose, and deploy a simple containerized web application.

---

## 📁 Project Structure

```
ansible-docker-project/
│
├── ansible.cfg
├── hosts
│
├── playbooks/
│   └── deploy-docker.yml
│
└── roles/
    └── docker/
        ├── tasks/
        │   ├── install.yml
        │   ├── deploy.yml
        │   └── main.yml
        ├── templates/
        │   └── docker-compose.yml.j2
        └── vars/
            └── main.yml
```

---

## ⚙️ Prerequisites

* AWS EC2 instances (Amazon Linux or Ubuntu)
* SSH access to EC2 instances (keypair created & configured)
* Ansible installed on control node (your local machine or one EC2)
* Docker and Docker Compose **not pre-installed** (Ansible will handle it)

---

## 🔑 Key Pair Creation (for EC2 Access)

Before running Ansible, create your EC2 keypair and set permissions:

```bash
aws ec2 create-key-pair --key-name MyKey --query 'KeyMaterial' --output text > MyKey.pem
chmod 400 MyKey.pem
```

Use this key in your **inventory file** to let Ansible connect securely.

---

## 🧩️ Inventory File (hosts)

Define your managed nodes (EC2 instances):

```ini
[webservers]
node1 ansible_host=13.233.xxx.xxx ansible_user=ec2-user ansible_ssh_private_key_file=/path/to/MyKey.pem
node2 ansible_host=3.108.xxx.xxx ansible_user=ec2-user ansible_ssh_private_key_file=/path/to/MyKey.pem
```

---

## ⚙️ Ansible Configuration (ansible.cfg)

```ini
[defaults]
inventory = ./hosts
remote_user = ec2-user
host_key_checking = False
```

---

## 🕛 Playbook: `playbooks/deploy-docker.yml`

```yaml
---
- name: Deploy Dockerized Application
  hosts: all
  become: yes

  roles:
    - docker
```

---

## 🗂️ Role: docker

### **roles/docker/tasks/main.yml**

```yaml
---
- include_tasks: install.yml
- include_tasks: deploy.yml
```

---

### **roles/docker/tasks/install.yml**

```yaml
---
- name: Install Docker
  yum:
    name: docker
    state: present

- name: Start and enable Docker service
  service:
    name: docker
    state: started
    enabled: yes

- name: Install Docker Compose
  get_url:
    url: https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64
    dest: /usr/local/bin/docker-compose
    mode: '0755'
```

---

### **roles/docker/tasks/deploy.yml**

```yaml
---
- name: Copy docker-compose file
  template:
    src: docker-compose.yml.j2
    dest: /home/ec2-user/docker-compose.yml

- name: Deploy Docker Compose app
  command: docker-compose -f /home/ec2-user/docker-compose.yml up -d
```

---

### **roles/docker/templates/docker-compose.yml.j2**

```yaml
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
```

---

## ▶️ Run the Project

### Step 1: Verify Connection

```bash
ansible all -m ping
```

✅ You should see:

```
node1 | SUCCESS => {"changed": false, "ping": "pong"}
node2 | SUCCESS => {"changed": false, "ping": "pong"}
```

---

### Step 2: Execute the Playbook

```bash
ansible-playbook playbooks/deploy-docker.yml -v
```

Ansible will:

1. Install Docker on all target EC2 instances
2. Start the Docker service
3. Install Docker Compose
4. Deploy an NGINX container on each instance

---

## ✅ Verification

1. SSH into any EC2 instance:

   ```bash
   ssh -i MyKey.pem ec2-user@<EC2_PUBLIC_IP>
   ```
2. Check running containers:

   ```bash
   docker ps
   ```
3. Access your app:

   ```
   http://<EC2_PUBLIC_IP>
   ```

You should see the **default NGINX welcome page**.

---

## 🧠 What You Learned

* How to automate Docker installation with Ansible
* How to use Ansible **roles** for clean modular code
* How to deploy and manage Docker containers remotely
* How to configure EC2 inventory and connection setup

---

## 🌟 Future Improvements

* Add **Ansible Vault** for storing credentials securely
* Use **Docker Compose** to deploy multi-container apps
* Integrate with **Jenkins** or **GitHub Actions** for CI/CD automation

