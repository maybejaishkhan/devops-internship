# Wordpress - CMS

WordPress is a free, open-source **Content Management System (CMS)** primarily used for creating websites and blogs. It is written in PHP and backed by a MySQL or MariaDB database. Initially launched as a blogging platform in 2003, it has since evolved into a powerful CMS capable of handling anything from simple portfolios to complex e-commerce sites and enterprise-grade applications.

WordPress powers over **40% of websites** on the internet due to its ease of use, large plugin ecosystem, active community, and flexibility.

## Features

1. Theming - Themes are stored in `wp-content/themes/`. Each theme typically includes:

   - `style.css` – Theme metadata and base styles
   - `functions.php` – Theme-specific PHP logic
   - Template files like `header.php`, `footer.php`, `page.php`, etc.

2. Plugins - Installable extensions that add functionality to WordPress. They're stored in `wp-content/plugins/` and can be activated or deactivated via the admin dashboard.

    - Drag-and-Drop: **Elementor**
    - SEO: Yoast SEO, RankMath
    - E-Commerce: WooCommerce
    - Caching: W3 Total Cache, WP Super Cache
    - Security: Wordfence, iThemes Security
    - Contact Forms: Contact Form 7, WPForms

3. User Roles and Permissions - WordPress includes a built-in user system with predefined roles:

   - *Administrator* (Full control)
   - *Editor* (Manages content from all users)
   - *Author* (Manages their own posts)
   - *Contributor* (Can write but not publish)
   - *Subscriber* (Read-only access)

## Project Structure

It is installed in `/var/www/html/wordpress/` (default; can be changed by the webserver).

```txt
wordpress/
├── wp-admin/                  - Admin Dashboard files
├── wp-content/                
│   ├── plugins/                - Installed plugins
│   ├── themes/                 - Installed themes
│   └── uploads/                - User media uploads
├── wp-includes/               - Libraries/Functions/Classes
├── wp-config.php              - Main config file
└── index.php                  - Entrypoint
```

The folder is named after the site usually. Docker uses the same path but we usually create separate containers for PHP/Wordpress, MySQL and WebServer (Apache/Nginx).

When installed via LocalWP the path is `~/Local\ Sites/<site>/{app,conf,logs}`.

We can install it via the GUI on a server that has cpanel installed. This way since cpanel already runs on php/apache/mysql so we wouldn't need to again get those dependencies for Wordpress.

### Configuration

Wordpress uses the `wp-config.php` file for its configuration and it uses `define('<key>', '<value>');` clauses.

```php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wp_user');
define('DB_PASSWORD', 'password');
define('DB_HOST', 'localhost');

define('WP_DEBUG', true);         // Enable logging
define('WP_DEBUG_LOG', true);     // Application Level Logs -> wp-content/debug.log
define('WP_DEBUG_DISPLAY', false); // Don’t show on frontend
define('WP_CACHE', true);       // Enable cache (optional)

// Extra configuration for security and stability
define('DISALLOW_FILE_EDIT', true);         // Disable file editor in admin
define('FS_METHOD', 'direct');              // Avoid FTP prompts
define('WP_ENVIRONMENT_TYPE', 'production'); // or 'development'
```

**.htaccess for Apache:**

```apache
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
```

**Nginx Equivalent:**

```nginx
location / {
    try_files $uri $uri/ /index.php?$args;
}
```

**Logging Paths**

- WordPress logs (if enabled): `wp-content/debug.log`
- Apache: `/var/log/apache2/access.log`, `/var/log/apache2/error.log`
- Nginx: `/var/log/nginx/access.log`, `/var/log/nginx/error.log`
- MySQL: `/var/log/mysql/error.log`
- PHP: `/var/log/php*/php-fpm.log` or as defined in `php.ini`

<details>
    <summary>Fresh Installation on a Server</summary>

Install Required Software then start and enable webserver (Apache) and database (MySQL) to run on boot. Secure MySQL (Root password, Remove anonymous users, Disallow remote root login, Remove test database) and lastly create a database for wordpress.

```bash
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php php-cli unzip curl -y
sudo systemctl start apache2 mysql && sudo systemctl enable apache2 mysql
sudo mysql_secure_installation # press enter when asked for password
sudo mysql -u root -p
```

```sql
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'strong_password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
-- Optional Hardening
DELETE FROM mysql.user WHERE User='';
DROP DATABASE test;
FLUSH PRIVILEGES;
EXIT;
```

Download WordPress

```bash
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
```

Configure WordPress

```bash
cp -R wordpress /var/www/html/<your-site>
cd /var/www/html/<your-site>
cp wp-config-sample.php wp-config.php
```

Edit the config file `nano wp-config.php` and Update:

```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wpuser' );
define( 'DB_PASSWORD', 'strong_password' );
define( 'DB_HOST', 'localhost' );
```

Set Permissions

```bash
sudo chown -R www-data:www-data /var/www/html/<your-site>
sudo find /var/www/html/<your-site> -type d -exec chmod 755 {} \;
sudo find /var/www/html/<your-site> -type f -exec chmod 644 {} \;
```

Configure WebServer

- Apache: `sudo nano /etc/apache2/sites-available/<your-site>.conf`

```apache
<VirtualHost *:80>
    ServerName yourdomain.com
    DocumentRoot /var/www/html/<your-site>
    <Directory /var/www/html/<your-site>>
        AllowOverride All
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/<your-site>-error.log
    CustomLog ${APACHE_LOG_DIR}/<your-site>-access.log combined
</VirtualHost>
```

```bash
sudo a2ensite <your-site>
sudo a2enmod rewrite
sudo systemctl reload apache2
```

Go to `http://yourdomain.com` and complete the WordPress installation wizard.

</details>

<details>
    <summary>WP-CLI Tips</summary>

Install WP-CLI from [https://wp-cli.org/](https://wp-cli.org/)

```bash
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp
```

Useful Commands:

```bash
wp plugin install elementor --activate
wp theme install astra --activate
wp cache flush
```

</details>

<details>
<summary>Docker Compose Setup</summary>

```yaml
services:
  wordpress:
    image: wordpress
    ports:
      - "8000:80"
    volumes:
      - ./wordpress:/var/www/html
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: strong_password
      WORDPRESS_DB_NAME: wordpress

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: strong_password
```

</details>
