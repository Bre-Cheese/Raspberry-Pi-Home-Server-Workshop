# Raspberry Pi Home Server Workshop (Docker + Portainer) — Localhost Only

A **2-hour, beginner-friendly** workshop for 1st/2nd year engineering & computer science students (and anyone curious) showing how a Raspberry Pi can run real services at home using **Docker** and **Portainer**.

> OS flashing is **pre-done by the instructor**.  
> This workshop is completed **entirely on the Raspberry Pi** using a **monitor + keyboard + mouse**.

---

## School Wi‑Fi / Networking limitation (important)

On many campus Wi‑Fi networks, **inbound connections between devices are blocked** (often called *client isolation*).  
So even if your Pi has an IP address, other devices usually **cannot** reach it.

**For this workshop we will use localhost only:**
- Portainer: `http://127.0.0.1:9000`
- Services: `http://127.0.0.1:<port>` (example: `http://127.0.0.1:8080`)

This works because the browser and the services are running on the **same Raspberry Pi**.

---

## Table of Contents
- [0) Workshop flow (2 hours)](#0-workshop-flow)
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

## 0) Workshop flow

**End goal:** Docker installed + Portainer running + 2–3 services deployed.

  1- Intro + safety + confirm internet works on each Pi
  2- Why home servers?
  3- Why Docker + Portainer?
  4- Install Docker + verify
  5- Install Portainer + learn the UI
  6- Mini-game: deploy 2–3 services
  6- Scaling up + wrap

---

## 1) Why home servers are important

A home server is a computer you control that provides services for you (and optionally your household/team). Even a Raspberry Pi can be a powerful learning + utility platform.

### What makes home servers valuable?

**Ownership & control**
- You choose what runs and when it updates.
- You choose where your data lives.
- You can remove services anytime.

**Privacy**
- Self-hosting can reduce how much personal data is sent to third parties.
- You can keep services entirely on your own network.

**Real-world skills**
You’ll practice:
- Linux basics (users, permissions, services)
- Networking (ports, localhost vs LAN, basic security thinking)
- Deployment (containers, configuration, logs)
- Reliability (restart policies, updates, backups)

**Cost-effective**
Start small on a Pi; later reuse the same container configs on stronger hardware.

---

## 2) Why Docker and Portainer?

### Docker (containers)
Docker runs apps in isolated “boxes” (containers) with their own dependencies.

**Why that’s good:**
- Repeatable installs (less “it works on my machine”)
- Cleaner OS (less random package clutter)
- Easy updates (pull image, restart container)
- Easier migration to stronger hardware later

### Portainer (Docker management UI)
Portainer is a web dashboard for Docker. You can:
- deploy stacks (Docker Compose)
- start/stop containers
- view logs
- inspect volumes and networks

It’s perfect for workshops because it provides quick visual feedback and makes Docker concepts easier to explore.

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

Apply the group change (simplest is reboot):

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

Start Portainer:

```bash
docker compose up -d
docker ps
```

Open the browser **on the Pi**:
- `http://127.0.0.1:9000` (Portainer UI)

Create the admin user and select the **local** environment.

> We use `127.0.0.1` so it works even when campus Wi‑Fi blocks device-to-device access.

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

Deploy, then open on the Pi:
- `http://127.0.0.1:8080`

---

## 4) What now? Lightweight services you can try 

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

Open:
- `http://127.0.0.1:3001`

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

Open:
- `http://127.0.0.1:9999`

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

Open:
- `http://127.0.0.1:8081`


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

Open:
- `http://127.0.0.1:3000`

---

## 5) Scaling up to stronger hardware

With a stronger server you can:
- run more services simultaneously
- use better storage (SSD/NVMe, backups, redundancy)
- add reverse proxy + HTTPS
- run monitoring/alerting more seriously
- automate updates and backups

**Key idea:** Docker Compose makes migration easier: your setup can move from Pi → mini PC → server with minimal changes.

---

## Troubleshooting

**Docker permission denied**
```bash
sudo usermod -aG docker $USER
sudo reboot
```

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
