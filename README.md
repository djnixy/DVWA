# DAMN VULNERABLE WEB APPLICATION

Damn Vulnerable Web Application (DVWA) is a PHP/MySQL web application that is damn vulnerable. Its main goal is to be an aid for security professionals to test their skills and tools in a legal environment, help web developers better understand the processes of securing web applications and to aid both students & teachers to learn about web application security in a controlled class room environment.

The aim of DVWA is to **practice some of the most common web vulnerabilities**, with **various levels of difficulty**, with a simple straightforward interface.
Please note, there are **both documented and undocumented vulnerabilities** with this software. This is intentional. You are encouraged to try and discover as many issues as possible.
- - -

## WARNING!

Damn Vulnerable Web Application is damn vulnerable! **Do not upload it to your hosting provider's public html folder or any Internet facing servers**, as they will be compromised. It is recommended using a virtual machine (such as [VirtualBox](https://www.virtualbox.org/) or [VMware](https://www.vmware.com/)), which is set to NAT networking mode. Inside a guest machine, you can download and install [XAMPP](https://www.apachefriends.org/en/xampp.html) for the web server and database.

### Disclaimer

We do not take responsibility for the way in which any one uses this application (DVWA). We have made the purposes of the application clear and it should not be used maliciously. We have given warnings and taken measures to prevent users from installing DVWA on to live web servers. If your web server is compromised via an installation of DVWA, it is not our responsibility, it is the responsibility of the person/s who uploaded and installed it.

- - -

## License

This file is part of Damn Vulnerable Web Application (DVWA).

Damn Vulnerable Web Application (DVWA) is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

Damn Vulnerable Web Application (DVWA) is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with Damn Vulnerable Web Application (DVWA).  If not, see <https://www.gnu.org/licenses/>.

- - -

### Quick install ###
```
apt-get update && apt-get -y install apache2 php php-mysqli php-gd libapache2-mod-php ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

apt-get update && apt-get -y install docker-ce docker-ce-cli containerd.io
```

### Run mariaDB docker container:

```
docker run -itd --rm --name dvwadb -e MARIADB_DATABASE=dvwa -e MARIADB_USER=dvwa -e MARIADB_PASSWORD=p@ssw0rd -e MARIADB_ROOT_PASSWORD=p@ssw0rd -v "dvwadb-data:/var/lib/mysql" -p 3306:3306 mariadb:latest
```

### Deploy app to apache web server:

```
git clone https://github.com/djnixy/DVWA
rm -r /var/www/html/index.html
rsync -avP DVWA/ /var/www/html/
```


- - -

### Other Configuration

Depending on your Operating System, as well as version of PHP, you may wish to alter the default configuration. The location of the files will be different on a per-machine basis.

**Folder Permissions**:

* `./hackable/uploads/` - Needs to be writeable by the web service (for File Upload).
* `./external/phpids/0.6/lib/IDS/tmp/phpids_log.txt` - Needs to be writable by the web service (if you wish to use PHPIDS).

**PHP configuration**:

