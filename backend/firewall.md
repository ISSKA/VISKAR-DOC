# Firewall configuration

No firewall was configured, and exposed ports were automatically available from the internet.
To fix this, I installed UFW and configured it with strict deny-first rules

```bash
apt install ufw
ufw allow "OpenSSH" # Allow SSH access on port 22
ufw allow "Nginx HTTPS" # Allow port 443 for all Nginx requests
ufw allow "Nginx HTTP" # Allow port 80 for all Nginx requests. Needed for Certbot to renew the certificates
ufw enable
```
