# Docker-HTPC
Docker Compose file + documentation for a docker-based HTPC automation

Docker-HTPC is a docker-compose script and auxillary instructions for setting up a docker-based HTPC automation workflow. Docker-HTPC leverages:

- Bazaarr - for subtitles
- Deluge - Torrent Client
- ExpressVPN - VPN client
- Ombi - Plex Media Request Portal
- Prowlarr - Torrent index aggregator
- Radarr - Movie Management
- Sonarr - TV Series Management
- Tautulli - Analytics

Docker-HTPC does not include Plex, primarily due to the complexity of handling hardware passthrough for GPU encoding with Docker. You could add Plex to this docker-compose file if you wish, however I highly encourage installing Plex directly into a Linux VM in Proxmox for passthrough or directly onto your base OS suchas Windows or Linux.

# Setup Process
1) Create NFS Mappings (optional)

Open your fstab file to add Network File Storage (NFS) if needed.

    sudo nano /etc/fstab

Add the following lines:

    //<Network File Storage IP>/Storage/Plex/Movies /mediastack/media/movies cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/Music /mediastack/media/music cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/Photos /mediastack/data/photos cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/TV /mediastack/media/tv cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0
    //<Network File Storage IP>/Storage/Plex/Downloads /Downloads cifs username=<NFS Username>,password=<NFS Password>,uid=1000,gid=1000 0 0

2) Create Folder Structure

-mediastack
  |-data
    |-movies
    |-music
    |-pictures
    |-tv
  |-torrents
    |-movies
    |-music
    |-pictures
    |-tv
  |-usenet
    |-movies
    |-music
    |-pictures
    |-tv

mkdir -p mediastack/data/{music,photos,movies,tv}
mkdir -p mediastack/torrents/{music,photos,movies,tv}
mkdir -p mediastack/usenet/{music,photos,movies,tv}

# Change Ownership
    sudo chown -R administrator.administrator data
    sudo chown -R administrator.administrator torrents
    sudo chown -R administrator.administrator usenet

# Change Permissions
    chmod -R 775 data
    chmod -R 775 torrents
    chmod -R 775 usenet

# Confirm Ownership and Permissions
    ls -lh

# Reboot

# Check Mappings, we should see our 4 mappings from fstab
    df -h 