* `allow_url_include = on` - Allows for Remote File Inclusions (RFI)   [[allow_url_include](https://secure.php.net/manual/en/filesystem.configuration.php#ini.allow-url-include)]
* `allow_url_fopen = on` -  Allows for Remote File Inclusions (RFI)    [[allow_url_fopen](https://secure.php.net/manual/en/filesystem.configuration.php#ini.allow-url-fopen)]
* `safe_mode = off` - (If PHP <= v5.4) Allows for SQL Injection (SQLi) [[safe_mode](https://secure.php.net/manual/en/features.safe-mode.php)]
* `magic_quotes_gpc = off` - (If PHP <= v5.4) Allows for SQL Injection (SQLi) [[magic_quotes_gpc](https://secure.php.net/manual/en/security.magicquotes.php)]
* `display_errors = off` - (Optional) Hides PHP warning messages to make it less verbose [[display_errors](https://secure.php.net/manual/en/errorfunc.configuration.php#ini.display-errors)]

**File: `config/config.inc.php`**:

* `$_DVWA[ 'recaptcha_public_key' ]` & `$_DVWA[ 'recaptcha_private_key' ]` - These values need to be generated from: https://www.google.com/recaptcha/admin/create

### Default Credentials

**Default username = `admin`**

**Default password = `password`**

_...can easily be brute forced ;)_

Login URL: http://127.0.0.1/login.php

_Note: This will be different if you installed DVWA into a different directory._

- - -

## Docker Container

- [dockerhub page](https://hub.docker.com/r/vulnerables/web-dvwa/)
`docker run --rm -it -p 80:80 vulnerables/web-dvwa`

Please ensure you are using aufs due to previous MySQL issues. Run `docker info` to check your storage driver. If it isn't aufs, please change it as such. There are guides for each operating system on how to do that, but they're quite different so we won't cover that here.

- - -

## Troubleshooting

These assume you are on a Debian based distro, such as Debian, Ubuntu and Kali. For other distros, follow along, but update the command where appropriate.

### I browsed to the site and got a 404

If you are having this problem you need to understand file locations. By default, the Apache document root (the place it starts looking for web content) is `/var/www/html`. If you put the file `hello.txt` in this directory, to access it you would browse to `http://localhost/hello.txt`.

If you created a directory and put the file in there - `/var/www/html/mydir/hello.txt` - you would then need to browse to `http://localhost/mydir/hello.txt`.

Linux is by default case sensitive and so in the example above, if you tried to browse to any of these, you would get a `404 Not Found`:

- `http://localhost/MyDir/hello.txt`
- `http://localhost/mydir/Hello.txt`
- `http://localhost/MYDIR/hello.txt`

How does this affect DVWA? Most people use git to checkout DVWA into `/var/www/html`, this gives them the directory `/var/www/html/DVWA/` with all the DVWA files inside it. They then browse to `http://localhost/` and get either a `404` or the default Apache welcome page. As the files are in DVWA, you must browse to `http://localhost/DVWA`.

The other common mistake is to browse to `http://localhost/dvwa` which will give a `404` because `dvwa` is not `DVWA` as far as Linux directory matching is concerned.

So after setup, if you try to visit the site and get a `404`, think about where you installed the files to, where they are relative to the document root, and what the case of the directory you used is.

### "Access denied" running setup

If you see the following when running the setup script it means the username or password in the config file do not match those configured on the database:

```
Database Error #1045: Access denied for user 'notdvwa'@'localhost' (using password: YES).
```

The error is telling you that you are using the username `notdvwa`.

The following error says you have pointed the config file at the wrong database.

```
SQL: Access denied for user 'dvwa'@'localhost' to database 'notdvwa'
```

It is saying that you are using the user `dvwa` and trying to connect to the database `notdvwa`.

The first thing to do is to double check what you think you put in the config file is what is actually there.

If it matches what you expect, the next thing to do is to check you can log in as the user on the command line. Assuming you have a database user of `dvwa` and a password of `p@ssw0rd`, run the following command:

```
mysql -u dvwa -pp@ssw0rd -D dvwa
```

*Note: There is no space after the -p*

If you see the following, the password is correct:

```
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 14
Server version: 10.3.22-MariaDB-0ubuntu0.19.10.1 Ubuntu 19.10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [dvwa]>
```

As you can connect on the command line, it is likely something wrong in the config file, double check that and then raise an issue if you still can't get things working.

If you see the following, the username or password you are using is wrong. Repeat the [Database Setup](#database-setup) steps and make sure you use the same username and password throughout the process.

```
ERROR 1045 (28000): Access denied for user 'dvwa'@'localhost' (using password: YES)
```

If you get the following, the user credentials are correct but the user does not have access to the database. Again, repeat the setup steps and check the database name you are using.

```
ERROR 1044 (42000): Access denied for user 'dvwa'@'localhost' to database 'dvwa'
```

The final error you could get is this:

```
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

This is not an authentication issue but tells you that the database server is not running. Start it with the following

```sh
sudo service mysql start
```

### Unknown authentication method

With the most recent versions of MySQL, PHP can no longer talk to the database in its default configuration. If you try to run the setup script and get the following message it means you have configuration.

```
Database Error #2054: The server requested authentication method unknown to the client.
```

You have two options, the easiest is to uninstall MySQL and install MariaDB. The following is the official guide from the MariaDB project:

<https://mariadb.com/resources/blog/how-to-migrate-from-mysql-to-mariadb-on-linux-in-five-steps/>

Alternatively, follow these steps:

1. As root, edit the following file: `/etc/mysql/mysql.conf.d/mysqld.cnf`
1. Under the line `[mysqld]`, add the following:
  `default-authentication-plugin=mysql_native_password`
1. Restart the database: `sudo service mysql restart`
1. Check the authentication method for your database user:

    ```sql
    mysql> select Host,User, plugin from mysql.user where mysql.user.User = 'dvwa';
    +-----------+------------------+-----------------------+
    | Host      | User             | plugin                |
    +-----------+------------------+-----------------------+
    | localhost | dvwa             | caching_sha2_password |
    +-----------+------------------+-----------------------+
    1 rows in set (0.00 sec)
    ```

1. You'll likely see `caching_sha2_password`. If you do, run the following command:

    ```sql
    mysql> ALTER USER dvwa@localhost IDENTIFIED WITH mysql_native_password BY 'p@ssw0rd';
    ```

1. Re-running the check, you should now see `mysql_native_password`.

    ```sql
    mysql> select Host,User, plugin from mysql.user where mysql.user.User = 'dvwa';
    +-----------+------+-----------------------+
    | Host      | User | plugin                |
    +-----------+------+-----------------------+
    | localhost | dvwa | mysql_native_password |
    +-----------+------+-----------------------+
    1 row in set (0.00 sec)
    ```

After all that, the setup process should now work as normal.

If you want more information see the following page: <https://www.php.net/manual/en/mysqli.requirements.php>.

### Database Error #2002: No such file or directory.

The database server is not running. In a Debian based distro this can be done with:

```sh
sudo service mysql start
```

### Errors "MySQL server has gone away" and "Packets out of order"

There are a few reasons you could be getting these errors, but the most likely is the version of database server you are running is not compatible with the version of PHP.

This is most commonly found when you are running the latest version of MySQL as PHP and it do not get on well. Best advice, ditch MySQL and install MariaDB as this is not something we can support.

For more information, see:

<https://www.ryadel.com/en/fix-mysql-server-gone-away-packets-order-similar-mysql-related-errors/>

### SQL Injection won't work on PHP v5.2.6.

PHP 5.x reached end of life in January 2019 so we would recommend running DVWA with a current 7.x version, if you must use 5.x...

If you are using PHP v5.2.6 or above, you will need to do the following in order for SQL injection and other vulnerabilities to work.

In `.htaccess`:

Replace:

```php
<IfModule mod_php5.c>
    php_flag magic_quotes_gpc off
    #php_flag allow_url_fopen on
    #php_flag allow_url_include on
</IfModule>
```

With:

```php
<IfModule mod_php5.c>
    magic_quotes_gpc = Off
    allow_url_fopen = On
    allow_url_include = On
</IfModule>
```

### Command Injection won't work

Apache may not have high enough privileges to run commands on the web server. If you are running DVWA under Linux make sure you are logged in as root. Under Windows log in as Administrator.

### Why can't the database connect on CentOS?

You may be running into problems with SELinux.  Either disable SELinux or run this command to allow the web server to talk to the database:

```
setsebool -P httpd_can_network_connect_db 1
```

### Anything else

For the latest troubleshooting information please read both open and closed tickets in the git repo:

<https://github.com/digininja/DVWA/issues>

Before submitting a ticket, please make sure you are running the latest version of the code from the repo. This is not the latest release, this is the latest code from the master branch.

If raising a ticket, please submit at least the following information:

- Operating System
- The last 5 lines from the web server error log directly after whatever error you are reporting occurs
- If it is a database authentication problem, go through the steps above and screenshot each step. Submit these along with a screenshot of the section of the config file showing the database user and password.
- A full description of what is going wrong, what you expect to happen, and what you have tried to do to fix it. "login broken" is no enough for us to understand your problem and to help fix it.

- - -

## SQLite3 SQL Injection

_Support for this is limited, before raising issues, please ensure you are prepared to work on debugging, do not simply claim "it does not work"._

By default, SQLi and Blind SQLi are done against the MariaDB/MySQL server used by the site but it is possible to switch to do the SQLi testing against SQLite3 instead.

I am not going to cover how to get SQLite3 working with PHP, but it should be a simple case of installing the `php-sqlite3` package and making sure it is enabled.

To make the switch, simply edit the config file and add or edit these lines:

```
$_DVWA["SQLI_DB"] = "sqlite";
$_DVWA["SQLITE_DB"] = "sqli.db";
```

By default it uses the file `database/sqli.db`, if you mess it up, simply copy `database/sqli.db.dist` over the top.

The challenges are exactly the same as for MySQL, they just run against SQLite3 instead.

- - -

## Links

Homepage: <https://dvwa.co.uk/>

Project Home: <https://github.com/digininja/DVWA>

*Created by the DVWA team*
