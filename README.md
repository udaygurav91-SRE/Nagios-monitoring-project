# 🚀 Nagios Monitoring Project

## 📌 Overview

This project demonstrates a complete monitoring setup using Nagios Core with NRPE for remote monitoring and email alerting using SMTP.

It includes:

* Nagios Server setup on EC2 (Amazon Linux)
* Remote client monitoring using NRPE
* Service checks (CPU, Disk)
* Email alerting using Gmail SMTP

---

## 🏗️ Architecture

Nagios Server → NRPE Agent → Client Machines

---

## 🛠️ Technologies Used

* Linux (Amazon Linux)
* Nagios Core
* NRPE (Nagios Remote Plugin Executor)
* Apache (httpd)
* Mailx (SMTP for alerts)

---

## ⚙️ Server Setup (Nagios Server)

### 1. Install Dependencies

sudo yum update -y
sudo yum install -y httpd php gcc glibc glibc-common wget unzip make net-snmp openssl-devel

### 2. Enable Apache

sudo systemctl enable httpd
sudo systemctl start httpd

### 3. Download & Install Nagios

cd /tmp
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.4.6.tar.gz
tar -xvf nagios-4.4.6.tar.gz
cd nagios-4.4.6

./configure
make all

### 4. Create Users & Groups

sudo useradd nagios
sudo groupadd nagios
sudo usermod -a -G nagios nagios

sudo groupadd nagcmd
sudo usermod -a -G nagcmd nagios
sudo usermod -a -G nagcmd apache

### 5. Install Nagios

sudo make install
sudo make install-init
sudo make install-commandmode
sudo make install-config
sudo make install-webconf

### 6. Create Login User

sudo htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin

### 7. Access Nagios UI

http://<EC2-PUBLIC-IP>/nagios

---

## 🔌 Install Nagios Plugins

cd /tmp
wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
tar -xvf nagios-plugins-2.3.3.tar.gz
cd nagios-plugins-2.3.3

./configure
make
sudo make install

---

## 🖥️ Client Machine Setup (Remote Monitoring)

### 1. Install Dependencies

sudo yum install -y gcc glibc glibc-common make wget openssl openssl-devel

### 2. Create User

sudo useradd nagios

### 3. Install Plugins

cd /tmp
wget https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
tar -xvf nagios-plugins-2.3.3.tar.gz
cd nagios-plugins-2.3.3

./configure
make
sudo make install

### 4. Install NRPE

cd /tmp
wget https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-4.1.0/nrpe-4.1.0.tar.gz
tar -xvf nrpe-4.1.0.tar.gz
cd nrpe-4.1.0

./configure
make all
sudo make install
sudo make install-config
sudo make install-init

### 5. Configure NRPE

sudo vi /usr/local/nagios/etc/nrpe.cfg

Update:
allowed_hosts=127.0.0.1,<NAGIOS_SERVER_PRIVATE_IP>

### 6. Start NRPE

sudo systemctl enable nrpe
sudo systemctl start nrpe

---

## 🔗 Add Client to Nagios Server

### 1. Edit hosts.cfg

sudo vi /usr/local/nagios/etc/objects/hosts.cfg

define host {
use             linux-server
host_name       client1
address         <CLIENT_PRIVATE_IP>
}

### 2. Edit services.cfg

sudo vi /usr/local/nagios/etc/objects/services.cfg

define service {
use                 generic-service
host_name           client1
service_description CPU Load
check_command       check_nrpe!check_load
}

### 3. Update nagios.cfg

sudo vi /usr/local/nagios/etc/nagios.cfg

cfg_file=/usr/local/nagios/etc/objects/hosts.cfg
cfg_file=/usr/local/nagios/etc/objects/services.cfg

### 4. Restart Nagios

sudo systemctl restart nagios

---

## 🔍 Verify NRPE Connection

On server:
/usr/local/nagios/libexec/check_nrpe -H <CLIENT_PRIVATE_IP>

---

## 📧 Email Alert Configuration

### 1. Install mailx

sudo yum install -y mailx

### 2. Configure SMTP

sudo vi /etc/mail.rc

set smtp=smtp://smtp.gmail.com:587
set smtp-auth=login
set smtp-auth-user=[your-email@gmail.com](mailto:your-email@gmail.com)
set smtp-auth-password=your-app-password
set smtp-use-starttls
set ssl-verify=ignore

### 3. Configure Contacts

sudo vi /usr/local/nagios/etc/objects/contacts.cfg

(Add your email here)

### 4. Link Contact Group

Add this in hosts.cfg and services.cfg:
contact_groups admins

### 5. Enable Notifications

sudo vi /usr/local/nagios/etc/nagios.cfg

enable_notifications=1

---

## 🔔 Features

* Monitor CPU Load
* Monitor Disk Usage
* Remote monitoring via NRPE
* Email alerts on failures
* Recovery notifications

---

## 📸 Screenshots

(Add screenshots here)

---

## 🧠 Learnings

* Installed and configured Nagios from source
* Implemented NRPE for remote monitoring
* Debugged real-world issues
* Configured SMTP-based email alerts

---

## 👨‍💻 Author

Uday Gurav

