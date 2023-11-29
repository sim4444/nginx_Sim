# Assignment 3 - Part 1

To begin with, we assume that we are currently connected to Debian server as a root user. We will go through all the important steps required to:
- Create a new regular user
- Make the user eligible to perform administrative tasks
- Assign bash as login shell for user
- Make the user eligible to access server via SSH
- Prevent the root user from connecting to the server via SSH
- Install nginx
- Configure nginx to serve a sample website.
  
Lets start with the most important thing that why do we need to create a regular user instead of continuing to use root user to connect to the Debian server via SSH.

So, Creating a regular user is like having a security guard for your computer. This user has limited powers and needs permission (sudo) to perform important tasks to reduce the risk of accidents or harm. It's a good practice for security and accountability. Disabling direct access for the superuser (root) adds an extra layer of security to ensure only trusted individuals have access to critical parts of your system like having keys to important areas.

## Steps to create a regular user, assign bash as login shell, make the user eligible to perform administrative tasks, and make the user eligible to access server via SSH

Before we dive in, it is crucial to note that regular user needs "sudo" so that regular user can access administrative privilege but the root user does not need "sudo" because root user already have elevated privileges. As currently we are assuming that we are logged in as root user so we dont need to use sudo in below commands:

- Step 1: Create a new user named sim in the home directory (by using -m) and assigns bash as login shell for user sim (by using -s) using command:

``` bash
  useradd -ms /bin/bash sim
```

- Step 2: Setup password for new user sim:
  Setting a password for the user "sim" is important for security to prevent unauthorized access and make sure that only authorized individuals can log in. This standard practice adds a important layer of protection to the user account. To do this, use command:

``` bash
  passwd sim
```

- Step 3: Add user sim to SUDO group so that user can use sudo:
  Adding a user to the sudo group allows that user to run commands with superuser privileges to grant them administrative access to the system. This is typically done to give trusted users the ability to perform administrative tasks without needing to log in as the root user. Here's how you can add ur created user sim to the sudo group:

``` bash
  usermod -aG sudo sim
```

- Step 4: Copy the .ssh directory from the root user's home directory to the new user's home directory (including any files in the directory) by using command:
  
``` bash
  cp -r /root/.ssh  /home/sim
```

- Step 5: Change ownership of the directory, and files in the directory so that the copy in your new users directory is owned by the new user and the new users primary group:

``` bash
  sudo chown -R sim:sim /home/sim/.ssh
```
- Step 6: Now, go ahead and run "exit" to exit as root user. Now, test that your new regular user sim can access the server via SSH. You can do this by replacing 'root' with your new user-name 'sim' in the ssh command you use to connect to your server:

``` bash
    ssh -i .ssh/do-key sim@ip_address
```

## Steps to prevent the root user from connecting to the server via SSH

As you are currently connected to server as regular user 'sim', now edit ssh configuration so that the root user can no longer connect to the server via ssh. The ssh configuration files are in etc/ssh directory. The primary configuration file we need is the sshd_config in directory etc/ssh. Run below commands:

- Step 1: Go to /etc/ssh:
``` bash
  cd /etc/ssh
```

- Step 2: Create a backup of original or old sshd_config file:
``` bash
  sudo cp sshd_config sshd_config.old
```

- Step 3: Open sshd_config file to edit:
``` bash
  sudo vim sshd_config
```

- Step 4: Now, you have to look for the line PermitRootLogin yes and change yes to no. To find this line in vim a little faster, press forward slash (/) and type first words of what we are looking for. In this case type /PermitRootL and save the file (:wq). Now restart the ssh service:
``` bash
  sudo systemctl restart ssh.service
```

- Step 5: Now, you have prevented the root user from connecting to the server via SSH. Test it by exiting and trying to connect to your server as root and you should get a permission denied error.

## Steps to install nginx and configure nginx to serve a sample website

Nginx is a widely-used web server and reverse proxy known for its speed and adaptability. It is amazing at hosting static and dynamic content to distribute incoming requests among servers and enhance security. Installing Nginx is important to host websites and ensure secure web operations. To install nginx, run below commands:

- Step 1: Firstly, connect to Debian server as regular user Sim via SSH and update the package list:
``` bash
  sudo apt update
```

- Step 2: Install nginx using install command:
``` bash
  sudo apt install nginx
```

- Step 3: Start nginx service:
``` bash
  sudo systemctl start nginx
```

- Step 4: Check status of nginx service and enable it if it is disabled:
``` bash
  systemctl status nginx
```

- Step 5: Enable the nginx service so that it can start on booting is done:
``` bash
  sudo systemctl enable nginx
```

At this point before configuring nginx to serve a sample website, it is important to note that nginx configuration files are located in directory /etc/nginx/ and important configuration file is nginx.conf. The server block configurations are in directory /sites-available which is inside directory /etc/nginx/. For today's assignment in terms of configuration, we will not make any modification to file nginx.conf.

- Step 6: There are many directories that have the documents which are served by nginx and the common one is /var/www. We will go inside directory /var/www and create a new directory named my_site in /var/www 
``` bash
  cd /var/www
  sudo mkdir my_site
```

- Step 7: Create a html file named index.html in directory /var/www/my_site
``` bash
  sudo vim index.html
```

- Step 8: Inside index.html file, add the below html script:
``` html
  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
</head>
<body>
    <h1>Hello, World</h1>
    <h2>Hello from Sim</h2>
    <h3>Have a fantabulous day ahead!</h3>
</body>
</html>
```

- Step 9: Create a file named my_site.config inside directory /etc/nginx/sites-available/. Run below command:
``` bash
  sudo vim my_site.config
```

- Step 10: Now paste the below script inside the newly created file my_site.config in directory /etc/nginx/sites-available/. Make sure the directory is correct and in this case, we are referrring to directory /var/www/my_site because it contains the file index.html which we created in step 8. 
```
server {
	listen 80 default_server;
	listen [::]:80 default_server;
	
	root /var/www/my_site;
	
	index index.html index.htm index.nginx-debian.html;
	
	server_name _;
	
	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
```

- Step 11: Create a symbolic link to your new config file named my_site.config in directory /etc/nginx/sites-enabled:
  
  Creating a symbolic link to your new configuration in /etc/nginx/sites-enabled helps to organize and manage different site configurations. It allows you to selectively activate or deactivate specific sites without modifying the original files which makes it easier to control which configurations are in use at any given time. Run below command:
``` bash
  sudo ln -s /etc/nginx/sites-available/my_site.config /etc/nginx/sites-enabled/
```

- Step 12: Run the below command to test your nginx configurations.
  
  Running sudo nginx -t is a good practice to catch configuration errors and ensure a smooth application of changes to your Nginx server.
``` bash
    sudo nginx -t
```

At this point, you will encounter an error as we missed an important step on purpose so that you remember this important step whenever you try to do it by yourself next time.

- Step 13: Check the default symbolic link inside directory /etc/nginx/sites-enabled (it will be visible as default@). The symbolic link often points to the default configuration file which might conflict with our new configuration file (my_site.config). So, we will remove the default symbolic link  (default@) so that our custom configuration (my_site.config) takes priority over the default one to make our site settings effective. Run below command:
``` bash
    sudo rm default@
```

Step 14: Do Step 12 again to check for any errors and test your nginx configurations.
	This time, if there is no error then you see a success message like below:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```









