+++ title = 'Using an old laptop to create a Nextcloud and Plex server' date = 2024-03-12T14:30:00Z draft = false tags = ['hello-world', 'nextcloud', 'plex', 'docker', 'linux'] +++

[Nextcloud](https://nextcloud.com/) is an open source cloud storage and collaboration product. It allows access to files on the go from all kinds of devices and works across Linux, Windows, Android and more. I want to use this to host a media library while also allowing an easy way to upload files from lots of different devices.  

I also want to use Nextcloud to backup other cloud services that I use, including Google drive, photos and Whatsapp. 

[Plex](https://www.plex.tv/) Is a media streaming service which I can use to serve my media files on the go. It's available on lots of devices and can also be made available outside of the local network if needed, so you can access files on the go. 

## Prepping the laptop to run as a server
### Install ubunutu server, change default behaviours and mount the drive
```
sudo apt update -y && sudo apt upgrade -y

# mask sleep, suspend and hibernate so laptop won't shutdown and stop serving
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
sudo service systemd-logind restart
```

### Set a static IP for the server

### Enable portforwarding on the router

### Firewall settings on the server
```
#enable ufw, allow ssh and restart the service
sudo ufw enable
sudo ufw allow ssh

#Allow ports 80 and 8443 for nextcloud and port 32400 for plex
sudo ufw allow 80
sudo ufw allow 8443
sudo ufw allow 32400

sudo systemctl restart ssh
```

### Install docker
```
# Add Docker's official GPG key
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

## Add the repository to Apt sources
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#Run docker's hello world to check everything is working correctly
sudo docker run hello-world

#Remove docker from snap, this was causing issues with installing plex using docker compose
snap remove docker
reboot
```

### Add noip client for dynamic DNS
Follow noip's configuration guide for setting up the dynamic dns client on the laptop. 

### Install Nextcloud AIO
```
#Install nextcloud-aio using docker
sudo docker run --init --sig-proxy=false --name nextcloud-aio-mastercontainer --restart always --publish 80:80 --publish 8080:8080 --publish 8443:8443 --volume nextcloud_aio_mastercontainer:/mnt/docker-aio-config --volume /var/run/docker.sock:/var/run/docker.sock:ro nextcloud/all-in-one:latest
```

### Install Plex
```
#Create a compose.yml file to use with docker compose
vi /opt/stacks/plex/compose.yml
```

```/opt/stacks/plex/compose.yml
# Compose file for plex pms-docker
version: '3'
services:
  plex:
    container_name: plex
    image: plexinc/pms-docker
    restart: unless-stopped
    environment:
     - PLEX_UID=0
     - PLEX_GID=0
     - TZ=Europe/London
     - PLEX_CLAIM=claim-vaJbV8rLFr3QWdN7GbyT
    network_mode: host
    volumes:
     - ./config:/config
     - ./transcode:/transcode
     - /var/lib/docker/volumes/nextcloud_aio_nextcloud_data/_data/admin/files/Media:/data
    devices:
     - /dev/dri:/dev/dri
```

```
# Run docker compose from /opt/stacks/plex/
cd /opt/stacks/plex/
docker compose up -d
```



#programming 
#nextcloud
#plex
#obsidian 

