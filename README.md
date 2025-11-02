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
