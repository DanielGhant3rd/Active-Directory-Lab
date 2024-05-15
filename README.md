# T-Pot: MAC_WIN
version: '3.9'

networks:
  tpotinit_local:
  adbhoney_local:
  ciscoasa_local:
  citrixhoneypot_local:
  cowrie_local:
  ddospot_local:
  dicompot_local:
  dionaea_local:
  elasticpot_local:
  heralding_local:
  ipphoney_local:
  mailoney_local:
  medpot_local:
  redishoneypot_local:
  sentrypeer_local:
  suricata_local:
  tanner_local:
  wordpot_local:
  nginx_local:
  ewsposter_local:

services:

########################################
#### DEV
########################################
#### T-Pot Init - Never delete this!
########################################

# T-Pot Init Service
  tpotinit:
    container_name: tpotinit
    env_file:
     - .env
    restart: always
    stop_grace_period: 60s
    tmpfs:
     - /tmp/etc:uid=2000,gid=2000
     - /tmp/:uid=2000,gid=2000
    networks:
     - tpotinit_local
    image: ${TPOT_REPO}/tpotinit:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DOCKER_COMPOSE}:/tmp/tpot/docker-compose.yml:ro
     - ${TPOT_DATA_PATH}/blackhole:/etc/blackhole
     - ${TPOT_DATA_PATH}:/data
     - /var/run/docker.sock:/var/run/docker.sock:ro


##################
#### Honeypots
##################

# Adbhoney service
  adbhoney:
    container_name: adbhoney
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - adbhoney_local
    ports:
     - "5555:5555"
    image: ${TPOT_REPO}/adbhoney:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/adbhoney/log:/opt/adbhoney/log
     - ${TPOT_DATA_PATH}/adbhoney/downloads:/opt/adbhoney/dl

# Ciscoasa service
  ciscoasa:
    container_name: ciscoasa
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    tmpfs:
     - /tmp/ciscoasa:uid=2000,gid=2000
    networks:
     - ciscoasa_local
    ports:
     - "5000:5000/udp"
     - "8443:8443"
    image: ${TPOT_REPO}/ciscoasa:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/ciscoasa/log:/var/log/ciscoasa

# CitrixHoneypot service
  citrixhoneypot:
    container_name: citrixhoneypot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - citrixhoneypot_local
    ports:
     - "443:443"
    image: ${TPOT_REPO}/citrixhoneypot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/citrixhoneypot/log:/opt/citrixhoneypot/logs

# Cowrie service
  cowrie:
    container_name: cowrie
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    tmpfs:
      - /tmp/cowrie:uid=2000,gid=2000
      - /tmp/cowrie/data:uid=2000,gid=2000
    networks:
      - cowrie_local
    ports:
      - "22:22"
      - "23:23"
    image: ${TPOT_REPO}/cowrie:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
      - ${TPOT_DATA_PATH}/cowrie/downloads:/home/cowrie/cowrie/dl
      - ${TPOT_DATA_PATH}/cowrie/keys:/home/cowrie/cowrie/etc
      - ${TPOT_DATA_PATH}/cowrie/log:/home/cowrie/cowrie/log
      - ${TPOT_DATA_PATH}/cowrie/log/tty:/home/cowrie/cowrie/log/tty

# Ddospot service
  ddospot:
    container_name: ddospot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - ddospot_local
    ports:
     - "19:19/udp"
#     - "53:53/udp"
     - "123:123/udp"
#     - "161:161/udp"
     - "1900:1900/udp"
    image: ${TPOT_REPO}/ddospot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/ddospot/log:/opt/ddospot/ddospot/logs
     - ${TPOT_DATA_PATH}/ddospot/bl:/opt/ddospot/ddospot/bl
     - ${TPOT_DATA_PATH}/ddospot/db:/opt/ddospot/ddospot/db

# Dicompot service
# Get the Horos Client for testing: https://horosproject.org/
# Get Dicom images (CC BY 3.0): https://www.cancerimagingarchive.net/collections/
# Put images (which must be in Dicom DCM format or it will not work!) into /data/dicompot/images
  dicompot:
    container_name: dicompot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - dicompot_local
    ports:
     - "11112:11112"
    image: ${TPOT_REPO}/dicompot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/dicompot/log:/var/log/dicompot
