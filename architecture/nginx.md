# Nginx

This document describes the steps taken to add a new host to the server. We had to do this when we deployed Grafana.

## DNS

Our DNS is managed by Infomaniak. To add a new entry in the DNS, you have to get in touch with Chatelain Info <thierry@chatelain-info.ch>, who handles everything IT at SISKA.

## Adding a host

The configuration seems to be managed my Nixos, but the Nixos binaries are nowhere to be found on the server.  
The Nginx site that is enabled is a symlink located at `/root/viskar-ops/docker-deploy/nginx_visualkarsys.config`

By copying one of the existing configs, I added the following configuration section:

```conf
server {

        # SSL configuration
        #
        listen 443 ssl ;
        listen [::]:443 ssl ;

        server_name grafana.visualkarsys.com; # managed by Certbot

        location / {
                proxy_pass "http://127.0.0.1:3000";
                proxy_set_header Host $http_host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
        }
}
```

Under "Default server configuration", I also added `grafana.visualkarsys.com` to the list of HTTPS redirects.

```conf
server_name www.visualkarsys.com dev.visualkarsys.com visualkarsys.com hydra.visualkarsys.com grafana.visualkarsys.com;
return 301 https://$host$request_uri;
```

## Updating certificates

The certificate is not issued with a wildcard. I did not question switching to a wildcard, and instead, I updated the certificate with the new subdomain.  
The certificates are managed by certbot. To add a new subdomain to the existing certificate, I did the following.

```bash
certbot run --expand -d dev.visualkarsys.com,hydra.visualkarsys.com,visualkarsys.com,www.visualkarsys.com,grafana.visualkarsys.com
sudo nginx -s reload
```

Certbot will ask if it should modify the nginx config to enable HTTPS redirects. I said say, but in retrospect, that probably added a redondant redirect in the configuration.  
This could always be manually cleaned up in the future.
