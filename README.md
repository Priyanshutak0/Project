# Project
## Hosting a HTML Website With Data Replication in MASTER AND SLAVE Instances
## Project is divided into Three Parts/Steps
## PART-1

### ROADMAP

create an instance, make two html pages. On page 1 we have to make a registration page which includes -
user, password, email, phone number.
it also includes a submit button and already a user log-in button.
after clicking on submit button we will proceed to login page which is page 2.
On page 2 we have to make login page which includes - user, password and login button.
All the data we are typing on these pages will go in the EC2 database.
if the data matches with the database it will take us to the welcome page.
if the data doesn't match it will show us a popup invalid user or password.

### SOLUTION-------------------------------
### First of all we have to create an instance using ubuntu AMI.
<img src="https://">

### Launch the instance and connect it through ssh on terminal.
```
ssh -i "your key" ubuntu@(your publicip).compute-1.amazonaws.com
```
<img src="https://">

### After connecting the instance, type -
```
sudo apt update
```
<img src="https://">

### In cmd in terminal.
### Now we have to install some required services to move further in our process.
### For web browsing we have to install  ```apache2```. to install apache2 services we have to type the command for installation which is -
```
sudo apt install apache2 -y
```
<img src="https://">

### After installation of apache2 services. start the servcies type -
```
sudo systemctl start apache2
```
### Enable the apache2 services, type -
```
sudo systemctl enable apache2

```
### Now Install the mysql server,
### Type - 
```
sudo apt install mysql-server -y
```
<img src="https://">

## NOTE- change the bind address from ```127.0.0.1``` to ```0.0.0.0``` in ```mysqld.conf``` file.
### Type -
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
<img src="https://">

### Then start mysql services-
```
sudo systemctl start mysql

```
### Install the PHP service for backend service-
```
sudo apt install php libapache2-mod-php php-mysql -y
```
<img src="https://">

### Configure Apache:
### Make sure Apache is running: 
```
sudo service apache2 start
```
### Set the correct permissions for your web files:
```
sudo chown -R www-data:www-data /var/www/html
```
```
sudo chmod -R 755 /var/www/html

```

### Create a MySQL User and Database

### Log in to MySQL: 

```
sudo mysql -u root 
```
<img src= "https://">



### Create a new user and grant privileges to the database:

```
CREATE USER 'your_username'@'localhost' IDENTIFIED BY 'your_password';
```
<img src="https://">

### Then type -

```
FLUSH PRIVILEGES;

```

### Create a new database: 

```
create database your_database_name;
```


### Type -
```
use your_database_name;
```
### Type to create your table.
```
create table yourtablename (userame varchar(50), password varchar(50), email varchar(50), phone int(50)); 
```
### To see your database is created type -
```
select * from your_table_name;
````

### To grant all the access to your database

```
GRANT ALL PRIVILEGES ON your_database_name.* TO 'your_username'@'localhost';
```

<img src="https://">

### Then type -

```
FLUSH PRIVILEGES;

```


<img src="https://">

### Create your project files here in  ```/var/www/html/```  for your project.

```
cd /var/www/html/

```

<img src="https://">


### Here the main thing starts-

### Now we have to create files in ```/var/www/html/```. so firstly , we have to create ```signup.hmtl``` page. type-

```
sudo vim signup.html

```

### Copy paste the script which is given below -

```
<!DOCTYPE html>
<html>
<head>
<title>signup Page</title>
<img src="https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExamh1OXdvOGc2MGhmMGFhYTV3Ym5saW0zcnA1azFjcjg3ajlrNHBvaiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/Ltz1ZA728qKw4mEY94/giphy.gif">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css">

<script>
function validateForm() {
    var username = document.forms["registrationForm"]["username"].value;
    var password = document.forms["registrationForm"]["password"].value;
    var email = document.forms["registrationForm"]["email"].value;
    var phone = document.forms["registrationForm"]["phone"].value;

    if (username == "" || password == "" || email == "" || phone == "") {
        alert("Please fill in all the fields.");
        return false;
    }
}
</script>
</head>
<body>
<h1>Registration Page</h1>
<form name="registrationForm" action="database.php" method="post" onsubmit="return validateForm()">
<input type="text" name="username" placeholder="Username">
<input type="password" name="password" placeholder="Password">
<input type="email" name="email" placeholder="Email">
<input type="tel" name="phone" placeholder="Phone Number">
<input type="submit" value="Submit">
<input type="button" value="Login" onclick="window.location.href='login.html'">
</form>
</body>
</html>
```
### Your page will look like this on internet browser ---


<img src="https://">




### Press escape ```esc``` button and type ```:wq``` and come out from  vim editor.
### Now create the ```login.html``` page by typing -
```
sudo vim login.html
```
### Copy paste the script in ```login.html``` which is given below-
```
<!DOCTYPE html>
<html>
<head>
<title>Login Page</title>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css">

