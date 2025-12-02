# 🐧 Linux Administration – Real-Time DevOps Use Case

## **Automated Server Setup for New Application (Amazon Linux)**

This README provides a structured roadmap of Linux administration skills needed to automate server setup in a real DevOps environment using **Amazon Linux / Amazon Linux 2**.

---

# 🟩 Level 1 – Basic (Foundational Skills)

## ✔️ 1. Set up Users & Groups for Development Team

```bash

sudo adduser devuser # Create user
sudo groupadd devteam # Create group
sudo usermod -aG devteam devuser # Add user to group
```
![alt text](1.png)

### Verify:

```bash
id devuser
grep devteam /etc/group
```
![alt text](2.png)
---

## ✔️ 2. Manage Permissions for Project Directories

```bash
sudo mkdir -p /var/www/project
sudo chown -R devuser:devteam /var/www/project
sudo chmod -R 770 /var/www/project
```
![alt text](3.png)

---

## ✔️ 3. Install Required Packages (Amazon Linux)

### **Amazon Linux 2 Package Installation**

```bash
sudo yum install -y git nginx java-1.8.0-openjdk
```
![alt text](4.png)

Enable & start Nginx:

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```
![alt text](5.png)


---

## ✔️ 4. Check System Information



```bash
free -h     ### Memory
```
![alt text](6.png)

```bash
lscpu       ### CPU
```
![alt text](7.png)

```bash
df -h       ### Disk
```
![alt text](8.png)

```bash
systemctl list-units --type=service ### Running services
```
![alt text](9.png)

---

# 🟨 Level 2 – Intermediate (Daily DevOps Tasks)

## ✔️ 1. Automate Backups Using Cron

### Edit Cron:

```bash
crontab -e
```

```
0 2 * * * tar -czf /backup/app-$(date +\%F).tar.gz /var/www/project   ### Daily backup at 2 AM:
```
![alt text](10.png)

![alt text](11.png)

---

## ✔️ 2. Create Shell Scripts

### **Log Cleanup Script**

`/scripts/cleanup_logs.sh`

```bash
#!/bin/bash
find /var/log -type f -name "*.log" -mtime +7 -exec rm {} \;
```

Make executable:

```bash
chmod +x /scripts/cleanup_logs.sh
```
![alt text](12.png)
---
```

---

## ✔️ 3. Manage Logs Under `/var/log`

```bash
cd /var/log
sudo tail -f dnf.log
sudo tail -50 /var/log/nginx/error.log
sudo journalctl -u nginx
```
![alt text](14.png)

![alt text](13.png)

---

## ✔️ 4. Monitor System Performance

### CPU/Memory Usage

```bash
top
htop     # install: sudo yum install -y htop
```
![alt text](16.png)

![alt text](15.png)

### Check Running Services

```bash
systemctl status nginx
```
![alt text](17.png)


### Network Stats

```bash
ss -tulpn
```
![alt text](18.png)

---

# 🟥 Level 3 – Advanced (Production Linux Admin)

## ✔️ 1. Create Custom **systemd Service** for Your Application

### Create service file:

`/etc/systemd/system/myapp.service`

```ini
[Unit]
Description=My Custom Application
After=network.target

[Service]
User=devuser
WorkingDirectory=/var/www/project
ExecStart=/usr/bin/java -jar app.jar
Restart=always

[Install]
WantedBy=multi-user.target
```
![alt text](19.png)

### Enable & Start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
sudo systemctl status myapp
```
![alt text](20.png)

---

## ✔️ 2. SSH Hardening (Amazon Linux)

Edit ssh config:

```bash
sudo vi /etc/ssh/sshd_config
```

Recommended changes:

```
PermitRootLogin no
PasswordAuthentication no
AllowUsers devuser
```
![alt text](21.png)

Restart SSH:

```bash
sudo systemctl restart sshd
```
![alt text](22.png)

---

## ✔️ 3. LVM Setup for Storage Scaling

```bash
sudo pvcreate /dev/nvme1n1
sudo vgcreate app_vg /dev/nvme1n1
sudo lvcreate -n app_lv -L 10G app_vg
sudo mkfs.ext4 /dev/app_vg/app_lv
sudo mkdir /mnt/appdata
sudo mount /dev/app_vg/app_lv /mnt/appdata
```
![alt text](24.png)

![alt text](25.png)

---

## ✔️ 4. Configure Firewall Rules (Amazon Linux uses firewalld)

Install:

```bash
sudo yum install -y firewalld
sudo systemctl enable --now firewalld
```
![alt text](26.png)


Allow ports:

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```
![alt text](27.png)

---

## ✔️ 5. Implement Logrotate for Application Logs

Create config:
`/etc/logrotate.d/myapp`

```bash
/var/www/project/logs/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
```
![alt text](28.png)

Test:

```bash
sudo logrotate -d /etc/logrotate.d/myapp
```
![alt text](29.png)
---

