# GeoIP Protection Python Script #

## Description
This is a Python script to check an IP address against MaxMind free GeoIP database. The builtin geoip features of iptables use geoip v1 which is now depreciated. This uses the current geoip v2 database.

This script is designed to be used as part of SSH host verification. But any service 
that uses these allow files can utilise the service.

The script provides a debug option to display the country codes from the DB lookup. 
## Installation
Please make sure the requirments are met below.  
Copy script to /usr/local/bin and update the permissions.  
```bash
sudo cp geoiplookup /usr/local/bin
chmod 755 /usr/local/bin/geoiplookup
```
Update the file /etc/hosts.deny  
`sshd: ALL`<br>
Update the file /etc/hosts.allow   
`sshd: ALL: aclexec python3 /usr/local/bin/geoiplookup %a`
## Usage
Update the ALLOWED_COUNTRIES at top of script. Format is 2 letter country code. <br>
ALLOWED_COUNTRIES="AU NZ"

```
geoiplookup [-d] x.x.x.x
```

Returns exit code of 0 on allowed country otherwise 1. The -d provides additional debug 
information which should not be used in production. Don't use this option
in the hosts.allow file.
### Example
ALLOWED_COUNTRIES="AU NZ"

```
geoiplookup -d 8.8.8.8
iso_code=US
```
Return code: 1

```
geoiplookup -d 202.158.214.106
iso_code=AU
```
Return code: 0

```
geoiplookup 202.158.214.106
```
Return code: 0
### Testing
Running on Debian 10 buster as SSH geoip protection.
## Requirements
This code requires Python 3.5+. Older versions are not supported.

You must first install the maxmind DB. Register a user on the maxmind website and 
install the free database into your system using one of the provided deb packages
from https://github.com/maxmind/geoipupdate/releases/latest

sudo dpkg -i geoipupdate_4.3.0_linux_amd64.deb<br>
Once installed update the /etc/GeoIP.conf with your free license from maxmind.com. 
Look for My License Key on the navigation menus. Typically it wont exist so you need select
generate new license key.<br>
One you have updated the config file, run the update to check its all working. 
sudo geoipupdate -v

Now install the maxmind C library for faster lookups and a compiler.  
sudo apt install libmaxminddb0 libmaxminddb-dev build-essentials

Finally you have to install the maxmind Python module  
https://github.com/maxmind/MaxMind-DB-Reader-python/archive/master.zip  
Extract the zip  
Change to the directory and then run<br>
python3 setup.py build  
To make sure it builds with the C library above. It will tell you if it fails.

Once it builds without problems  
sudo python3 setup.py install
## Support
Please report all issues with this code using the [GitHub issue tracker](https://github.com/rtfmoz2/geoiplookup/issues)

Please note, support is limited. I figured this out after a beer one night.