</head>
<body>
<h1>Login Page</h1>
<img src="https://codemyui.com/wp-content/uploads/2016/01/owl-login-form-replica-from-readme-io_.gif">

<form action="login.php" method="post">
  <input type="text" name="username" placeholder="Username" required>
  <input type="password" name="password" placeholder="Password" required pattern=".{6,}" title="Password must be at least 6 characters long.">
  <input type="submit" value="Login">
</form>
</body>
</html>
```

### Your page will look like this on internet browser ---


<img src="https://">


### Signup and login pages are done. now we have to create a database page ```database.php``` for the user data which will help us in login of user..

### To create a ``` database.php``` page type -
```
sudo vim database.php
```

### Copy paste the script given below in ```database.php``` page.


```
<?php
$servername = "localhost";
$username = "Priyanshu"; // Replace with your MySQL username
$password = "123"; // Replace with your MySQL password
$dbname = "mydb";

// Create a connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Get form data from the previous page (page1.html)
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = $_POST["password"];
    $email = $_POST["email"];
    $phone = $_POST["phone"];

    // Insert data into the 'users' table change mydb here with your table name
    $sql = "INSERT INTO mydb (username, password, email, phone) VALUES ('$username', '$password', '$email', '$phone')";

    if ($conn->query($sql) === TRUE) {
        echo "Registration Completed";
    } else {
        echo "Error: " . $sql . "<br>" . $conn->error;
    }
}

$conn->close();

```

## NOTE- I HAVE COMMENTED OUT  ```//```  WITH SOME MESSAGE WHERE WE HAVE TO CHANGE THE SCRIPT

### After making the ```database.php```page now we have to do the backend work.

### Now we have to make the ```.php``` page for the ```signup.html``` page.we will name that page ```index.php```

### To create the ```index.php``` page type -

```
sudo vim index.php
```

### Copy paste the script which is given below in the ```index.php``` page.

```
<?php

session_start();

if (isset($_SESSION["user_id"])) {

    $mysqli = require __DIR__ . "/database.php";

    $sql = "SELECT * FROM user
            WHERE id = {$_SESSION["user_id"]}";

    $result = $mysqli->query($sql);

    $user = $result->fetch_assoc();
}

?>
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css">
</head>
<body>

    <h1>Home</h1>

    <?php if (isset($user)): ?>

        <p>Hello <?= htmlspecialchars($user["name"]) ?></p>

        <p><a href="logout.php">Log out</a></p>

    <?php else: ?>

        <p><a href="login.html">Log in</a> or <a href="signup.html">sign up</a></p>

    <?php endif; ?>

</body>
</html>
```

### Now we have to make the ```.php``` page for the ```login.html``` page.we will name that page ```login.php```.
### To create the ```login.php``` page type -

```
sudo vim login.php
```

### Copy paste the script in ```login.php``` page-
```
<?php
// Replace 'your_db_host', 'your_db_username', 'your_db_password', and 'your_db_name' with your database credentials
$servername = 'localhost';
$username = 'Priyanshu';
$password = '123';
$dbname = 'mydb';

// Handling login form submission
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    // Retrieve user input from the login form
    $inputUsername = $_POST['username'];
    $inputPassword = $_POST['password'];

    // Connect to the database
    $conn = new mysqli($servername, $username, $password, $dbname);

    // Check connection
    if ($conn->connect_error) {
        die('Connection failed:'. $conn->connect_error);
    }

    // Fetch user data from the database based on the input username change the table name mydb to ur table name
    $query = "SELECT * FROM mydb WHERE username = '$inputUsername'";
    $result = $conn->query($query);

    if ($result->num_rows === 1) {
        // User found, verify the password
        $row = $result->fetch_assoc();
        $storedPassword = $row['password'];

        if ($inputPassword === $storedPassword) {
            // Passwords match, login successful
            $conn->close();
            header('Location:welcome.html');
            exit();
        }
    }

    // Login failed
    $conn->close();
    echo 'Invalid username or password. Please try again.';
}
?>
```
### Now we have to create the table in the  ```my sql```  for our users- name,password,email and phone number.
### Log in to MySQL: 

