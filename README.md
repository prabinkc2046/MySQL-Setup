# MySQL Server Installation and Setup Guide for Ubuntu
This guide provides step-by-step instructions for installing and configuring a MySQL server on Ubuntu.

## Step 1: Installation
Update the system package list:
```bash
sudo apt update -y
```
Install MySQL server:
```bash
sudo apt install mysql-server
```
Check the status of MySQL server:
```bash
sudo systemctl status mysql
```
If MySQL server is not running, start and enable it:

```bash
sudo systemctl start mysql
sudo systemctl enable mysql
```
Verify if MySQL server is running:
```bash
sudo ss -tap
```
t: TCP socket
a: Both listening and non-listening sockets
p: Process using the socket

A running MySQL server will list the port (typically 3306) it's listening on.

## Step 2: Basic Configuration
To allow access from outside the host or network, edit the MySQL configuration file:

```bash
sudo grep -r "bind_address" /etc/mysql
```
By default, MySQL only listens on localhost. Comment out or replace 127.0.0.1 with the server's IP to make it accessible over the network.

## Step 3: Secure Your MySQL Server
Set up the root user:

Gain access to the MySQL shell:

```bash
sudo mysql
```
There is no root password set initially.

Choose one of the following methods to set up the root user:

Method 1:
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
```
After this, the root user can access the MySQL shell using system user credentials (sudo mysql) without a password prompt.

Method 2 (less secure):
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
Method 3 (more secure, but may not be compatible with all applications):
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
```
Run MySQL secure installation:
```bash
sudo mysql_secure_installation
```
Follow the prompts to secure your MySQL installation.

Step 4: Advanced Configuration
To enhance performance, install MySQLTuner:

```bash
sudo apt install mysqltuner
```
After at least 24 hours of server usage, run MySQLTuner:

```bash
mysqltuner
```
Apply the suggested adjustments to improve performance, and then restart the MySQL server:

```bash
sudo systemctl restart mysql
```
Step 5: Creating a Dedicated User
To create a user on the localhost:

```sql
CREATE USER 'app'@'localhost' IDENTIFIED WITH caching_sha2_mysql BY 'password';
```
Grant permissions to the user:

```sql
GRANT SELECT, CREATE, INSERT, REFERENCES ON *.* TO 'app'@'localhost';
```
To create a user for remote access:

```sql
CREATE USER 'remote_user'@'%' IDENTIFIED WITH caching_sha2_mysql BY 'password';
```
Grant the required privileges:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'remote_user'@'%';
```
Step 6: Logging in as a New User
```bash
mysql -u app -p
```
To view databases:

```sql
SHOW DATABASES;
```
To create a database:

```sql
CREATE DATABASE mydb;
```
To create a table:

```sql
USE mydb;

CREATE TABLE customerInfo (
    customerID INT PRIMARY KEY,
    name VARCHAR(255)
);
```
To see tables:

```sql
SHOW TABLES;
```
To see the structure of a table:

```sql
DESCRIBE customerInfo;
```
To insert values:

```sql
INSERT INTO customerInfo VALUES (1, 'prabin');
```
To view table contents:

```sql
SELECT * FROM customerInfo;
```
Step 7: Using References
To establish a relationship between tables and use references:

```sql
CREATE TABLE contactInfo (
    contactID INT PRIMARY KEY,
    email VARCHAR(255),
    customerID INT,
    FOREIGN KEY (customerID) REFERENCES customerInfo(customerID)
);
```
This creates a relationship between the contactInfo table and the customerInfo table based on the customerID field.
