# Audiojacker - All-in-One Media Download & Streaming Stack

A comprehensive Docker Compose setup for automated media downloading and streaming with VPN protection.

## 🎯 Features

- **VPN Protection**: All download services route through ProtonVPN via gluetun
- **Torrent Downloads**: qBittorrent with Web UI
- **Music Management**: Lidarr for automated music collection management
- **Soulseek Integration**: slskd client + soularr for Lidarr integration
- **YouTube Downloads**: yubal for audio extraction
- **Torbox Integration**: Automated debrid downloading
- **Audiobook Streaming**: audiobookshelf server
- **Music Streaming**: Koel self-hosted music player

## 📋 Services Overview

| Service | Purpose | Port | Network |
|---------|---------|------|---------|
| gluetun | VPN Gateway | - | Bridge |
| qBittorrent | Torrent Client | 8080 | Via VPN |
| Lidarr | Music Manager | 8686 | Via VPN |
| slskd | Soulseek Client | 5000 | Via VPN |
| soularr | Lidarr-Soulseek Bridge | 8400 | Via VPN |
| yubal | YouTube Downloader | 8888 | Via VPN |
| torbox-auto-downloader | Torbox Integration | - | Via VPN |
| audiobookshelf | Audiobook Server | 13378 | Direct |
| koel | Music Streaming | 8000 | Direct |

## 🚀 Quick Start

### 1. Prerequisites

- Docker and Docker Compose installed
- ProtonVPN account (OpenVPN credentials)
- Soulseek account (for slskd)
- Torbox account (optional, for torbox-auto-downloader)

### 2. Clone/Create Directory Structure

```bash
# Create the base directory
sudo mkdir -p ./audiojacker/{downloads,audiobooks,music,config}

# Create subdirectories
sudo mkdir -p ./audiojacker/downloads/{torrents,soulseek,youtube,torbox,complete}
sudo mkdir -p ./audiojacker/music/{incoming,library}
```

### 3. Configure Environment

```bash
# Copy the example environment file
cp .env.example .env

# Edit with your credentials
nano .env
```

**Required credentials to fill in:**

