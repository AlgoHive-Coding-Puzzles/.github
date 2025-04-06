<p align="center">
  <img width="150px" src="https://raw.githubusercontent.com/AlgoHive-Coding-Puzzles/Ressources/refs/heads/main/images/algohive-logo.png" title="Algohive">
</p>

<h1 align="center">AlgoHive</h1>

<p align="center"><i><b>[Project under "active" development, some features may be unstable or change in the future. A first release version is planned to be packed soon].</b></i></p>

<p align="center">Algohive is a <b>self-hosted coding game platform.</b><br>that allows developers to create puzzles for developers to solve.</p>

<p align="center">
  <img src="https://img.shields.io/badge/Total lines-70k-blue">
  <img src="https://img.shields.io/badge/Microservices count-11-red">
  <img src="https://img.shields.io/badge/Total download-98-green">
  <a href="https://github.com/AlgoHive-Coding-Puzzles/AlgoHive-Infra/tree/main/production">
    <img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=fff">
  </a>
  <img src="https://img.shields.io/badge/System health-healthy-green">
</p>

---

Each puzzle contains two parts to solve, allowing developers to test their skills in a variety of ways. The puzzles are created using a proprietary file format that is compiled into a single file for distribution.

> Sweeten your skills, but the competition stings

---

<p align="center">
<img src="https://skillicons.dev/icons?i=go,ts,py,react,vite,postgres,redis,docker,githubactions,grafana,prometheus">
</p>

---

<p align="center">
  <img src="https://raw.githubusercontent.com/AlgoHive-Coding-Puzzles/Documentation/refs/heads/main/images/algohive-welcome.png">
</p>

## Why

Algohive is a coding game plateform for developers by developers. It is a self-hosted solution that allows developers, schools, and companies to create puzzles for other developers to solve. The platform is designed to be lightweight and easy to use, with a focus on creating and solving puzzles.

Most of the coding game platforms are usable one time only, and are not self-hosted. This means that the puzzles are not reusable, and the platform is not customizable.

### Flexibility and Adaptability

AlgoHive is built with a core philosophy of avoiding hardcoded limitations. Everything in the platform is configurable and customizable to suit your needs. The system is designed to be deployed across various scales:

- For a small group of developer friends collaborating on coding challenges
- For companies with multiple teams wanting to enhance their technical skills
- For educational institutions with hundreds of campuses, classes, and student groups

The hierarchical structure is highly parameterizable, allowing you to adapt the platform to your specific organizational needs. Whether you need simple flat organization or complex nested groups with different permission levels, AlgoHive can be configured accordingly.

<img src="https://raw.githubusercontent.com/AlgoHive-Coding-Puzzles/Documentation/refs/heads/main/docs/AlgoHivePermissions.drawio.png">

All this flexibility comes without sacrificing performance - the platform remains lightweight and resource-efficient, making it suitable for deployment in various environments.

## Trusted by

<p align="center">
  <img src="https://etudescreatives.com/wp-content/uploads/2022/11/ynov-campus.png" width="150px" title="Ynov Campus">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Ynov-Toulouse Campus-blue">
  <img src="https://img.shields.io/badge/Ynov-Lyon Campus-orange">
  <img src="https://img.shields.io/badge/Ynov-Montpellier Campus-red">
</p>

## Features Highlights