#     - ${TPOT_DATA_PATH}/dicompot/images:/opt/dicompot/images

# Dionaea service
  dionaea:
    container_name: dionaea
    stdin_open: true
    tty: true
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - dionaea_local
    ports:
     - "20:20"
     - "21:21"
     - "42:42"
     - "69:69/udp"
     - "81:81"
     - "135:135"
     # - "443:443"
     # - "445:445"
     - "1433:1433"
     - "1723:1723"
     - "1883:1883"
     - "3306:3306"
     # - "5060:5060"
     # - "5060:5060/udp"
     # - "5061:5061"
     - "27017:27017"
    image: ${TPOT_REPO}/dionaea:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/dionaea/roots/ftp:/opt/dionaea/var/dionaea/roots/ftp
     - ${TPOT_DATA_PATH}/dionaea/roots/tftp:/opt/dionaea/var/dionaea/roots/tftp
     - ${TPOT_DATA_PATH}/dionaea/roots/www:/opt/dionaea/var/dionaea/roots/www
     - ${TPOT_DATA_PATH}/dionaea/roots/upnp:/opt/dionaea/var/dionaea/roots/upnp
     - ${TPOT_DATA_PATH}/dionaea:/opt/dionaea/var/dionaea
     - ${TPOT_DATA_PATH}/dionaea/binaries:/opt/dionaea/var/dionaea/binaries
     - ${TPOT_DATA_PATH}/dionaea/log:/opt/dionaea/var/log
     - ${TPOT_DATA_PATH}/dionaea/rtp:/opt/dionaea/var/dionaea/rtp

# ElasticPot service
  elasticpot:
    container_name: elasticpot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - elasticpot_local
    ports:
     - "9200:9200"
    image: ${TPOT_REPO}/elasticpot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/elasticpot/log:/opt/elasticpot/log

# Heralding service
  heralding:
    container_name: heralding
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    tmpfs:
     - /tmp/heralding:uid=2000,gid=2000
    networks:
     - heralding_local
    ports:
    # - "21:21"
    # - "22:22"
    # - "23:23"
    # - "25:25"
    # - "80:80"
     - "110:110"
     - "143:143"
    # - "443:443"
     - "465:465"
     - "993:993"
     - "995:995"
    # - "3306:3306"
    # - "3389:3389"
     - "1080:1080"
     - "5432:5432"
     - "5900:5900"
    image: ${TPOT_REPO}/heralding:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/heralding/log:/var/log/heralding

# Ipphoney service
  ipphoney:
    container_name: ipphoney
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - ipphoney_local
    ports:
     - "631:631"
    image: ${TPOT_REPO}/ipphoney:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/ipphoney/log:/opt/ipphoney/log

# Mailoney service
  mailoney:
    container_name: mailoney
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    environment:
     - HPFEEDS_SERVER=
     - HPFEEDS_IDENT=user
     - HPFEEDS_SECRET=pass
     - HPFEEDS_PORT=20000
     - HPFEEDS_CHANNELPREFIX=prefix
    networks:
     - mailoney_local
    ports:
     - "25:25"
     - "587:25"
    image: ${TPOT_REPO}/mailoney:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/mailoney/log:/opt/mailoney/logs

# Medpot service
  medpot:
    container_name: medpot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - medpot_local
    ports:
     - "2575:2575"
    image: ${TPOT_REPO}/medpot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/medpot/log/:/var/log/medpot

# Redishoneypot service
  redishoneypot:
    container_name: redishoneypot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - redishoneypot_local
    ports:
     - "6379:6379"
    image: ${TPOT_REPO}/redishoneypot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/redishoneypot/log:/var/log/redishoneypot

# SentryPeer service
  sentrypeer:
    container_name: sentrypeer
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
#    environment:
#     - SENTRYPEER_PEER_TO_PEER=1
    networks:
     - sentrypeer_local
    ports:
