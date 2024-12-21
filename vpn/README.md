# WireGuard

## Get Started
WireGuard is a modern, fast, and secure VPN protocol. It is lightweight and easy to configure. Here’s how to set it up:

### Installation
- **On Linux:**
  ```bash
  sudo apt update
  sudo apt install wireguard
  ```

- **On Windows:**
  1. Download and install WireGuard from [the official website](https://www.wireguard.com/install/).

- **On macOS:**
  ```bash
  brew install wireguard-tools
  ```

- **On Android/iOS:**
  Download the WireGuard app from the Play Store or App Store.

## How to Configuration
1. **Generate Keys:**
   On the server, generate private and public keys:
   ```bash
   wg genkey | tee privatekey | wg pubkey > publickey
   ```

2. **Configure Server:**
   Create the configuration file `/etc/wireguard/wg0.conf`:
   ```ini
   [Interface]
   PrivateKey = <server_private_key>
   Address = 10.0.0.1/24
   ListenPort = 51820

   [Peer]
   PublicKey = <client_public_key>
   AllowedIPs = 10.0.0.2/32
   ```

3. **Configure Client:**
   On the client, create a configuration file (e.g., `wg0.conf`):
   ```ini
   [Interface]
   PrivateKey = <client_private_key>
   Address = 10.0.0.2/24

   [Peer]
   PublicKey = <server_public_key>
   Endpoint = <server_ip>:51820
   AllowedIPs = 0.0.0.0/0
   PersistentKeepalive = 25
   ```

4. **Start WireGuard:**
   - On Linux:
     ```bash
     sudo wg-quick up wg0
     ```
   - On Windows/Mac, use the WireGuard app to import and activate the configuration.

5. **Enable Port Forwarding:**
   On the server, enable IP forwarding:
   ```bash
   echo 1 > /proc/sys/net/ipv4/ip_forward
   ```

   Configure firewall rules to allow traffic.

---

# NetBird

NetBird is a simple and secure VPN solution. It provides easy management of networks across devices.

## Installation
1. Visit [NetBird's website](https://netbird.io/) to download the installer for your platform.
2. Install the NetBird agent on each device.

## Configuration
1. Sign up and create an account on NetBird’s dashboard.
2. On each device, log in to the NetBird client using your credentials.
3. Create networks and assign devices using the web interface.
4. NetBird automatically handles secure connections between devices.

---

# Tailscale

Tailscale is a zero-config VPN solution based on WireGuard. It simplifies private network setup.

## Installation
1. **On Linux:**
   ```bash
   curl -fsSL https://tailscale.com/install.sh | sh
   ```

2. **On Windows/Mac:**
   Download the installer from [Tailscale's website](https://tailscale.com/).

3. **On Android/iOS:**
   Install the Tailscale app from the Play Store or App Store.

## Configuration
1. Log in to the Tailscale client using your preferred authentication method (Google, Microsoft, GitHub, etc.).
2. Devices will automatically appear on your Tailscale dashboard.
3. Assign access rules using Tailscale ACLs in the admin panel.
4. Use Tailscale’s MagicDNS to access devices by hostname (e.g., `hostname.tailnet`).

---
