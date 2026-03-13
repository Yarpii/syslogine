---
title: Magento 2 Complete Installation Guide
sidebar_label: Installation
---


## 🎯 Welcome!

This guide will help you install **Magento 2**, a professional e-commerce platform, on your server. Don't worry if you're new to server management—we'll walk through every step together, explaining what each piece does and why it matters.

**Time needed:** 2-4 hours  
**Technical difficulty:** Medium  
**What you need:**
- Access to a Linux server (Debian 13)
- A domain name (like shop.example.com)
- Patience and a cup of coffee ☕

---

## 📋 Quick Overview: What Are We Installing?

Before we start, let's understand what each component does:

| Component | What It Does | Why You Need It |
|-----------|------------|-----------------|
| **PHP** | The language Magento is written in | Makes Magento actually run |
| **MariaDB** | The database (where all your product/customer data lives) | Stores everything permanently |
| **OpenSearch** | Super-fast product search engine | Makes searching products lightning fast |
| **Redis** | Super-fast memory storage | Caches data so pages load quickly |
| **Nginx** | Web server (delivers pages to visitors) | Shows your store to customers |

Think of it like a restaurant:
- **PHP** = The kitchen (prepares food)
- **MariaDB** = The storage room (keeps ingredients)
- **OpenSearch** = The menu system (finds dishes quickly)
- **Redis** = Pre-made dishes in the warmer (ready instantly)
- **Nginx** = The waiter (brings food to customers)

---

## 📚 Table of Contents