#     - "4222:4222/udp"
     - "5060:5060/tcp"
     - "5060:5060/udp"
#     - "127.0.0.1:8082:8082"
    image: ${TPOT_REPO}/sentrypeer:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/sentrypeer/log:/var/log/sentrypeer

#### Snare / Tanner
## Tanner Redis Service
  tanner_redis:
    container_name: tanner_redis
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    tty: true
    networks:
     - tanner_local
    image: ${TPOT_REPO}/redis:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true

## PHP Sandbox service
  tanner_phpox:
    container_name: tanner_phpox
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    tty: true
    networks:
     - tanner_local
    image: ${TPOT_REPO}/phpox:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true

## Tanner API Service
  tanner_api:
    container_name: tanner_api
    restart: always
    depends_on:
     - tanner_redis
    tmpfs:
     - /tmp/tanner:uid=2000,gid=2000
    tty: true
    networks:
     - tanner_local
    image: ${TPOT_REPO}/tanner:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/tanner/log:/var/log/tanner
    command: tannerapi

## Tanner Service
  tanner:
    container_name: tanner
    restart: always
    depends_on:
     - tanner_api
     - tanner_phpox
    tmpfs:
     - /tmp/tanner:uid=2000,gid=2000
    tty: true
    networks:
     - tanner_local
    image: ${TPOT_REPO}/tanner:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    command: tanner
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/tanner/log:/var/log/tanner
     - ${TPOT_DATA_PATH}/tanner/files:/opt/tanner/files

## Snare Service
  snare:
    container_name: snare
    restart: always
    depends_on:
     - tanner
    tty: true
    networks:
     - tanner_local
    ports:
     - "80:80"
    image: ${TPOT_REPO}/snare:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}

# Wordpot service
  wordpot:
    container_name: wordpot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - wordpot_local
    ports:
     - "8080:80"
    image: ${TPOT_REPO}/wordpot:${TPOT_VERSION}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/wordpot/log:/opt/wordpot/logs/


##################
#### NSM
##################

# Fatt service
  fatt:
    container_name: fatt
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    network_mode: "host"
    cap_add:
     - NET_ADMIN
     - SYS_NICE
     - NET_RAW
    image: ${TPOT_REPO}/fatt:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DATA_PATH}/fatt/log:/opt/fatt/log

# P0f service
  p0f:
    container_name: p0f
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    network_mode: "host"
    cap_add:
     - NET_ADMIN
     - SYS_NICE
     - NET_RAW
    image: ${TPOT_REPO}/p0f:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/p0f/log:/var/log/p0f

# Suricata service
  suricata:
    container_name: suricata
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - suricata_local
    cap_add:
     - NET_ADMIN
     - SYS_NICE
     - NET_RAW
    environment:
     - OINKCODE=${OINKCODE:-OPEN} # Default to OPEN if unset or NULL (value provided by T-Pot .env)
    # Loading external Rules from URL
    # - FROMURL="https://username:password@yoururl.com|https://username:password@otherurl.com"
    image: ${TPOT_REPO}/suricata:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DATA_PATH}/suricata/log:/var/log/suricata


##################
#### Tools
##################

#### ELK
## Elasticsearch service
  elasticsearch:
    container_name: elasticsearch
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    environment:
     - bootstrap.memory_lock=true
     - ES_JAVA_OPTS=-Xms2048m -Xmx2048m
     - ES_TMPDIR=/tmp
    networks:
     - nginx_local
    cap_add:
     - IPC_LOCK
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    mem_limit: 4g
    ports:
     - "127.0.0.1:64298:9200"
    image: ${TPOT_REPO}/elasticsearch:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DATA_PATH}:/data

## Kibana service
  kibana:
    container_name: kibana
    restart: always
    depends_on:
      elasticsearch:
        condition: service_healthy
    networks:
     - nginx_local
    mem_limit: 1g
    ports:
     - "127.0.0.1:64296:5601"
    image: ${TPOT_REPO}/kibana:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}

