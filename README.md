# Pihole + macvlan + cloudflared DoH + cloudflare gateway

This repo sets up pihole with an upstream DoH connection to Cloudflare Gateway for custom traffic filtering.

I've set up my pihole container to have its own ip address to get around issues with opening port 53 and host networking.
In this setup my pihole container runs on 192.168.8.4. Additionally, you need to set up a second container ip so the 
container can talk to the raspberry pi itself; I've picked 192.168.8.20 for this purpose.

It also places the cloudflared container directly into the pihole container's network so that pihole can query it over 127.0.0.1.

The conditional forwarding setup assumes you have a dhcp server running on 192.168.8.1. I use this with a unifi security gateway.

Be sure to replace the values marked with `CHANGEME` in `compose.yml`.

## System Setup

```sh
# If on a recent ubuntu-server release on a rasp pi:
sudo apt update
sudo apt install linux-modules-extra-raspi
# reboot

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

## Container Time

Pull up https://github.com/pi-hole/docker-pi-hole#environment-variables so you can configure things here in a moment.

```sh
# Place the other files in this repo into `/opt/pihole`
cd /opt/pihole
# Replace the CHANGEME items in compose.yml. You probably want to set up Cloudflare Zero Trust to grab the TUNNEL_DNS_UPSTREAM.
# You should also change the 192.168.8.x subnet with the one your server/pi is running on. Make sure to set the macvlan ip (192.168.8.4 in this example) to something outside of your dhcp range.
docker compose up -d
docker compose logs -f
# Your pihole should be up and sending dns request through DoH to cloudflare!
```