1. [Before You Start](#before-you-start)
2. [Step 1: Install PHP Extensions](#step-1-install-php-extensions)
3. [Step 2: Install OpenSearch](#step-2-install-opensearch)
4. [Step 3: Install Redis](#step-3-install-redis)
5. [Step 4: Create a Database](#step-4-create-a-database)
6. [Step 5: Download Magento](#step-5-download-magento)
7. [Step 6: Fix MariaDB Compatibility](#step-6-fix-mariadb-compatibility)
8. [Step 7: Install Magento](#step-7-install-magento)
9. [Step 8: Configure Nginx](#step-8-configure-nginx)
10. [Step 9: Finish Setup](#step-9-finish-setup)
11. [Common Problems & Fixes](#common-problems--fixes)
12. [After Installation](#after-installation)

---

## Before You Start

### What You Need to Gather

Before running any commands, collect these items:

**1. Your Magento Marketplace Keys**

Magento requires two special keys. Here's how to get them:

1. Go to: https://commercemarketplace.adobe.com/
2. Log in to your account
3. Click on your profile (usually top right corner)
4. Look for **"Access Keys"** or **"My Downloads"**
5. Click **"Generate New Keys"** or copy existing ones
6. You'll see two things:
   - **Public Key** (looks like a username)
   - **Private Key** (looks like a long password)
7. **Save these somewhere safe**—you'll need them soon

**2. Database Password**

Create a strong password. A strong password:
- Has uppercase letters (A-Z)
- Has lowercase letters (a-z)
- Has numbers (0-9)
- Has symbols (!@#$%)
- Is at least 12 characters long

Example: `MyStore2024!@Secure`

**3. Admin Password**

This is YOUR password to manage the store. Make it different from the database password, and also strong.

**4. Your Domain Name**

Example: `shop.example.com`

**5. Your Email Address**

For the store admin account.

### Before You Begin - Important Notes

⚠️ **Read these carefully:**

- **Backup first:** If you have existing data on this server, back it up before starting
- **Root access needed:** You need administrator (root) access to your server
- **Take your time:** Don't rush through steps. If something fails, read the error message carefully
- **Copy commands carefully:** One small typo can cause problems
- **Keep passwords safe:** Write them down in a secure place
- **Ask for help:** If you get stuck, don't guess—look up the error message or ask for help

---

## Step 1: Install PHP Extensions

### What This Does

PHP needs special add-ons (called extensions) to work with Magento. Think of them like plugins for a web browser.

### How to Do It

**1. Open terminal/command line on your server**

**2. Copy this entire command and paste it into your terminal:**

```bash
apt install php8.4-bcmath php8.4-curl php8.4-intl php8.4-soap php8.4-xsl \
  php8.4-zip php8.4-gd php8.4-xml php8.4-json php8.4-mbstring -y
```

**3. Press Enter and wait**

The system will automatically download and install everything. You'll see text scrolling—this is normal. It usually takes 2-5 minutes.

**4. When it's done, you'll see a prompt again**

It should look something like: `root@server:~#`

### Did It Work?

To check if everything installed correctly, copy and paste this:

```bash
php -m | grep bcmath
```

If it shows `bcmath`, you're good! If you see nothing, something went wrong—try installing again.

---

## Step 2: Install OpenSearch

### What This Does

OpenSearch is a superfast search engine. When customers search for "blue shirt," it finds all blue shirts instantly instead of searching through thousands of products one by one.

### Step 2a: Set a Password for OpenSearch

**This is VERY important.** OpenSearch needs a password BEFORE we install it.

Copy this command, but **change the password part**:

```bash
export OPENSEARCH_INITIAL_ADMIN_PASSWORD="YourStrongPassword123!"
```

Replace `YourStrongPassword123!` with your own strong password. For example:

```bash
export OPENSEARCH_INITIAL_ADMIN_PASSWORD="OpenSearch2024!@Blue"
```

**Write down this password!** You'll need it later.

### Step 2b: Install OpenSearch Package

Now copy and paste this:

```bash
dpkg -i opensearch-3.5.0-linux-x64.deb
```

Wait for it to finish (usually 1-3 minutes).

### Step 2c: Start OpenSearch

Copy and paste:

```bash
systemctl daemon-reload
systemctl enable opensearch
systemctl start opensearch
```

Wait 10-15 seconds for OpenSearch to start. It's like starting a car—takes a moment to warm up.

### Step 2d: Check If It's Working

Copy and paste this, **but use your password** from Step 2a:

```bash
curl -u admin:YourStrongPassword123! http://localhost:9200
```

If you see some text with `"status": 200`, it's working! 🎉

If you see an error, try again in 10 seconds—OpenSearch can take time to start.

### Step 2e: Fix SSL (Don't worry, this is easy)

OpenSearch has a security feature called SSL that we need to turn off for Magento. Here's how:

**1. Open the configuration file:**

```bash
nano /etc/opensearch/opensearch.yml
```

**2. Find this line:**

```
plugins.security.ssl.http.enabled: true
```

(Use Ctrl+W to search if the file is long)

**3. Change `true` to `false`:**

```
plugins.security.ssl.http.enabled: false
```

**4. Save the file:**

Press Ctrl+X, then Y, then Enter

**5. Restart OpenSearch:**

```bash
systemctl restart opensearch
```

**6. Check it again:**

```bash
curl -u admin:YourStrongPassword123! http://localhost:9200
```

---

## Step 3: Install Redis

### What This Does

Redis is like a super-fast memo board. Instead of looking something up in a book every time (slow), it keeps frequently used information on a memo board nearby (fast).

### The Installation

Copy and paste this:

```bash
apt install redis-server redis-tools -y
systemctl enable redis-server
systemctl start redis-server
```

### Check If It's Working

```bash
redis-cli ping
```

You should see: `PONG`

That's it! Redis is installed and running. 👍

---

## Step 4: Create a Database

### What This Does

This creates a safe storage area (database) where Magento will keep all your products, customers, orders, etc.

### The Steps

**1. Open the database tool:**

```bash
mysql -u root -p
```

You'll be asked for your root password. Type it (the cursor won't show anything—that's normal).

**2. You should now see:**

```
MariaDB [(none)]>
```

**3. Copy this command, but change the password:**

```sql
CREATE DATABASE magento2 CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'magento'@'localhost' IDENTIFIED BY 'YourDatabasePassword!';
GRANT ALL PRIVILEGES ON magento2.* TO 'magento'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**Before you paste it:** Change `YourDatabasePassword!` to the strong password you created earlier.

**4. Paste the entire thing into the terminal and press Enter**

You should see `Query OK` messages. This means it worked.

### Test It

Let's make sure the database user can actually log in:

```bash
mysql -u magento -p magento2
```

Type your database password. You should see:

```
MariaDB [magento2]>
```

Type `EXIT;` and press Enter. If that worked, you're done with this step! ✅

---

## Step 5: Download Magento

### What This Does

This downloads all the Magento files from the internet. It's like downloading a program—the files get unpacked and organized automatically.

### Step 5a: Go to Your Installation Folder

Copy and paste this (change the path to match your setup):

```bash
cd ~/domains/dev.scooterdynamics.com/magento2
```

Or if the folder doesn't exist, create it first:

```bash
mkdir -p ~/domains/dev.scooterdynamics.com/magento2
cd ~/domains/dev.scooterdynamics.com/magento2
```

### Step 5b: Download Magento

Copy and paste this:

```bash
composer create-project --repository=https://repo.magento.com/ magento/project-community-edition .
```

**When it asks for a username and password:**

- **Username:** Your Magento Marketplace **Public Key** (from earlier)
- **Password:** Your Magento Marketplace **Private Key** (from earlier)

⏱️ This will take 10-30 minutes depending on your internet speed. Don't interrupt it—just wait patiently.

You'll see lots of text showing files being downloaded. At the end, you should see something like:

```
✓ All good!
```

---

## Step 6: Fix MariaDB Compatibility

### What This Does

Magento was made for older versions of MariaDB database. We're using a newer version, so we need to make a small fix. Think of it like updating an instruction manual for a newer product.

### How to Check If You Need This

Run this:

```bash
mariadb --version
```

If you see `11.8` or higher, follow the steps below. If it's lower, skip this section.

### The Fix - Part 1

**1. Open this file:**

```bash
nano vendor/magento/framework/DB/Adapter/SqlVersionProvider.php
```

**2. Find this line** (use Ctrl+W to search for `MARIA_DB_11_4`):

```php
public const MARIA_DB_11_4_VERSION = '11.4.';
```

**3. Add this line right after it:**

```php
public const MARIA_DB_11_8_VERSION = '11.8.';
```

**4. Find the function** `getMariaDbSuffixKey()` (use Ctrl+W again)

**5. Find this line inside it:**

```php
$isMariaDB114 = str_contains($sqlVersion, SqlVersionProvider::MARIA_DB_11_4_VERSION);
```

**6. Add this line right after it:**

```php
$isMariaDB118 = str_contains($sqlVersion, SqlVersionProvider::MARIA_DB_11_8_VERSION);
```

**7. Find the if/else chain** in that same function

**8. Add this new condition** after the `$isMariaDB114` condition:

```php
} elseif ($isMariaDB118) {
    return SqlVersionProvider::MARIA_DB_10_6_11_VERSION;
```

**9. Save the file:** Ctrl+X, Y, Enter

### The Fix - Part 2

**1. Open the app configuration file:**

```bash
nano app/etc/di.xml
```

**2. Find this section** (Ctrl+W to search for `supportedVersionPatterns`):

```xml
<item name="MariaDB-11.4" xsi:type="string">^11\.4\.</item>
```

**3. Add this new line right after it:**

```xml
<item name="MariaDB-11.8" xsi:type="string">11\.8\.</item>
```

Notice: This one does NOT start with `^` (that's intentional and important!)

**4. Save the file:** Ctrl+X, Y, Enter

### Clear Temporary Files

```bash
rm -rf var/cache/* var/page_cache/* generated/* var/di/*
```

---

## Step 7: Install Magento

### What This Does

This is the main installation step. It connects everything together, creates the database tables, and sets up your store.

### The Installation Command

This is a long command. **Copy it carefully:**

```bash
php bin/magento setup:install \
  --base-url=https://dev.scooterdynamics.com \
  --base-url-secure=https://dev.scooterdynamics.com \
  --db-host=localhost \
  --db-name=magento2 \
  --db-user=magento \
  --db-password=YourDatabasePassword! \
  --admin-firstname=Admin \
  --admin-lastname=User \
  --admin-email=your@email.com \
  --admin-user=admin \
  --admin-password=YourAdminPassword! \
  --language=en_US \
  --currency=EUR \
  --timezone=Europe/Amsterdam \
  --use-rewrites=1 \
  --search-engine=opensearch \
  --opensearch-host=localhost \
  --opensearch-port=9200 \
  --opensearch-username=admin \
  --opensearch-password=YourOpenSearchPassword! \
  --opensearch-enable-auth=1 \
  --opensearch-ssl-verify=0 \
  --session-save=redis \
  --session-save-redis-host=127.0.0.1 \
  --session-save-redis-port=6379 \
  --session-save-redis-db=0 \
  --cache-backend=redis \
  --cache-backend-redis-server=127.0.0.1 \
  --cache-backend-redis-port=6379 \
  --cache-backend-redis-db=1
```

### What to Change

**Before pasting, change these parts:**

| Part | Change To | Example |
|------|-----------|---------|
| `dev.scooterdynamics.com` | Your domain name | `shop.mystore.com` |
| `YourDatabasePassword!` | Your database password | `MyStore2024!@Secure` |
| `your@email.com` | Your email | `admin@mystore.com` |
| `YourAdminPassword!` | Your admin password | `AdminPass2024!@` |
| `YourOpenSearchPassword!` | Your OpenSearch password | `OpenSearch2024!@Blue` |
| `en_US` | Your language code | `nl_NL` for Dutch, `fr_FR` for French |
| `EUR` | Your currency | `USD`, `GBP`, etc. |
| `Europe/Amsterdam` | Your timezone | `America/New_York`, `Asia/Tokyo`, etc. |

### Run the Installation

Paste the command (with your changes) into the terminal and press Enter.

⏱️ **This takes 5-15 minutes.** You'll see lots of text. This is normal. Just wait.

### How to Know It Worked

At the end, you should see:

```
[SUCCESS]: Magento installation complete.
```

If something goes wrong, see the "Common Problems" section at the bottom.

---

## Step 8: Configure Nginx

### What This Does

Nginx is the web server that delivers your store to customers. We need to tell it how to handle Magento requests.

### Make a Backup First

```bash
cp /etc/nginx/sites-available/dev.scooterdynamics.com \
   /etc/nginx/sites-available/dev.scooterdynamics.com.backup
```

This creates a copy in case something goes wrong.

### Open the Configuration File

```bash
nano /etc/nginx/sites-available/dev.scooterdynamics.com
```

### Replace With This Configuration

Clear everything in the file and paste this (edit the domain names and IP addresses to match your setup):

```nginx
# Magento 2 Configuration
upstream fastcgi_backend {
    server unix:/run/php/17721605021378014.sock;
}

server {
    server_name dev.scooterdynamics.com www.dev.scooterdynamics.com;
    
    listen 192.168.33.18;
    listen [2001:4c3c:4801:2800:d294:66ff:fe60:de29];
    listen 192.168.33.18:443 ssl http2;
    listen [2001:4c3c:4801:2800:d294:66ff:fe60:de29]:443 ssl http2;

    ssl_certificate /etc/ssl/virtualmin/17721605021378014/ssl.combined;
    ssl_certificate_key /etc/ssl/virtualmin/17721605021378014/ssl.key;
    ssl_protocols TLSv1.2 TLSv1.3;

    root /home/scooterdynamics/domains/dev.scooterdynamics.com/magento2/pub;
    index index.php;

    access_log /var/log/virtualmin/dev.scooterdynamics.com_access.log;
    error_log /var/log/virtualmin/dev.scooterdynamics.com_error.log;

    # Force HTTPS
    if ($scheme = http) {
        rewrite ^(?!/.well-known)(.*)$ https://$host$1 redirect;
    }

    # Main location
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    # Static files (images, CSS, JS)
    location /static/ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        location ~ ^/static/version\d+/ {
            rewrite ^/static/version\d+/(.*)$ /static/$1 last;
        }
    }

    # Media files
    location /media/ {
        try_files $uri $uri/ /get.php$is_args$args;
        expires 1y;
    }

    # PHP files
    location ~ "\.php(/|$)" {
        try_files $uri =404;
        fastcgi_split_path_info "^(.+\.php)(/.+)$";
        fastcgi_pass fastcgi_backend;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param HTTPS $https;
        fastcgi_read_timeout 600;
        fastcgi_connect_timeout 600;
        fastcgi_send_timeout 600;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 256 16k;
        include fastcgi_params;
    }

    # Deny hidden files
    location ~ /\. {
        deny all;
    }

    # Deny sensitive files
    location ~* \.(htaccess|htpasswd|git)$ {
        deny all;
    }
}
```

### Save the File

Press Ctrl+X, then Y, then Enter

### Check for Errors

```bash
nginx -t
```

You should see:

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

If you see errors, something went wrong. Go back and check your edits.

### Reload Nginx

```bash
systemctl reload nginx
```

---

## Step 9: Finish Setup

### Generate Static Files

Static files are CSS (styling), JavaScript (interactivity), and fonts. They need to be generated:

```bash
php bin/magento setup:static-content:deploy -f en_US nl_NL
```

Change `en_US nl_NL` to match your languages.

⏱️ This takes 3-5 minutes.

### Clear Cache

```bash
php bin/magento cache:flush
```

### Fix File Permissions

Make sure the web server can access all files:

```bash
cd ~/domains/dev.scooterdynamics.com/magento2
chown -R www-data:www-data .
chmod -R 755 var pub generated app/etc
```

### Update Search Index

```bash
php bin/magento indexer:reindex
```

⏱️ This takes 2-5 minutes.

---

## 🎉 You're Done!

### Visit Your Store

Open your web browser and go to:

```
https://dev.scooterdynamics.com
```

You should see your Magento store!

### Log In to Admin Panel

Go to:

```
https://dev.scooterdynamics.com/admin
```

Or find the admin URL in the welcome email if Magento sent one.

**Login with:**
- Username: `admin`
- Password: The admin password you set earlier

---

## Common Problems & Fixes

### "Something went wrong" but I don't know what

**This means:** An error happened but it's hidden

**How to find the real error:**

```bash
# Look at the log file
tail -f var/log/system.log
```

The real error message will appear here. Read it carefully—it usually tells you exactly what's wrong.

### "Permission denied" when running commands

**This means:** The system won't let you run this command

**How to fix it:**

```bash
# Use the full path to PHP
/usr/bin/php bin/magento cache:flush
```

Or check that you're in the right directory:

```bash
pwd
# Should show: .../magento2
```

### Website shows "502 Bad Gateway"

**This means:** The web server can't talk to PHP

**How to fix it:**

1. Check if PHP is running:
```bash
systemctl status php8.4-fpm
```

Should show `active (running)`. If not:

```bash
systemctl start php8.4-fpm
```

2. Check the error log:
```bash
tail -f /var/log/php8.4-fpm.log
```

### Website shows blank page

**This means:** Something crashed but it's not showing the error

**How to fix it:**

1. Enable developer mode:
```bash
php bin/magento deploy:mode:set developer
```

2. Check the error logs:
```bash
tail -f var/log/exception.log
```

### CSS and images not loading (website looks broken)

**This means:** Static files weren't deployed properly

**How to fix it:**

```bash
# Regenerate static files
php bin/magento setup:static-content:deploy -f en_US nl_NL

# Clear cache
php bin/magento cache:flush
```

### "MariaDB not supported" error during setup

**This means:** Your MariaDB version isn't recognized

**How to fix:**

Go back to **Step 6: Fix MariaDB Compatibility** and make sure you made both changes.

### OpenSearch says "No alive nodes"

**This means:** OpenSearch isn't running or isn't reachable

**How to fix:**

```bash
# Check if OpenSearch is running
systemctl status opensearch

# If not, start it
systemctl start opensearch

# Wait 15 seconds, then test
curl -u admin:YourPassword! http://localhost:9200
```

### Stuck on installation screen

**This means:** Something is taking a very long time

**What to do:**

1. Wait at least 5 more minutes—installation can be slow
2. Don't refresh the page or close the terminal
3. If it's still stuck after 15 minutes, check the error log:
```bash
tail -f var/log/system.log
```

### "Database connection failed"

**This means:** The database isn't working

**How to fix:**

```bash
# Test the database connection
mysql -u magento -p -h localhost magento2

# Type your password when asked
```

If it connects, type `EXIT;`. If it doesn't connect, your password is wrong.

---

## After Installation

### Regular Maintenance

Once you've installed Magento, you should do these things regularly:

**Daily:**
- Check for errors: `tail -f var/log/system.log`
- Monitor performance

**Weekly:**
- Backup your database:
```bash
mysqldump -u magento -p magento2 > backup_$(date +%Y%m%d).sql
```

- Clear old cache files:
```bash
php bin/magento cache:flush
```

**Monthly:**
- Review logs for patterns
- Check disk space: `df -h`
- Update Magento: Follow Magento's official update guide

### Useful Commands for Later

Here are commands you'll probably need:

**If something goes wrong:**
```bash
# Clear all caches
php bin/magento cache:flush

# Rebuild search index
php bin/magento indexer:reindex

# Check system status
php bin/magento module:status
```

**If you forget the admin password:**
```bash
php bin/magento admin:user:create \
  --admin-user=newadmin \
  --admin-password=NewPassword123! \
  --admin-email=admin@example.com \
  --admin-firstname=Admin \
  --admin-lastname=User
```

**To switch between development and production modes:**
```bash
# Development mode (shows errors)
php bin/magento deploy:mode:set developer

# Production mode (faster, hides errors)
php bin/magento deploy:mode:set production
```

### Where to Get Help

If you get stuck:

1. **Read the error message carefully** - it usually tells you exactly what's wrong
2. **Search Google for the error** - you're probably not the first person to have this problem
3. **Check the logs:** `tail -f var/log/system.log`
4. **Visit Magento Forums:** https://magento.stackexchange.com/
5. **Ask in communities:** Reddit's r/magento, Magento Discord servers, etc.

### Next Steps

Now that Magento is installed:

1. **Set up products** - Add your items for sale
2. **Configure payment methods** - PayPal, Stripe, etc.
3. **Set up shipping** - Decide how customers get orders
4. **Customize the design** - Make it look like your brand
5. **Set up taxes** - If applicable in your region
6. **Test buying** - Buy something as a customer to test everything

---

## Quick Reference Card

Save this for quick lookup:

### Common Commands

```bash
# Clear cache
php bin/magento cache:flush

# Rebuild search index
php bin/magento indexer:reindex

# Deploy static content
php bin/magento setup:static-content:deploy -f en_US nl_NL

# Check status
php bin/magento module:status

# View logs
tail -f var/log/system.log
tail -f var/log/exception.log
```

### Service Control

```bash
# Start/stop/restart services
systemctl start/stop/restart opensearch
systemctl start/stop/restart redis-server
systemctl start/stop/restart php8.4-fpm
systemctl start/stop/restart nginx
```

### Database Operations

```bash
# Connect to database
mysql -u magento -p magento2

# Backup database
mysqldump -u magento -p magento2 > backup.sql

# Restore database
mysql -u magento -p magento2 < backup.sql
```

---

## 🎓 Learning Resources

Want to understand Magento better?

- **Official Docs:** https://docs.magento.com
- **Tutorials:** https://devdocs.magento.com/guides/
- **Community:** https://magento.stackexchange.com
- **YouTube:** Search "Magento 2 tutorial" for video guides

---

## Final Checklist

Before considering your installation complete, verify:

- ✅ You can visit your store: `https://yourdomain.com`
- ✅ You can log in to admin: `https://yourdomain.com/admin`
- ✅ Store pages load with proper styling (CSS/images show)
- ✅ Search works (try searching for something)
- ✅ You can add products
- ✅ Error logs are clean: `tail -f var/log/system.log` shows no errors
- ✅ Redis is caching: `redis-cli info` shows activity
- ✅ Database backups are working

---

## Troubleshooting Summary

If something goes wrong:

1. **Don't panic** - it's probably fixable
2. **Read the error message** - it tells you what's wrong
3. **Check the logs** - `var/log/system.log` has details
4. **Search online** - you're not alone
5. **Ask for help** - communities are friendly

---

## Glossary of Terms

**API Keys:** Special security codes that let Magento download packages  
**Cache:** Temporary storage that makes things faster  
**CLI:** Command Line Interface (the terminal where you type commands)  
**Database:** Where all your store data lives  
**Extension:** An add-on that adds features to Magento  
**FastCGI:** How Nginx talks to PHP  
**Index:** A database table that makes searching fast  
**OpenSearch:** Fast product search engine  
**PHP:** Programming language Magento is built with  
**Redis:** Super-fast temporary memory storage  
**SSH:** Secure way to access your server remotely  
**SSL:** Encryption that makes your website secure (the "https")  

---

Good luck with your Magento store! 🚀
