Steps
1. Created VM in GCP
Set up a new VM instance in Google Cloud Platform.

Logged into GCP Console.

Navigated to Compute Engine > VM instances.

Clicked “Create Instance” and configured:

Name: task14-instance.

Machine type: e2-micro (free tier).

Boot disk: Ubuntu 20.04 LTS.

Allowed HTTP and HTTPS traffic in firewall settings.

Clicked “Create”.

Screenshot:

2. Installed Apache and MySQL
Updated package list and installed necessary software:

sh

Copy
sudo apt update
sudo apt install apache2 git mysql-server -y
Started and enabled Apache:

sh

Copy
sudo systemctl start apache2
sudo systemctl enable apache2
Screenshot:

3. Cloned Git Repository
Cloned repository from Task 12 into the web root:

sh

Copy
git clone https://github.com/stillness1998/task12
sudo cp -r task12/* /var/www/html/
sudo service apache2 restart
Screenshot:

4. Accessed Web Application
Opened browser and navigated to http://your-vm-ip to see the cloned page.

Screenshot:

5. Installed PHP and Configured MySQL
Installed PHP and necessary modules:

sh

Copy
sudo apt install php libapache2-mod-php php-mysqli
Secured MySQL and created database:

sh

Copy
sudo mysql_secure_installation
sudo mysql -u root -p
CREATE DATABASE yourdb;
CREATE USER 'youruser'@'localhost' IDENTIFIED BY 'yourpassword';
GRANT ALL PRIVILEGES ON yourdb.* TO 'youruser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
Screenshot:

6. Created Register and Login PHP Scripts
Created register.php in /var/www/html/:

php

Copy
<?php
$conn = new mysqli('localhost', 'youruser', 'yourpassword', 'yourdb');
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST['username'];
    $password = password_hash($_POST['password'], PASSWORD_BCRYPT);
    $sql = "INSERT INTO users (username, password) VALUES ('$username', '$password')";
    if ($conn->query($sql) === TRUE) {
        echo "New record created successfully";
    } else {
        echo "Error: " . $sql . "<br>" . $conn->error;
    }
}
?>
<form method="post">
    Username: <input type="text" name="username">
    Password: <input type="password" name="password">
    <input type="submit">
</form>
Created login.php in /var/www/html/:

php

Copy
<?php
session_start();
$conn = new mysqli('localhost', 'youruser', 'yourpassword', 'yourdb');
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST['username'];
    $password = $_POST['password'];
    $sql = "SELECT * FROM users WHERE username='$username'";
    $result = $conn->query($sql);
    if (!$result) {
        echo "Query Error: " . $conn->error;
    } elseif ($result->num_rows > 0) {
        $row = $result->fetch_assoc();
        if (password_verify($password, $row['password'])) {
            $_SESSION['username'] = $username;
            echo "Login successful";
        } else {
            echo "Invalid password";
        }
    } else {
        echo "No user found";
    }
}
?>
<form method="post">
    Username: <input type="text" name="username">
    Password: <input type="password" name="password">
    <input type="submit">
</form>
7. Tested Registration and Login
Registered a new user at http://your-vm-ip/register.php.

Logged in with the new user at http://your-vm-ip/login.php.

Screenshot:

Screenshot:
