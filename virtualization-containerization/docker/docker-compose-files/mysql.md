---
description: 2 SQL servers
---

# MySQL

{% code title="docker-compose.yml" %}
```yaml
version: "3.8"
services:
  ngiqdev:
    container_name: ngiqdev
    image: mysql:5.7
    command: --default-authentication-plugin=mysql_native_password --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=ngiqdev
      - MYSQL_USER=ngiqdev
      - MYSQL_PASSWORD=ngiqdev
    volumes:
      - /home/globefish/.local/etc/docker-compose/ngiq/ngiqdev-data:/var/lib/mysql
    network_mode: bridge
    ports:
      - 3306:3306
    restart: unless-stopped
  ngiqtest:
    container_name: ngiqtest
    image: mysql:5.7
    command: --default-authentication-plugin=mysql_native_password --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=ngiqtest
      - MYSQL_USER=ngiqtest
      - MYSQL_PASSWORD=ngiqtest
    volumes:
      - /home/globefish/.local/etc/docker-compose/ngiq/ngiqtest-data:/var/lib/mysql
    network_mode: bridge
    ports:
      - 3308:3306
    restart: unless-stopped

```
{% endcode %}

