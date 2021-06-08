# How to install LEMP on Linux Debian, Deepin, Ubuntu, etc.

## _(Linux, Nginx (Engine-X), MariaDB/MySQL and PHP)_

_By: Francisco Dara_

This tutorial will show you how to install the LEMP stack (Nginx, MariaDB and PHP7.4) on Debian based Linux distributions like Ubuntu, Deepin, Mint, ElementaryOS, Pop_OS and similar. A software stack is a set of software tools grouped together. LEMP stands for Linux, Nginx (Engine-X), MariaDB/MySQL and PHP, all open source and free to use. It is the most common software stack that powers dynamic web sites and applications, where:

- Linux is the operating system;
- Nginx is the web server;
- MariaDB/MySQL is the database server and;
- PHP is the server-side scripting language responsible for generating dynamic web pages.

## Prerequisites

To follow this tutorial, you need one of the above operating systems running on your local computer or on a remote server. If you are looking for a VPS (Virtual Private Server), then you can register an account with DigitalOcean via [this  link](https://www.digitalocean.com/)  or on your preferred VPS server. 

You will also need to have a root user account with `sudo` privileges.

## Step 1: Update Software Packages

Before we install the LEMP stack, it’s a good practice to update repository and software packages by running the following commands:

```bash
sudo apt update
```

```bash
sudo apt upgrade
```

## Step 2: Install Nginx Web Server

Nginx is a high-performance web server that is very popular today. It can also be used as a reverse proxy and caching server.

All software used in this procedure will come from your operating system's default package repositories due to the update performed in the previous step.

This means that we can use the apt package management pack to complete the necessary installations. You will be asked for your password `[sudo] password for your username:` and confirm the installation with "Y", press Enter. Digit the following command to install the Nginx web server:

```
sudo apt install nginx
```

After it’s installed, we can enable Nginx to auto-start at boot time by running the following command.

```
sudo systemctl enable nginx
```

Then start Nginx with this command:

```
sudo systemctl start nginx
```

Now check out its status.

```bash
sudo systemctl status nginx
```

Output:

```bash
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset:>
     Active: active (running) since Mon 2021-06-07 17:54:22 -03; 2min 33s ago
       Docs: man:nginx(8)
    Process: 1311 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_proce>
    Process: 1370 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (c>
   Main PID: 1376 (nginx)
      Tasks: 13 (limit: 18924)
     Memory: 14.9M
     CGroup: /system.slice/nginx.service
             ├─1376 nginx: master process /usr/sbin/nginx -g daemon on; master_>
             ├─1378 nginx: worker process
             ├─1380 nginx: worker process
```

“**Enabled**” indicates that auto-start at boot time is  enabled and we can see that Nginx is running. You can also see how much  RAM Nginx is using from the output. If the above command doesn’t  immediately quit after running. You need to press “**q**” to make it quit.

Check Nginx version.

```bash
nginx -v
```

Output:

```bash
nginx version: nginx/*.*.* (YourOperatingSystem)
```

Now enter your local server's public IP address into your browser's address bar. You should see the “Welcome to Nginx” web page, which means the Nginx web server is working correctly. If you are installing LEMP on your computer, type `http://127.0.0.1:80` or `http://localhost:80` in your browser's address bar.

If you're using a remote server and you don't have a domain name pointing to your server and you don't know its public IP address, you can find it by running the following command:

```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

As an alternative, check which IP address is accessible, as seen by other places on the internet:

```bash
curl -4 icanhazip.com
```

If the connection is refused or failed to complete, there might be a  firewall preventing incoming requests to TCP port 80. If you are using  iptables firewall, then you need to run the following command to open  TCP port 80.

```bash
sudo iptables -I INPUT -p tcp --dport 80 -j ACCEPT
```

If you are using UFW firewall, then run this command to open TCP port 80.

```bash
sudo ufw allow 'Nginx HTTP'
```

Finally, we need to make `www-data` (Nginx user) as the owner of web directory. By default, it’s owned by the root user.

```bash
sudo chown www-data:www-data /usr/share/nginx/html -R
```

Check whether the changes were successful by running:

```bash
sudo ufw enable
sudo ufw default deny
sudo ufw status
```

Output:

```bash
Status: active

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
80/tcp (v6)                ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

## Step 3: Install MariaDB Database Server

MariaDB is an immediate replacement for MySQL. It was developed by former members of the MySQL team to ensure open source if Oracle can turn MySQL into a closed source product. Enter the following command to install MariaDB.

```bash
sudo apt install mariadb-server mariadb-client
```

After it’s installed, MariaDB server should be automatically stared. Use **systemctl** to check its status.

```bash
systemctl status mariadb
```

Output:

```bash
● mariadb.service - MariaDB 10.5.10 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor prese>
     Active: active (running) since Mon 2021-06-07 18:51:45 -03; 59s ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
    
   Main PID: 11845 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 15 (limit: 18924)
     Memory: 69.5M
     CGroup: /system.slice/mariadb.service
             └─11845 /usr/sbin/mariadbd
```

You need to press “**q**” to make it quit.

If it’s not running, start it with this command:

```bash
sudo systemctl start mariadb
```

To enable MariaDB to automatically start at boot time, run

```bash
sudo systemctl enable mariadb
```

Now run the post installation security script.

```bash
sudo mysql_secure_installation
```

When you are asked to enter the MariaDB root password, press the Enter key, as the root password has not yet been set. Then type y to set the root password for the MariaDB server.

```bash
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user. If you've just installed MariaDB, and
haven't set the root password yet, you should just press enter here.

Enter current password for root (enter for none): 
```

Answer `Y` for yes on next requests or anything else to continue without enabling it.

```bash
#Press Y to:
Switch to unix_socket authentication [Y/n]

#Press Y to:
You already have your root account protected, so you can safely answer 'n'.
```

You will then be asked to submit and confirm a root password:

```bash
Change the root password? [Y/n] y

New password:
Re-enter the new password:
```

For the rest of the questions, press `Y` and click the ENTER key at each prompt. This will remove some anonymous users and test database, disable remote root logins and load these new rules so MariaDB immediately respects the changes we've made. This step is a basic requirement for MariaDB database security. (Note that Y is capitalized, which means this is the default answer.) 

```bash
# Output samples:

Remove anonymous users? [Y/n] 
Disallow root login remotely? [Y/n
Remove test database and access to it? [Y/n]
Reload privilege tables now? [Y/n]
```

If everything went well, you will receive the message:

```bash
Cleaning up...

All done! If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

By default, the MaraiDB package on Linux uses `unix_socket` to authenticate user login, which basically means you can use username  and password of the OS to log into MariaDB console. So you can run the  following command to login without providing MariaDB root password.

```bash
sudo mariadb -u root
```

To exit, run

```bash
exit;
```

Check MariaDB server version information.

```bash
mariadb --version
```

As you can see, we have installed MariaDB 10.*.*.

## Create MySQL|MariaDB user and grant privileges

Access the server with the command:

```bash
sudo mariadb -u root
```

Create a new user:

```bash
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

Set new user permissions:

```bash
GRANT ALL PRIVILEGES ON * . * TO 'username'@'localhost' IDENTIFIED BY 'password';
```

The asterisks in this command refer to the database and table (respectively) they can access - this specific command allows the user to read, edit, execute and perform all tasks on all databases and tables.

Load privileges:

```bash
FLUSH PRIVILEGES;
```

Go out:

```bash
quit;
```

Login with the new user:

```Bash
mariadb -u username -p
# Also works: mysql -u username -p
```

**Here is a short list of other possible common permissions that users can use:**

- ALL PRIVILEGES- as we saw earlier, this would give a MySQL user all access to a given database (or if no database is selected, the entire system)
- CREATE- allows you to create new tables or databases
- DROP- allows you to delete tables or databases
- DELETE - allows you to delete rows from tables
- INSERT - allows you to insert rows into tables
- SELECT- allows you to use the Select command to read databases
- UPDATE - allows you to update table rows
- GRANT OPTION- allows you to grant or revoke privileges from other users

**To give permission to a specific user, you can use this structure:**

> GRANT [permission type] ON [database name].[table name] TO ‘[user name]’@'localhost’;

## Step 4: Install PHP7.4

PHP7.4 is included in the repository. Enter the following command to install PHP7.4 and some common extensions.

```bash
sudo apt install php7.4 php7.4-fpm php7.4-mysql php-common php7.4-cli php7.4-common php7.4-json php7.4-opcache php7.4-readline php7.4-mbstring php7.4-xml php7.4-gd php7.4-curl
```

PHP extensions are commonly needed for content management systems (CMS) such as [WordPress](https://wordpress.org/). For example, if your installation doesn't have `php7.4-xml` then some of your WordPress site pages might be blank and you might find an error in the Nginx error log like:

> PHP message: PHP Fatal error:  Uncaught Error: Call to undefined function xml_parser_create()

Installing these PHP extensions ensures that your CMS runs smoothly. Now start php7.4-fpm.

```bash
sudo systemctl start php7.4-fpm
```

Enable auto-start at boot time.

```bash
sudo systemctl enable php7.4-fpm
```

Check status:

```bash
systemctl status php7.4-fpm
```

Sample output:

```bash
● php7.4-fpm.service - The PHP 7.4 FastCGI Process Manager
     Loaded: loaded (/lib/systemd/system/php7.4-fpm.service; enabled; vendor pr>
     Active: active (running) since Mon 2021-06-07 21:10:39 -03; 4min 47s ago
       Docs: man:php-fpm7.4(8)
   Main PID: 34435 (php-fpm7.4)
     Status: "Processes active: 0, idle: 2, Requests: 0, slow: 0, Traffic: 0req>
      Tasks: 3 (limit: 18924)
     Memory: 9.2M
     CGroup: /system.slice/php7.4-fpm.service
             ├─34435 php-fpm: master process (/etc/php/7.4/fpm/php-fpm.conf)
             ├─34436 php-fpm: pool www
             └─34437 php-fpm: pool www
```

## Step 5: Create an Nginx Server Block

An Nginx server block is like a virtual host in Apache. We will not  use the default server block because it’s inadequate to run PHP code and if we modify it, it becomes a mess. So remove the `default` symlink in `sites-enabled` directory by running the following command. (It’s still available as `/etc/nginx/sites-available/default`.)

```bash
sudo rm /etc/nginx/sites-enabled/default
```

Then use a command-line text editor like Nano to create a brand new server block file under **/etc/nginx/conf.d/** directory.

```bash
sudo nano /etc/nginx/conf.d/default.conf
```

Paste the following text into the file. The following snippet will  make Nginx listen on IPv4 port 80 and IPv6 port 80 with a catch-all  server name.

```bash
server {
  listen 80;
  listen [::]:80;
  server_name _;
  root /usr/share/nginx/html/;
  index index.php index.html index.htm index.nginx-debian.html;

  location / {
    try_files $uri $uri/ /index.php;
  }

  location ~ \.php$ {
    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
    include snippets/fastcgi-php.conf;
  }

 # A long browser cache lifetime can speed up repeat visits to your page
  location ~* \.(jpg|jpeg|gif|png|webp|svg|woff|woff2|ttf|css|js|ico|xml)$ {
       access_log        off;
       log_not_found     off;
       expires           360d;
  }

  # disable access to hidden files
  location ~ /\.ht {
      access_log off;
      log_not_found off;
      deny all;
  }
}
```

Save and close the file. (To save a file in Nano text editor, press `Ctrl+O`, then press `Enter` to confirm. To exit, press `Ctrl+X`.)

Here's what each of these location blocks and guidelines do:

- listen — Defines what the Nginx port will listen on. In this case, it will listen on port 80, the default port for HTTP.
- root— Defines the root document where files presented by the site are stored.
- index— Configures Nginx to prioritize service files named index.php when an index file is requested, if they are available.
- server_name— Defines which server block should be used for a given request to your server.** Point this directive to your server's domain name or public IP address.**
- location /— The first location block includes a try_files directive, which checks for files that match a URI request. If Nginx cannot find the proper file, it will return a 404 error.
- location ~ \.php$— This location block handles current PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.2-fpm.sock file which declares which socket is associated with php-fpm .
- location ~ /\.ht — The last location block handles .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess file finds its path to the root of the document it will not be presented to visitors.

Then test Nginx configurations.

```bash
sudo nginx -t
```

Sample output:

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

If the test is successful, reload Nginx.

```bash
sudo systemctl reload nginx
```

## Step 6: Test PHP

To test PHP-FPM with Nginx Web server, we need to create a `info.php` file in the webroot directory.

```bash
sudo nano /usr/share/nginx/html/info.php
```

Paste the following PHP code into the file.

```bash
<?php phpinfo(); ?>
```

Save and close the file. Now in the browser address bar, enter `server-ip-address/info.php`. Replace `sever-ip-address` with your actual IP. If you follow this tutorial on your local computer, then type `127.0.0.1/info.php` or `localhost/info.php`.

You should see your server’s PHP information. This means PHP scripts can run properly with Nginx web server.

Congrats! You have successfully installed Nginx, MariaDB and PHP7.4  on Linux. For your server’s security, you should delete `info.php` file now to prevent hacker seeing it.

```bash
sudo rm /usr/share/nginx/html/info.php
```

## Troubleshooting Tip

If you encounter errors, you can check the Nginx error log (`/var/log/nginx/error.log`) to find out what’s wrong.

## Nginx Automatic Restart

If for any reason your Nginx process is killed, you need to run the following command to restart it.

```bash
sudo systemctl restart nginx
```

Instead of manually typing this command, we can make Nginx automatically restart by editing the `nginx.service` systemd service unit. To override the default systemd service configuration, we create a separate directory.

```bash
sudo mkdir -p /etc/systemd/system/nginx.service.d/
```

Then create a file under this directory.

```bash
sudo nano /etc/systemd/system/nginx.service.d/restart.conf
```

Add the following lines in the file, which will make Nginx automatically restart **5 seconds** after a failure is detected. The default value of `RetartSec` is **100ms**, which is too small. Nginx may complain that “start request repeated too quickly” if `RestartSec` is not big enough.

```bash
[Service]
Restart=always
RestartSec=5s
```

Save and close the file. Then reload systemd.

```bash
sudo systemctl daemon-reload
```

To check if this would work, kill Nginx with:

```bash
sudo pkill nginx
```

Then check Nginx status. You will find Nginx automatically restarted.

```bash
systemctl status nginx
```

## Congratulations!

Remember if:
“Software is like sex, it’s better when it’s free!”
- Linus Torvalds
