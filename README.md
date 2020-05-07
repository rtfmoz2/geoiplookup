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
Update the ALLOWED_COUNTRIES variable at top of script. Format is 2 letter country code.  

ALLOWED_COUNTRIES="AU NZ US GB PL SE"

```
usage: geoiplookup [-h] [-d] address

positional arguments:
  address      check the address against the geoip db

optional arguments:
  -h, --help   show this help message and exit
  -d, --debug  display debug information
```

Returns exit code of 0 on allowed country otherwise 1. The -d provides additional debug 
information which should not be used in production. Don't use this option
in the hosts.allow file.
### Example
ALLOWED_COUNTRIES="AU NZ"

Test 1 - Debug Allowed Region
```
$ geoiplookup -d 8.8.8.8
Allowed Countries = AU NZ US GB SE PL
ISO name found as US. Country name found as United States.
{"geoname_id": 6252001, "iso_code": "US", "names": {"de": "USA", "en": "United States", "es": "Estados Unidos", "fr": "\u00c9tats-Unis", "ja": "\u30a2\u30e1\u30ea\u30ab\u5408\u8846\u56fd", "pt-BR": "Estados Unidos", "ru": "\u0421\u0428\u0410", "zh-CN": "\u7f8e\u56fd"}}
Access ALLOWED from 8.8.8.8 from country United States
```
Return code: 0  

Test 2 - Allowed Region
```
$ geoiplookup 8.8.8.8
```
Return code: 0  

Test 3 - Debug Denied Region
```
$ geoiplookup -d 1.2.3.4
Allowed Countries = AU NZ US GB SE PL
ISO name found as RU. Country name found as Russia.
{"geoname_id": 2017370, "iso_code": "RU", "names": {"de": "Russland", "en": "Russia", "es": "Rusia", "fr": "Russie", "ja": "\u30ed\u30b7\u30a2", "pt-BR": "R\u00fassia", "ru": "\u0420\u043e\u0441\u0441\u0438\u044f", "zh-CN": "\u4fc4\u7f57\u65af\u8054\u90a6"}}
Access DENIED from 1.2.3.4 from country Russia
```
Return code: 1  

Test 4 - Denied Region
```
$ geoiplookup 1.2.3.4
```
Return code: 1  

### Testing
Running on Debian 10 buster as SSH geoip protection.  

Syslog output from the above commands.  
Test 1 - Debug Mode Allowed Region  
```
May  7 23:15:40 Allowed Countries = AU NZ US GB SE PL
May  7 23:15:40 ISO name found as US. Country name found as United States.
May  7 23:15:40 localhost  {"geoname_id": 6252001, "iso_code": "US", "names": {"de": "USA", "en": "United States", "es": "Estados Unidos", "fr": "\u00c9tats-Unis", "ja": "\u30a2\u30e1\u30ea\u30ab\u5408\u8846\u56fd", "pt-BR": "Estados Unidos", "ru": "\u0421\u0428\u0410", "zh-CN": "\u7f8e\u56fd"}}
May  7 23:15:40 Access ALLOWED from 8.8.8.8 from country United States
```
Test 2 - Allowed Region  
```
NO SYSLOG OUTPUT
```
Test 3 - Debug Mode Denied Region  
```
May  7 23:15:51 Allowed Countries = AU NZ US GB SE PL
May  7 23:15:51 ISO name found as RU. Country name found as Russia.
May  7 23:15:51 localhost  {"geoname_id": 2017370, "iso_code": "RU", "names": {"de": "Russland", "en": "Russia", "es": "Rusia", "fr": "Russie", "ja": "\u30ed\u30b7\u30a2", "pt-BR": "R\u00fassia", "ru": "\u0420\u043e\u0441\u0441\u0438\u044f", "zh-CN": "\u4fc4\u7f57\u65af\u8054\u90a6"}}
May  7 23:15:51 Access DENIED from 1.2.3.4 from country Russia
```
Test 4 - Denied Region  
May  7 23:15:57 Access DENIED from 1.2.3.4 from country Russia
```
### Output
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
