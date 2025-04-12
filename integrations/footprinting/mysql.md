# MySQL

MySQL is a **Relational Database Management System (RDBMS)** that stores data in tables. Each table has **rows (records)** and **columns (fields)** that define the structure.

* MySQL works on a **Client-Server Model**, meaning there's a **MySQL Server** where the data is stored, and **Clients** (like applications or users) connect to this server to retrieve or modify data.
* MySQL listens on **port 3306** by default.
* MySQL is widely used in web applications like **WordPress, Linux**, **Apache, PHP** (LAMP) or **Nginx** (LEMP)

#### **MariaDB & MySQL:**

MariaDB is a fork of MySQL, created after Oracle acquired MySQL. It remains widely used due to its compatibility and additional enhancements.

***

#### **Footprinting the Service**

```bash
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*

3306/tcp open  mysql
| mysql-brute:
|   Accounts:
|     root:<empty> - Valid credentials
|_  root account has empty password
# show root account is without password
```

**to connect on database**

1st way → if account without password

```bash
mysql -u root -h 10.129.14.128
```

2nd way → have password

```bash
mysql -u root -p0xpass -h 10.129.14.128
```

some command we can use it after we connect on database

```bash
show databases;
use mysql;
show tables;
```

***

#### **Important MySQL Databases & Tables**

* **`mysql`** → Stores system information, users, passwords, and privileges.
* **`information_schema`** → Metadata about tables, columns, indexes, etc.
* **`performance_schema`** → Logs and performance metrics.
* **`sys`** → Useful reports for performance monitoring.

#### Securing a MySQL Server

1️⃣ **Change the Default MySQL Port (3306)**

Edit MySQL config file:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

2️⃣ **Disable Remote Root Login**

```sql
UPDATE mysql.user SET Host='localhost' WHERE User='root';
FLUSH PRIVILEGES;
```

3️⃣ **Set a Strong Password for Root**

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Str0ngP@ssw0rd!';
```

4️⃣ **Block External Access to MySQL Port**

```bash
sudo ufw deny 3306/tcp
```

5️⃣ **Run MySQL Secure Installation Script**

```bash
mysql_secure_installation
```