## Logstash service
  logstash:
    container_name: logstash
    restart: always
    depends_on:
      elasticsearch:
        condition: service_healthy
    networks:
     - nginx_local
    environment:
     - LS_JAVA_OPTS=-Xms1024m -Xmx1024m
     - TPOT_TYPE=${TPOT_TYPE:-HIVE}
     - TPOT_HIVE_USER=${TPOT_HIVE_USER}
     - TPOT_HIVE_IP=${TPOT_HIVE_IP}
    ports:
      - "127.0.0.1:64305:64305"
    mem_limit: 2g
    image: ${TPOT_REPO}/logstash:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DATA_PATH}:/data

## Map Redis Service
  map_redis:
    container_name: map_redis
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - nginx_local
    stop_signal: SIGKILL
    tty: true
    image: ${TPOT_REPO}/redis:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true

## Map Web Service
  map_web:
    container_name: map_web
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - nginx_local
    environment:
     - MAP_COMMAND=AttackMapServer.py
    stop_signal: SIGKILL
    tty: true
    ports:
     - "127.0.0.1:64299:64299"
    image: ${TPOT_REPO}/map:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}

## Map Data Service
  map_data:
    container_name: map_data
    restart: always
    depends_on:
      elasticsearch:
        condition: service_healthy
    networks:
     - nginx_local
    environment:
     - MAP_COMMAND=DataServer_v2.py
     - TPOT_ATTACKMAP_TEXT=${TPOT_ATTACKMAP_TEXT}
     - TZ=${TPOT_ATTACKMAP_TEXT_TIMEZONE}
    stop_signal: SIGKILL
    tty: true
    image: ${TPOT_REPO}/map:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
#### /ELK

# Ewsposter service
  ewsposter:
    container_name: ewsposter
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - ewsposter_local
    environment:
     - EWS_HPFEEDS_ENABLE=false
     - EWS_HPFEEDS_HOST=host
     - EWS_HPFEEDS_PORT=port
     - EWS_HPFEEDS_CHANNELS=channels
     - EWS_HPFEEDS_IDENT=user
     - EWS_HPFEEDS_SECRET=secret
     - EWS_HPFEEDS_TLSCERT=false
     - EWS_HPFEEDS_FORMAT=json
    image: ${TPOT_REPO}/ewsposter:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DATA_PATH}:/data
     - ${TPOT_DATA_PATH}/ews/conf/ews.ip:/opt/ewsposter/ews.ip

# Nginx service
  nginx:
    container_name: nginx
    restart: always
    environment:
      - TPOT_OSTYPE=${TPOT_OSTYPE}
    depends_on:
      tpotinit:
        condition: service_healthy
    tmpfs:
     - /var/tmp/nginx/client_body
     - /var/tmp/nginx/proxy
     - /var/tmp/nginx/fastcgi
     - /var/tmp/nginx/uwsgi
     - /var/tmp/nginx/scgi
     - /run
     - /var/lib/nginx/tmp:uid=100,gid=82
    networks:
     - nginx_local
    ports:
     - "64297:64297"
    image: ${TPOT_REPO}/nginx:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    read_only: true
    volumes:
     - ${TPOT_DATA_PATH}/nginx/cert/:/etc/nginx/cert/:ro
     - ${TPOT_DATA_PATH}/nginx/conf/nginxpasswd:/etc/nginx/nginxpasswd:ro
     - ${TPOT_DATA_PATH}/nginx/log/:/var/log/nginx/

# Spiderfoot service
  spiderfoot:
    container_name: spiderfoot
    restart: always
    depends_on:
      tpotinit:
        condition: service_healthy
    networks:
     - nginx_local
    ports:
     - "127.0.0.1:64303:8080"
    image: ${TPOT_REPO}/spiderfoot:${TPOT_VERSION}
    pull_policy: ${TPOT_PULL_POLICY}
    volumes:
     - ${TPOT_DATA_PATH}/spiderfoot:/home/spiderfoot/.spiderfoot

<h1>Active Directory Home Lab</h1>

 ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo)

<h2>Description</h2>
Discription of Home Lab
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Diskpart</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

<h2>Program walk-through:</h2>

<p align="center">
Creating Users With Powershell <br/>
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
