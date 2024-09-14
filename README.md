# Dynamic DNS Update Script for Cloudflare

This script updates DNS records for multiple domains using Cloudflare's API. It supports both IPv4 and IPv6, includes error handling, logging, retries, and allows adding new domain configurations via command-line options. Can set TTL and Cloudflare DNS Proxy

cf_ddns_bash - requires bash, uses bashisms

cd_ddns_posix - should work with sh (dash etc)

Script checks for required dependencies, they are: curl, jq, flock, tail, mv, mkdir, chmod, touch, date, sleep, echo, test

### Edit any static and default configs in script head as needed

```
############################################
# Configuration Variables and Defaults     #
############################################

BASEDIR="$HOME/bin/cloudflare_ddns"
CONFDIR="$BASEDIR/.ddns_configs"
CONFIG_FILE="$CONFDIR/ddns.conf"
LOG_FILE="$CONFDIR/ddns.log"
MAX_LOG_SIZE=200 #Lines
IPV4_SERVICE="http://ipv4.icanhazip.com"
IPV6_SERVICE="http://ipv6.icanhazip.com"
MAX_RETRIES=3
RETRY_DELAY=5
VERBOSE=false
SPECIFIC_DOMAIN=""
ADD_CONFIG=false

############################################
# End Configuration Variables and Defaults #
############################################
```

### Example Config File
Can be created / added to by script

```
{
  "domains": [
    {
      "domain": "sub1.domain.tld",
      "cf_token": "XXXXX",
      "zoneid": "YYYYY",
      "proxied": false,
      "ttl": 60
    },
    {
      "domain": "domain.tld",
      "cf_token": "XXXXX",
      "zoneid": "YYYYY",
      "proxied": false,
      "ttl": 90
    },
    {
      "domain": "sub2.domain.tld",
      "cf_token": "XXXXX",
      "zoneid": "YYYYY",
      "proxied": true,
      "ttl": 120
    }
  ]
}
```

### Usage

```
[BASH] Usage: ./cf_ddns_bash [options]
[POSIX] Usage: ./cf_ddns_posix [options]

Examples:

# Updates all instances in config file
./cf_ddns_bash  # Updates all instances in config file

# Update a single domain in the config file
./cf_ddns_bash -d my.domain.tld

# Change config file location, remove -d to process all domains listed in the config.  
./cf_ddns_bash -c /path/to/config -d my.domain.tld

# Adds an entry for a domain to the configuration file
# Recommend using a space and HISTCONTROL ignorespace or ignoreboth to not add keys to shell history
./cf_ddns_bash -a -d my.domain.tld -z "cf_zone_id" -t "cf_api_token_with_update_dns" -T 60 

Options:
  -a            Add a new domain configuration
  -c CONFIG     Path to configuration file (default: $HOME/bin/cloudflare_ddns/.ddns_configs/ddns.conf)
  -d DOMAIN     Specify a domain to update or add
  -z ZONEID     Specify the Zone ID (used with -a)
  -t TOKEN      Specify the Cloudflare API Token (used with -a)
  -p            Enable proxying through Cloudflare (used with -a)
  -T TTL        Set TTL for the DNS record (used with -a), default is 60
  -v            Enable verbose output
  -h            Display this help message
```