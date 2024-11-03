# Qbittorrent

```yaml
services:
  qbittorrent-nox:
    restart: always
    container_name: qbittorrent-nox
    environment:
      - QBT_LEGAL_NOTICE=confirm
      - QBT_VERSION=latest
      - QBT_WEBUI_PORT=8080
    image: docker.arvancloud.ir/qbittorrentofficial/qbittorrent-nox:latest
    ports:
      # for bittorrent traffic
      - 6881:6881/tcp
      - 6881:6881/udp
      # for WebUI
      - 8080:8080/tcp
    read_only: true
    stop_grace_period: 30m
    tmpfs:
      - /tmp
    tty: true
    volumes:
      - ./config:/config
      - ./downloads:/downloads
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 150m
```