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

## Steps to create a regular user:
- 

``` 
  <h>
```
