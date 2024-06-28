### Task
Some of the developers from Nautilus project team have asked for SFTP access to at least one of the app server in Stratos DC. After going through the requirements, the system admins team has decided to configure the SFTP server on App Server 2 server in Stratos Datacenter. Please configure it as per the following instructions:


a. Create a SFTP user kareem and set its password to dCV3szSGNA. There is already a group called ftp, you can utilise the same.

```sudo useradd --gid ftp --no-create-home --no-user-group kareem```

b. Password authentication should be enabled for this user.

```echo 'dCV3szSGNA' | sudo passwd --stdin kareem```

c. SFTP user should only be allowed to make SFTP connections.

Edit /etc/ssh/sshd_config  file and add:

```
Match User kareem
  ForceCommand internal-sftp
```

Ref: https://www.techrepublic.com/article/how-to-set-up-an-sftp-server-on-linux/
