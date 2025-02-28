# Pihole + macvlan + NextDNS

This repo sets up pihole with an upstream DoH connection to NextDNS for custom traffic filtering.

I've set up my pihole container to have its own ip address to get around issues with opening port 53 and host networking.
In this setup my pihole container runs on 192.168.8.4. Additionally, you need to set up a second container ip so the
container can talk to the raspberry pi itself; I've picked 192.168.8.20 for this purpose.

It also places the NextDNS container directly into the pihole container's network so that pihole can query it over 127.0.0.1.

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
    gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Pin this pi's DNS to something other than the pi hole.
sudo echo "DNS=1.1.1.1" >> /etc/systemd/resolved.conf
sudo echo "FallbackDNS=8.8.8.8" >> /etc/systemd/resolved.conf
sudo systemctl restart systemd-resolved
```

## Container Time

Pull up <https://github.com/pi-hole/docker-pi-hole#environment-variables> so you can configure things here in a moment.

```sh
# Place the other files in this repo into `/opt/pihole`
cd /opt/pihole
# Create `.env` file and replace CHANGEME values. You probably want to set up NextDNS as your dns_revServer.
# You should also change the 192.168.8.x subnet with the one your server/pi is running on. Make sure to set the macvlan ip (192.168.8.4 in this example) to something outside of your dhcp range.
sudo systemctl enable --now /opt/pihole/pihole.service
docker compose logs -f
# Your pihole should be up and sending dns requests through DoH to NextDNS!
```
