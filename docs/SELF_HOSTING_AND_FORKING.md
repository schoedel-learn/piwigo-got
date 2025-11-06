# Self-Hosting and Forking Guide for Piwigo Customization

This guide will help you set up a local development environment for customizing Piwigo.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- **Git** - For version control and cloning the repository
- **PHP 7.4+** - Required PHP version (PHP 7.0+ technically works but is end-of-life)
- **MySQL 5+ or MariaDB** - Database server
- **Web Server** - Apache or nginx recommended
- **ImageMagick** (recommended) or **PHP GD** - For image processing

### macOS Setup

```bash
# Install via Homebrew
brew install php mysql nginx
# or
brew install php mysql httpd  # for Apache
```

### Linux Setup

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install php mysql-server nginx
# or
sudo apt-get install php mysql-server apache2  # for Apache
```

## Setting Up Your Development Environment

### 1. Clone the Repository

If you're working with a fork:

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/Piwigo.git piwigo-dev
cd piwigo-dev

# Or if you're working directly with the main repository
git clone https://github.com/Piwigo/Piwigo.git piwigo-dev
cd piwigo-dev
```

### 2. Create a Development Branch

Following the contributing guidelines, always work on a branch:

```bash
git checkout master
git checkout -b customization-dev
```

### 3. Configure Your Web Server

#### Apache Configuration

Create a virtual host configuration (e.g., `/etc/apache2/sites-available/piwigo-dev.conf`):