- 🗃️ A file format created for defining puzzles [.alghive]()
- 🧪 A Pypi Python library for implementing in your own solutions : [HiveCraft](https://github.com/AlgoHive-Coding-Puzzles/HiveCraft)
- ⌨️ A CLI forge for creating, testing, and managing puzzles : [BeeLine](https://github.com/AlgoHive-Coding-Puzzles/BeeLine)
- 🐝 A GitHub Action workflow for automatically test and compile your puzzles [BeeToFlow](https://github.com/AlgoHive-Coding-Puzzles/BeeToFlow)
- 🖨️ A self-hostable API for loading and serving puzzles : [BeeAPI](https://github.com/AlgoHive-Coding-Puzzles/BeeAPI)
- 📦 A self-hostable Web interface for managing the puzzles inside the BeeAPIs : [BeeHub](https://github.com/AlgoHive-Coding-Puzzles/BeeHub)
- 🧩 A self-hostable web platform for managing users, competitions, and puzzles : [AlgoHive Client/API](https://github.com/AlgoHive-Coding-Puzzles/AlgoHive-Client)

## Architecture

<p align="center">
  <img src="https://raw.githubusercontent.com/AlgoHive-Coding-Puzzles/Documentation/refs/heads/main/docs/AlgoHiveArchi.drawio.png">
</p>

## Quick Start

Installing Alghive is pretty straight forward, in order to do so follow these steps:

- Create a folder where you want to place all the Algohive related files.
- Inside that folder, create a file named docker-compose.yml with this content:

```yaml
name: algohive
services:
  algohive-server:
    container_name: algohive-server
    image: ghcr.io/algohive-coding-puzzles/api:latest
    env_file: server.env
    depends_on:
      - algohive-db
      - algohive-cache
    restart: unless-stopped
    ports:
      - "8001:8080"
    networks:
      - algohive-network
    environment:
      - TZ=Europe/Paris
    volumes:
      - /etc/localtime:/etc/localtime:ro

  algohive-client:
    container_name: algohive-client
    image: ghcr.io/algohive-coding-puzzles/client:latest
    restart: unless-stopped
    ports:
      - "7002:80"
    networks:
      - algohive-network
    environment:
      - TZ=Europe/Paris
    volumes:
      - /etc/localtime:/etc/localtime:ro

  beehub:
    container_name: beehub
    image: ghcr.io/algohive-coding-puzzles/beehub:latest
    ports:
      - "8002:8081"
    networks:
      - algohive-network
    env_file: server.env
    environment:
      - TZ=Europe/Paris
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - beehub-db:/app/backend/db
    depends_on:
      - beeapi-server-first
      - beeapi-server-second

  beeapi-server-first:
    container_name: beeapi-server-first
    image: ghcr.io/algohive-coding-puzzles/beeapi-go:latest
    restart: unless-stopped
    volumes:
      - puzzles-first:/app/puzzles
    ports:
      - "5000:5000"
    networks:
      - algohive-network
    environment:
      - TZ=Europe/Paris
      - SERVER_NAME=BeeAPI-First
      - SERVER_DESCRIPTION=This is the server containing puzzles for the first scope
    labels:
      - "algohive.service.type=beeapi"

  beeapi-server-second:
    container_name: beeapi-server-second
    image: ghcr.io/algohive-coding-puzzles/beeapi-go:latest
    restart: unless-stopped
    volumes:
      - puzzles-second:/app/puzzles
    ports:
      - "5001:5000"
    networks:
      - algohive-network
    environment:
      - TZ=Europe/Paris
      - SERVER_NAME=BeeAPI-Second
      - SERVER_DESCRIPTION=This is the server containing puzzles for the second scope
    labels:
      - "algohive.service.type=beeapi"

  algohive-db:
    container_name: algohive-db
    image: postgres:17-alpine
    restart: unless-stopped
    env_file: server.env
    environment:
      - TZ=Europe/Paris
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - db-data:/var/lib/postgresql/data
    ports:
      - "5434:5432"
    networks:
      - algohive-network

  algohive-cache:
    container_name: algohive-cache
    image: redis:alpine
    restart: always
    ports:
      - "6379:6379"
    networks:
      - algohive-network
    environment:
      - TZ=Europe/Paris
    volumes:
      - /etc/localtime:/etc/localtime:ro

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"
      - "--storage.tsdb.path=/prometheus"
      - "--storage.tsdb.retention.time=5d"
      - "--web.console.libraries=/etc/prometheus/console_libraries"
      - "--web.console.templates=/etc/prometheus/consoles"
      - "--web.enable-lifecycle"
    ports:
      - "9090:9090"
    networks:
      - algohive-network
    restart: unless-stopped
    environment:
      - TZ=Europe/Paris

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
    environment:
      - TZ=Europe/Paris
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_USERS_ALLOW_SIGN_UP=false
    ports:
      - "3000:3000"
    networks:
      - algohive-network
    depends_on:
      - prometheus
    restart: unless-stopped

volumes:
  db-data:
  puzzles-first:
  puzzles-second:
  beehub-db:
  grafana_data:
  prometheus_data:

networks:
  algohive-network:
```

- Create a file named server.env with this content:

```env
#
# DB
#
POSTGRES_HOST=algohive-db
POSTGRES_PORT=5432
POSTGRES_DB=algohive
POSTGRES_USER=algohive
POSTGRES_PASSWORD=algohive

#
# JWT
#
JWT_SECRET=algohive
JWT_EXPIRATION=86400

#
# Server
#
API_PORT=8080
ENV=production
ALLOWED_ORIGINS=*
DEFAULT_PASSWORD=algohive
BEE_APIS=http://beeapi-server-first:5000,http://beeapi-server-second:5000
CLIENT_URL=http://localhost
MAIL_USERNAME=admin.admin@admin.com
MAIL_PASSWORD=
MAIL_PORT=587
MAIL_HOST=smtp.admin.com

#
# Cache
#
CACHE_TTL=86400
CACHE_HOST=algohive-cache
CACHE_PASSWORD=
CACHE_DB=0
CACHE_PORT=6379
CACHE_EXPI_MIN=30

#
# BeeHub
#
DATABASE_URL=sqlite:///./db/beehub.db
SECRET_KEY=superSecretPassword
ACCESS_TOKEN_EXPIRE_MINUTES=1440  # 24 hours
ADMIN_USERNAME=admin
ADMIN_PASSWORD=admin
APP_PORT=8081
DISCOVERY_ENABLED=true
DISCOVERY_PORT_RANGE_START=5000
DISCOVERY_PORT_RANGE_END=5099
DISCOVERY_SERVICE_TYPE=beeapi
DISCOVERY_REFRESH_INTERVAL=60
DISCOVERY_URLS=http://beeapi-server-first:5000,http://beeapi-server-second:5000
```

- Start the services by running the following command:

```bash
docker-compose up -d --build
```

> Useful if you don't want to use the `BeeHub` web interface to manage the puzzles.

- Authorize access to the BeeAPI folder(s) for every instance of the BeeAPI server:

> This will allow you to put puzzles in the folder and have them available in the API.

````bash
sudo chown -R $USER:$USER ./data/beeapi-server-first/puzzles
```
````
