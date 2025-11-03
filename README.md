# Besu-Prysm optimized setup

This guide will help you migrate from an existing RPC setup to Besu Prysm setup.

## Steps

### Step 1: Stop and Remove Existing Containers

Stop all running containers and remove volumes:

```bash
cd ~/ethereum && docker compose down -v
```
for reth-prysm, use `Ethereum` instead of `ethereum` in every cmds. or find your rpc directory with `ls` .

### Step 2: Remove the ethereum Directory

```bash
cd ~ && rm -rf ethereum
```

### Step 3: Install Prerequisites
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

```bash
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev ufw screen gawk pv zstd -y
```
#### 3.1: Install Docker (you can skip this if it's already installed)
```bash
#!/bin/bash
set -e

if [ ! -f /etc/os-release ]; then
  echo "Not Ubuntu or Debian"
  exit 1
fi

sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc docker-ce docker-ce-cli docker-buildx-plugin docker-compose-plugin; do
  sudo apt-get remove --purge -y $pkg 2>/dev/null || true
done
sudo apt-get autoremove -y
sudo rm -rf /var/lib/docker /var/lib/containerd /etc/docker /etc/apt/sources.list.d/docker.list /etc/apt/keyrings/docker.gpg

sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release
sudo install -m 0755 -d /etc/apt/keyrings

. /etc/os-release
repo_url="https://download.docker.com/linux/$ID"
curl -fsSL "$repo_url/gpg" | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] $repo_url $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

if sudo docker run hello-world; then
  sudo docker rm $(sudo docker ps -a --filter "ancestor=hello-world" --format "{{.ID}}") --force 2>/dev/null || true
  sudo docker image rm hello-world 2>/dev/null || true
  sudo systemctl enable docker
  sudo systemctl restart docker
  clear
  echo -e "• Docker Installed ✓"
fi
```

### Step 4: Run the One-Line Installation

```bash
screen -S rpc bash -c "wget -O setup.sh https://raw.githubusercontent.com/AminMAD86/Besu-Prysm-1script-installation/refs/heads/main/OneLinearCmd && chmod +x setup.sh && ./setup.sh"
```

**What this does:**
- Creates a screen session named "rpc"
- Runs the complete installation with downloading snapshots nad runing the node.
- you don't need to do anything. just leave it for 6hrs. the installation finishes here, the remaining cmds are unneccasary.

**Screen session commands:**
- **Detach** (keep running in background): Press `Ctrl+A`, then `D`
- **Reattach** to session: `screen -r rpc`

### Step 5: check sync status

```bash
# Check Besu sync status
curl -X POST -H 'Content-Type: application/json' \
  --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' \
  http://localhost:8545

# Check Prysm sync status
curl http://localhost:3500/eth/v1/node/syncing
```

**View logs:**
```bash
cd ~/ethereum && docker compose logs -f
```
