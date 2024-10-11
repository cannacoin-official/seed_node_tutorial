# seed_node_tutorial

This guide provides a comprehensive, step-by-step process for setting up a **Cannacoin Seed Node** that connects to the blockchain, stays synchronized, and assists in peer discovery.

---

# Cannacoin Seed Node Setup Guide

Welcome to the **Cannacoin Seed Node Setup Guide**! This tutorial will walk you through the process of setting up a dedicated seed node for the Cannacoin blockchain. A seed node is essential for maintaining network stability, facilitating peer discovery, and ensuring the decentralization of the Cannacoin network.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Step-by-Step Setup](#step-by-step-setup)
    - [Step 1: Choose and Set Up Your Server](#step-1-choose-and-set-up-your-server)
    - [Step 2: Install Necessary Dependencies](#step-2-install-necessary-dependencies)
    - [Step 3: Clone and Build Cannacoin Node Software](#step-3-clone-and-build-cannacoin-node-software)
    - [Step 4: Configure the Node as a Seed Node](#step-4-configure-the-node-as-a-seed-node)
    - [Step 5: Configure Firewall](#step-5-configure-firewall)
    - [Step 6: Start and Enable Cannacoin Node](#step-6-start-and-enable-cannacoin-node)
    - [Step 7: Verify Synchronization and Functionality](#step-7-verify-synchronization-and-functionality)
4. [Maintaining Your Seed Node](#maintaining-your-seed-node)
    - [Ensure High Uptime](#ensure-high-uptime)
    - [Keep Software Updated](#keep-software-updated)
    - [Backup Configuration Files](#backup-configuration-files)
    - [Optimize Performance](#optimize-performance)
5. [Security Considerations](#security-considerations)
    - [Secure Access](#secure-access)
    - [Firewall and Network Security](#firewall-and-network-security)
    - [Regular Audits](#regular-audits)
6. [Optional Enhancements](#optional-enhancements)
    - [Use a Domain Name](#use-a-domain-name)
    - [Implement DNS Seeds](#implement-dns-seeds)
    - [Load Balancing and Redundancy](#load-balancing-and-redundancy)
7. [Example Configuration File](#example-configuration-file-cannacoinnconf)
8. [Automating Node Management](#automating-node-management)
    - [Using Systemd for Service Management](#using-systemd-for-service-management)
    - [Implementing Monitoring and Alerts](#implementing-monitoring-and-alerts)
9. [Troubleshooting Common Issues](#troubleshooting-common-issues)
    - [Node Not Syncing](#node-not-syncing)
    - [Unable to Connect to Peers](#unable-to-connect-to-peers)
    - [Service Fails to Start](#service-fails-to-start)
10. [Additional Resources](#additional-resources)
11. [Conclusion](#conclusion)

---

## Introduction

A **seed node** is a crucial component of the Cannacoin network. It assists in peer discovery, ensures network stability, and contributes to the decentralization of the blockchain. This guide provides detailed instructions to set up a seed node that connects to the Cannacoin blockchain, remains synchronized, and serves as a reliable entry point for new nodes.

---

## Prerequisites

Before proceeding, ensure you have the following:

- **Reliable Server or VPS**:
  - **Operating System**: Preferably a stable Linux distribution (e.g., Ubuntu 20.04 LTS).
  - **Specifications**: Minimal requirements since it's a seed node (e.g., 1 CPU core, 1 GB RAM, sufficient bandwidth).
  - **Uptime**: High uptime guarantees (99.9% or higher).

- **Domain Name (Optional)**:
  - Facilitates easier connection for other nodes (e.g., `seed.cannacoin.com`).

- **Static IP Address**:
  - Ensures consistent accessibility for other nodes.

- **Firewall Configuration**:
  - Secure your server by configuring firewall rules to allow only necessary ports.

---

## Step-by-Step Setup

### Step 1: Choose and Set Up Your Server

1. **Select a VPS Provider**:
   - Popular options include [DigitalOcean](https://www.digitalocean.com/), [Linode](https://www.linode.com/), [Vultr](https://www.vultr.com/), and [AWS](https://aws.amazon.com/).

2. **Deploy a Server Instance**:
   - Choose a lightweight plan (e.g., 1 GB RAM, 1 CPU).
   - Select a Linux distribution (Ubuntu 20.04 LTS is recommended).

3. **Access Your Server**:
   - Use SSH to connect:
     ```bash
     ssh root@your_server_ip
     ```
   - **Change Default Password** and create a new user with `sudo` privileges for enhanced security:
     ```bash
     adduser your_username
     usermod -aG sudo your_username
     ```
   - **Log in as the New User**:
     ```bash
     ssh your_username@your_server_ip
     ```

### Step 2: Install Necessary Dependencies

Assuming Cannacoin uses similar dependencies as Bitcoin, install the following:

1. **Update Package Lists**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install Build Essentials**:
   ```bash
   sudo apt install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils libboost-all-dev -y
   ```

3. **Install Additional Libraries**:
   ```bash
   sudo apt install libzmq3-dev libprotobuf-dev protobuf-compiler -y
   ```

4. **Install Git**:
   ```bash
   sudo apt install git -y
   ```

### Step 3: Clone and Build Cannacoin Node Software

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/cannacoin-official/cannacoin.git
   cd cannacoin
   ```

2. **Checkout the Desired Branch/Tag** (if applicable):
   ```bash
   git checkout main
   ```

3. **Build the Software**:
   ```bash
   ./autogen.sh
   ./configure --without-gui --disable-wallet
   make
   sudo make install
   ```
   - **Flags Explanation**:
     - `--without-gui`: Builds the node without graphical interface components.
     - `--disable-wallet`: Since it's a seed node, wallet functionalities are unnecessary, reducing resource usage.

### Step 4: Configure the Node as a Seed Node

1. **Create Configuration Directory**:
   ```bash
   mkdir ~/.cannacoin
   nano ~/.cannacoin/cannacoin.conf
   ```

2. **Edit `cannacoin.conf`**:

   Add the following configurations:

   ```plaintext
   # Enable listening for incoming connections
   listen=1

   # Specify the port (default is 8333 for Bitcoin; replace with Cannacoin's port if different)
   port=7143

   # Allow other nodes to connect
   discover=1

   # Specify this node as a seed node
   addnode=<node1_address>:<port>
   addnode=<node2_address>:<port>
   # Add more nodes as needed

   # Maximum number of connections (optional, adjust based on server capacity)
   maxconnections=125

   # Enable serving as a seed node
   # (This depends on Cannacoin's node implementation; some implementations have specific flags)
   # Example:
   # seednode=1

   # Log verbosity (optional, set to 1 or 2 for minimal logging)
   debug=1

   # Enable RPC (optional, secure if enabled)
   rpcuser=your_rpc_username
   rpcpassword=your_rpc_password
   rpcallowip=127.0.0.1
   ```

   **Notes**:
   - **`addnode`**: Specify known active nodes to help your seed node find peers.
   - **RPC Settings**: Only enable RPC if necessary and secure it properly.

3. **Save and Exit**:
   - Press `CTRL + O` to save and `CTRL + X` to exit the editor.

### Step 5: Configure Firewall

1. **Install UFW (Uncomplicated Firewall)**:
   ```bash
   sudo apt install ufw -y
   ```

2. **Allow SSH Connections**:
   ```bash
   sudo ufw allow ssh
   ```

3. **Allow Cannacoin Port**:
   ```bash
   sudo ufw allow 7143/tcp
   ```

4. **Enable UFW**:
   ```bash
   sudo ufw enable
   ```

5. **Check UFW Status**:
   ```bash
   sudo ufw status
   ```

### Step 6: Start and Enable Cannacoin Node

1. **Start the Node**:
   ```bash
   cannacoind -daemon
   ```
   - **Check if Node is Running**:
     ```bash
     cannacoin-cli getblockchaininfo
     ```

2. **Enable Node to Start on Boot**:

   Create a systemd service file:

   ```bash
   sudo nano /etc/systemd/system/cannacoin.service
   ```

   **Add the Following Content**:

   ```ini
   [Unit]
   Description=Cannacoin Daemon
   After=network.target

   [Service]
   ExecStart=/usr/local/bin/cannacoind -daemon -conf=/root/.cannacoin/cannacoin.conf -datadir=/root/.cannacoin
   ExecStop=/usr/local/bin/cannacoin-cli -conf=/root/.cannacoin/cannacoin.conf stop
   Restart=on-failure
   User=root
   Group=root

   [Install]
   WantedBy=multi-user.target
   ```

   **Save and Exit**:
   - Press `CTRL + O` to save and `CTRL + X` to exit.

3. **Reload systemd and Enable the Service**:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable cannacoin
   sudo systemctl start cannacoin
   ```

4. **Check Service Status**:
   ```bash
   sudo systemctl status cannacoin
   ```

### Step 7: Verify Synchronization and Functionality

1. **Check Blockchain Info**:
   ```bash
   cannacoin-cli getblockchaininfo
   ```
   - Ensure that the `blocks` count is increasing, indicating synchronization.

2. **Check Connected Peers**:
   ```bash
   cannacoin-cli getpeerinfo
   ```
   - Verify that your seed node has multiple peers connected.

3. **Ensure Seed Node Accessibility**:
   - From another machine or network, attempt to connect to your seed node to ensure it's reachable.
   - Example using `telnet`:
     ```bash
     telnet your_server_ip 7143
     ```
   - A successful connection indicates that the port is open and the node is accepting connections.

---

## Maintaining Your Seed Node

### Ensure High Uptime

- **Choose Reliable Hosting**: Opt for reputable VPS providers with high uptime guarantees.
- **Use Monitoring Tools**: Implement tools like [Monit](https://mmonit.com/monit/), [Nagios](https://www.nagios.org/), or [UptimeRobot](https://uptimerobot.com/) to monitor the health of your seed node.
- **Automate Restarts**: Configure systemd to restart the seed node automatically if it crashes.

### Keep Software Updated

- **Regularly Update Node Software**:
  ```bash
  cd /path/to/cannacoin
  git pull origin main
  ./autogen.sh
  ./configure --without-gui --disable-wallet
  make
  sudo make install
  sudo systemctl restart cannacoin
  ```
- **Apply Security Patches**: Stay informed about updates and security patches related to Cannacoin and its dependencies.

### Backup Configuration Files

- **Backup `cannacoin.conf`**:
  ```bash
  cp ~/.cannacoin/cannacoin.conf ~/cannacoin_conf_backup.conf
  ```
- **Store Backups Securely**: Use secure storage solutions or cloud backups to prevent data loss.

### Optimize Performance

- **Resource Monitoring**: Use tools like `htop`, `vmstat`, or `iostat` to monitor server performance.
- **Log Management**: Regularly review logs to identify and resolve issues.
  ```bash
  tail -f ~/.cannacoin/debug.log
  ```

---

## Security Considerations

### Secure Access

- **Use SSH Keys**: Disable password authentication and use SSH keys for server access.
  ```bash
  sudo nano /etc/ssh/sshd_config
  ```
  - Set `PasswordAuthentication no`
  - Restart SSH:
    ```bash
    sudo systemctl restart ssh
    ```
- **Change Default SSH Port** (Optional): To reduce automated attacks.
  ```bash
  sudo nano /etc/ssh/sshd_config
  ```
  - Change `Port 22` to another port (e.g., `Port 2222`)
  - Update UFW rules:
    ```bash
    sudo ufw allow 2222/tcp
    sudo ufw delete allow 22/tcp
    sudo systemctl restart ssh
    ```

### Firewall and Network Security

- **Restrict Access to RPC (If Enabled)**:
  - Ensure RPC is only accessible from trusted IPs (e.g., localhost).

- **Enable Fail2Ban**: Protect against brute-force attacks.
  ```bash
  sudo apt install fail2ban -y
  sudo systemctl enable fail2ban
  sudo systemctl start fail2ban
  ```

### Regular Audits

- **Monitor Logs**: Regularly check `debug.log` and system logs for suspicious activities.
- **Update Dependencies**: Keep all system packages and node software up to date to mitigate vulnerabilities.

---

## Optional Enhancements

### Use a Domain Name

- **Assign a Domain**:
  - Point a subdomain (e.g., `seed.cannacoin.com`) to your seed node's IP.

- **Update `cannacoin.conf`**:
  ```plaintext
  externalip=seed.cannacoin.com
  ```

### Implement DNS Seeds

- **Create DNS Entries**:
  - Configure DNS records to allow nodes to discover your seed node via DNS.

- **Example**:
  - Add A records for `seed1.cannacoin.com`, `seed2.cannacoin.com`, etc.

### Load Balancing and Redundancy

- **Deploy Multiple Seed Nodes**:
  - Increase network resilience by having multiple seed nodes across different regions.

- **Use Load Balancers**:
  - Distribute incoming connection requests across multiple seed nodes.

---

## Example Configuration File (`cannacoin.conf`)

Here's an example of a `cannacoin.conf` tailored for a seed node:

```plaintext
# Basic Settings
listen=1
server=1
daemon=1

# Network Settings
port=7143
rpcport=7142
externalip=cannacoin.duckdns.org

# Peer Discovery
discover=1
addnode=<peer1_ip>:7143
addnode=<peer2_ip>:7143
# Add more peers as needed

# Connection Limits
maxconnections=125

# Logging
logtimestamps=1
logips=1
debug=1

# RPC Settings (Optional, secure if used)
rpcuser=your_rpc_username
rpcpassword=your_secure_password
rpcallowip=127.0.0.1
```

**Notes**:

- **`externalip`**: Replace with your domain or static IP.
- **`addnode`**: Predefine peers to assist in network connectivity.
- **RPC Settings**: Only enable if necessary and ensure secure access.

---

## Automating Node Management

### Using Systemd for Service Management

Ensure your seed node starts on boot and restarts on failure.

1. **Create Systemd Service File**:
   ```bash
   sudo nano /etc/systemd/system/cannacoin-seed.service
   ```

2. **Add the Following Content**:
   ```ini
   [Unit]
   Description=Cannacoin Seed Node
   After=network.target

   [Service]
   ExecStart=/usr/local/bin/cannacoind -conf=/root/.cannacoin/cannacoin.conf -datadir=/root/.cannacoin
   ExecStop=/usr/local/bin/cannacoin-cli -conf=/root/.cannacoin/cannacoin.conf stop
   Restart=on-failure
   User=root
   Group=root

   [Install]
   WantedBy=multi-user.target
   ```

3. **Reload Systemd and Enable Service**:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable cannacoin-seed
   sudo systemctl start cannacoin-seed
   ```

4. **Check Service Status**:
   ```bash
   sudo systemctl status cannacoin-seed
   ```

### Implementing Monitoring and Alerts

- **Set Up Monitoring Tools**:
  - **Prometheus & Grafana**: For comprehensive monitoring dashboards.
  - **UptimeRobot**: To monitor server uptime and receive alerts.

- **Configure Alerts**:
  - Set up email or SMS alerts for downtimes or synchronization issues.

---

## Troubleshooting Common Issues

### Node Not Syncing

- **Check Logs**:
  ```bash
  tail -f ~/.cannacoin/debug.log
  ```
- **Ensure Ports Are Open**: Verify that your firewall isn't blocking necessary ports.
- **Verify Peer Connections**:
  ```bash
  cannacoin-cli getpeerinfo
  ```

### Unable to Connect to Peers

- **Check External IP**: Ensure `externalip` is correctly set in `cannacoin.conf`.
- **Verify DNS Resolution**: If using a domain, ensure it points to the correct IP.
- **Network Issues**: Check for any network restrictions or ISP blocks.

### Service Fails to Start

- **Check Service Status**:
  ```bash
  sudo systemctl status cannacoin-seed
  ```
- **Review Logs**:
  ```bash
  journalctl -u cannacoin-seed -f
  ```

---

## Additional Resources

- **Cannacoin Documentation**: Refer to Cannacoin's official documentation for specific configurations and features related to seed nodes.
- **Blockchain Development Communities**:
  - [Bitcoin Stack Exchange](https://bitcoin.stackexchange.com/)
  - [r/Bitcoin](https://www.reddit.com/r/Bitcoin/) on Reddit (if applicable to Cannacoin)
- **Networking and Security Guides**: Enhance your understanding of network configurations and security practices.

---

## Conclusion

Setting up a **Cannacoin Seed Node** is a pivotal contribution to the Cannacoin blockchain network. By following this guide, you will:

1. **Provision a Reliable Server**: Ensuring consistent uptime and accessibility.
2. **Install and Configure Node Software**: Tailoring the node to function optimally as a seed node.
3. **Implement Robust Security Measures**: Protecting your server and the network.
4. **Maintain Synchronization**: Keeping the node up-to-date with the latest blockchain data.
5. **Monitor and Maintain**: Ensuring the node's performance and health through continuous monitoring.

Your seed node will play a crucial role in peer discovery, network stability, and the overall health of the Cannacoin ecosystem. 

---

## Need Further Assistance?

If you encounter any specific issues during the setup or have additional questions about configuring your seed node, feel free to open an issue or reach out for support. We're here to help you ensure your seed node operates smoothly and effectively supports the Cannacoin blockchain.

---

**Happy Coding and Best of Luck with Your Cannacoin Project!**

---

*This guide is maintained by the Cannacoin community. Contributions and suggestions are welcome!*
