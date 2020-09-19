# traefik-ovpn-docker-swarm

For details, see the blog entry: [alex.thom.ae/2020/09/19/traefik-openvpn-docker-swarm/](https://alex.thom.ae/2020/09/19/traefik-openvpn-docker-swarm/)

## Folders
```sh
mkdir -p /var/docker/traefik
mkdir -p /var/docker/openvpn
```

## Variables
```sh
# OpenVPN variables
export OVPN_DATA="/var/docker/openvpn"
export ENDPOINT="vpn.example.com"
# Cloudflare Credentials
export CF_DNS_API_TOKEN="FOO"
export CF_ZONE_API_TOKEN="BAR"
# Spielwiese Domain
export SPIELWIESE_DOMAIN="spielwiese.example.com"
```

## Networks
```sh
docker network create --attachable --scope swarm --opt encrypted traefik-vpn
docker network create --attachable --scope swarm --opt encrypted vpn
docker network create --scope swarm --opt encrypted traefik-web
```

## Deploy
```sh
docker run -v "${OVPN_DATA}":/etc/openvpn --log-driver=none --rm ixdotai/openvpn ovpn_genconfig -u udp://"${ENDPOINT}" -b -Q -n 192.168.255.1 -n 1.1.1.1 -n 1.0.0.1
docker run -v "${OVPN_DATA}":/etc/openvpn --log-driver=none --rm -it ixdotai/openvpn ovpn_initpki
docker stack deploy vpn -c stack-vpn.yml
docker config create traefik_services.yml.v1 services.yml
docker stack deploy traefik -c stack-traefik.yml
docker stack deploy websites -c stack-websites.yml
```
