\*\*                                         Docker Mattermost

\*\*





This document summarizes all the commands and steps required to build a Mattermost docker.



\*\*STEP 1 :\*\* Creating docker-compose



Here's how it looks:

\```

services:

`  `postgres:

`    `image: postgres:13

`    `container\_name: mattermost-db

`    `restart: unless-stopped

`    `environment:

`      `POSTGRES\_DB: mattermost

`      `POSTGRES\_USER: mmuser

`      `POSTGRES\_PASSWORD: mmuser\_password

`      `PGDATA: /var/lib/postgresql/data/pgdata

`    `volumes:

`      `- ./dbdata:/var/lib/postgresql/data

`    `networks:

`      `docker\_net:

`        `ipv4\_address: 192.168.3.11



`  `mattermost:

`    `image: mattermost/mattermost-team-edition:latest

`    `container\_name: mattermost-app

`    `restart: unless-stopped

`    `ports:

`      `- '8065'

`    `environment:

`      `MM\_CONFIG: /mattermost/config/config.json

`      `MM\_DBTYPE: postgres

`      `MM\_SQLSETTINGS\_DATASOURCE: postgres://mmuser:mmuser\_password@postgres:5432/mattermost?sslmode=disable

`    `volumes:

`      `- ./mattermost/config:/mattermost/config:rw

`      `- ./mattermost/data:/mattermost/data:rw

`      `- ./mattermost/logs:/mattermost/logs:rw

`      `- ./mattermost/plugins:/mattermost/plugins:rw

`      `- ./mattermost/client-plugins:/mattermost/client-plugins:rw

`      `- /tmp:/tmp:rw

`    `depends\_on:

`      `- postgres

`    `networks:

`      `docker\_net:

`        `ipv4\_address: 192.168.3.10



`  `nginx:

`    `image: nginx:alpine

`    `container\_name: mattermost-nginx

`    `restart: unless-stopped

`    `ports:

`      `- "80:80"

`      `- "443:443"

`    `volumes:

`      `- ./nginx/conf.d:/etc/nginx/conf.d

`      `- ./nginx/certs:/etc/nginx/certs

`      `- ./nginx/log:/var/log/nginx

`    `networks:

`      `docker\_net:

`        `ipv4\_address: 192.168.3.15



networks:

`  `docker\_net:

`    `driver: bridge

`    `ipam:

`      `config:

`        `- subnet: 192.168.3.0/24

\```

\*\*NB:\*\* I use for this docker, a local DNS



\*\*STEP 2 :\*\* Change ownership of your volumes

\```

sudo chown -R 2000:2000 your\_volumes\_path\_/mattermost

\```



\*\*STEP 3 :\*\* Run your docker-compose

\```

docker-compose up –d

\```



\*\*STEP 4 :\*\* Test





![](Aspose.Words.2f69e40f-094e-4967-889b-a9fbce723181.001.png)