| Variable | Where to get it |
|----------|-----------------|
| `PROTONVPN_USER` | [ProtonVPN Account](https://account.protonvpn.com/settings) → OpenVPN/IKEv2 username |
| `PROTONVPN_PASSWORD` | [ProtonVPN Account](https://account.protonvpn.com/settings) → OpenVPN/IKEv2 password |
| `SOULSEEK_USER` | [Soulseek](https://www.slsknet.org) account |
| `SOULSEEK_PASS` | [Soulseek](https://www.slsknet.org) password |
| `TORBOX_API_KEY` | [Torbox](https://torbox.app/settings) API key |

### 4. Set Permissions

```bash
# Set ownership (replace with your PUID:PGID)
sudo chown -R 1000:1000 ./audiojacker

# Set permissions
sudo chmod -R 755 ./audiojacker
```

### 5. Start the Stack

```bash
# Start all services
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f
```

### 6. Access Web Interfaces

| Service | URL | Default Credentials |
|---------|-----|---------------------|
| qBittorrent | http://localhost:8080 | `admin` / `adminadmin` |
| Lidarr | http://localhost:8686 | None (set on first access) |
| slskd | http://localhost:5000 | Set in .env (`SLSKD_ADMIN_USER/PASS`) |
| soularr | http://localhost:8400 | - |
| yubal | http://localhost:8888 | - |
| audiobookshelf | http://localhost:13378 | None (set on first access) |
| koel | http://localhost:8000 | Set in .env (`KOEL_ADMIN_EMAIL/PASS`) |

## 📁 Directory Structure

```
./audiojacker/
├── downloads/
│   ├── torrents/          # qBittorrent downloads
│   ├── soulseek/          # slskd downloads
│   ├── youtube/           # yubal downloads
│   ├── torbox/            # Torbox downloads
│   └── complete/          # Completed downloads
├── audiobooks/            # Audiobook library
├── music/
│   ├── incoming/          # Music being processed
│   └── library/           # Organized music library
└── config/
    ├── gluetun/
    ├── qbittorrent/
    ├── lidarr/
    ├── slskd/
    ├── soularr/
    ├── yubal/
    ├── torbox/
    ├── audiobookshelf/
    └── koel/
```

## ⚙️ Configuration

### ProtonVPN Setup

1. Log into [ProtonVPN](https://account.protonvpn.com/settings)
2. Go to **Downloads** → **OpenVPN configuration**
3. Note your **OpenVPN / IKEv2 username** and **password**
4. (Optional) Choose preferred server countries in `.env`

### Lidarr Setup

1. Access Lidarr at http://localhost:8686
2. Complete the setup wizard
3. Add your music library path: `/music/library`
4. Add download client: qBittorrent at `localhost:8080`
5. Get API key from **Settings** → **General** → **Security**
6. Update `.env` with `LIDARR_API_KEY`
7. Restart soularr: `docker-compose restart soularr`

### slskd Setup

1. Access slskd at http://localhost:5000
2. Login with credentials from `.env`
3. Configure share directories in **Shares** settings
4. Add `/music` as a share directory

### soularr Setup

soularr connects Lidarr with Soulseek for automated downloads:

1. Ensure Lidarr is configured and running
2. Ensure slskd is configured and running
3. soularr will automatically search Soulseek for Lidarr requests

### qBittorrent Setup

1. Access qBittorrent at http://localhost:8080
2. Login with `admin` / `adminadmin`
3. **Change password immediately** in **Tools** → **Options** → **Web UI**
4. Set download path to `/downloads`
5. Configure connection settings as needed

### audiobookshelf Setup

1. Access audiobookshelf at http://localhost:13378
2. Create admin account on first access
3. Add library pointing to `/audiobooks`
4. Configure metadata provider preferences

### Koel Setup

1. Access Koel at http://localhost:8000
2. Login with credentials from `.env`
3. Scan music library: `/music`
4. Configure settings as needed

## 🔧 Maintenance

### View Logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f gluetun
docker-compose logs -f qbittorrent
```

### Restart Services

```bash
# Restart all
docker-compose restart

# Restart specific service
docker-compose restart gluetun
```

### Update Services

```bash
# Pull latest images
docker-compose pull

# Recreate containers with new images
docker-compose up -d
```

### Stop Services

```bash
# Stop all
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

## 🐛 Troubleshooting

### VPN Not Connecting

```bash
# Check gluetun logs
docker-compose logs gluetun

# Common issues:
# - Wrong OpenVPN credentials
# - Server country not available
# - Network connectivity issues
```

### Services Can't Connect Through VPN

Ensure services are using `network_mode: service:gluetun` and gluetun is healthy:

```bash
# Check gluetun health
docker-compose ps gluetun

# Check VPN connection
docker-compose exec gluetun curl -s https://ipinfo.io
```

### Permission Issues

```bash
# Check current PUID/PGID
id

# Update .env with correct values
# Fix ownership
sudo chown -R $(id -u):$(id -g) /mnt/fast-block/audiojacker
```

### Port Conflicts

If default ports are in use, modify the port mappings in `.env`:

```bash
# Example: Change qBittorrent port
QBITTORRENT_PORT=18080
```

## 📚 Additional Resources

- [gluetun Wiki](https://github.com/qdm12/gluetun/wiki)
- [qBittorrent Documentation](https://github.com/linuxserver/docker-qbittorrent)
- [Lidarr Documentation](https://lidarr.audio/)
- [slskd Documentation](https://github.com/slskd/slskd)
- [soularr GitHub](https://github.com/guillevc/yubal)
- [audiobookshelf Documentation](https://www.audiobookshelf.org/)
- [Koel Documentation](https://koel.dev/)

## 📜 License

This configuration is provided as-is. Individual services have their own licenses.

## ⚠️ Disclaimer

This setup is for personal use with legally obtained content. Ensure compliance with copyright laws in your jurisdiction. The authors are not responsible for any misuse of this software.