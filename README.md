# Pihole-installation
Welcome to the world of self-hosting! This setup is a classic "HomeLab" foundation. By the end of this guide, you’ll have a device that blocks ads, protects your privacy by not using big-tech DNS, and lets you access your home network securely from anywhere in the world.


Step 0: The Preparation

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
```bash

Before we install anything, we need to make sure your Raspberry Pi is fully up to date. Open your terminal (either via SSH or directly on the Pi) and run

sudo apt update && sudo apt upgrade -y



sudo apt update && sudo apt upgrade -y
This might take a few minutes. Once finished, it’s a good idea to reboot: sudo reboot

Step 1: Install Tailscale (Remote Access)
What it is: A "Zero-Config" VPN. It lets you access your Pi (and its web dashboard) from your phone or laptop even when you aren't home, without opening dangerous ports on your router.

Run the installer:



curl -fsSL https://tailscale.com/install.sh | sh
Log in:



sudo tailscale up
Authenticate: It will give you a link. Copy that link into your browser, log in with your account (Google, Microsoft, etc.), and your Pi is now part of your private network.

Step 2: Install btop (The "Dashboard")
What it is: A beautiful, terminal-based resource monitor. It shows you your CPU usage, RAM, and network speeds in real-time.

Install it directly from the repository:



sudo apt install btop -y
Run it: Just type btop.

Tip: Use your mouse to click on things in the interface, or press q to quit.

Step 3: Install Pi-hole (The Ad-Blocker)
What it is: A "DNS sinkhole" that blocks ads and trackers for every device on your network.

Run the automated installer:



curl -sSL https://install.pi-hole.net | 
Follow the Blue Screens:

Most default options are fine.

Static IP: When it asks about a static IP, say Yes (it needs this to stay at the same address).

Upstream DNS: Choose "Google" or "Cloudflare" for now—we will change this to Unbound later.

Finish & Save Credentials: At the end, it will show you an Admin Password. Write this down! You’ll need it to log into http://<your-pi-ip>/admin.

Step 4: Install Unbound (Privacy Resolver)
What it is: Instead of Pi-hole asking Google "Where is google.com?", Unbound asks the "Root Servers" directly. This means no single company sees your entire browsing history.

Install the package:



sudo apt install unbound -y
Configure it: You need to create a specific configuration file for Pi-hole.



sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
Paste this exact text into that window (Right-click to paste in most terminals):

Kod snippet'i

server:
    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    access-control: 127.0.0.0/8 allow
(Press Ctrl+O, then Enter to save, and Ctrl+X to exit.)

Fix a common Debian conflict: Modern Raspberry Pi OS has a service that can break Unbound. Run these to disable it:



sudo systemctl disable --now unbound-resolvconf.service
sudo sed -Ei 's/^unbound_conf=/#unbound_conf=/' /etc/resolvconf.conf
sudo rm /etc/unbound/unbound.conf.d/resolvconf_resolvers.conf
Restart Unbound:



sudo systemctl restart unbound
Step 5: Connecting the Pieces
Now we need to tell Pi-hole to use Unbound (which is living at port 5335) instead of the internet.

Open your browser and go to your Pi-hole Admin Dashboard (e.g., http://192.168.1.XX/admin or your Tailscale IP).

Log in and go to Settings > DNS.

Uncheck everything under "Upstream DNS Servers" (Google, OpenDNS, etc.).

On the right side, under Custom 1 (IPv4), type: 127.0.0.1#5335

Scroll to the bottom and click Save.

Final Check
To make sure everything is working:

Open btop on your Pi and watch the network traffic.

On your Router, set your DNS server to the IP address of your Raspberry Pi.

On your Tailscale Admin Console, you can go to DNS and add your Pi's Tailscale IP as a "Global Nameserver" to get ad-blocking on your phone while you're out!
