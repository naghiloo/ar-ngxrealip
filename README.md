# ArvanCloud User Real IP in Nginx
Show Real IPs instead of ArvanCloud IPs in Nginx servers

## How To Use ?

### Modify nginx configuration
Open your nginx.conf or vhost configuration file and add this line to your http, server or location block

```nginx
include /etc/nginx/arvancloud;
```
> be sure that, the path should be same as ARVANCLOUD_FILE_PATH variable in ```arvancloud-ip-whitelist-sync.sh```

### Fetch arvancloud ip addresses
Execute the ```arvancloud-ip-whitelist-sync.sh``` to refresh arvancloud ip addresses.

```bash
#!/bin/bash

ARVANCLOUD_FILE_PATH=/etc/nginx/arvancloud

echo "# Arvancloud" > $ARVANCLOUD_FILE_PATH;
echo "" >> $ARVANCLOUD_FILE_PATH;

echo "# - IPv4" >> $ARVANCLOUD_FILE_PATH;
for i in `curl https://www.arvancloud.com/fa/ips.txt`; do
    echo "set_real_ip_from $i;" >> $ARVANCLOUD_FILE_PATH;
done

echo "" >> $ARVANCLOUD_FILE_PATH;
echo "real_ip_header x-forwarded-for;" >> $ARVANCLOUD_FILE_PATH;

# check configuration and reload nginx
nginx -t && systemctl reload nginx

```

### Check Your Output
Your ```/etc/nginx/arvancloud``` file may look like as below

```nginx
# Arvancloud

# - IPv4
set_real_ip_from 92.114.16.80/28;
set_real_ip_from 185.112.35.144/28;
.
.
# there are couple of lines here that they set the real ip trusted ips
.
.
set_real_ip_from 89.45.48.8/29;
set_real_ip_from 185.105.101.200/29;

real_ip_header x-forwarded-for;
```

## Crontab
Change the location of ```arvancloud-ip-whitelist-sync.sh``` anywhere you want and add cron job to fetch Arvancloud ip address changes. moretherefore nginx will be realoded when synchronization is completed.

```bash
# Auto sync ip addresses of Arvancloud and reload nginx
30 2 * * * /path-to/arvancloud-ip-whitelist-sync.sh >/dev/null 2>&1
```
