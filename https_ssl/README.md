# HTTPS SSL

Configure subdomains for `danieleze.tech`, audit them with a Bash script,
and terminate SSL on the HAProxy load balancer.

## Servers

| Name | IP | Subdomain |
| ---- | -- | --------- |
| `7268-web-01` | 13.135.166.215 | `web-01.danieleze.tech` |
| `7268-web-02` | 18.133.221.184 | `web-02.danieleze.tech` |
| `7268-lb-01` | 35.177.182.1 | `lb-01.danieleze.tech`, `www.danieleze.tech` |

## Requirements

- Ubuntu 16.04 LTS
- Bash scripts start with `#!/usr/bin/env bash`, followed by a comment
  explaining what the script does
- All Bash scripts are executable and pass Shellcheck

## Files

| File | Description |
| ---- | ----------- |
| `0-world_wide_web` | Display the record type and destination of a domain's subdomains (`www`, `lb-01`, `web-01`, `web-02`, or a single given subdomain) |
| `1-haproxy_ssl_termination` | HAProxy config (`/etc/haproxy/haproxy.cfg`) on lb-01 that accepts HTTPS on port 443, decrypts it, and passes it to web-01 and web-02 |

## SSL certificate

The certificate for `www.danieleze.tech` comes from Let's Encrypt, using
certbot in standalone mode on lb-01. HAProxy expects the certificate and
private key in one file, so a deploy hook combines them:

```
certbot certonly --standalone -d www.danieleze.tech \
    --pre-hook "service haproxy stop" \
    --post-hook "service haproxy start" \
    --deploy-hook 'cat "$RENEWED_LINEAGE/fullchain.pem" "$RENEWED_LINEAGE/privkey.pem" > /etc/haproxy/certs/www.danieleze.tech.pem'
```

Certbot saves the hooks, so automatic renewals free port 80 and rebuild
the combined file each time.

## Usage

```
$ ./0-world_wide_web danieleze.tech
The subdomain www is a A record and points to 35.177.182.1
The subdomain lb-01 is a A record and points to 35.177.182.1
The subdomain web-01 is a A record and points to 13.135.166.215
The subdomain web-02 is a A record and points to 18.133.221.184

$ ./0-world_wide_web danieleze.tech web-02
The subdomain web-02 is a A record and points to 18.133.221.184

$ curl https://www.danieleze.tech
Holberton School for the win!
```
