GeoIP Protection Python Script
==========================================

Description
-----------

This is a Python script to check an IP address against MaxMind DB files. The script 
includes a debug option to display the country codes from the DB lookup. Returns exit 
code 0 on a permitted country. This script is designed to be used as part of SSH host
verification. But any service that uses these allow files can utilise the service.

Installation
------------

Copy script to /usr/local/bin<br>
Chmod 755 /usr/local/bin/geoiplookup<br>
Update the file /etc/hosts.deny with sshd: ALL<br>
Update the file /etc/hosts.allow with sshd: ALL: aclexec /usr/local/bin/geoiplookup %a

Usage
-----

Update the ALLOWED_COUNTRIES at top of script. Format is 2 letter country code. <br>
ALLOWED_COUNTRIES="AU NZ"

geoiplookup x.x.x.x

Returns exit code of 0 on allowed country otherwise 1. The -d provides additional debug 
information which should not be used in production.

Example
-------

ALLOWED_COUNTRIES="AU NZ"

geoiplookup -d 8.8.8.8<br>
iso_code=US<br>
Return code: 1

geoiplookup -d 202.158.214.106<br>
iso_code=AU<br>
Return code: 0

geoiplookup 202.158.214.106<br>
Return code: 0

Requirements
------------

This code requires Python 3.5+. Older versions are not supported.

You must first install the maxmind DB. Register a user on the maxmind 
website and install the free database. Update the database using your license key.

Then install the maxmind C library for faster lookups and a compiler. <br>
apt install libmaxminddb0 libmaxminddb-dev build-essentials

Finally you have to install the maxmind Python module<br>
https://github.com/maxmind/MaxMind-DB-Reader-python/archive/master.zip<br>
Extract the zip<br>
Change to the directory and then run<br>
python setup.py build<br>
To make sure it builds with the library above. It will tell you if it fails.

Once it builds without problems<br>
sudo python setup.py install

Testing
-------
Running on Debian 10 buster as SSH geoip protection.

Support
-------

Please report all issues with this code using the `GitHub issue tracker
<https://github.com/rtfmoz2/geoiplookup/issues>`_

Please note, support is limited. I figured this all out after a beer in one night.
