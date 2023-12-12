# ARR Config that works for me

### Legal Disclaimer
This configuration and setup guide is intended for educational and informational purposes only. The user acknowledges that the services and configurations mentioned herein might involve accessing and using third-party services, and it is the user's responsibility to comply with all relevant laws and regulations. The author does not endorse or recommend the use of this setup for any illegal or unauthorized activities. Any actions taken by the user based on this information are at their own risk.

The entire process of setting up an 'ARR' is about 10 minutes, mainly revolving around switching between a number of tabs.

### Docker compose

- Firstly, re-add the openvpn credentials for Private Internet Access or modify to use another provider following Gluetun's settings.
- Second, change the mounted volume from /mnt/plex/movies to the correct directory depending on the system.


```yaml
version: '3'
services:
  gluetun:
    container_name: gluetun
    image: qmcgaw/gluetun
    cap_add:
      - NET_ADMIN
    devices:
      - /dev/net/tun:/dev/net/tun
    ports:
      - 8888:8888/tcp # HTTP proxy
      - 8388:8388/tcp # Shadowsocks TCP
      - 8388:8388/udp # Shadowsocks UDP
      - 8112:8112/tcp # Deluge WebUI
      - 9091:9091/tcp # Deluge TCP
      - 7878:7878/tcp # Radarr TCP
      - 8989:8989/tcp # Sonarr TCP
      - 9696:9696 # Prowlarr
      - 5055:5055 # Overseerr
    volumes:
      - ./gluetun:/gluetun
    environment:
      - VPN_SERVICE_PROVIDER=private internet access
      - VPN_TYPE=openvpn
      - OPENVPN_USER=
      - OPENVPN_PASSWORD=
      - TZ=Europe/London
      - PUID=1000
      - PGID=1000
    restart: unless-stopped

  prowlarr:
    image: linuxserver/prowlarr
    container_name: prowlarr
    network_mode: "service:gluetun" # Uses Gluetun's network
    volumes:
      - ./prowlarr:/config
      - ./downloads:/downloads
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    restart: unless-stopped

  deluge:
    image: linuxserver/deluge
    container_name: deluge
    network_mode: "service:gluetun" # Uses Gluetun's network
    volumes:
      - ./deluge:/config
      - ./downloads:/downloads
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    restart: unless-stopped

  radarr:
    image: linuxserver/radarr
    container_name: radarr
    network_mode: "service:gluetun" # Uses Gluetun's network
    volumes:
      - ./radarr:/config
      - ./downloads:/downloads # Access to Deluge download folder
      - /mnt/plex/movies:/movies # Expansion Drive
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    restart: unless-stopped

  sonarr:
    image: linuxserver/sonarr
    container_name: sonarr
    network_mode: "service:gluetun" # Uses Gluetun's network
    volumes:
      - ./sonarr:/config
      - ./downloads:/downloads # Access to Deluge download folder
      - /mnt/plex/tv:/tv # Expansion Drive
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    restart: unless-stopped

  overseerr:
    image: lscr.io/linuxserver/overseerr:latest
    container_name: overseerr
    network_mode: "service:gluetun" # Uses Gluetun's network
    environment:
      - LOG_LEVEL=debug
      - TZ=Europe/London
      - PORT=5055 #optional
      - PUID=1000
      - PGID=1000
    volumes:
      - ./overseerr/config:/app/config
    restart: unless-stopped
```

### After that

You will need to:

## Deluge Setup:
1. **Access Deluge:** Navigate to port `:8112` in your browser.
2. **Login:** The default password is usually 'deluge'. You can change this password on your first login. Remember to note down the new password as it's essential for Deluge-based authentication.
3. **Enable Plugins:** Click on 'Preferences' at the top of the page, select 'Plugins', and enable the 'Label' plugin. This is crucial for Radarr and Sonarr to detect downloaded content.

## Radarr and Sonarr Configuration:
- Both Radarr and Sonarr share similar setups.
- Access Radarr at port `:7878` and Sonarr at port `:8989`.
- In `Settings`:
    - Enable `Rename Movies` or `Rename Episodes` under `Media Management`.
    - Map corresponding mount folders like /tv or /movies to `Root Folders`.
    - Add a download client for `Deluge`
- In `General` settings, locate `Security`.
    - Copy both API keys to a notepad document for future use in Overseerr and Prowlarr.

## Prowlarr
- Add the Indexers you desire for content consumption.
- Access via `:9696`
- In `Settings`
    - Go to the `Apps` section and add both Radarr and Sonarr using their API keys. You should be able to use `localhost` due to the containers being on the same Docker network.
    - Press the `Sync App Indexers`, Radarr and Sonarr should both have new `Indexers` if the indexer supports the media type Sonarr and Radarr ask for.
    - Add a download client for Deluge (Not Strictly Required)

## Overseerr
- Navigate to `:5055`
- Login with Plex
- Follow the installation instructions, clicking `test` if boxes are grayed out.
- Add Radarr and Sonarr as `Default servers`