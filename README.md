### 1. Install LAMP & phpMyAdmin
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 mysql-server php php-mysql phpmyadmin -y
```
* **Prompts:** 
  * Select **apache2** (Press `Space`, then `Enter`).
  * Select **Yes** to configure the database for phpMyAdmin with `dbconfig-common`.
  * Set a password for the phpMyAdmin app database (or leave blank to auto-generate).

### 2. Configure Apache & Start Services
Force Apache to recognize phpMyAdmin and start the services:
```bash
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-enabled/phpmyadmin.conf
sudo systemctl enable --now apache2 mysql
sudo systemctl restart apache2
```

### 3. Create MySQL Admin User
MySQL 8.4+ blocks `root` logins via phpMyAdmin. Create a dedicated admin user:
```bash
sudo mysql
```
Run these SQL commands (change the password):
```sql
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'Admin@123';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;
```

### 4. Maximize Import Limits
Edit the PHP configuration to allow large `.sql` imports:
```bash
sudo nano /etc/php/*/apache2/php.ini
```
Find (`Ctrl+F`) and update these four values (e.g., to `512M` or `1G`):
```ini
upload_max_filesize = 512M
post_max_size = 512M
memory_limit = 512M
max_execution_time = 300
```
*(Rule: `memory_limit` ≥ `post_max_size` ≥ `upload_max_filesize`)*.

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`), then restart Apache:
```bash
sudo systemctl restart apache2
```

### 5. Access phpMyAdmin
Open your browser and navigate to:
`http://localhost/phpmyadmin` *(or `http://YOUR_SERVER_IP/phpmyadmin`)*

Log in using the **`admin`** username and the password you created in Step 3.
