# v2ray

{% code title="docker-compose.yml" %}
```yaml
version: "3.8"
services:
  v2fly-proxy:
    container_name: v2fly-proxy
    image: ufonion/v2fly
    volumes: 
      - ${PWD}/v2ray-config:/etc/v2ray/
      - ${PWD}/log:/var/log/v2ray/
    ports:
      - "127.0.0.1:1080:10808"
      - "127.0.0.1:8080:10809"
```
{% endcode %}

{% code title="v2ray-perparation.sh" %}
```bash
#!/bin/sh

# Set ARG
PLATFORM=$1
if [ -z "$PLATFORM" ]; then
    ARCH="64"
else
    case "$PLATFORM" in
        linux/386)
            ARCH="32"
            ;;
        linux/amd64)
            ARCH="64"
            ;;
        linux/arm/v6)
            ARCH="arm32-v6"
            ;;
        linux/arm/v7)
            ARCH="arm32-v7a"
            ;;
        linux/arm64|linux/arm64/v8)
            ARCH="arm64-v8a"
            ;;
        linux/ppc64le)
            ARCH="ppc64le"
            ;;
        linux/s390x)
            ARCH="s390x"
            ;;
        *)
            ARCH=""
            ;;
    esac
fi
[ -z "${ARCH}" ] && echo "Error: Not supported OS Architecture" && exit 1

DOWNLOAD_LINK_GEOIP="https://github.com/v2fly/geoip/releases/latest/download/geoip.dat"
DOWNLOAD_LINK_GEOSITE="https://github.com/v2fly/domain-list-community/releases/latest/download/dlc.dat"
DOWNLOAD_LINK_H2Y="https://raw.githubusercontent.com/ToutyRater/V2Ray-SiteDAT/master/geofiles/h2y.dat"

# Download files
dir_tmp="$(mktemp -d)"
echo "Using Temp Dir: $dir_tmp"
V2RAY_FILE="v2ray-linux-${ARCH}.zip"
DGST_FILE="v2ray-linux-${ARCH}.zip.dgst"

file_ip='geoip.dat'
file_site='geosite.dat'
file_h2y='h2y.dat'

file_v2ray='v2ray.zip'
file_v2ray_dgst='v2ray.zip.dgst'

echo "Downloading binary file: ${V2RAY_FILE}"
echo "Downloading binary file: ${DGST_FILE}"

TAG=$(curl -L https://raw.githubusercontent.com/v2fly/docker/master/ReleaseTag | head -n1)
curl -L -o ${dir_tmp}/v2ray.zip https://github.com/v2fly/v2ray-core/releases/download/${TAG}/${V2RAY_FILE}
curl -L -o ${dir_tmp}/v2ray.zip.dgst https://github.com/v2fly/v2ray-core/releases/download/${TAG}/${DGST_FILE}

if [ $? -ne 0 ]; then
    echo "Error: Failed to download binary file: ${V2RAY_FILE} ${DGST_FILE}" && exit 1
fi
echo "Download binary file: ${V2RAY_FILE} ${DGST_FILE} completed"

# Check SHA512
LOCAL=$(openssl dgst -sha512 ${dir_tmp}/v2ray.zip | sed 's/([^)]*)//g')
STR=$(cat ${dir_tmp}/v2ray.zip.dgst | grep 'SHA512' | head -n1)

if [ "${LOCAL}" = "${STR}" ]; then
    echo " Check passed" && rm -fv v2ray.zip.dgst
else
    echo " Check have not passed yet " && exit 1
fi

echo "Downloading data files: ${file_ip}, ${file_site} & ${file_h2y}"
curl -L -o ${dir_tmp}/${file_h2y} ${DOWNLOAD_LINK_H2Y}
curl -L -o ${dir_tmp}/${file_site} ${DOWNLOAD_LINK_GEOSITE}
curl -L -o ${dir_tmp}/${file_ip} ${DOWNLOAD_LINK_GEOIP}
echo "Download data file: ${file_h2y} ${file_site} ${file_ip} completed"

cp -Rv ${dir_tmp}/* root/

rm -rfv ${dir_tmp}/

echo "Done"
```
{% endcode %}

{% code title="Dockerfile" %}
```bash
FROM alpine:latest
LABEL maintainer "V2Fly Community <dev@v2fly.org>"

WORKDIR /root

COPY ${PWD}/root /root

RUN set -ex \
	&& apk add --no-cache tzdata openssl ca-certificates \
	&& mkdir -p /etc/v2ray /usr/local/share/v2ray /var/log/v2ray \
	&& unzip v2ray.zip && chmod +x v2ray v2ctl \
	&& mv v2ray v2ctl /usr/bin/ \
	&& mv dat/*.dat /usr/local/share/v2ray/

VOLUME /etc/v2ray
CMD [ "/usr/bin/v2ray", "-confdir", "/etc/v2ray/" ]

```
{% endcode %}



