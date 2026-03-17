# Raspberry Pi Home Server Workshop

A **2-hour, beginner-friendly** workshop for 1st/2nd year engineering & computer science students (and anyone curious) showing how a Raspberry Pi can run real services at home using **Docker** and **Portainer**.

> OS flashing is **pre-done by the instructor** for this workshop. Attendees start from “Pi is on the network + you can SSH in”.

## What you’ll learn
1. **Why home servers matter** (privacy, ownership, learning, local-first tools)
2. **Why Docker + Portainer** (repeatable installs, easy management)
3. **How to set it up** on a Raspberry Pi
4. **What now?** Lightweight services you can run immediately
5. **How to scale up** to more powerful hardware later

## Prerequisites (attendee)
- A Raspberry Pi already imaged by the instructor (Raspberry Pi OS recommended)
- Power + network (Ethernet preferred)
- A laptop with:
  - SSH client (macOS/Linux Terminal, Windows PowerShell/Windows Terminal)
  - A text editor

## Workshop flow (2 hours)
- 0:00–0:10 — Intro + safety + verify SSH access
- 0:10–0:25 — Why home servers? (`docs/00-overview.md`)
- 0:25–0:40 — Docker + Portainer basics (`docs/01-why-docker-portainer.md`)
- 0:40–1:05 — Lab: install Docker (`docs/02-lab-install-docker.md`)
- 1:05–1:30 — Lab: Portainer + your first stack (`docs/03-lab-portainer.md`)
- 1:30–1:55 — “Lightweight services mini-game” (`docs/04-what-now-fun-services.md`)
- 1:55–2:00 — Scale-up ideas + wrap (`docs/05-scaling-up.md`)

## Quick links
- Start here: `docs/02-lab-install-docker.md`
- Portainer lab: `docs/03-lab-portainer.md`
- Fun services: `docs/04-what-now-fun-services.md`
- Troubleshooting: `docs/troubleshooting.md`

## Sources used (as requested)
- Portainer management guide (It’s FOSS)
- Raspberry Pi + Docker guide (Pi My Life Up)
- Workshop video reference (YouTube)

(See each doc for inline attribution and notes.)

---

## Instructor notes (optional)
If you want to reduce friction even more:
- Pre-create a user account for attendees.
- Pre-set a hostname label on each Pi.
- Preconfigure Wi‑Fi or provide Ethernet.
- Consider a printed card per Pi: hostname/IP + username/password.