```
sudo mysql -u your_useer_name -p 
```
### Type -
```
show databases;
```
### Type -
```
use your_database_name;
```
### Type to create your table.
```
create table yourtablename (userame varchar(50), password varchar(50), email varchar(50), phone int(50)); 
```
### To see your database is created type -
```
select * from your_table_name;
````
### Now you have created table for data entry and everything is done...

## PART-2
### OVER-VIEW DIAGRAM

<img src="https://">

### ROUTEMAP

create two instances.we have to create a database in instance one and whenever we type some information in database..it shows us in our second instance too... (master and slave data replication with the help of mysql)
### SOLUTION------------------------------------------
### To set up MySQL database replication between two AWS Ubuntu instances, where Instance 1 acts as the master and Instance 2 acts as the slave, follow these steps:
### 1- Install MySQL on both instances:
### SSH into each AWS Ubuntu instance and install MySQL server:
```
sudo apt update
```
```
sudo apt install mysql-server
```
### 1- Configure MySQL on the master (Instance 1):
### Open the MySQL configuration file on the master server:
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
### Locate the [mysqld] section and add the following lines to enable binary logging:
```
server-id=1
log_bin = /var/log/mysql/mysql-bin.log
```
### I am showing an example how my ```mysqld.cnf``` file looks like-
```
#
# The MySQL database server configuration file.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
# port          = 3306
# datadir       = /var/lib/mysql


# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmpdir
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
mysqlx-bind-address     = 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size         = 16M
# max_allowed_packet    = 64M
# thread_stack          = 256K

# thread_cache_size       = -1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP

# max_connections        = 151

# table_open_cache       = 4000

#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
#
# Log all queries
# Be aware that this log type is a performance killer.
# general_log_file        = /var/log/mysql/query.log
# general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
# slow_query_log                = 1
# slow_query_log_file   = /var/log/mysql/mysql-slow.log
# long_query_time = 2
# log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
 server-id              = 1
 log_bin                        = /var/log/mysql/mysql-bin.log
# binlog_expire_logs_seconds    = 2592000
max_binlog_size   = 100M
# binlog_do_db          = include_database_name
# binlog_ignore_db      = include_database_name
```
### Save the file and restart MySQL:
```
sudo systemctl restart mysql
```
### 1- Create a replication user on the master:
### Log in to MySQL as the root user:
```
sudo mysql -u root
```
### Once logged in, run the following SQL commands to create a replication user and grant appropriate privileges:
```
CREATE USER 'replication_user_you_created'@'%' IDENTIFIED BY 'your_password_here';
```
```
GRANT ALL PRIVILEGES ON *.* TO 'your_user'@'%';
```
```
FLUSH PRIVILEGES;
```
### 1- Get the master's binary log position:
### While still logged in as root on the master, note the current binary log position:
```
SHOW MASTER STATUS;

```
## NOTE - You'll need the values of File and Position for configuring the slave later.
### Lets move on the instance-2]
### 1- Configure MySQL on the slave (Instance 2):
### Open the MySQL configuration file on the slave server:
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
### Locate the [mysqld] section and add the following lines to enable replication:
```
server-id=2
```
### I am showing an example how my ```mysqld.cnf``` file looks like-
```

# The MySQL database server configuration file.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
# port          = 3306
# datadir       = /var/lib/mysql


# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmpdir
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
# mysqlx-bind-address   = 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size         = 16M
# max_allowed_packet    = 64M
# thread_stack          = 256K

# thread_cache_size       = -1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP

# max_connections        = 151

# table_open_cache       = 4000

