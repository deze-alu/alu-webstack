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

## Usage

```
$ ./0-world_wide_web danieleze.tech
The subdomain www is a A record and points to 35.177.182.1
The subdomain lb-01 is a A record and points to 35.177.182.1
The subdomain web-01 is a A record and points to 13.135.166.215
The subdomain web-02 is a A record and points to 18.133.221.184

$ ./0-world_wide_web danieleze.tech web-02
The subdomain web-02 is a A record and points to 18.133.221.184
```
