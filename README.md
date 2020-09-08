# Pi-hole macvlan docker setup

This repository is meant to be used with the offical pihole docker image.

I've set up my pihole container to have its own ip address to get around issues with opening port 53 and host networking.
In this setup, I have my raspberry pi running on 192.168.8.2 and my pihole container runs on 192.168.8.4. Additionally,
you need to set up a second container ip so the container can talk to the raspberry pi itself; I've picked 192.168.8.20
for this purpose.

The conditional forwarding setup assumes you have a dhcp server running on 192.168.8.1. I use this with a unifi security gateway.

There are two values that must be replaced in `docker-compose.yml` and are marked with `CHANGEME`.

Setup:
- `cd /opt`
- `sudo git clone https://github.com/ryansch/docker-pihole pihole`
- Set password and timezone in `docker-compose.yml`.
- `sudo systemctl enable /opt/pihole/pihole.service`
- `docker pull pihole/pihole:latest`
- `sudo systemctl start pihole`
