# Raspberry Pi Home Server Workshop (Docker + Portainer)

A **2-hour, beginner-friendly** workshop for 1st/2nd year engineering & computer science students (and anyone curious) showing how a Raspberry Pi can run real services at home using **Docker** and **Portainer**.

> **Important:** OS flashing is **pre-done already**. Attendees start from:  
> **“My Pi is on the network and I can SSH into it.”**

---

## Table of Contents
- [0) Workshop flow (2 hours)](#0-workshop-flow-2-hours)
- [1) Why home servers are important](#1-why-home-servers-are-important)
- [2) Why Docker and Portainer?](#2-why-docker-and-portainer)
- [3) How to do it (hands-on lab)](#3-how-to-do-it-hands-on-lab)
  - [3.1 Verify SSH + update the Pi](#31-verify-ssh--update-the-pi)
  - [3.2 Install Docker](#32-install-docker)
  - [3.3 Quick Docker sanity checks](#33-quick-docker-sanity-checks)
  - [3.4 Install Portainer (recommended via Compose)](#34-install-portainer-recommended-via-compose)
  - [3.5 Your first “Stack” in Portainer](#35-your-first-stack-in-portainer)
- [4) What now? Lightweight services mini-game](#4-what-now-lightweight-services-mini-game)
  - [4.1 The rules](#41-the-rules)
  - [4.2 Service options (pick 2–3)](#42-service-options-pick-23)
  - [4.3 Mini-game scoring](#43-mini-game-scoring)
- [5) Scaling up: what you can achieve on stronger hardware](#5-scaling-up-what-you-can-achieve-on-stronger-hardware)
- [Troubleshooting](#troubleshooting)
- [Safety notes (please read)](#safety-notes-please-read)
- [Sources](#sources)

---

## 0) Workshop flow (2 hours)

This agenda is designed so everyone finishes with:
- Docker installed
- Portainer working
- At least **one additional service** deployed and accessible

**Timeline (suggested)**
- **0:00–0:10** Intro + safety + verify SSH access
- **0:10–0:25** Why home servers?  
- **0:25–0:40** Why Docker + Portainer?
- **0:40–1:05** Lab: Install Docker + verify
- **1:05–1:25** Lab: Install Portainer + learn the UI
- **1:25–1:55** Mini-game: deploy 2–3 lightweight services
- **1:55–2:00** Scale-up ideas + wrap

---

## 1) Why home servers are important

A “home server” is just a computer you control that runs services for you (and possibly your household/team). A Raspberry Pi is a great starter platform because it is:
- inexpensive,
- low-power,
- and forces you to learn good habits (resource awareness, simplicity, reliability).

### Key reasons home servers matter

**Ownership & control**
- You decide what runs.
- You decide where data lives.
- You decide who can access it.

**Privacy**
- You can host tools that would otherwise send data to third parties.
- Even when you do use cloud tools, a home server can reduce what you share.

**Learning (huge for early CS/Eng students)**
You’ll touch real-world skills:
- Linux basics (users, permissions, services)
- Networking (IP addresses, ports, DNS)
- Deployment (containers, configuration, logs)
- Reliability practices (updates, backups, restarts)

**Local-first reliability**
Some services can remain available even if your internet goes out (local dashboards, local file access, local monitoring).

**Cost-effective scaling**
You can start tiny and scale up later:
- same Docker Compose files,
- same Portainer workflows,
- same service patterns.

---

## 2) Why Docker and Portainer?

### Why Docker?
Docker lets you run applications in **containers** (lightweight isolated environments).

**What you gain**
- **Repeatability:** “works on my Pi” becomes “works anywhere with Docker.”
- **Easy install/uninstall:** you don’t have to “pollute” the OS with many dependencies.
- **Isolation:** services don’t step on each other as much.
- **Upgrades & rollbacks:** update a container image, restart, done.
- **Portability:** if you move to a stronger server later, you can often reuse the same compose.

### Why Portainer?
Portainer is a web UI that helps you manage Docker:
- see containers and their status,
- view logs,
- restart containers,
- manage volumes,
- deploy “Stacks” (Docker Compose) from a browser.

**Why it’s great for workshops**
- It reduces command-line friction.
- It gives fast visual feedback (“is it running?”).
- It helps beginners learn Docker concepts without memorizing every CLI command.

---

## 3) How to do it (hands-on lab)

### Prerequisites (attendee)
You need:
- A Raspberry Pi that is already imaged (done by instructor)
- Network connectivity (Ethernet preferred)
- Your Pi’s **IP address or hostname**
- A laptop with SSH (macOS/Linux Terminal; Windows PowerShell/Terminal)

> If you don’t know the Pi’s IP, ask your instructor. (Common solutions: router device list, `ping raspberrypi.local`, etc.)

---

### 3.1 Verify SSH + update the Pi

1) SSH in:

```bash
ssh <username>@<pi-ip-or-hostname>
```

2) Confirm you’re on Linux and see system info:

```bash
uname -a
cat /etc/os-release
```

3) Update packages:

```bash
sudo apt update
sudo apt -y upgrade
```

(Optional but often helpful)
```bash
sudo reboot
```

Then SSH back in.

---

### 3.2 Install Docker

```bash
curl -fsSL https://get.docker.com | sudo sh
```

Add your user to the `docker` group:

```bash
sudo usermod -aG docker $USER
```

Log out and back in (or reboot):

```bash
exit
# reconnect via ssh
```

---

### 3.3 Quick Docker sanity checks

```bash
docker version
docker run --rm hello-world
```

---

### 3.4 Install Portainer (recommended via Compose)

1) Create a working directory:

```bash
mkdir -p ~/stacks/portainer
cd ~/stacks/portainer
```

2) Create `docker-compose.yml`:

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

3) Start Portainer:

```bash
docker compose up -d
docker ps
```

4) Open Portainer:
- `http://<pi-ip>:9000` or `https://<pi-ip>:9443`

---

### 3.5 Your first “Stack” in Portainer

Create a Portainer stack named `whoami`:

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

Open:
- `http://<pi-ip>:8080`

---

## 4) What now? Lightweight services mini-game

### 4.1 The rules
Deploy **2–3 services** below (Portainer Stacks recommended).  
Show they work by opening the web UI and/or showing container logs.

### 4.2 Service options (pick 2–3)

#### Option A: Uptime Kuma (monitoring)
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
Open `http://<pi-ip>:3001`

#### Option B: Homepage (dashboard)
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
Open `http://<pi-ip>:3000`

#### Option C: File Browser (file manager)
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
Open `http://<pi-ip>:8081`

#### Option D: Dozzle (log viewer)
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
Open `http://<pi-ip>:9999`

### 4.3 Mini-game scoring (target: 10 points)
- +2 Deploy a service and show it works
- +1 Explain one port mapping from your compose
- +2 Find logs in Portainer and identify an event
- +2 Change one env var or config and redeploy successfully
- Bonus +2 Help another group debug

---

## 5) Scaling up: what you can achieve on stronger hardware
- Run more services at once (databases + apps)
- Better storage (SSD/NVMe, backups)
- Reverse proxy + HTTPS
- Better monitoring, alerting, and reliability practices
- Easier migration if everything is containerized

---

## Troubleshooting

**Docker permission denied**
```bash
sudo usermod -aG docker $USER
# then log out and back in (or reboot)
```

**Portainer not loading**
```bash
docker ps
docker logs portainer --tail 200
```

**Port already in use**
Change the left side of `HOST:CONTAINER` port mappings (e.g., `8080:80` → `8082:80`) and redeploy.

---

## Safety notes (please read)
- Don’t expose random ports to the public internet.
- Use strong passwords (especially for Portainer).
- Keep the Pi updated:
  ```bash
  sudo apt update && sudo apt -y upgrade
  ```
- Containers are easy to recreate; **data volumes are not**. Back them up.

---

## Sources
- https://itsfoss.gitlab.io/post/how-to-manage-docker-containers-using-portainer-in-linux/
- https://pimylifeup.com/raspberry-pi-docker/
- https://www.youtube.com/watch?v=AtgCcMjtqF0&t=2667s
