### Download and install NSClient++
> Source: https://www.nsclient.org/download/ (```Version 0.5.2.35```)  
> Other Source: https://www.youtube.com/watch?v=GxdPh9hk844


### Install
1. Select Generic
2. Select Typical
3. Set 'AllowedHost' to NAGIO_SERVER_IP
4. Remove Password (unless setup in Nagio server)
5. Check first 3 checkboxes (Enable common, nsclientserver, NRPE)
6. Enable ICMP ```netsh firewall set icmp 8```
7. Navigate to ```"C:\Program Files\NSClient++\nsclient.ini"``` and make sure that the ```CheckXXX``` keys are set to '1' (```CheckSystem = 1```)


### Setup

On the NAGIOS server navigate to
```/usr/local/nagios/etc/``` and edit the ```nagios.cfg``` file  
Uncomment the windows config file in line ```38```
```cfg_file=/usr/local/nagios/etc/objects/windows.cfg```

Save and navigate to ```/usr/local/nagios/etc/objects```
and make a backup copy of the ```windows.cfg``` file
```
sudo cp windows.cfg windows.cfg.bak
```

### Config settings
Change the settings as needed in the ```windows.cfg``` file.
Restart the nagios service
```
systemctl restart nagios
```
### Troubleshoot
If you run into issues, check the log files / error report
```
# run to find error in your config file
/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

# run to check client data
/usr/local/nagios/libexec/check_nt -H HOST_IP -p HOST_PW -v MEMUSE -w 80 -c 90

```