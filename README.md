# Linux-setup
Overall linux setup for packages installation, conteneurisation and easier everyday experience

## 01 - Security updates and useful tools


```bash
# update and upgrade linux packages and distribution
sudo apt update -y && sudo apt upgrade -y

# Install useful tool packages [if needed]
sudo apt install curl build-essential git net-tools ssh nfs-common tree
```

## 02 - Change static IP address


```bash
# Modify static IP address
cd /etc/netplan/
sudo vim 00-installer-config.yaml
```

```yaml
# Write the network configuation
network:
  ethernets:
    ens18:
      addresses:
      - 192.168.1.2/24
      gateway4: 192.168.1.254
      nameservers:
        addresses:
        - 192.168.1.254
        search: []
  version: 2
```

```bash
# Apply your modifications
sudo netplan apply

# Verify that the modifications are correctly applied
ip a
```

## 03 - Customize your shell and prompt : Fish shell


```bash
# update and upgrade
sudo apt update

# Download the key from the keyserver (using fish PUBKEY from https://launchpad.net/~fish-shell/+archive/ubuntu/release-3)
sudo gpg --homedir /tmp --no-default-keyring --keyring /usr/share/keyrings/fish.gpg   --keyserver keyserver.ubuntu.com --recv-keys 59FDA1CE1B84B3FAD89366C027557F056DC33CA5

# Add PPA
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/fish.gpg] https://ppa.launchpadcontent.net/fish-shell/release-3/ubuntu jammy main" | sudo tee /etc/apt/sources.list.d/fish-shell.list > /dev/null

# Update the apt package index and install Fish
sudo apt-get update
sudo apt-get install fish

# Install Oh My Fish!
curl https://raw.githubusercontent.com/oh-my-fish/oh-my-fish/master/bin/install | fish

# Customize your prompt using fisk theme
omf install fisk

# Set Fish shell as your default shell
which fish
chsh -s /usr/bin/fish
```

## 04 - Setup autoupdate with systemd


```bash
# Locate the systemd directory
cd /etc/systemd/system

# Create the autoupdate service, timer and script into the directory
sudo touch autoupdate.timer autoupdate.service autoupdate.sh
```

```bash
`autoupdate.service`

[Unit]
Description=Autoupdate everyday with apt

[Service]
ExecStart=/etc/systemd/system/autoupdate.sh

[Install]
WantedBy=multi-user.target
```

```bash
`autoupdate.timer`

[Unit]
Description=autoupdate linux with apt

[Timer]
OnCalendar=*-*-* 04:00:00
Persistent=true
Unit=autoupdate.service

[Install]
WantedBy=multi-user.target
```

```bash
`autoupdate.sh`

#!/bin/bash
apt update -y && apt upgrade -y
apt autoremove
reboot
```

```bash
# Make the autoupdate.sh executable
sudo chmod +x autoupdate.sh

# Activate the autoupdate.timer
sudo systemctl enable autoupdate.timer

# Verify the activation of the service
sudo systemctl status autoupdate.timer
```

## 05 - Mount NFS shared filesystem

```bash
# Edit the fstab configuration file
sudo vim /etc/fstab

# Verify that the NFS server is running on the remote host
showmount -e 192.168.1.130
```

```bash
`/etc/fstab`

192.168.1.3:/mnt/mediastorage /mnt/mediastorage nfs defaults 0 0
192.168.1.130:/export/data /mnt/NAS nfs defaults 0 0
```

```bash
# Mount the shared filesystem without restarting
mount -a

# Verify the shared filesystem is successfully mounted
df -h
```

## 06 - Setup your Docker environment

```bash
# Remove older versions of Docker if installed
sudo apt-get remove docker docker-engine docker.io containerd runc

# Update the apt package index and install packages to allow apt to use a repository over HTTPS
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg

# Add Docker’s official GPG key
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Use the following command to set up the repository
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update the apt package index
sudo apt-get update

# Install the latest version of Docker engine
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker --version

# Create a docker group and add your current user to it
sudo groupadd docker
sudo usermod -aG docker $USER
```
