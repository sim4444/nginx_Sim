# Assignment 3

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

## Steps to create a regular user, assign bash as login shell, make the user eligible to perform administrative tasks, and make the user eligible to access server via SSH:

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

- Step 1: As you are currently connected to server as regular user 'sim', now edit ssh configuration so that the root user can no longer connect to the server via ssh. The ssh configuration files are in etc/ssh directory. The primary configuration file we need is the sshd_config in directory etc/ssh. Run below commands:

Go to /etc/ssh:
``` bash
  cd /etc/ssh
```
Create a backup of original or old sshd_config file:
``` bash
  sudo cp sshd_config sshd_config.old
```
Open sshd_config file to edit:
``` bash
  sudo vim sshd_config
```
Now, you have to look for the line PermitRootLogin yes and change yes to no. To find this line in vim a little faster, press forward slash (/) and type first words of what we are looking for. In this case type /PermitRootL and save the file (:wq). Now restart the ssh service:
``` bash
  sudo systemctl restart ssh.service
```
Now, you have prevented the root user from connecting to the server via SSH. Test it by exiting and trying to connect to your server as root and you should get a permission denied error.

## 


