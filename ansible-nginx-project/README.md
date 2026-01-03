# Ansible Nginx Deployment Project

## 📌 Project Overview

This project demonstrates a **production-style yet simple Ansible automation** for installing and configuring **Nginx** on Linux servers. It is designed to showcase **core and intermediate Ansible concepts** expected from a **5+ years experienced DevOps engineer**, while keeping the implementation easy to understand and explain in interviews.

The automation is **idempotent**, **secure**, and **role-based**, following Ansible best practices.

---

## 🎯 Objectives

* Automate Nginx installation
* Manage configuration using templates
* Secure sensitive data using Ansible Vault
* Restart services only when required using handlers
* Maintain clean, reusable project structure using roles

---

## 🧠 Ansible Concepts Demonstrated

* Roles and role-based structure
* Handlers for service management
* Templates (Jinja2)
* Ansible Vault for secrets management
* Idempotency
* Privilege escalation (become)
* Inventory-based host targeting

---

## 📂 Project Structure

```
ansible-nginx-project/
├── inventory
├── site.yml
├── group_vars/
│   └── secrets.yml        # Vault-encrypted file
└── roles/
    └── nginx/
        ├── tasks/
        │   └── main.yml
        ├── handlers/
        │   └── main.yml
        ├── templates/
        │   └── nginx.conf.j2
        └── vars/
            └── main.yml
```

---

## ⚙️ Prerequisites

* Ansible installed on control node
* One or more Linux target servers
* SSH connectivity from control node to target servers
* sudo/root access on managed nodes

---

## 🗂 Inventory Configuration

Define target hosts in the `inventory` file:

```
[web]
<server-ip> ansible_user=<ssh-user>
```

---

## 🔐 Secrets Management (Ansible Vault)

Sensitive information such as credentials is stored securely using **Ansible Vault**.

Create and encrypt the secrets file:

```
ansible-vault encrypt group_vars/secrets.yml
```

Example variables inside `secrets.yml`:

```
nginx_basic_auth_user: admin
nginx_basic_auth_password: StrongPass123
```

---

## ▶️ Main Playbook (`site.yml`)

This playbook applies the Nginx role to the target hosts:

```
- hosts: web
  become: yes
  roles:
    - nginx
```

---

## 🧩 Role Details

### Tasks

* Install Nginx package
* Deploy Nginx configuration using template
* Ensure Nginx service is started and enabled

### Handlers

* Restart Nginx service only when configuration changes

### Templates

* Dynamic Nginx configuration using Jinja2

---

## 🚀 How to Run the Playbook

Execute the playbook with vault password prompt:

```
ansible-playbook -i inventory site.yml --ask-vault-pass
```

---

## ✅ Verification

After successful execution:

* Nginx service should be running
* Access the server using browser or curl:

```
curl http://<server-ip>
```

Expected output:

```
Nginx deployed using Ansible
```

---

## 🔁 Idempotency Check

Re-run the playbook:

```
ansible-playbook -i inventory site.yml --ask-vault-pass
```

No changes should be reported if the system is already in the desired state.

---

## 🧪 Possible Enhancements

* Add rollback mechanism for configuration changes
* Introduce environment-based inventories (dev/prod)
* Add OS-specific conditionals
* Integrate with Jenkins pipeline
* Add health checks


## 📌 Use Case

This project is suitable for:

* DevOps interview demonstrations
* Learning Ansible best practices
* Small to medium-scale infrastructure automation
