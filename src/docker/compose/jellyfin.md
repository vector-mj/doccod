# Jellyfin

```yaml
services:
  jellyfin:
    image: docker.arvancloud.ir/jellyfin/jellyfin
    container_name: jellyfin
    user: '0'
    volumes:
      - ./config:/config
      - ./cache:/cache
      - type: bind
        source: ../downloads
        target: /media
        read_only: true
    ports:
      - 8096:8096
    restart: 'unless-stopped'
    # Optional - alternative address used for autodiscovery
    environment:
      - JELLYFIN_PublishedServerUrl=http://<YOUR-SERVER-ADDRESS>
    extra_hosts:
      - 'host.docker.internal:host-gateway'
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 300m
```