```apache
<VirtualHost *:80>
    ServerName piwigo-dev.local
    DocumentRoot /path/to/piwigo-dev
    
    <Directory /path/to/piwigo-dev>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Enable the site and add to `/etc/hosts`:
```bash
sudo a2ensite piwigo-dev
sudo systemctl restart apache2
echo "127.0.0.1 piwigo-dev.local" | sudo tee -a /etc/hosts
```

#### nginx Configuration

Create `/etc/nginx/sites-available/piwigo-dev`:

```nginx
server {
    listen 80;
    server_name piwigo-dev.local;
    root /path/to/piwigo-dev;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

Enable and restart:
```bash
sudo ln -s /etc/nginx/sites-available/piwigo-dev /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### 4. Set Up the Database

Create a MySQL database for your development environment:

```bash
mysql -u root -p
```

In MySQL:
```sql
CREATE DATABASE piwigo_dev CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'piwigo_dev'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON piwigo_dev.* TO 'piwigo_dev'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 5. Configure Local Settings

Piwigo uses the `local/` directory for local configuration (this directory is gitignored). You need to create configuration files:

#### Create Database Configuration

Create `local/config/database.inc.php`:

```php
<?php
$conf['dblayer'] = 'mysqli';
$conf['db_base'] = 'piwigo_dev';
$conf['db_user'] = 'piwigo_dev';
$conf['db_password'] = 'your_password';
$conf['db_host'] = 'localhost';

$prefixeTable = 'piwigo_';

define('PHPWG_INSTALLED', true);
define('PWG_CHARSET', 'utf-8');
define('DB_CHARSET', 'utf8mb4');
define('DB_COLLATE', 'utf8mb4_unicode_ci');
?>
```

**Note**: The installer will create this file automatically with the correct format. If you create it manually, make sure it matches the format above exactly (no trailing spaces or line breaks after the closing `?>`).

#### Create Local Configuration Override

Create `local/config/config.inc.php`:

```php
<?php
// Local development configuration overrides
// See include/config_default.inc.php for all available options

// Enable debug mode
$conf['show_php_errors'] = E_ALL;
$conf['show_php_errors_on_frontend'] = true;

// Development logging
$conf['log_level'] = 'DEBUG';

// Disable caching for development (optional)
// $conf['enable_cache'] = false;

// Allow file uploads during development
$conf['upload_form_all_types'] = true;
?>
```

**Important**: The `local/` directory structure should already exist with placeholder `index.php` files. If not, create:
- `local/config/`
- `local/css/`
- `local/language/`

### 6. Run the Installation

Navigate to your Piwigo installation in a browser:

```
http://piwigo-dev.local/install.php
```

Or if using PHP built-in server:

```bash
php -S localhost:8000
# Then visit http://localhost:8000/install.php
```

Follow the installation wizard. The installer will:
- Verify your database connection
- Create necessary database tables
- Set up initial configuration
- Create an admin user

**Note**: If you've already created `local/config/database.inc.php`, the installer should detect it and use those settings.

### 7. Directory Permissions

Ensure proper permissions for upload and data directories:

```bash
# Create necessary directories if they don't exist
mkdir -p _data/{galleries,upload,plugins,themes,cache,include}
mkdir -p galleries upload

# Set permissions (adjust user/group as needed)
chmod -R 755 _data galleries upload
chown -R www-data:www-data _data galleries upload  # for Apache
# or
chown -R nginx:nginx _data galleries upload  # for nginx
```

## Customization Options

### Template Customization

Instead of modifying core template files directly (which would be overwritten on upgrade), use the `template-extension/` directory:

1. Copy template files you want to customize to `template-extension/your-theme/local/`
2. Modify them as needed
3. Update the relevant PHP files to reference your custom templates

Example from `template-extension/yoga/local/README`:
- Copy `themes/default/header.tpl` to `template-extension/default/local/my-header.tpl`
- Edit the PHP file that includes it to point to your custom location

### Plugin Development

Create plugins in the `plugins/` directory (also gitignored). Each plugin should have:
- `main.inc.php` - Main plugin file
- `admin/` - Admin interface (optional)
- `include/` - PHP includes (optional)
- Language files as needed

### Theme Development

Create custom themes in the `themes/` directory. Each theme needs:
- `themeconf.inc.php` - Theme configuration
- Template files (`.tpl` files)
- CSS and JavaScript files
- Images and assets

### Local CSS Overrides

Add custom CSS to `local/css/` directory - these files won't be overwritten during upgrades.

### Language Customizations

Add custom language files to `local/language/` directory.

## Development Workflow

### Making Changes

1. **Always work on a branch** - Never commit directly to `master`
2. **Test your changes** - Ensure functionality works as expected
3. **Follow coding standards** - Match the existing code style
4. **Commit with descriptive messages** - Prefix with issue numbers if applicable

```bash
git checkout -b feature/my-customization
# Make your changes
git add .
git commit -m "Add custom feature description"
git push origin feature/my-customization
```

### Syncing with Upstream

If working with a fork, keep it updated:

```bash
# Add upstream remote (if not already added)
git remote add upstream https://github.com/Piwigo/Piwigo.git

# Fetch latest changes
git fetch upstream

# Merge into your master
git checkout master
git merge upstream/master

# Update your feature branch
git checkout feature/my-customization
git rebase master
```

## Testing Your Customizations

### Enable Debug Mode

In `local/config/config.inc.php`:
```php
$conf['show_php_errors'] = E_ALL;
$conf['show_php_errors_on_frontend'] = true;
$conf['log_level'] = 'DEBUG';
```

### Check Logs

Logs are stored in `_data/logs/`. Check for errors and debug information.

### Testing Checklist

- [ ] Database connection works
- [ ] Images upload correctly
- [ ] Templates render properly
- [ ] Plugins/themes activate without errors
- [ ] No PHP errors in logs
- [ ] Admin panel accessible
- [ ] Frontend displays correctly

## Backup and Recovery

### Backup Your Database

```bash
mysqldump -u piwigo_dev -p piwigo_dev > piwigo_dev_backup.sql
```

### Backup Your Customizations

The `local/` directory contains your customizations. Consider backing it up separately:
```bash
tar -czf local-customizations.tar.gz local/
```

### Version Control

Remember:
- `local/` is gitignored (as it should be - contains sensitive data)
- Keep your custom template extensions, plugins, and themes in version control
- Document your customizations in your repository's README

## Troubleshooting

### Database Connection Issues

- Verify database credentials in `local/config/database.inc.php`
- Check MySQL service is running: `sudo systemctl status mysql`
- Verify database exists: `mysql -u root -p -e "SHOW DATABASES;"`

### Permission Issues

- Check directory permissions: `ls -la _data galleries upload`
- Ensure web server user can write to these directories
- On macOS/Linux: `chmod -R 755` for directories, `644` for files

### PHP Errors

- Check PHP error logs: `tail -f /var/log/php-errors.log`
- Verify PHP version: `php -v` (needs 7.4+)
- Check required PHP extensions are installed: `php -m`

### Installation Issues

- Clear browser cache and cookies
- Delete `local/config/database.inc.php` and let installer recreate it
- Check web server error logs

## Setting Up Cursor/VSCode for PHP Development

Cursor (and VSCode) can be configured as a powerful PHP development environment. The project includes configuration files to optimize your development experience.

### Required Extensions

Install the recommended extensions when prompted, or install manually:

1. **PHP Intelephense** (`bmewburn.vscode-intelephense-client`)
   - Advanced PHP code completion, navigation, and refactoring
   - Provides IntelliSense for PHP code

2. **PHP Debug** (`xdebug.php-debug`)
   - Debug PHP code with Xdebug
   - Set breakpoints and step through code

3. **Smarty** (`imperez.smarty`)
   - Syntax highlighting and basic support for Smarty templates (`.tpl` files)

4. **MySQL Client** (`cweijan.vscode-mysql-client2`)
   - Connect to your MySQL/MariaDB database
   - Run queries, browse tables, and manage data

5. **Error Lens** (`usernamehw.errorlens`)
   - Show inline error and warning messages

### Configuration Files Included

The project includes these configuration files:

- **`.vscode/settings.json`** - PHP settings, file associations, Intelephense configuration
- **`.vscode/extensions.json`** - Recommended extensions list
- **`.vscode/launch.json`** - Debugging configuration for Xdebug
- **`.vscode/tasks.json`** - Useful tasks like PHP syntax checking and starting a dev server
- **`.phpcs.xml`** - PHP CodeSniffer configuration (optional, requires phpcs installed)

### Using the Debugger

1. **Install Xdebug** on your PHP installation:
   ```bash
   # macOS with Homebrew
   brew install php-xdebug
   
   # Or install via PECL
   pecl install xdebug
   ```

2. **Configure Xdebug** in your `php.ini`:
   ```ini
   [xdebug]
   zend_extension=xdebug.so
   xdebug.mode=debug
   xdebug.start_with_request=yes
   xdebug.client_port=9003
   ```

3. **Set breakpoints** in your PHP code by clicking left of the line number

4. **Start debugging**:
   - Press `F5` or use Command Palette → "Debug: Start Debugging"
   - Select "Listen for Xdebug"
   - Navigate to your site in a browser to trigger breakpoints

### Using Tasks

Access tasks via Command Palette (`Cmd+Shift+P` → "Tasks: Run Task"):

- **Check PHP Syntax** - Validate the currently open PHP file
- **PHP Lint (All PHP files)** - Check syntax for all PHP files in the project
- **Start PHP Built-in Server** - Run `php -S localhost:8000` for quick testing

### Database Connection

1. Install the **MySQL Client** extension
2. Open Command Palette → "MySQL: New Connection"
3. Enter your database credentials:
   - Host: `localhost`
   - Port: `3306` (default)
   - User: `piwigo_dev` (or your DB user)
   - Password: (your password)
   - Database: `piwigo_dev`

### Tips for Piwigo Development

- **File Associations**: `.tpl` files are automatically recognized as Smarty templates
- **IntelliSense**: Get autocomplete for Piwigo functions and classes after the extension indexes
- **Search**: Use `Cmd+Shift+F` to search across the codebase (excludes `_data`, `upload`, `galleries`)
- **Quick Navigation**: `Cmd+P` to quickly open files, `Cmd+Shift+O` to jump to symbols

### Troubleshooting Intelephense

If Intelephense doesn't provide good autocomplete:

1. Check that the PHP executable path is correct in settings
2. Restart Cursor/VSCode after installing the extension
3. Wait for initial indexing to complete (check bottom status bar)
4. If issues persist, try: Command Palette → "Intelephense: Index workspace"

## Additional Resources

- [Piwigo Official Documentation](https://piwigo.org/doc/)
- [Piwigo Forums](https://piwigo.org/forum)
- [Contribution Guide](./CONTRIBUTING.md)
- [README](../README.md)

## Best Practices

1. **Never modify core files directly** - Use template extensions, plugins, or local config overrides
2. **Keep backups** - Regularly backup database and customizations
3. **Test upgrades** - Test your customizations after upgrading Piwigo
4. **Document changes** - Keep notes on what you've customized and why
5. **Use version control** - Commit your custom code regularly
6. **Follow security practices** - Don't commit passwords or sensitive data

---

Happy customizing! 🎨

