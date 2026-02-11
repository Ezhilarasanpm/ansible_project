# 🚀 Ansible File Transfer & Automated File Reader

## 📌 Project Overview

This project demonstrates how to use **Ansible** to automate:

- File transfer from a Rocky Linux Control Node
- To an Ubuntu Managed Node
- Automatically read the file every 1 minute
- Log the output using a cron job

This project covers:
- Ansible Inventory Configuration
- SSH Key-Based Authentication
- Privilege Escalation (sudo / become)
- Cron Job Automation
- YAML Playbook Execution

---

## 🏗️ Architecture

Control Node: Rocky Linux  
IP Address: 10.15.4.19  

Managed Node: Ubuntu Linux  
IP Address: 10.15.4.30  

Communication Method: SSH (Ansible)

---

## ⚙️ Technologies Used

- Ansible
- SSH
- Cron
- YAML
- Linux
- Sudo Privilege Escalation

---

## 📁 Project Structure

```
ansible-file-automation/
│
├── new.ini
├── share.yml
└── sample.txt
```

---

## 🔧 Prerequisites

### Install Ansible on Control Node

```bash
sudo dnf install ansible -y
```

Verify:

```bash
ansible --version
```

---

## 🔐 SSH Configuration (Passwordless Login)

### Generate SSH Key

```bash
ssh-keygen
```

### Copy SSH Key to Managed Node

```bash
ssh-copy-id ubuntu@10.15.4.30
```

Test connection:

```bash
ssh ubuntu@10.15.4.30
```

---

## 📄 Inventory File (new.ini)

```ini
[new]
10.15.4.30 ansible_user=ubuntu
```

---

## 📄 Sample File (sample.txt)

Location on Control Node:

```
/home/ezhil/sample.txt
```

Example Content:

```
This file is sent using Ansible.
Automation test file.
```

---

## 📄 Ansible Playbook (share.yml)

```yaml
---
- name: Share file and read every minute
  hosts: new
  become: yes

  tasks:

    - name: Copy txt file to Ubuntu
      copy:
        src: /home/ezhil/sample.txt
        dest: /tmp/sample.txt

    - name: Create script to read the file
      copy:
        dest: /usr/local/bin/open_file.sh
        mode: '0755'
        content: |
          #!/bin/bash
          cat /tmp/sample.txt >> /tmp/sample_read.log

    - name: Run script every 1 minute
      cron:
        name: "Read txt file every minute"
        minute: "*"
        job: "/usr/local/bin/open_file.sh"
```

---

## 🔑 Configure Sudo on Managed Node

Login to Ubuntu:

```bash
ssh ubuntu@10.15.4.30
```

Edit sudoers file:

```bash
sudo visudo
```

Add this line:

```
ubuntu ALL=(ALL) NOPASSWD: ALL
```

Save and exit.

---

## ▶️ How to Run the Project

### Test Inventory

```bash
ansible -i new.ini all --list-hosts
```

### Test Connection

```bash
ansible -i new.ini new -m ping
```

Expected Output:

```
"ping": "pong"
```

### Run Playbook

```bash
ansible-playbook -i new.ini share.yml
```

---

## 🔍 Verification on Managed Node

Login:

```bash
ssh ubuntu@10.15.4.30
```

Check copied file:

```bash
cat /tmp/sample.txt
```

Check script:

```bash
ls -l /usr/local/bin/open_file.sh
```

Check cron job:

```bash
sudo crontab -l
```

Expected:

```
* * * * * /usr/local/bin/open_file.sh
```

Check log file (after 1 minute):

```bash
cat /tmp/sample_read.log
```

---

## 📂 File Locations

| Component | Location |
|------------|----------|
| Source File | /home/ezhil/sample.txt |
| Copied File | /tmp/sample.txt |
| Script File | /usr/local/bin/open_file.sh |
| Log File | /tmp/sample_read.log |
| Cron Entry | Root crontab |

---

## 🧠 Key Learning Outcomes

- Understanding Ansible inventory and playbooks
- SSH key-based authentication setup
- Privilege escalation using become
- Cron automation using Ansible
- Difference between `ansible -m ping` and `ansible-playbook`

