# Protect the Docker daemon socket

## Generate self-signed certificates

{% page-ref page="../../network/tls-ssl/openssl-self-signed-certificates.md" %}

## Update docker service configuration

### Create a daemon configuration

{% code title="/etc/docker/daemon.json" %}
```bash
{
  "tlsverify": true,
  "tlscacert": "/etc/docker/certs/ca.pem",
  "tlscert": "/etc/docker/certs/server-cert.pem",
  "tlskey": "/etc/docker/certs/server-key.pem",
  "hosts": ["tcp://0.0.0.0:2376", "unix:///var/run/docker/sock"]
}

```
{% endcode %}

### Change service configuration \(Ubuntu 18.04 systemd\)

{% code title="/etc/systemd/system/multi-user.target.wants/docker.service" %}
```bash
# other content
# comment original ExecStart
#ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ExecStart=/usr/bin/dockerd
# other content
```
{% endcode %}

### Reload systemd daemon and restart docker service

```bash
systemctl daemon-reload
systemctl restart docker
```

### Configure client certificate and environment variables

{% code title="~/.bashrc" %}
```bash
declare -x DOCKER_CERT_PATH="/c/Users/emmayyg/.docker/machine/machines/default"
declare -x DOCKER_HOST="tcp://127.0.0.1:2376"
declare -x DOCKER_TLS="1"
declare -x DOCKER_TLS_VERIFY="1"
```
{% endcode %}

