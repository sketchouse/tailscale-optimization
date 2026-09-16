# 🔐 Tailscale Network Routing & Optimization Workflows

A practical reference and configuration guide for optimizing mesh VPN topologies using Tailscale. This project details performance tuning, subnet routing configuration, exit node deployment, and granular Access Control List (ACL) policies for secure remote access.

---

## 📐 Architecture & Key Features

* **Subnet Router Deployment:** Exposing local LAN subnets (e.g., home lab or virtual machine environments) to the tailnet without installing Tailscale on every endpoint.
* **Exit Node Routing:** Securely tunneling all outbound internet traffic through a designated gateway node with split-DNS optimization.
* **Granular ACL Policies:** Implementing tailnet-wide security rules to restrict traffic between specific user tags, virtual machines, and management ports.
* **Performance Tuning:** Benchmarking throughput using `iperf3` and optimizing MTU/UDP buffer sizes across Linux and macOS clients.

---

## 🛠 Prerequisites & Tools

* Tailscale Client (v1.50+ recommended)
* Linux host (Debian/Ubuntu) or macOS machine for routing nodes
* IP forwarding enabled on host machine:
```bash
# Enable IPv4/IPv6 forwarding on Linux
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf

```
---

## 🚀 Quick Start / Configuration Examples

### 1. Enabling a Subnet Router & Exit Node

```bash
# Advertise a local subnet (e.g., 192.168.1.0/24) and designate as an exit node
sudo tailscale up --advertise-routes=192.168.1.0/24 --advertise-exit-node
```
### 2. Tailscale ACL Policy Example (`tailscale.hujson`)

```json
{
  "acls": [
    // Allow admin access to all devices
    { "action": "accept", "src": ["group:admin"], "dst": ["*:*"] },
    
    // Restrict guest access to specific service ports
    { "action": "accept", "src": ["tag:guest"], "dst": ["tag:webserver:80,443"] }
  ],
  "tagOwners": {
    "tag:webserver": ["group:admin"],
    "tag:guest": ["group:admin"]
  }
}
```
---

## 📊 Performance Benchmarks & Results

| Configuration | Connection Type | Bandwidth (iperf3) | Latency (avg) |
| :--- | :--- | :--- | :--- |
| **Direct Peer-to-Peer** | UDP / WireGuard | 450 Mbps | 12 ms |
| **Relayed (DERP Server)** | Encrypted Relay | 45 Mbps | 68 ms |

---

## 📋 Related Documentation & SOPs

* [Tailscale Official Documentation](https://tailscale.com/kb)
