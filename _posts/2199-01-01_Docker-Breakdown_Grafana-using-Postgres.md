---
title: 'Docker Breakdown : Grafana using Postgres'
date: 2199-01-01
permalink: /posts/2199/01/Docker-Breakdown_Grafana-using-Postgres/
tags:
  - Docker-Compose
  - Docker
  - Linux
  - Grafana
---
Here we are taking a look at one of the Docker-Compose configurations that I have deployed inside of my portainer managed docker server.

## Networking
- I am using an established docker network called `myDockerBridge` as this network is not exposed and can only be accessed by other containers inside this network. 
```
networks:
  my_network:
    external: true
    name: myDockerBridge
```

## Database

```
services:
  grafana_postgres:
    container_name: grafana_postgres
    image: postgres:15
    restart: always
    environment:
      POSTGRES_DB: grafana_db
      POSTGRES_USER: grafana_usr
      POSTGRES_PASSWORD: CREATEaPASSWORD
    volumes:
      - /portainer/volumes/grafana/postgres/data:/var/lib/postgresql/data
    user: "0"
    networks:
      my_network:
        ipv4_address: 10.69.69.33
```

## Application

```
  grafana:
    image: grafana/grafana-enterprise:latest
    container_name: grafana
    restart: unless-stopped
    environment:
      GF_DATABASE_TYPE: postgres
      GF_DATABASE_HOST: grafana_postgres
      GF_DATABASE_NAME: grafana_db
      GF_DATABASE_USER: grafana_usr
      GF_DATABASE_PASSWORD: CREATEaPASSWORD
    ports:
      - '3000:3000'
    volumes:
      - /portainer/volumes/grafana/data:/var/lib/grafana
    user: "0"
    networks:
      my_network:
        ipv4_address: 10.69.69.34
```


# Full Docker-Compose File
```
version: "3.8"
services:
  grafana_postgres:
    container_name: grafana_postgres
    image: postgres:15
    restart: always
    environment:
      POSTGRES_DB: grafana_db
      POSTGRES_USER: grafana_usr
      POSTGRES_PASSWORD: CREATEaPASSWORD
    volumes:
      - /portainer/volumes/grafana/postgres/data:/var/lib/postgresql/data
    user: "0"
    networks:
      my_network:
        ipv4_address: 10.69.69.33

  grafana:
    image: grafana/grafana-enterprise:latest
    container_name: grafana
    restart: unless-stopped
    environment:
      GF_DATABASE_TYPE: postgres
      GF_DATABASE_HOST: grafana_postgres
      GF_DATABASE_NAME: grafana_db
      GF_DATABASE_USER: grafana_usr
      GF_DATABASE_PASSWORD: CREATEaPASSWORD
    ports:
      - '3000:3000'
    volumes:
      - /portainer/volumes/grafana/data:/var/lib/grafana
    user: "0"
    networks:
      my_network:
        ipv4_address: 10.69.69.34

networks:
  my_network:
    external: true
    name: myDockerBridge
```
