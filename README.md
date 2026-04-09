
---

# 🧠 Overleaf Toolkit Quick-Start Guide

This guide assumes you’re running **Ubuntu / Debian / Kali / Mint** in a Linux environment (can be WSL).

## 0️⃣ Install Ubuntu WSL (if not already installed)

```bash
# Open PowerShell
wsl --install -d Ubuntu 
  
# Launch Ubuntu  
wsl -d Ubuntu
```

![[images/Pasted image 20260409202551.png]]

- After installation, your WSL Ubuntu shell is ready to run **docker & Overleaf Toolkit**.

---

## 1️⃣ Prepare the System

```bash
# Optional: become root
sudo -i

# Add passwordless sudo for your user
echo "$SUDO_USER ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$SUDO_USER
chmod 440 /etc/sudoers.d/$SUDO_USER
exit
```

![[images/Pasted image 20260409201035.png]]


---

### 2️⃣ Install Docker, Docker Compose v5, and utilities

```bash
sudo apt update
sudo apt install -y docker.io curl wget tree nodejs python3-pip
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
newgrp docker

# Remove old docker-compose v1 if exists
sudo apt remove docker-compose -y

# Install Docker Compose v5
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version   # should show v5.x
```

![[images/Pasted image 20260409201112.png]]

---

## 3️⃣ Clone the Overleaf Toolkit

```bash
mkdir -p ~/overleaf && cd ~/overleaf
git clone https://github.com/overleaf/toolkit.git ./overleaf-toolkit
cd overleaf-toolkit
ls
# bin  CHANGELOG.md  config  data  doc  lib  LICENSE  README.md
```

![[images/Pasted image 20260409201200.png]]

---

## 4️⃣ Initialise Configuration

```bash
bin/init
```

- Check `config/`:
    

```bash
ls config
# overleaf.rc  variables.env  version
```

- Edit `config/overleaf.rc`:
    

```bash
nano config/overleaf.rc
# Disable sibling containers for CE:
SIBLING_CONTAINERS_ENABLED=false
```

> This is **mandatory for Community Edition**, otherwise CE will fail to start.

![[images/Pasted image 20260409201241.png]]

---

## 5️⃣ Run the Doctor

```bash
bin/doctor
```

- Should output something like:
    

```
====== Overleaf Doctor ======
- Host Information
    - Linux
- Dependencies
    - bash
    - docker
    - docker compose
- Configuration
- Warnings: ...
====== End ======
```

![[images/Pasted image 20260409201324.png]]

---

## 6️⃣ Start Overleaf (Detached Mode)

```bash
bin/up -d
```

- Pulls required docker images (Mongo, Redis, CE) and starts **all containers**
    
- Logs can be followed if needed:


```bash
bin/logs
# or for multiple services:
bin/logs -f web filestore docstore clsi
```

![[images/Pasted image 20260409201406.png]]

---

## 7️⃣ Access Launchpad & Create Admin Account

- Go to [http://localhost/launchpad](http://localhost/launchpad)
    
- Fill in email & password to **create first admin account**
    
- Ignore any test step, just log in and start using Overleaf
    
![[images/Pasted image 20260409201436.png]]

---

## 8️⃣ Create or Import Projects

- Go to project page inside Overleaf
    
- Either **import a project** or **create a blank project** for testing/editing
    

![[images/Pasted image 20260409201443.png]]

---

##  Optional: Install Full TeX Live inside container (~5GB+)

Inside the **sharelatex container**:

```bash
# Get the container ID
docker ps

# Enter the container
docker exec -it <container_id> bash
```

### Update tlmgr to the latest release

```bash
cd /tmp
curl -LO https://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh
chmod +x update-tlmgr-latest.sh

# Run the updater
./update-tlmgr-latest.sh -- --confirm

# Check version
tlmgr --version
# Example: tlmgr revision 78301 (2026-03-07)
```

### Install full TeX Live

```bash
tlmgr install scheme-full
```

- Installs **all LaTeX packages**, XeLaTeX, LuaLaTeX, fonts, etc.
    
- Recommended for **Arabic or custom fonts**.
    
- This may take a while (~5–6GB).
- In the project **File** ~ **Settings** ~ **Compiler** ~ Choose **XeLatex**
    
---
    
**Screenshot placeholder** – TeX Live installation

![[images/Pasted image 20260409201606.png]]

---
