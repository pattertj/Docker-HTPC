# Docker-HTPC
A Docker Compose file + documentation for docker-based HTPC automation

## Docker-HTPC leverages:
- Bazaarr - for subtitles
- Deluge - Torrent Client
- ExpressVPN - VPN client
- Prowlarr - Torrent index aggregator
- Radarr - Movie Management
- Sonarr - TV Series Management

### Coming Later
- Ombi - Plex Media Request Portal
- Tautulli - Analytics

Docker-HTPC specifically does not include Plex, primarily due to the complexity of handling hardware passthrough for GPU encoding with Docker. You could add Plex to this docker-compose file if you wish, however I highly encourage installing Plex directly into a Linux VM in Proxmox for passthrough or directly onto your base OS suchas Windows or Linux.

## Setup Process
This guide assumes you have an existing Linux server with Docker and Docker-Compose installed, ExxpressVPN, and optionally Portainer.

### Install Docker
```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce

sudo systemctl status docker
```
### Add User to the docker group 
```
sudo usermod -aG docker ${USER}
su - ${USER}

id -nG
```
### Install Docker-Compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

### Install Portainer
```
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

1) Create our Folder Structure
```
mkdir -p mediastack/media/{movies,music,photos,tv}
mkdir -p mediastack/torrents/{movies,music,photos,tv}
```

Our resulting file structure should be:
```
mediastack
  |-media
    |-movies
    |-music
    |-photos
    |-tv
  |-torrents
    |-movies
    |-music
    |-photos
    |-tv
```

2) Take Ownership of the folders
```
chown -R administrator.administrator mediastack/media
chown -R administrator.administrator mediastack/torrents
```

3) Change Permissions on the folders
```
chmod -R 775 mediastack/media
chmod -R 775 mediastack/torrents
```

4) Create NFS Mappings (optional)

Open your fstab file to add Network File Storage (NFS) if needed.

    sudo nano /etc/fstab

Add the following lines:

    //<Network File Storage IP>/Storage/Plex/Movies /mediastack/media/movies cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/Music /mediastack/media/music cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/Photos /mediastack/media/photos cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/TV /mediastack/media/tv cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/Torrents /mediastack/torrents cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0

5) Confirm Ownership and Permissions
```
ls -lh
```
6) Reboot
```
sudo shutdown -r
```

7) Check Mappings, we should see our 4 mappings from fstab
```
df -h 
```
