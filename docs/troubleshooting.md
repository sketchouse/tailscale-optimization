# Performance Validation with iperf3
To validate optimizations, I conducted peer-to-peer testing between the Debian exit node and a macOS client.
- **Before:** Relay (DERP) connections showed high jitter and limited throughput (~15 Mbps).
- **After:** Direct P2P connection via optimized UDP paths achieved near-line-rate speeds (900+ Mbps) with 1ms latency.
