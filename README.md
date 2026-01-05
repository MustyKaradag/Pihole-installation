# Pihole-installation
Welcome to the world of self-hosting! This setup is a classic "HomeLab" foundation. By the end of this guide, you’ll have a device that blocks ads, protects your privacy by not using big-tech DNS, and lets you access your home network securely from anywhere in the world.



# 🥧 Raspberry Pi HomeLab Setup Guide
A beginner-friendly guide to setting up a privacy-focused network with Ad-blocking, a Private DNS resolver, and Remote Access.

## 📋 Table of Contents
- [Prerequisites](#-prerequisites)
- [Step 1: Tailscale (Remote Access)](#step-1-tailscale-remote-access)
- [Step 2: btop (Monitoring)](#step-2-btop-monitoring)
- [Step 3: Pi-hole (Ad-Blocking)](#step-3-pi-hole-ad-blocking)
- [Step 4: Unbound (Privacy DNS)](#step-4-unbound-privacy-dns)
- [Step 5: Final Configuration](#step-5-final-configuration)

---

## 🛠 Prerequisites
- Raspberry Pi (running Raspberry Pi OS / Debian)
- Basic knowledge of the Terminal (SSH)
- A Tailscale account (Free)

---

## 🚀 Installation Steps

### Step 0: System Update
Always start with a fresh system:
Before we install anything, we need to make sure your Raspberry Pi is fully up to date. 
Open your terminal (either via SSH or directly on the Pi) and run,


```bash

- sudo apt update
- sudo apt upgrade -y
```

---

## Step 1: Tailscale (Remote Access)
Install Tailscale to access your Pi from anywhere without opening firewall ports.

```bash

- curl -fsSL https://tailscale.com/install.sh | sh
- sudo tailscale up
```

---

## Step 2: btop (Monitoring)
A visual monitor for your CPU, RAM, and Network.

```bash

sudo apt install btop -y
```

---

## Step 3: Pi-hole (Ad-Blocking)
The network-wide ad blocker.

```bash

- curl -sSL https://install.pi-hole.net | bash
- Note: Follow the on-screen prompts. Choose a static IP and save your admin password at the end!
```

---

## Step 4: Unbound (Privacy DNS)
This prevents big-tech companies from seeing your DNS queries.

Install

```bash

- sudo apt install unbound -y
- Configure: Create the config file: sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

Config:

Kod snippet'i

server
```bash
  -   interface: 127.0.0.1 
  -   port: 5335 
  -   do-ip4: yes 
  -   do-udp: yes 
  -   do-tcp: yes 
  -   access-control: 127.0.0.0/8 allow 
  ```
    
Fix Conflicts & Restart

```bash

- sudo systemctl disable --now unbound-resolvconf.service
- sudo sed -Ei 's/^unbound_conf=/#unbound_conf=/' /etc/resolvconf.conf
- sudo rm /etc/unbound/unbound.conf.d/resolvconf_resolvers.conf
- sudo systemctl restart unbound
```

---

## Step 5: Final Configuration
Connect Pi-hole to Unbound:

Navigate to your Pi-hole Admin Web Interface.

Go to Settings > DNS.

Uncheck all public DNS providers.

Add 127.0.0.1#5335 to Custom 1 (IPv4).

Click Save.

---


📊 Monitoring
To check your system status at any time, simply run:


```bash

btop

```
