
Tailscale Performance & Infrastructure Optimization
Project Goal: Eliminate network latency and streaming bottlenecks in a distributed Debian/macOS environment through kernel-level tuning and network path optimization.
📋 Table of Contents
* The Problem
* The Solution
* Tech Stack
* Key Features & Optimizations
* How to Reproduce
🔍 The Problem
While using a Debian server as a Tailscale exit node, network performance suffered from:
1. Relayed Connections: Traffic was routing through DERP relay servers rather than direct peer-to-peer paths, increasing latency.
2. Packet Fragmentation: Standard MTU sizes caused overhead and "stuttering" in high-bandwidth streams.
3. CPU Bottlenecks: The Debian server was struggling to process encrypted UDP packets at the software level.
🛠 Tech Stack
* OS: Debian GNU/Linux, macOS
* Networking: Tailscale (WireGuard-based mesh VPN)
* Tools: ethtool, udev, systemd, iperf3
🚀 Key Features & Optimizations
1. Direct Peer-to-Peer Routing
Verified 100% direct connection status using tailscale ping, achieving a consistent 1ms latency by bypassing relay servers.
2. MTU Synchronization (1280)
Synchronized the Maximum Transmission Unit (MTU) to 1280 on both macOS and Debian to eliminate packet fragmentation. This was made permanent on Debian using a custom udev rule:
Bash

# /etc/udev/rules.d/99-tailscale-mtu.rules
ACTION=="add", SUBSYSTEM=="net", KERNEL=="tailscale0", RUN+="/usr/sbin/ip link set dev tailscale0 mtu 1280"
3. UDP Hardware Offloading
Enabled Generic Receive Offload (GRO) for UDP forwarding to offload packet processing from the CPU to the network interface. This was automated via a systemd service to ensure persistent performance:
Ini, TOML

[Service]
ExecStart=/usr/sbin/ethtool -K ens33 rx-udp-gro-forwarding on rx-gro-list off
📈 Performance Gains
* Latency: Reduced from variable relay speeds to a stable 1ms.
* Streaming: Successfully enabled 4K video throughput without buffering.
* CPU Efficiency: Lowered per-packet overhead on the Debian exit node through hardware offloading.

Implementation Details
For a deeper dive into the specific commands and troubleshooting steps, see the docs/ folder or check the Systemd configuration.
