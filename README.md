# Nginx + PHP 7.4 + OCI8 Setup on Ubuntu

This guide provides step-by-step instructions to set up **Nginx**, **PHP 7.4 with FPM**, and **OCI8 (Oracle Database extension)** on Ubuntu 20.04/22.04.

---

## Table of Contents

1. [Prerequisites](#prerequisites)  
2. [Setup Nginx](#setup-nginx)  
    - [Step 1: Update Your System](#step-1-update-your-system)  
    - [Step 2: Install Nginx](#step-2-install-nginx)  
3. [Setup PHP 7.4 and PHP-FPM](#setup-php-74-and-php-fpm)  
    - [Install PHP and Extensions](#install-php-and-extensions)  
    - [Start and Enable PHP-FPM](#start-and-enable-php-fpm)  
4. [Setup OCI8 for PHP 7.4](#setup-oci8-for-php-74)  
    - [Step 1: Install Dependencies](#step-1-install-dependencies)  
    - [Step 2: Download Oracle Instant Client](#step-2-download-oracle-instant-client)  
    - [Step 3: Configure Instant Client](#step-3-configure-instant-client)  
    - [Step 4: Install OCI8 via PECL](#step-4-install-oci8-via-pecl)  
    - [Step 5: Configure PHP for OCI8](#step-5-configure-php-for-oci8)  
5. [Configure Nginx for PHP](#configure-nginx-for-php)  
6. [Verify Setup](#verify-setup)  

---

## Prerequisites

- Ubuntu 20.04 or 22.04  
- Root or sudo privileges  
- Internet connection for package downloads  

---

## Setup Nginx

### Step 1: Update Your System

```bash
sudo apt update && sudo apt upgrade -y

Setup Nginx
Step 1: Update Your System
sudo apt update && sudo apt upgrade -y
Step 2: Install Nginx
sudo apt install nginx -y
Start and enable Nginx:
sudo systemctl start nginx
sudo systemctl enable nginx
Check status:
sudo systemctl status nginx
setup php7.4fpm
Install PHP 7.4 and PHP-FPM
sudo apt install php7.4 php7.4-fpm php7.4-cli php7.4-mysql php7.4-curl php7.4-xml php7.4-mbstring -y
Start and enable PHP-FPM:
sudo systemctl start php7.4-fpm
sudo systemctl enable php7.4-fpm
Check PHP-FPM status:
sudo systemctl status php7.4-fpm

For OCI8
Ubuntu এর standard repositories–এ php7.4-oci8 প্যাকেজ নেই।
Ubuntu 20.04 বা 22.04-এ PHP 7.4-এর জন্য সরাসরি apt install php7.4-oci8 কাজ করে না।
ধাপে ধাপে PECL দিয়ে OCI8 ইনস্টল (PHP 7.4)
১️⃣ প্রয়োজনীয় ডিপেনডেন্সি ইনস্টল করুন
sudo apt update
sudo apt install php7.4-dev php-pear build-essential libaio1 unzip wget -y
 And then go to : https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html
 1. Download :instantclient-basic-linux.x64-21.20.0.0.0dbru.zip
 2. Download: instantclient-sdk-linux.x64-21.20.0.0.0dbru.zip
 
And Then 
sudo mkdir -p /opt/oracle
sudo chmod -R 777 /opt/oracle
Then 
Unzip those file in this directory /opt/oracle
sudo mv instantclient_* /opt/oracle/instantclient_21_13
ডিরেক্টরি এবং ফাইলের অনুমতি চেক করুন এবং প্রয়োজনে ঠিক করুন:
bashsudo chown -R root:root /opt/oracle/instantclient_21_13
sudo chmod -R 755 /opt/oracle/instantclient_21_13
Then
বর্তমান ফাইল চেক করুন (instantclient_21_20)
bashcd /opt/oracle/instantclient_21_20
ls -l libclntsh.so*
আউটপুটে আশা করা যায়:
textlibclntsh.so -> libclntsh.so.21.1
libclntsh.so.21.1
Then
সিমলিঙ্ক তৈরি করুন (যদি না থাকে)
bashsudo ln -s /opt/oracle/instantclient_21_20/libclntsh.so.21.1 \
          /opt/oracle/instantclient_21_20/libclntsh.so

Then
চেক করুন:
bashls -l /opt/oracle/instantclient_21_20/libclntsh.so
আউটপুট হবে:
textlibclntsh.so -> libclntsh.so.21.1

Then
লাইব্রেরি পাথ সেট করুন
bashecho /opt/oracle/instantclient_21_20 | sudo tee /etc/ld.so.conf.d/oracle-instantclient.conf
sudo ldconfig
চেক করুন পাথ সঠিক কিনা:
bashldconfig -p | grep libclntsh
আউটপুটে দেখবেন:
textlibclntsh.so.21.1 (libc6,x86-64) => /opt/oracle/instantclient_21_20/libclntsh.so.21.1
libclntsh.so (libc6,x86-64) => /opt/oracle/instantclient_21_20/libclntsh.so

Then
OCI8 ইনস্টল করুন (PECL দিয়ে)
bashsudo pecl install oci8-2.2.0
প্রম্পটে টাইপ করুন:
textinstantclient,/opt/oracle/instantclient_21_20
Then
চেক করুন
bashphp7.4 -m | grep oci8
আউটপুট:oci8

Then
go to /etc/php/7.4/cli/php.ini and /etc/php/7.4/fpm/php.ini uncomment : extension=oci8.so
Then
Go to : /etc/nginx/sites-available/default file
##

server {
	listen 80 default_server;
	listen [::]:80 default_server;

	# SSL configuration

	root /var/www/html;

	# Add index.php to the list if you are using PHP
	index index.php index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	# pass PHP scripts to FastCGI server
	#
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
	#
	#	# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/run/php/php7.4-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
	}


	location ~ /\.ht {
		deny all;
	}
}

Then 
sudo systemctl restart nginx


