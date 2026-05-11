# ⚙️ Ansible and SSH Key Setup in AWS

## 📌 Step 1: Launch EC2 Instances

- Create **3 Ubuntu EC2 Instances**:
  - `ansible` (Control Node)
  - `server1` (Managed Node)
  - `server2` (Managed Node)

- Allow **HTTP** traffic while creating all 3 instances.

---

## 📌 Step 2: Setup on Ansible EC2 Instance

### 🔧 Login and Install Ansible

```bash
sudo su -
apt update -y
apt-add-repository ppa:ansible/ansible
apt update
apt install ansible -y
ansible --version
````

---

## 📌 Step 3: Configure Hostnames for Easier Access

Edit the hosts file:

```bash
nano /etc/hosts
```

Add the following lines:

```
server1-public-ip server1
server2-public-ip server2
```

---

## 📌 Step 4: Generate SSH Keys and Share with Servers

### 🗝️ Generate SSH key on Ansible server:

```bash
ssh-keygen -t rsa
# Press Enter three times to accept defaults
cat ~/.ssh/id_rsa.pub
```

### 🔑 Copy and paste the public key to `server1` and `server2`:

#### On both server1 and server2:

```bash
sudo apt update
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
# Paste the public key here (do not remove existing text)
```

---

## 📌 Step 5: Verify SSH Connection from Ansible

On Ansible server, test SSH access:

```bash
ssh ubuntu@server1
exit

ssh ubuntu@server2
exit
```

✅ If no password is asked, SSH keys are properly set up.

---

## 📌 Step 6: Setup Ansible Inventory and Config

### Create ansible directory:

```bash
mkdir /root/ansible
cd /root/ansible
```

### Create inventory file:

```bash
nano inventory
```

Add:

```
[webservers]
server1
server2
```

### Create ansible.cfg file:

```bash
nano ansible.cfg
```

Add:

```
[defaults]
inventory=/root/ansible/inventory
remote_user=ubuntu
ask_pass=false
```

---

## 📌 Step 7: Create YAML Playbook to Install Web Servers

```bash
nano install_webservers.yml
```

Paste:

```yaml
---
- name: Install Web Servers
  hosts: webservers
  become: true
  tasks:

    - name: Install Nginx on server1
      apt:
        name: nginx
        state: present
      when: inventory_hostname == 'server1'

    - name: Install Apache on server2
      apt:
        name: apache2
        state: present
      when: inventory_hostname == 'server2'

    - name: Ensure Nginx is started and enabled on server1
      service:
        name: nginx
        state: started
        enabled: yes
      when: inventory_hostname == 'server1'

    - name: Ensure Apache is started and enabled on server2
      service:
        name: apache2
        state: started
        enabled: yes
      when: inventory_hostname == 'server2'
```

---

## 📌 Step 8: Run the Playbook

```bash
ansible-playbook -i /root/ansible/inventory install_webservers.yml
```

### ✅ Verification:

* Open `server1` public IP in browser → You should see **Nginx**
* Open `server2` public IP in browser → You should see **Apache2**

🎉 **Done!** You’ve successfully set up Ansible with SSH keys and deployed web servers.

---
