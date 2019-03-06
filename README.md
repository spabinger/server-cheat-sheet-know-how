### :: TOC ::
[Cron](#cron) <br/>
[Docker](#docker) <br/>
[IPMI](#ipmi) <br/>
[IPTABLES](#iptables) <br/>
[LXC](#lxc) <br/>
[Network](#network) <br/>
[Resources/Misc](#resources_misc) <br/>
[Screen](#screen) <br/>
[SSH](#ssh) <br/>
[Visudo](#visudo) <br/>
[ZFS](#zfs) <br/>




### :: Update Server ::
https://help.ubuntu.com/lts/serverguide/installing-upgrading.html
``` do-release-upgrade ```

<a name="cron" /> <br/>
### :: Cron ::
* Explain the crontab: https://crontab.guru/


<a name="network" /> <br/>
### :: Network ::

* Hints
  * Do not mix ```ifconfig XX up``` with ```ifup XXX```
  * If ```ifup``` is not working use ```--force```
  * Handle ```service networking restart``` with care
  * Do not specify 2 or more gateways on the same interface
  * Shut down interfaces: ```sudo ip link set eth0 down```
  * Remove virtual interface: ```ifconfig eth0:1 down```
  * Good ifup, ifdown description: https://www.computerhope.com/unix/ifup.htm

* NAT
  * Introduction: http://www.the-art-of-web.com/system/iptables-nat/
  * Introduction2: https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Security_Guide/s1-firewall-ipt-fwd.html
  * Masquerade: ```POSTROUTING allows packets to be altered as they are leaving the firewall's external device. The -j MASQUERADE target is specified to mask the private IP address of a node with the external IP address of the firewall/gateway.```
  


* Check state of interfaces <br/>
```cat /run/network/ifstate```

* List all network cards <br/>
``` lspci | egrep -i --color 'network|ethernet' ```

* Show all ip addresses <br/>
``` ip addr show ```

* Show interfaces and their name <br/>
``` lshw -class network```

* Show/manipulate network interfaces <br/>
``` cat /etc/network/interfaces ```

* Check speed / connection of network cards <br/>
``` ethtool <eth0> ```

* Bring interface up <br/>
http://mirrors.deepspace6.net/Linux+IPv6-HOWTO/x1028.html

* Network class <br/>
``` lshw -class network ```

* Find active internet connections <br/>
``` netstat -tulpen ```

* Get names of interfaces <br/>
``` ip link ```

* Services listening on port <br/>
``` lsof -nPi tcp:the-port ```

* Monitor traffic <br/>
``` nethogs <interface>```

* Port forwarding <br />
``` /etc/rc.local ```

* Host configuration ```/etc/hosts``` and ```/etc/hostname``` <br/>
https://askubuntu.com/questions/59458/error-message-sudo-unable-to-resolve-host-user/177381

* Check speed between two servers <br/>
  * ```sudo apt-get install iperf```
  * We'll start an iperf server on one of the machines: <br/>
  ```iperf -s```
  * And then on the other computer, tell iperf to connect as a client:<br/>
  ```iperf -c <address of other computer>```

* Login problems via SSH <br/>
Getting a ```pam_systemd(sshd:session): Failed to stat runtime dir: No such file or directory``` message:
Added directory with user_id in ```/run/users/```

* Chaging DNS resolving <br/>
  * ``` sudo nano /etc/resolvconf/resolv.conf.d/base ```
  * ``` sudo resolvconf -u```

* Virtual networks <br />
https://en.wikipedia.org/wiki/Virtual_network <br />
https://linuxconfig.org/configuring-virtual-network-interfaces-in-linux <br />

* Reload an interface (e.g., after changing /etc/network/interfaces) <br />
``` sudo ifdown <interface> && sudo ifup <interface> ``` <br />
``` sudo service networking restart ```

* Netplan (new config with Ubuntu 18.04.)
https://netplan.io/examples
https://www.linux.com/learn/intro-to-linux/2018/9/how-use-netplan-network-configuration-tool-linux
https://websiteforstudents.com/configure-static-ip-addresses-on-ubuntu-18-04-beta/



<a name="iptables" /> <br/>
#### IPTABLES
* List IPTABLES <br/>
``` iptables -S ``` <br />
``` iptables -L ```

* IPTABLES Links <br/>
https://help.ubuntu.com/community/IptablesHowTo

* Portforwardings settings <br/>
``` Rules are set in /etc/rc.local ```

* Portforwarding: show current setup <br/>
```iptables -t nat -v -L -n --line-number```

* Portforwarding: set rule <br/>
```iptables -t nat -A PREROUTING -i br0 -p tcp -m tcp --dport PORT -m comment --comment "COMMENT" -j DNAT --to-destination xxx.xxx.xxx.xxx:PORT``` <br />
Example: <br />
```iptables -t nat -A PREROUTING -i br0 -p tcp -m tcp --dport 10002 -m comment --comment "My-LXC" -j DNAT --to-destination 10.0.0.10:22``` <br />

* Portforwarding: Delete rule (use line number) <br/>
https://www.cyberciti.biz/faq/how-to-iptables-delete-postrouting-rule/ <br />
``` iptables -t nat -D PREROUTING 3 ```




### :: Disks ::

* Display block devices <br/>
``` blkid -o list ```

* Display all disks <br/>
``` 
parted
print all
```

* Display all SCSI disks <br/>
```lsscsi -s```



<a name="zfs" /> <br/>
* http://docs.oracle.com/cd/E19253-01/820-2313/6ndu3p9cf/index.html
* http://docs.oracle.com/cd/E19253-01/820-2313/6ndu3p9cd/index.html
* http://docs.oracle.com/cd/E19253-01/820-2313/gbiqe/index.html
* http://docs.oracle.com/cd/E19253-01/819-5461/gbinw/

### :: ZFS ::
* List all zfs-folders/zfs-volumes <br/>
``` zfs list ```

* Status of zpool <br/>
``` zpool status ```

* Export zpool (unmount)<br/>
``` zpool export <zpoolname> ```

* Remove/destroy <br/>
``` zpool destroy <zpoolname> ```

* Show snapshots <br/>
``` zfs list -t snapshot ```

* Volumes <br/>
```
zfs list -t volume
Volumes are listed here: /dev/zvol/tank/

```
* Custom ZFS list <br/>
``` zfs list -o name,mountpoint,mounted,my.custom:property ```


* Zpool not showing<br/>
https://github.com/zfsonlinux/zfs/issues/6077
```
zpool import <storage>   <- name of the storage
```


* Show iostats <br/>
``` zpool iostat 2 ```

* Show detailed io <br/>
``` sudo zpool iostat -v <pool> ```

* ZFS on Root <br/>
https://github.com/zfsonlinux/zfs/wiki/Ubuntu-16.04-Root-on-ZFS

* Send a ZFS snapshot <br/>
``` zfs send storage/xx@xx | ssh xxx.xxx.xxx.xxx@root "zfs receive -Fd xxx" ```
or recursive
``` zfs send -R storage/xx@xx | ssh xxx.xxx.xxx.xxx@root "zfs receive -Fd xxx" ```
``` zfs send -v storage/xxx@29062017 | pv -B 1g | ssh xxx.xxx.xxx.xxx zfs receive storage/xxx ```

* ZFS Raid levels <br/>
http://www.zfsbuild.com/2010/05/26/zfs-raid-levels/

* Improve performance of ZFS <br/>
https://icesquare.com/wordpress/how-to-improve-zfs-performance/

* ZFS Evaluation (Bachelor Thesis <br/>
http://pubman.mpdl.mpg.de/pubman/item/escidoc:2403926/component/escidoc:2403925/Then_Bachelor.pdf

* ZFS RAID level performance <br/>
https://icesquare.com/wordpress/zfs-performance-mirror-vs-raidz-vs-raidz2-vs-raidz3-vs-striped/
https://calomel.org/zfs_raid_speed_capacity.html

* ZFS Cache <br/>
http://serverascode.com/2014/07/03/add-ssd-cache-zfs.html

* More information on ZFS <br/>
http://breden.org.uk/2009/05/10/home-fileserver-zfs-file-systems/ <br/>
https://ramsdenj.com/2016/08/29/arch-linux-on-zfs-part-3-followup.html

* Understanding the spaces used by ZFS <br/>
https://blogs.oracle.com/observatory/understanding-the-space-used-by-zfs

* Problems with mount points <br/>
https://support.symantec.com/en_US/article.TECH179042.html

* ZFS after first boot <br/>
https://support.symantec.com/en_US/article.TECH179042.html

* Send snapshot to other ZFS system <br/>
https://www.flagword.net/2010/02/send-a-complete-zfs-pool/
https://unix.stackexchange.com/questions/263677/how-to-one-way-mirror-an-entire-zfs-pool-to-another-zfs-pool

* ZFS - Docker problem <br>
```
Docker not starting?
> zpool export <poolname>
> service docker stop (just for safety)
> mv var/lib/docker /var/lib/docker_bak
> zpool import <poolname>
> service docker start
```


* ZREP <br/>
http://www.bolthole.com/solaris/zrep/
http://www.bolthole.com/solaris/zrep/zrep.documentation.html
https://github.com/bolthole/zrep


<a name="docker" /> <br/>
### :: Docker ::

* Cheat-sheet <br/>
https://github.com/wsargent/docker-cheat-sheet

* List Running containers <br/>
``` docker ps -a ```

 * Create image from Dockerfile <br/>
 ``` docker build ```

* Create and start a container in one operation <br/>
``` 
docker run 
-d         detach
--name     Name of the container
--restart  Automatically restart the container -  no, always
-p         Ports
-v         Bind a volume
```
creates and starts a container

* Docker start <br/>
starts a container

* Update a single container of docker-compose <br/>
```docker-compose up -d --no-deps --build <service_name> ```


* Look at all the info on a container (including IP address) <br/>
``` docker inspect ```

* Delete a container <br/>
``` docker rm ```

* Connect to docker <br/>
```docker exec -it <containerIdOrName> bash```

* Problem loading packages during docker build <br/>
https://stackoverflow.com/questions/42064246/failed-to-establish-a-new-connection-errno-2-name-or-service-not-known
```
sudo nano /lib/systemd/system/docker.service 
    Add the dns after ExecStar. --dns 10.252.252.252 --dns 10.253.253.253 
    Should look like that: ExecStart=/usr/bin/dockerd -H fd:// --dns 10.252.252.252 --dns 10.253.253.253

systemctl daemon-reload
sudo service docker restart
```

* Change port binding of existing container <br/>
https://stackoverflow.com/questions/19335444/how-do-i-assign-a-port-mapping-to-an-existing-docker-container
```
1) stop the container 
2) change the file /var/lib/docker/containers/[hash_of_the_container]/hostconfig.json
3) restart your docker engine (to flush/clear config caches)
4) start the container
```

* Problem restarting with network issue
```
docker network ls
docker network disconnect -f <networkname>
docker network rm <networkname>
```
https://github.com/moby/moby/issues/20398

* Problem with Docker inside a VM on Windows<br/>
http://abeak.blogspot.co.at/2016/08/solving-docker-in-virtualbox-dns-issue.html



<a name="screen" />

### :: Screen ::
* Fix ```'/var/run/screen': Permission denied```<br/>
https://superuser.com/questions/1195962/cannot-make-directory-var-run-screen-permission-denied <br/>
```sudo /etc/init.d/screen-cleanup start```




<a name="ipmi" />

### :: IPMI ::
https://www.thomas-krenn.com/de/wiki/IPMI_Grundlagen
https://help.ubuntu.com/community/IPMI
https://www.thomas-krenn.com/de/wiki/IPMI_Konfiguration_f%C3%BCr_Supermicro_Systeme
https://www.thomas-krenn.com/de/wiki/Softwaretools_f%C3%BCr_IPMI_im_%C3%9Cberblick

* Read sensors (other way):<br/>
```/usr/sbin/ipmimonitoring```

* Read the SEL - system error log:<br/>
```ipmitool sel list```<br/>
or<br/>
```/usr/sbin/ipmi-sel```<br/>

* Clear the SEL<br/>
```ipmitool sel clear```<br/>
or
```/usr/sbin/ipmi-sel --clear```<br/>

* List sensor IDs<br/>
```/usr/sbin/ipmimonitoring --entity-sensor-names```


### :: Switch ::

#### Dell 5500
"Although they can work in small EQL (and other iSCSI) SAN networks they should be seen as campus-access switches and not as SAN switches."
https://en.wikipedia.org/wiki/Dell_PowerConnect#5500_series



<a name="kvm" /> <br/>
### :: KVM ::

**kvm** list machines: `virsh list --all` <br />
**kvm** shutdown: `virsh shutdown vm-name` <br />
**kvm** shutdown: `connect to the machine via ssh and type "init 0"` <br />
**kvm** start:  `virsh start vm-name`

<a name="lxc" /> <br/>
### :: LXC ::

* LXC information: <br />
http://www.cyberciti.biz/faq/howto-forcefully-stop-and-kill-lxc-container-on-linux/ <br />
https://help.ubuntu.com/lts/serverguide/lxc.html <br />
https://gist.github.com/edlerd/bef77bb0a9469ce6afce2d22760233e7 <br/>
http://blog.boyeau.com/quick-install-install-zfs-file-system-on-ubuntu-14-04/ <br/>

* List machines: <br/>
`lxc-ls -f`
* Shutdown: <br/>
`lxc-stop --name [container-name] --nokill` <br />
* Start: <br/>
`lxc-start --name [container-name] -d` <br />
* Reboot: <br/>
`lxc-stop --name [container-name] -r` <br />
* Attach: 
`lxc-attach -n [container-name] ` <br />
run command inside container
* Copy from one host to another <br />
Simply copy the folder in /var/lib/lxc
* LXC on ZFS: <br />
https://www.scotte.org/2016/07/lxc-containers-on-zfs
* Networking <br />
http://containerops.org/2013/11/19/lxc-networking/
* Network config <br/>
To make LXC respect the network config set ```iface eth0 inet manual``` <br/>
https://serverfault.com/questions/571714/setting-up-bridged-lxc-containers-with-static-ips

* Port forwarding LXC: https://wiki.debian.org/LXC/MasqueradedBridge

* Create a new LXC container <br/>
```
lxc-create -t download -n my-container
-- enter the distribution
-- enter the release
-- enter the architecture
lxc-start -n my-container -d
lxc-attach -n my-container
```

* Backup <br />
https://stackoverflow.com/questions/23427129/how-do-i-backup-move-lxc-containers
```
lxc-stop -n $NAME
cd /var/lib/lxc
tar --numeric-owner -czvf container_fs.tar.gz $NAME
rsync -avh container_fs.tar.gz user@newserver:/var/lib/lxc/
rsync -avPrh -e "ssh -p 10009" folder user@SERVER:/DEST/
```



### :: Services ::
* List all running services <br />
``` service --status-all ```

<a name="visudo" /> <br/>
### :: Visudo ::
```sudo visudo```
Be aware that adding a user to the *sudo* group overrides the entries in sudoers

<a name="ssh" /> <br/>
### :: SSH ::
* OpenSSH help
https://help.ubuntu.com/lts/serverguide/openssh-server.html

* Slow ssh prompt?
https://askubuntu.com/questions/246323/why-does-sshs-password-prompt-take-so-long-to-appear

<a name="resources_misc" /> <br/>
### :: Resources / Misc ::
* Memory <br/>
http://www.linuxatemyram.com/

* Htop <br/>
https://github.com/brentp/450k-analysis-guide

* Find <br/>
http://www.binarytides.com/linux-find-command-examples/

* Mount Windows share<br/>
https://superuser.com/questions/850301/mount-error5input-output-error-on-mount
```
Add vers=3.0 if 'Mount error(5):Input/output error on mount'
//ADDRESS	/mnt/xy	cifs	credentials=.mycreds,uid=1000,gid=1000,vers=3.0	0	0
```

### :: RSYNC ::

* Use only limited bandwith:<br/>
``` rsync --bwlimit=<kb/second> <source> <dest> ```


### :: FIO (IO Performance testing ::

* Output description
https://tobert.github.io/post/2014-04-17-fio-output-explained.html

* Introduction (German)
https://www.thomas-krenn.com/de/wiki/Fio_Grundlagen

* Doc (man-page)
https://linux.die.net/man/1/fio




### :: Useful information ::

1) **Move to the previous directory** - We all use ```cd ..``` to move to move to an upper directory. You can also use ```cd -``` to move to the previous directory - just like a back button. 
```
test@linoxide:~/Downloads$ cd -
 /home/xy
test@linoxide:~$ cd -
 /home/xy/Downloads
```
2) **Repeat your last command** - To replay as the previous command, just type ```!!```
```
$ apt install vlc
 E: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
$ sudo !!
 sudo apt install vlc
```
3) **Keep executing a command until it succeeds** - use the exit code of the command directly. The command kept running until it found run.sh and printed out its content.
```
$ while ! ./run.sh; do sleep 1; done
cat: run.sh: No such file or directory
linoxide.com
```
4) **View progress of file transfers**
In Linux, you cannot really know the rate of a file transfer progress until it's done. Using the ```pv``` command, you can monitor the progress of file transfers.
```
$ pv access.log | gzip > access.log.gz
 611MB 0:00:11 [58.3MB/s] [=> ] 15% ETA 0:00:59
```
5) **Easily schedule events**
Using the at command, you can easily schedule events at anytime.
```
echo wget https://sample.site/test.mp4 | at 2:00 PM
To view the queued jobs, type 
atq
```
6) **Display at output as a table**
When you use the ```ls``` command or other commands to throw outputs, they are often very long and need scrolling. You can easily display all the outputs in a table form using the ```column -t``` command.
```
$ cat /etc/passwd | column -t
```
7) **Keyboard Tricks**
  * The clear command clears the terminal screen with a blank one. Pressing ```Ctrl + L``` on your keyboard does the same thing, but faster.
  * To go through previous commands, press ```Alt + . .```
  * ```Ctrl + U``` clears the content you've typed already. Try this when you want to clear the password field in the command line.
  * To reverse search your command history, press ```Ctrl + R```
8) **Compress, split and encrypt files**
Trying to transfer large files across computers is a tedious task. We can easily do this by compressing the files and creating a multi-part archive if the files are extremely large. To encrypt, we add the ```-e``` switch.
```
$ zip -re test.zip AdbeRdr11010_en_US.exe run.sh Smart_Switch_pc_setup.exe
 Enter password:
 Verify password:
 adding: AdbeRdr11010_en_US.exe (deflated 0%)
 adding: run.sh (stored 0%)
 adding: Smart_Switch_pc_setup.exe (deflated 2%)
```
9) **Stress test your battery** - Try this command:
```
$ cat /dev/urandom > /dev/null
```
10) **Renaming/moving files with suffixes** - If you want to quickly rename or move a bunch of files with suffix, try this command.
```
$ cp /home/sample.txt{,-old}
This will translate to:
$ cp /home/sample.txt /home/sample.txt-old
To rename files of a particular extension in batch, try this:
$ rename 's/comes_here_/goes_there/' *.txt
```



