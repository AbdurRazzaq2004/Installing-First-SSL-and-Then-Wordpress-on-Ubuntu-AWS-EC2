```markdown
# Installing WordPress on AWS EC2 (Ubuntu)

This guide covers the steps to install SSL and WordPress on a custom domain hosted on an AWS EC2 instance running Ubuntu.

## 1. Installing SSL

### Step 1: Install Certbot
First, install Certbot and the necessary plugin for Apache:

```bash
sudo apt install certbot python3-certbot-apache
```

### Step 2: Update Apache Configuration
Edit the Apache configuration file for your domain:

```bash
sudo nano /etc/apache2/sites-available/your_domain.conf
```

Add or update the following lines:

```apache
ServerName your_domain
ServerAlias www.your_domain
```

Replace `your_domain` with your actual domain name.

### Step 3: Test Apache Configuration
Test the Apache configuration to ensure there are no syntax errors:

```bash
sudo apache2ctl configtest
```

Expected output: `Syntax OK`

### Step 4: Reload Apache
Reload the Apache service to apply the changes:

```bash
sudo systemctl reload apache2
```

### Step 5: Allow HTTPS Through the Firewall
Enable UFW (Uncomplicated Firewall) and allow the necessary ports:

```bash
sudo ufw enable
sudo ufw status
sudo ufw allow 'Apache Full'
sudo ufw delete allow 'Apache'
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 22/tcp
```

### Step 6: Obtain SSL Certificate
Use Certbot to obtain an SSL certificate for your domain:

```bash
sudo certbot --apache
```

During the process:
- Enter your email address when prompted.
- Agree to the Terms of Service (type `A`).
- Opt-out of sharing your email address with the Electronic Frontier Foundation (type `N`).
- When asked which domain names to activate HTTPS for, enter your domain name in this format:

  ```plaintext
  domain-name.com www.domain-name.com
  ```

- If prompted, type `2` to confirm.

You have successfully installed SSL!

---

## 2. Downloading and Installing WordPress

### Step 1: Download WordPress
Download the latest version of WordPress:

```bash
wget https://wordpress.org/latest.zip
```

### Step 2: Install Unzip
Install the `unzip` utility:

```bash
sudo apt install unzip
```

### Step 3: Unzip WordPress
Unzip the WordPress package:

```bash
unzip latest.zip
```

### Step 4: Move WordPress Files
Move the WordPress files to the Apache web root:

```bash
sudo cp -R wordpress/* /var/www/html
cd /var/www/html
ls -l
```

### Step 5: Set Permissions
Set the appropriate ownership for the WordPress files:

```bash
sudo chown www-data:www-data -R /var/www/html
```

### Step 6: Remove Default Apache Index Page
Remove the default Apache index page:

```bash
sudo rm -rf index.html
```

---

## 3. Creating a Virtual Host for the Domain

### Step 1: Navigate to Sites-Available Directory
Move to the `sites-available` directory in Apache:

```bash
cd /etc/apache2/sites-available
```

### Step 2: Create a Virtual Host Configuration
Create a new virtual host configuration file:

```bash
sudo vim yourdomain-name.com.conf
```

Copy and paste the following into the file, replacing `yourdomain-name.com` with your actual domain name:

```apache
<VirtualHost *:80>
    ServerAdmin admin@yourdomain-name.com
    ServerName yourdomain-name.com
    ServerAlias www.yourdomain-name.com
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### Step 3: Enable the Site
Enable your new virtual host configuration and disable the default one:

```bash
sudo a2ensite yourdomain-name.com.conf
sudo a2dissite 000-default.conf
```

### Step 4: Restart Apache
Restart the Apache service to apply the changes:

```bash
sudo systemctl restart apache2
```

### Step 5: Check Apache Status
Verify that Apache is running:

```bash
sudo systemctl status apache2
```

---

This document provides a comprehensive guide to installing SSL First and WordPress on a custom domain. Make sure to replace all instances of `your_domain` and `yourdomain-name.com` with your actual domain name.
```
