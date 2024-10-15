## Docker Mattermost
This document summarizes all the commands and steps required to build a Mattermost docker.

**STEP 1 :** Creating docker-compose

Here's how it looks:
```
services:
  postgres:
    image: postgres:13
    container_name: mattermost-db
    restart: unless-stopped
    environment:
      POSTGRES_DB: mattermost
      POSTGRES_USER: mmuser
      POSTGRES_PASSWORD: mmuser_password
      PGDATA: /var/lib/postgresql/data/pgdata
    volumes:
      - ./dbdata:/var/lib/postgresql/data
    networks:
      docker_net:
        ipv4_address: 192.168.3.11

  mattermost:
    image: mattermost/mattermost-team-edition:latest
    container_name: mattermost-app
    restart: unless-stopped
    ports:
      - '8065'
    environment:
      MM_CONFIG: /mattermost/config/config.json
      MM_DBTYPE: postgres
      MM_SQLSETTINGS_DATASOURCE: postgres://mmuser:mmuser_password@postgres:5432/mattermost?sslmode=disable
    volumes:
      - ./mattermost/config:/mattermost/config:rw
      - ./mattermost/data:/mattermost/data:rw
      - ./mattermost/logs:/mattermost/logs:rw
      - ./mattermost/plugins:/mattermost/plugins:rw
      - ./mattermost/client-plugins:/mattermost/client-plugins:rw
      - /tmp:/tmp:rw
    depends_on:
      - postgres
    networks:
      docker_net:
        ipv4_address: 192.168.3.10
        
  nginx:
    image: nginx:alpine
    container_name: mattermost-nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d
      - ./nginx/certs:/etc/nginx/certs
      - ./nginx/log:/var/log/nginx
    networks:
      docker_net:
        ipv4_address: 192.168.3.15

networks:
  docker_net:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.3.0/24
```
**NB:** I use for this docker, a local DNS

**STEP 2 :** Change ownership of your volumes
```
sudo chown -R 2000:2000 your_volumes_path_/mattermost
```

**STEP 3 :** Run your docker-compose
```
docker-compose up –d
```

**STEP 4 :** Test
