---
description: Step-by-step guide
---

# Deploy v2ray service on Vultr VPS

## Install v2ray core & service

### Install v2ray core & enable v2ray service.

```bash
$ bash <(curl -L -s https://install.direct/go.sh)
$ systemctl enable v2ray
```

### Edit v2ray service configuration.

> /etc/v2ray/config.json

### Start v2ray service.

```bash
$ systemctl start v2ray
```

## Install acme.sh to issue TLS certificate for your DNS \(Optional\)

### Install acme.sh

```bash
$ curl https://get.acme.sh | sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   775    0   775    0     0   3189      0 --:--:-- --:--:-- --:--:--  3202
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  191k  100  191k    0     0  5809k      0 --:--:-- --:--:-- --:--:-- 5809k
[Mon Mar 16 08:05:21 UTC 2020] Installing from online archive.
[Mon Mar 16 08:05:21 UTC 2020] Downloading https://github.com/acmesh-official/acme.sh/archive/master.tar.gz
[Mon Mar 16 08:05:21 UTC 2020] Extracting master.tar.gz
[Mon Mar 16 08:05:21 UTC 2020] It is recommended to install socat first.
[Mon Mar 16 08:05:21 UTC 2020] We use socat for standalone server if you use standalone mode.
[Mon Mar 16 08:05:21 UTC 2020] If you don't use standalone mode, just ignore this warning.
[Mon Mar 16 08:05:21 UTC 2020] Installing to /root/.acme.sh
[Mon Mar 16 08:05:21 UTC 2020] Installed to /root/.acme.sh/acme.sh
[Mon Mar 16 08:05:21 UTC 2020] Installing alias to '/root/.bashrc'
[Mon Mar 16 08:05:21 UTC 2020] OK, Close and reopen your terminal to start using acme.sh
[Mon Mar 16 08:05:21 UTC 2020] Installing cron job
no crontab for root
no crontab for root
[Mon Mar 16 08:05:21 UTC 2020] Good, bash is found, so change the shebang to use bash as preferred.
[Mon Mar 16 08:05:22 UTC 2020] OK
[Mon Mar 16 08:05:22 UTC 2020] Install success!

```

### Setup Cloudflare API parameters & Issue certificate

```bash
$ export CF_KEY="YOUR_CLOUDFLARE_KEY"
$ export CF_EMAIL="your_email@domain"
$ export CF_TOKEN="YOUR_CF_TOKEN"
$ export CF_ACCOUNT_ID="YOUR_ACCOUNT_ID"
$ acme.sh --issue --dns dns_cf -d mayongcong.top -d '*.mayongcong.top'
```

## Install v2-ui GUI \(Optional\)

### Install v2-ui

```text
$ bash <(curl -Ls https://blog.sprov.xyz/v2-ui.sh)
```

### Restore v2-ui configuration if applicable

```text
$ scp vps.mayongcong.top:/etc/v2-ui/v2-ui.db /etc/v2-ui/v2-ui.db
```

### Make symbolic link of certificate

```text
$ ln -s ~/.acme.sh/mayongcong.top/mayongcong.top.key /etc/cloudflare/mayongcong.top.key
$ ln -s ~/.acme.sh/mayongcong.top/mayongcong.top.cer /etc/cloudflare/mayongcong.top.pem
```

### Create cron job to restart v2ray

```text
$ crontab -l
10 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
30 0 * * * /root/bin/restart-v2ray.sh  > /dev/null
```

