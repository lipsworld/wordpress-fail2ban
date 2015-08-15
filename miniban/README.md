# Miniban

Ban hosts on webservers and reverse proxies.

## Installation of htaccess method

Copy this to your `wp-config.php`.

```php
require_once dirname( __FILE__ ) . '/wp-miniban-htaccess.inc.php';
Miniban::init(
    dirname( __FILE__ ) . '/.htaccess',
    // These IP addresses and IP ranges will get whitelisted.
    array( '127.0.0.0/8', 'SERVER-IP', '66.249.64.0/19' ),
    array( 'header' => 'Remote_Addr' )
);
```

`wp-miniban-htaccess.inc.php` contains `miniban-base.php` and `miniban-htaccess.php`.

Set up daily cron job to unban old bans.

```
php -r 'require "/PATH/TO/wp-miniban-htaccess.inc.php"; Miniban::unban();'
```

### .htaccess method for Apache

Based on which HTTP header hosts should be banned:

- Apache without reverse proxy
- CloudFlare
- Rackspace
- Varnish
- HA proxy

```apache
# Apache < 2.3
<IfModule !mod_authz_core.c>
    Order allow,deny
    Deny from env=mini_ban
    Allow from all
    Satisfy All
</IfModule>

# Apache ≥ 2.3
<IfModule mod_authz_core.c>
    <RequireAll>
        Require all granted
        Require not env mini_ban
    </RequireAll>
</IfModule>

# Mini Ban for Apache directory configuration
SetEnvIf Remote_Addr "^192\.168\.1\.100$" mini_ban

# CloudFlare header
#SetEnvIf X-FORWARDED-FOR "^192\.168\.1\.100$" mini_ban

# Incapsula

# Rackspace header
#SetEnvIf X-CLUSTER-CLIENT-IP "^192\.168\.1\.100$" mini_ban

# Varnish

# HA proxy

```

### Tarpit method

Wait for specified time and send random bytes continously.

### CloudFlare method

Communicate with cloudFlare API and ban/unban hosts.

### Forbidden methos

Only respond with HTTP 403 forbidden.

### RewriteMap method for Apache

```apache
# Virtual host configuration
RewriteEngine On
RewriteMap ipblocklist "txt:/path/to/ipblocklist.txt"
RewriteCond "${ipblocklist:%{REMOTE_ADDR}|NOT-FOUND}" !=NOT-FOUND
RewriteRule ^ - [F]
```

### Nginx user configuration

```nginx
@TODO
```