# Magento - CMS

It is an open-source CMS by Adobe. Its on [version 2](https://github.com/magento/magento2/releases) (since 2015) is thus called **Magento2** as well.

## Installation on a Server

There is a one-liner script that installs as docker containers.

```bash
curl -s https://raw.githubusercontent.com/markshust/docker-magento/master/lib/onelinesetup | bash -s -- magento.test community <version>
```

### Magento 2.3.x on cPanel

- **Download Magento 2 Archive** - Go to [Adobe Experience](https://experience.adobe.com/home), Create an Adobe account and Download the `.zip` version of **Magento Open Source** from the Tech Resources section.
- **Unzip and Upload via cPanel** - Go to `public_html`, upload the `.zip` file then Extract the `.zip` file in that folder
- **Set File & Folder Permissions**

```bash
# Find
find . -type d -exec chmod 755 {} \;   # All folders → 755 (rwxr-xr-x)
find . -type f -exec chmod 644 {} \;   # All files → 644 (rw-r--r--)

# First, set group ownership to the web server
chgrp -R nobody var pub/static pub/media app/etc

# Then, give group write permissions (775)
chmod -R 775 var pub/static pub/media app/etc

```

- **Create MySQL Database and User** with All privileges and assign the database to the user.
- **Run the Web Installer** - Go to `http://yourdomain.com/setup`
Follow the **Magento Web Setup Wizard** to:
- **After install completes**: Delete `/setup` folder.

Problems with this Approach

- You can't update individual modules or libraries easily.
- You'll have to re-download a newer `.zip` manually to upgrade to a newer version.
- Dev tools like `bin/magento` CLI may not work fully

<details>
    <summary><h3>Magento 2.4+ on cPanel</h3></summary>

**Requirements:**

- **PHP**: `7.4 - 8.1` with required extensions
- **MySQL**: `8.0` OR **MariaDB**: `10.4+`
- **Composer** (for Magento dependencies)
- **Elasticsearch** (required for Magento 2.4+)

1. Create a MySQL Database and User (all privileges)
2. Navigate to your site folder: `cd ~/public_html/store`
3. Install Composer

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
mv composer.phar ~/bin/composer
```

4. Add `~/bin` to your `$PATH` in `.bashrc` or `.bash_profile`.
5. Download Magento 2 via Composer

You need **Magento Marketplace authentication keys**
Create them here: [https://marketplace.magento.com/customer/accessKeys/](https://marketplace.magento.com/customer/accessKeys/)

```bash
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition .
```

Enter the **Public/Private** keys when prompted.

---

### 📂 6. Set Correct Permissions

```bash
find var generated vendor pub/static pub/media app/etc -type f -exec chmod 644 {} \;
find var generated vendor pub/static pub/media app/etc -type d -exec chmod 755 {} \;
chmod u+x bin/magento
```

---

### 🧙 7. Install Magento via CLI

Replace the values with your own:

```bash
php bin/magento setup:install \
--base-url=http://store.example.com \
--db-host=localhost \
--db-name=magentodb \
--db-user=magentouser \
--db-password=YourDBPassword \
--admin-firstname=Admin \
--admin-lastname=User \
--admin-email=admin@example.com \
--admin-user=admin \
--admin-password=Admin123! \
--language=en_US \
--currency=USD \
--timezone=Asia/Karachi \
--use-rewrites=1 \
--search-engine=elasticsearch7 \
--elasticsearch-host=127.0.0.1 \
--elasticsearch-port=9200
```

### 🌐 8. Set Up .htaccess and Rewrite Rules

Make sure `.htaccess` files are present in the root and `/pub` folders.
Check Apache’s **mod\_rewrite** is enabled (cPanel usually has it enabled).

Set the document root to `public_html/store/pub` (optional but recommended for security).

---

### ✅ 9. Finish Setup

Visit `http://store.example.com` to see the frontend.

Visit `http://store.example.com/admin` to log into the backend.

---

### 🔧 Optional: Run Post-Install Commands

```bash
php bin/magento setup:static-content:deploy -f
php bin/magento indexer:reindex
php bin/magento cache:flush
```

</details>
