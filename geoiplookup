# Lookup the IP address against the maxmind geoIP database
import json
import ipaddress
import sys
import syslog
import maxminddb

ALLOWED_COUNTRIES="AU NZ US GB SE PL"
debug = 0
arg = sys.argv[1]
if arg == "-d":
  debug = 1
  arg = sys.argv[2]

# ignore private address space
if ipaddress.ip_address(arg).is_private:
  sys.exit(0)

# open DB and lookup address
reader = maxminddb.open_database('/usr/share/GeoIP/GeoLite2-City.mmdb')
geolookup = reader.get(arg)
reader.close()

# process the reply
if type(geolookup) is dict:
  if 'country' in geolookup:
    if 'iso_code' in geolookup['country']:
      iso = geolookup['country']['iso_code']
    else:
      syslog.syslog('iso code not found')
      sys.exit(1)
  else:
    syslog.syslog('country not found')
    sys.exit(1)
else:
  syslog.syslog('empty response')
  sys.exit(1)

if debug:
        print("iso_code="+iso)
#       print(json.dumps(geolookup['country']))

# iterate through allowed countries until we find one that exists
for country in ALLOWED_COUNTRIES.split():
    if country in iso:
        sys.exit(0)

# country not found so exit with error code
syslog.syslog('Access denied from country '+iso)
sys.exit(1)
