## Public RPC
- https://avail-da-mainnet.tech-coha05.xyz
- https://explorer.availproject.org/?light=https%3A%2F%2Favail-da-mainnet.tech-coha05.xyz#/explorer
# Avail Node Setup Guide

This guide will walk you through setting up an Avail node on your VPS. Follow the steps below to install necessary dependencies, download the latest Avail node release, and configure it as a service.

## Step 1: Update and Install Dependencies

Start by updating your package manager and installing the required dependencies.

```bash
sudo apt-get update
sudo apt install build-essential
sudo apt install --assume-yes git clang curl libssl-dev protobuf-compiler
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
rustup default stable
rustup update
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
```
## Step 2: System Update and Directory Setup
Update your system and create a directory for your Avail node.
```
sudo apt update && sudo apt upgrade -y
mkdir -p ~/avail
cd ~/avail
```
## Step 3: Fetch the Latest Stable Release
Fetch the latest stable Avail node release from the GitHub repository. This step ensures only stable releases (e.g., v2.2.5.0) are selected, excluding testnet or release candidate versions (e.g., -rc1, -rc2).
```
LATEST_URL=$(curl -s https://api.github.com/repos/availproject/avail/releases | grep browser_download_url | grep x86_64-ubuntu-2204-avail-node.tar.gz | grep -v rc | head -n 1 | cut -d '"' -f 4)
wget $LATEST_URL
```
## Step 4: Extract the Binary
Extract the downloaded Avail node binary.
```
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz
```
## Step 5: Create Node Data Directory
Create a directory to store your node data.
```
mkdir -p ~/avail/node-data
```
## Step 6: Create the Systemd Service
Create a service file to manage the Avail node. This service will run the node on the `mainnet` chain.
```
sudo tee /etc/systemd/system/avail.service > /dev/null <<EOF
[Unit]
Description=Avail Node

[Service]
Type=simple
ExecStart=/root/avail/avail-node --chain mainnet --name $NODE_NAME --validator -d /avail/node-data
Restart=always
RestartSec=3
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=avail-node

[Install]
WantedBy=multi-user.target
EOF
```
## Step 7: Start the Node Service
Reload systemctl, start the Avail node service, and enable it to start on boot.
```
sudo systemctl daemon-reload
sudo systemctl start avail.service
sudo systemctl enable avail.service
```
## Step 8: Check Service Status
To ensure that your node is running correctly, check the status of the Avail node service.
```
sudo systemctl status avail.service
```
## Step 9: Checking Logs
```
journalctl -u avail.service
```
