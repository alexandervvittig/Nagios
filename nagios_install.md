# nagios install
>Source: https://assets.nagios.com/downloads/nagioscore/docs/Nagios-Core-Installing-On-Centos7.pdf

Switch to root mode, if you run some commands without sudo it will cause issues
```
su
```

### Disable SELinux
Prior to starting our install, we need to disable SELinux.  
```setenforce 0```  
Modify ```/etc/selinux/config``` and change ```enforcing``` to ```disabled```.

### Install Prerequisites

We now need to install the programs that Core will need in order to function / install properly.

```yum install httpd php php-cli gcc glibc glibc-common gd gd-devel net-snmp openssl-devel wget unzip -y```

Now, let's create a user and group for Nagios to use.

```
useradd nagios
groupadd nagcmd
usermod -a -G nagcmd nagios
usermod -a -G nagcmd apache
```

### Download and Install Nagios
This is where we will download the required .tar.gz files for Nagios and Nagios Plugins.
```
cd /tmp
wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-4.1.1.tar.gz
wget http://www.nagios-plugins.org/download/nagios-plugins-2.1.1.tar.gz
tar zxf nagios-4.1.1.tar.gz
tar zxf nagios-plugins-2.1.1.tar.gz
cd nagios-4.1.1
```
Now that the files are extracted, let's start compiling Nagios!
```
./configure --with-command-group=nagcmd
make all
make install
make install-init
make install-config
make install-commandmode
make install-webconf
```

### Creating a password for nagiosadmin
We now need to create a password for the nagiosadmin user. This will be used to login to your core web GUI.
```
htpasswd -c /usr/local/nagios/etc/htpasswd.users nagiosadmin
```
If you get an error in this step, you might have to manually create the folder.
> sudo mkdir /usr/local/nagios  
> sudo mkdir /usr/local/nagios/etc

### Install Nagios Plugins
Now that Nagios is installed, we need to install the plugins so that it can utilize them for checks.
```
cd /tmp/nagios-plugins-2.1.1
./configure --with-nagios-user=nagios --with-nagios-group=nagios --with-openssl
make all
make install
```
### Start Nagios
If everything worked correctly prior to this, you will be able to start your Nagios Core service.
```
service httpd start
service nagios start
```
You should now be able to access your machine by navigating to http://SERVER_IP_HERE/nagios/ - if this does not work then please try these two
troubleshooting steps.

### Set Services to AutoStart on reboot
```
systemctl is-enabled nagios
systemctl is-enabled httpd
systemctl enable nagios
systemctl enable httpd
```

### Troubleshooting
If you cannot access the Nagios web page, it may be related to your firewall rules. The following command will open up port 80 on your
Nagios Core machine.
```
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --reload
```
If you are still unable to access the web GUI, your web server may be only listening on IPv6. Modify ```/etc/httpd/conf/httpd.conf```
and look for the part that says ```'Listen :80'```. Modify it to be ```'Listen 0.0.0.0:80'```. Then, run ```service httpd restart```
