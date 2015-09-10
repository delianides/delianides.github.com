## Problem

Amazon ELB's only provide DNS names instead of IP address because they can have multiple IP addresses that could distribute traffic to each backend webhost. If you don't want to add the dns name as a CNAME record and you are using a DNS service that does not support ANAME or ALIAS records (which allow you to use a dns name as the apex) the only solution is set up a forward proxy. 

## Solution

The solution is an Amazon EC2 t2.medium, the t2.micros seemed to have slower network responses. I first installed the build-essential and apache2 packages. 

```$ sudo apt-get update && sudo apt-get install -y build-essential apache2```

Make sure the following apache modules are enabled.

```$ sudo a2enmod ssl request headers proxy proxy_http xml2enc```

Then modify the default apache site with the following config:

```apache
<IfModule mod_ssl.c>
# Catch non-SSL requests and redirect to SSL
<VirtualHost *:80>
  ServerName 52.21.37.187
  Redirect / https://newspring.cc/
</VirtualHost>

# Handle SSL requests on the static IP
<VirtualHost *:443>
  ServerAdmin web@newspring.cc
  ServerName newspring.cc
  ServerAlias *.newspring.cc

  # SSL Configuration
  SSLEngine on
  SSLProxyEngine on
  SSLProxyCACertificateFile /etc/apache2/ssl/ns_chain.crt
  SSLCertificateFile    /etc/apache2/ssl/ns.crt
  SSLCertificateKeyFile /etc/apache2/ssl/ns.key
  # Additional defaults, e.g. ciphers, defined in apache's ssl.conf

  # Where the magic happens
  ProxyPreserveHost On
  ProxyPass / https://ns-ark-504144412.us-east-1.elb.amazonaws.com/
  ProxyPassReverse / https://ns-ark-504144412.us-east-1.elb.amazonaws.com/

  # Might want this on; sets X-Forwarded-For and other useful headers
  ProxyVia off

  # This came from an example I found online, handles broken connections from IE
  BrowserMatch "MSIE [2-6]" \
    nokeepalive ssl-unclean-shutdown \
    downgrade-1.0 force-response-1.0
  # MSIE 7 and newer should be able to use keepalive
  BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
</VirtualHost>
</IfModule>
```

The IP address in the HTTP Virtualhost is an Elastic IP address generated from AWS. This will ensure the IP address for the server doesn't change. This should resolve any domains with .newspring.cc through the load balancer. 

