### Task
One of the Nautilus project developers need access to run docker commands on App Server 1. This user is already created on the server. Accomplish this task as per details given below:


User anita is not able to run docker commands on App Server 1 in Stratos DC, make the required changes so that this user can run docker commands without sudo.

### Solution
```
[tony@stapp01 ~]$ id anita
uid=1002(anita) gid=1002(anita) groups=1002(anita)

[tony@stapp01 ~]$ grep docker /etc/group
docker:x:995:tony

[tony@stapp01 ~]$ sudo usermod -aG docker anita

[tony@stapp01 ~]$ id anita
uid=1002(anita) gid=1002(anita) groups=1002(anita),995(docker)
```
