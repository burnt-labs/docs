# ✨ No-Brainer XION Mainnet Node Setup (Using Docker Compose)

This is the **fastest and cleanest way** to run a XION mainnet node using Docker Compose. No need to manually configure anything — just init, clone, replace config files, and run.

---

# 🚀 XION Mainnet Node Setup Guide

This guide walks you through installing `xiond`, initializing the config, replacing it with pre-made files, and running the node via Docker.

---

## 🧱 Step 1: Install Dependencies

Install system packages and Go (version 1.21+):

```bash
sudo apt update
sudo apt install -y build-essential curl git jq

curl -LO https://golang.org/dl/go1.21.6.linux-amd64.tar.gz
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.21.6.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

---

## 🛠️ Step 2: Build `xiond` from Source

Clone and compile the XION daemon:

```bash
git clone https://github.com/burnt-labs/xion.git
cd xion
git checkout v0.18.0
make install
```

Verify the installation:

```bash
xiond version
```

Expected output:

```
0.18.0
```

---

## ⚙️ Step 3: Initialize the Node

Run the following command to generate the `.xiond` directory and base config:

```bash
xiond init moniker-name --chain-id xion-mainnet-1 --home ./xion/.xiond
```

This creates the folder structure like:

```
xion/
└── .xiond/
    ├── config/
    │   ├── app.toml
    │   ├── client.toml
    │   ├── config.toml
    │   └── genesis.json
    └── data/
```

---

## 🧾 Step 4: Replace the Default Config with Pre-Configured Files

Clone the config repo and copy over the ready-made files:

```bash
git clone https://github.com/web3dopamine/xion-docker-compose.git
cp -r xion-docker-compose/config/* ./xion/.xiond/config/
```

This replaces `genesis.json`, `app.toml`, `client.toml`, and `config.toml` with updated versions.

---

## ▶️ Step 5: Run the Node with Docker Compose

Ensure you're inside the directory containing `docker-compose.yml`, then run:

```bash
docker-compose up -d
```

To view logs:

```bash
docker logs -f xiond-mainnet
```

---

## 🧠 Tips

- If you face permission issues, make sure to set folder access:

```bash
chmod -R 777 ./xion/.xiond
```

- Wait it out! Your node will take time to sync. While syncing:
  - The RPC endpoint at `http://localhost:26657/status` might return an **empty response** or connection error.
  - You can monitor progress by checking the data folder size:

```bash
du -sh ./xion/.xiond/data
```

- Ensure the final folder structure is:

```
xion/
└── .xiond/
    ├── config/
    ├── data/
    └── other xiond runtime files
```

---

You're now running a fully configured XION mainnet node with ease. 🔥