#
# * Logging and Replication
#
# Both location gets rotated by cronjob
#
# Log all queries
# Be aware that this log type is a performance killer.
# general_log_file        = /var/log/mysql/query.log
# general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
# slow_query_log                = 1
# slow_query_log_file   = /var/log/mysql/mysql-slow.log
# long_query_time = 2
# log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
# server-id             = 1
server-id               = 2
# log_bin                       = /var/log/mysql/mysql-bin.log
# binlog_expire_logs_seconds    = 2592000
max_binlog_size   = 100M
# binlog_do_db          = include_database_name
# binlog_ignore_db      = include_database_name
```
### Save the file and restart MySQL:
```
sudo systemctl restart mysql
```
# NOTE - we have to make sure that our inbound and outbound rules have access to allow traffic from port 3306 for my sql and add new rule in inbound rules and allow,
# ```ALL/ICMP``` traffic and ```anywhere``` in CIDR and save the rules.
### 1-Start the replication process on the slave:
### Log in to MySQL as the root user on the slave:
```
sudo mysql -u root
```
### Run the following SQL command to configure the replication settings on the slave:
```
CHANGE MASTER TO MASTER_HOST='master_ip_address', MASTER_USER='replication_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='binlog_file_from_master', MASTER_LOG_POS=binlog_position_from_master;
```
## ```master_ip_address:``` The private or public IP address of Instance 1 (master).
## ```replication_user:``` The replication user you created earlier.
## ```password:``` The password for the replication user.
## ```binlog_file_from_master``` and ```binlog_position_from_master:``` The values you obtained from the ```SHOW MASTER STATUS;``` command on the master.

### 1- Start replication on the slave:
### After running the CHANGE MASTER TO command, start the replication process on the slave:
```
START SLAVE;
```
### 1- Check slave status:
### Verify that the replication process is running without errors on the slave:
```
SHOW SLAVE STATUS\G
```
### Check the ```Slave_IO_Running``` and ```Slave_SQL_Running``` fields to ensure both are Yes.

### Now we have to go to the master instance (instance1) and login to mysql to create a database and insert a table.
### Log in to MySQL: 

```
sudo mysql -u your_user -p 
```
### Create a new database: 

```
create database your_database_name;
```
```
show databases;
```
```
use your_database_name;
```
### Create anytype of table you want to create.i am taking an example of creating an employee
### Table which is -
```
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    hire_date DATE,
    department VARCHAR(100)
);
```
### To see your tables type-
```
show tables;
```
### To insert data in your table type-
```
insert into table_name ('name','city','standard') values('kitty','england','A');
```
### To see what data have you typed in your table type -
```
select * from table_name;
```
### Now check the entry in slave instance.

### Everything is done and With these steps, you should have set up MySQL database replication between the two AWS Ubuntu instances. Any changes made to the master database on Instance 1 will now be replicated to the slave database on Instance 2.

## PART-3

### OVER-VIEW DIAGRAM

<img src="https://">

### ROADMAP

Now we have to use master instance as registration database and slave instance as login database by which, whenever user type information on registration page it will be saved in master instance and replicate the data in slave instance and whenever user try to login then it will use slave instance to cross verify the user data and give access.
### SOLUTION----------------------------------------------------------------------------
### To do this task firstly, we have to change the ```servername``` from ```localhost``` to `public-ip` of master instance in database.php which we created in /html/.
### Go to your website instance,connect your instance. After the connection type-
```
cd /var/www/html
```
### Edit the file-
```
sudo vim database.php
```
### Change the `servername` to `public-ip` of your master instance. press `esc` and type`:wq` exit from the file.
### Move on to the slave instance, Now we have to create a new mysql user for the cross verification of data in slave instance for login.php. which will verfiy the user and let user access the welcome page.
### To create user in `mysql` type -
```
sudo mysql
```
```
CREATE USER 'replication_user_you_created'@'%' IDENTIFIED BY 'your_password_here';
```
```
GRANT ALL PRIVILEGES ON *.* TO 'your_user'@'%';
```
```
FLUSH PRIVILEGES;
```
### Now GO BACK IN WEBSITE INSTANCE and open the `login.php` file and change the `servername` to `public_ip` of slave instance.change the `username` to the user you just created im slave instance.
### Change the `password` with your `user-password`  and change the `dbname` with the database you created in MASTER INSTANCE IN MYSQL. FOR EX- if i created `moon` database with the help of mysql user which i created in master instance then i have to copy paste the name of `moon` database in the place of `dbname`.
### type-
```
cd /var/www/html
```
```
sudo vim login.php
```
### Change everthing which i mentioned above and press `esc` and type`:wq` exit from the file.
### Everything is done and we have completed our project.
