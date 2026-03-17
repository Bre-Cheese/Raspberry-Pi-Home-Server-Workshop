# Raspberry Pi Home Server Workshop (Docker + Portainer) — Pi-Local

A **2-hour, beginner-friendly** workshop for 1st/2nd year engineering & computer science students (and anyone curious) showing how a Raspberry Pi can run real services at home using **Docker** and **Portainer**.

> OS flashing is **pre-done by the instructor**.  
> This workshop is completed **entirely on the Raspberry Pi** using a **monitor + keyboard + mouse** (no laptop SSH required).

---

## Table of Contents
- [0) Workshop flow (2 hours)](#0-workshop-flow-2-hours)
- [1) Why home servers are important](#1-why-home-servers-are-important)
- [2) Why Docker and Portainer?](#2-why-docker-and-portainer)
- [3) How to do it (hands-on lab)](#3-how-to-do-it-hands-on-lab)
  - [3.1 Open Terminal + update the Pi](#31-open-terminal--update-the-pi)
  - [3.2 Install Docker](#32-install-docker)
  - [3.3 Sanity check Docker](#33-sanity-check-docker)
  - [3.4 Install Portainer (Compose)](#34-install-portainer-compose)
  - [3.5 First Stack: `whoami`](#35-first-stack-whoami)
- [4) What now? Lightweight services mini-game](#4-what-now-lightweight-services-mini-game)
  - [4.1 Rules](#41-rules)
  - [4.2 Choose 2–3 services](#42-choose-23-services)
  - [4.3 Scoreboard](#43-scoreboard)
- [5) Scaling up to stronger hardware](#5-scaling-up-to-stronger-hardware)
- [Troubleshooting](#troubleshooting)
- [Safety notes](#safety-notes)
- [Sources](#sources)

---

## 0) Workshop flow (2 hours)

**End goal:** Docker installed + Portainer running + 2–3 services deployed.

- **0:00–0:10** Intro + safety + confirm internet works on each Pi
- **0:10–0:25** Why home servers?
- **0:25–0:40** Why Docker + Portainer?
- **0:40–1:05** Install Docker + verify
- **1:05–1:25** Install Portainer + learn the UI
- **1:25–1:55** Mini-game: deploy 2–3 services
- **1:55–2:00** Scaling up + wrap

---

## 1) Why home servers are important

A home server is a computer you control that provides services for you (and optionally your household/team). Even a Raspberry Pi can be a powerful learning + utility tool.

### What makes home servers valuable?

**Ownership & control**
- You choose what runs and when it updates.
- You choose where your data lives.
- You can remove services anytime.

**Privacy**
- Self-hosting can reduce how much personal data is sent to third parties.
- You can keep some services entirely on your local network.

**Real-world skills**
You’ll practice:
- Linux basics (users, permissions, services)
- Networking (ports, IP addresses, DNS)
- Deployment (containers, configuration, logs)
- Reliability (restart policies, updates, backups)

**Cost-effective**
Start small on a Pi; later reuse the same container configs on stronger hardware.

---

## 2) Why Docker and Portainer?

### Docker (containers)
Docker runs apps in isolated “boxes” with their own dependencies.

**Why that’s good:**
- **Repeatable installs:** fewer “it works on mine” problems
- **Clean OS:** less random package clutter
- **Quick updates:** pull a new image, restart
- **Easy migration:** move your setup from Pi → mini PC → server later

### Portainer (Docker management UI)
Portainer is a web dashboard for Docker.

You can:
- deploy stacks (Compose)
- start/stop containers
- view logs
- inspect volumes and networks

It’s perfect for beginners because it makes Docker visible and less scary.

---

## 3) How to do it (hands-on lab)

Everything here is done on the Pi.

### 3.1 Open Terminal + update the Pi
Open **Terminal** and run:

```bash
sudo apt update
sudo apt -y upgrade
```

Optional reboot:

```bash
sudo reboot
```

---

### 3.2 Install Docker
Install Docker:

```bash
curl -fsSL https://get.docker.com | sudo sh
```

Add your user to the docker group (so you don’t need `sudo docker`):

```bash
sudo usermod -aG docker $USER
```

Apply group change (simplest is reboot):

```bash
sudo reboot
```

---

### 3.3 Sanity check Docker
Back in Terminal:

```bash
docker version
docker run --rm hello-world
```

If `hello-world` runs, Docker works.

---

### 3.4 Install Portainer (Compose)

Create a stacks folder:

```bash
mkdir -p ~/stacks/portainer
cd ~/stacks/portainer
```

Create `docker-compose.yml`:

```yaml
version: "3.8"
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
      - "9443:9443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

volumes:
  portainer_data:
```

Start it:

```bash
docker compose up -d
docker ps
```

Open the Pi’s browser and go to:

- `http://127.0.0.1:9000`  (Portainer UI)

Create the admin user and select the **local** environment.

> Why `127.0.0.1`? It always works from the Pi itself and avoids IP/address confusion during a workshop.

---

### 3.5 First Stack: `whoami`

In Portainer:
- **Stacks → Add stack**
- Name: `whoami`
- Paste:

```yaml
version: "3.8"
services:
  whoami:
    image: traefik/whoami:latest
    container_name: whoami
    restart: unless-stopped
    ports:
      - "8080:80"
```

Deploy, then open:

- `http://127.0.0.1:8080`

---

## 4) What now? Lightweight services mini-game

### 4.1 Rules
Deploy **2–3 services** below using Portainer Stacks.

To “complete” a service:
- open the service page in the Pi browser, and
- show its logs in Portainer (or explain what it does).

### 4.2 Choose 2–3 services

#### A) Uptime Kuma (monitoring)
```yaml
version: "3.8"
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    restart: unless-stopped
    ports:
      - "3001:3001"
    volumes:
      - uptime_kuma_data:/app/data
volumes:
  uptime_kuma_data:
```
Open `http://127.0.0.1:3001`

Challenge: monitor `http://127.0.0.1:8080`

---

#### B) Dozzle (view Docker logs in a browser)
```yaml
version: "3.8"
services:
  dozzle:
    image: amir20/dozzle:latest
    container_name: dozzle
    restart: unless-stopped
    ports:
      - "9999:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
```
Open `http://127.0.0.1:9999`

Challenge: find `whoami` logs; refresh `whoami` to generate more.

---

#### C) File Browser (simple file manager)
```yaml
version: "3.8"
services:
  filebrowser:
    image: filebrowser/filebrowser:latest
    container_name: filebrowser
    restart: unless-stopped
    ports:
      - "8081:80"
    volumes:
      - filebrowser_data:/srv
      - filebrowser_db:/database
    environment:
      - FB_BASEURL=/
volumes:
  filebrowser_data:
  filebrowser_db:
```
Open `http://127.0.0.1:8081`

Challenge: upload a file and explain what a volume is.

---

#### D) Homepage (service dashboard)
```yaml
version: "3.8"
services:
  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    restart: unless-stopped
    ports:
      - "3000:3000"
    volumes:
      - homepage_config:/app/config
      - /var/run/docker.sock:/var/run/docker.sock:ro
volumes:
  homepage_config:
```
Open `http://127.0.0.1:3000`

Challenge: explain what data is stored in the volume.

---

### 4.3 Scoreboard (target 10 points)
- +2 deploy a service and show it works
- +2 find logs in Portainer and explain one meaningful line
- +1 explain one port mapping like `8081:80`
- +2 change one setting (env var / port / restart policy) and redeploy successfully
- +3 help another team debug (and explain the fix)

---

## 5) Scaling up to stronger hardware
With a stronger server you can:
- run more services simultaneously
- use better storage (SSD/NVMe, RAID/ZFS depending on platform)
- add reverse proxy + HTTPS
- run real monitoring/alerting
- do proper backups and automation

**Key idea:** Docker Compose makes migration easier: your setup can move from Pi → mini PC → server with minimal changes.

---

## Troubleshooting

**Docker permission denied**
```bash
sudo usermod -aG docker $USER
sudo reboot
```

**Port already in use**
Change the left side of `HOST:CONTAINER` (e.g., `8080:80` → `8082:80`) and redeploy.

**Portainer won’t open**
```bash
docker ps
docker logs portainer --tail 200
```

---

## Safety notes
- Don’t expose random ports to the public internet.
- Use strong passwords (especially for Portainer).
- Keep updated:
  ```bash
  sudo apt update && sudo apt -y upgrade
  ```
- Containers are easy to recreate; **volumes contain your real data**—back them up.

---

## Sources
(Attribution)
- https://itsfoss.gitlab.io/post/how-to-manage-docker-containers-using-portainer-in-linux/
- https://pimylifeup.com/raspberry-pi-docker/
- https://www.youtube.com/watch?v=AtgCcMjtqF0&t=2667s
