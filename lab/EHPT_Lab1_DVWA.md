# EHPT Lab 1 – Installing DVWA (Damn Vulnerable Web Application)
# Run on Kali Linux

# STEP 1: Update system
sudo apt update

# STEP 2: Install Apache, MariaDB, PHP and Git
sudo apt install apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php git -y

# STEP 3: Start services
sudo service apache2 start
sudo service mariadb start

# STEP 4: Clone DVWA into web root
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git DVWA

# STEP 5: Set permissions
sudo chmod -R 777 /var/www/html/DVWA

# STEP 6: Setup MariaDB – create DB and user
sudo mysql
# Inside mysql prompt, paste these 4 lines one by one:
CREATE DATABASE dvwa;
CREATE USER 'dvwauser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwauser'@'localhost';
FLUSH PRIVILEGES;
EXIT

# STEP 7: Configure DVWA
cd /var/www/html/DVWA/config
sudo cp config.inc.php.dist config.inc.php
sudo nano config.inc.php
# In nano, change these values:
# $_DVWA['db_user']     = 'dvwauser';
# $_DVWA['db_password'] = 'password';
# $_DVWA['db_database'] = 'dvwa';
# Save: Ctrl+O, Enter, Ctrl+X

# STEP 8: Restart Apache
sudo service apache2 restart

# STEP 9: Open browser and go to:
# http://localhost/DVWA/setup.php
# Click "Create / Reset Database"
# Then go to: http://localhost/DVWA/login.php
# Login: admin / password

# STEP 10: Verify everything is running
sudo service apache2 status
sudo service mariadb status

# Conclusion:
# DVWA was successfully installed using Apache, MariaDB, and PHP on Kali Linux.
# It provides a vulnerable web app for practicing SQL injection, XSS, brute force, etc.
