This projects explains the processes involved in deploying a LAMP Stack on AWS with the aim of building a static and dynamic web app.


Linux: The operating system that provides the foundation for the stack.

Apache: The web server software that handles requests and serves web pages.

MySQL: The database management system that stores and manages data.

PHP: The server-side scripting language used to build dynamic web content.

**Step 0: Launch an EC2 instance**
1. On the AWS Console, Create an Ubuntu EC2 instance
- Name or Tag: Instance name. e.g Allens-Web
* Choose AMI: Select Ubuntu 24.04 LTS HVM (64-bit architecture).
+ Instance type: Select the t2.micro machine type (eligible for free tier). ![1](https://github.com/user-attachments/assets/7c67336b-b4f0-4683-97f6-f71cc4be2415)
+ Key Pair: Create a new key pair and download and save the .pem file. Use a 3rd party open source tool like Putty Key Generator to convert the .pem key to a .ppk key. ![putty key gen](https://github.com/user-attachments/assets/2ba87b0a-ce2e-4190-bee4-9c4cae9bdb31)

    
2. Launch the instance and ensure the all checks are passed indicating readiness of the instance. ![image](https://github.com/user-attachments/assets/5943c15e-8427-420c-b7f9-03126eabc8ad)
3. Check the security tab section of the instance to verify the presence of the security group allowing incoming SSH traffic on port 22 to the newly launched instance. ![security tab ssh sg](https://github.com/user-attachments/assets/a3090070-846f-4a4f-a1a5-c139292a0b98)

4. Browse through the network tab of the instance information to see private and public IP address, private and public IP address ![network tab](https://github.com/user-attachments/assets/49aa0e57-4d16-4afc-9f7c-f9c59d3a8186)
5. Establish an SSH connection to the instance using Putty using the public DNS name and the private key downloaded.
   ![putty](https://github.com/user-attachments/assets/9a5b3202-0b8d-4546-a456-1d97a3f0575e)
 



    
 
    

**Step 1. Install Apache2 on the instance**
   
   
   Apache2 is a widely-used open-source web server software that allows websites to be hosted on the internet. It serves web content (HTML, PHP, etc.) to users' browsers when they request a website. Apache 
   is highly configurable, reliable, and can run on various operating systems like Linux, Windows, and macOS.
- Install apache2 using the command:
       sudo apt update
       sudo apt install apache2 
       ![image](https://github.com/user-attachments/assets/d3e408f8-9e51-4948-8a40-f84f820c686d)


* Verify apache2 is up and running using the command:
       sudo systemctl status apache2
       ![image](https://github.com/user-attachments/assets/fa390e30-8ad7-4214-8f13-fe6897ef5670)

+ Edit the security group and create a new inbound rule for http on Port 80 to traffic from any IPV4 address 0.0.0.0/0
       ![image](https://github.com/user-attachments/assets/8599cf8a-6688-43a7-9b6c-60967967d8cd)

+ Using curl http://localhost:80 or  curl http://127.0.0.1:80 on the terminal or http://<public ip address> of the instance on a browser to confirm our apache2 web server is functioning appropriately
  
  ![image](https://github.com/user-attachments/assets/68cd74ac-be2a-4559-9813-64436875219a)
  
  ![image](https://github.com/user-attachments/assets/36f6260a-a252-496a-8e98-ca5f2d0a59b3)








**Step 2: Install MySQL**


  We need to install a database management system to be able to store and manage relational data for our website. 
- To install MySQL we use the command: 
sudo apt install mysql-server
 ![image](https://github.com/user-attachments/assets/c63afb6c-5834-4576-92c1-97b990484bc4)
* Login to the MYSQL console by typing: sudo mysql

  
    ![image](https://github.com/user-attachments/assets/10ce7726-72af-429d-b5b9-b37d68919a63)
 
  
   Following best practice, it is recommended to run a security script that comes pre-installed with MYSQL to remove some insecure default settings and lock down access to the database system. Before we do 
   this, we will set a password for the root user. For simplicity, we will use password123@. We will be using mysql_native_password as the default authentication method:
+ Set MYSQL root user password:
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password123@';

    ![image](https://github.com/user-attachments/assets/e9963d43-3420-41fa-88d2-5614b2c628e1)

- Exit the MySQL shell by typing: >exit
* Secure mysql installation by starting the interactive script: sudo mysql_secure_installation ![image](https://github.com/user-attachments/assets/8b499a43-5a8a-4a40-bef7-6a1df9e1b74d)
+ This will ask if you want to configure the VALIDATE PASSWORD PLUGIN. Choose Y for Yes and you would be be asked to select a level for password validation which are 0 = LOW, 1 = MEDIUM and 2 = STRONG. These levels determine the complexity of the characters for the MySQL root user password to be created. We already set a root user password (password123@) that matches the validation level 1, hence we choose 1.
- Next we will be asked to set password if the password we already set does not match the specification in the password validation policy.
Because the password we set already matches the specification, password creation is skipped as shown.

     ![pass-word-skipped-for-validation](https://github.com/user-attachments/assets/a87345dd-8c2e-405f-b6cc-1ddfbe9881fd)

* Next we are asked to remove Anonymous Users?: Press Y.

+ Disallow Root Login Remotely?: Press Y.

- Remove Test Database and Access to it?: Press Y.

* Reload Privilege Tables Now?: Press Y.

     ![answer-Yes-for-rest](https://github.com/user-attachments/assets/6b5ffb8a-f10f-4bcd-9854-b6b1b1a00402)

+ You can now test if you are able to login to the MYSQL shell by typing:sudo mysql -p
 If you are successful, you should be able to login. You can exit the MySQL monitor by entering exit

    ![test-mysql-login-again](https://github.com/user-attachments/assets/b231ffaf-f06e-4a7a-8331-037a683422d9)














**Step 3: Install PHP**



  PHP (Hypertext Preprocessor) is a popular open-source scripting language used primarily for web development, enabling the creation of dynamic and interactive websites. It runs on the server side and is 
  embedded in HTML to handle tasks like form data processing, database interactions, and content management.
  You have to install PHP and two php packages: 1. **php-Mysql** module, to help PHP communicate with MySql based databases and also **libapache2-mod-php** to enable apache2 handle PHP files. Core PHP 
  packages are installed automatically upon installlation of PHP.
  - Install all three using the command:
    

    sudo apt install php libapache2-mod-php php-mysql


    ![image](https://github.com/user-attachments/assets/edd46283-c257-43da-9c5d-8d92357f31bc)




  - Verify PHP running using the command

    php -v

    
    ![image](https://github.com/user-attachments/assets/c45d9a7e-6362-4b0b-9742-4e7c341e7c0b)




   - To test the PHP script, create a virtual host that will contain the website's files and folders as shown below


     ![image](https://github.com/user-attachments/assets/e2406cdb-9d22-4934-9f86-82de5f22ddb1)

   **Step 4: Create a Virtual Host**

   
Apache has a default server block from which it serves documents from the /var/www/html directory.
We would be creating a new directory /var/www/projectlamp/ which would host our project's files using the command:











- Create a new directory using the command:
    
       sudo mkdir /var/www/projectlamp/


       ![image](https://github.com/user-attachments/assets/0ada6baf-8199-430a-99f5-913331863dac)
  

* Enter the new directory using the command
    
       cd /var/www/projectlamp/

+ Next, we would assign ownership of the directory with the $USER environment variable
  which will reference your current system user using the command below:
       sudo chown -R $USER:$USER /var/www/projectlamp



  ![image](https://github.com/user-attachments/assets/998f4198-65d4-4856-9921-9844c8c34957)




+ We then create a configuration file for our project lamp and use a command line editor like NANO to edit it and pasting the command below to enable apache use the directory /var/www/projectlamp/ as its web 
 root directory:

        <VirtualHost *:80> 
        ServerName projectlamp 
        ServerAlias www.projectlamp 
        ServerAdmin webmaster@localhost 
        DocumentRoot /var/www/projectlamp 
        ErrorLog ${APACHE_LOG_DIR}/error.log 
        CustomLog ${APACHE_LOG_DIR}/access.log combined 
        </VirtualHost> 

 + We can comment out the server name and server alias since we want to test apache without a domain name:
   


        <VirtualHost *:80> 
        #ServerName projectlamp 
        #ServerAlias www.projectlamp 
        ServerAdmin webmaster@localhost 
        DocumentRoot /var/www/projectlamp 
        ErrorLog ${APACHE_LOG_DIR}/error.log 
        CustomLog ${APACHE_LOG_DIR}/access.log combined 
        </VirtualHost> 

 + We then enable the new virtual host using the command :


        sudo a2ensite projectlamp

   
 + We then disable apache default site since we are not using domain name:

   
        sudo a2dissite 000-default

   
 + To check the syntax of our configuration file:
   
        sudo apache2ctl configtest

   
 + Finally reload apache using:
   
        sudo systemctl reload apache2

 
 + Your new website is now active, but the web root /var/www/projectlamp is
   still empty. Create an index.html file in that location and copy the following code into it so that we can test that
   the virtual host works as expected:



                tee /var/www/projectlamp/index.html<<EOF
                <!DOCTYPE html>
                <html lang="en">
                  <head>
                    <meta charset="utf-8">
                    <title>Welcome to My site</title>
                  </head>
                  <body>
                    <h1>Success! My site home page!</h1>
                  </body>
                </html>
                EOF



 



   ![image](https://github.com/user-attachments/assets/bf0be627-1c1c-488d-9346-da40cf0744d9)





   




 + Open a browser and enter the public ip v4 address returns the simple website created in the index.html file




   ![image](https://github.com/user-attachments/assets/16045e13-9f79-4c54-bf1f-11697d977dbd)


 + The website can also be accessed through the public DNS name




   ![image](https://github.com/user-attachments/assets/ba12623f-1e53-4553-bcc9-a12f20755ec8)



 + You can leave this file in place as a temporary landing page for your
   application until you set up an index.php file to replace it.
   Once you do that, remember to remove or rename the index.html file from your document
   root, as it would take precedence over an index.php file by default.



**Step 5: Enable PHP on the website**

Due to the fact that the index.html file always takes precendence ove r the index.php file, we have to change the behaviour by editing our  **/etc/apache2/mods-enabled/dir.conf file**. We will use nano text editor to comment out the 2nd to 4th lines from the file so it does not carry out the change.

                sudo nano /etc/apache2/mods-enabled/dir.conf 



 - Copy and paste the code:               

                <IfModule mod_dir.c> 
                #Change this: 
                #DirectoryIndex index.html index.cgi index.pl index.php 
                index.xhtml index.htm 
                #To this: 
                DirectoryIndex index.php index.html index.cgi index.pl 
                index.xhtml index.htm 
                </IfModule> 




- Reload Apache:

  
          sudo systemctl reload apache2






 ![image](https://github.com/user-attachments/assets/7b7d371e-d862-4546-819c-b07721dfa2e3)

                
PS: the index.xhtm caused the apache server to crash, I had to remove it, reloaded apache2 again and it worked.



![image](https://github.com/user-attachments/assets/1b046c06-1fea-4ad7-9630-0beda0821142)





                   







 + We then create the index.php file to confirm that apache is able to handle and process request for php files. To do this, edit the index.php file

                nano /var/www/projectlamp/index.php
   
 
 + And copy the following code into it:

   
                <?php 
