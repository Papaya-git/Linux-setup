# Linux-setup
Overall linux setup for packages installation, conteneurisation and easier everyday experience
________________________________________________________________________________________________

# 01 - Security updates and useful tools

## update and upgrade

```bash
sudo apt update -y && sudo apt upgrade -y
```

## Install useful tool packages

```bash
sudo apt install curl build-essential git net-tools ssh
```

# 02 - Customize your shell and prompt : Fish shell

## update and upgrade

```bash
sudo apt update

# Download the key from the keyserver (using fish PUBKEY from https://launchpad.net/~fish-shell/+archive/ubuntu/release-3)
sudo gpg --homedir /tmp --no-default-keyring --keyring /usr/share/keyrings/fish.gpg   --keyserver keyserver.ubuntu.com --recv-keys 59FDA1CE1B84B3FAD89366C027557F056DC33CA5

# Add PPA
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/fish.gpg] https://ppa.launchpadcontent.net/fish-shell/release-3/ubuntu jammy main" | sudo tee /etc/apt/sources.list.d/fish-shell.list > /dev/null

# Update the apt package index and install Fish
sudo apt-get update

sudo apt-get install fish

# Set Fish shell as your default shell
which fish

chsh -s /usr/bin/fish
```

# 03 - Setup your Docker environment

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
```
