<div align="center">

<img src="./assets/banners/github-readme-1280.png" alt="Virtual IQ AI NetOps Toolset — Beta" width="100%"/>

<br/>
<br/>

# Virtual IQ AI NetOps Toolset — Beta Channel

### A Professional Engineer's Toolset for Modern Network Operations

**A self-contained desktop application bundling 30+ network discovery, troubleshooting, security, and performance tools — comparable in scope to enterprise engineer toolsets that retail for $1,500–$2,000 per seat, but free for personal and non-commercial use.**

<br/>

[![Channel](https://img.shields.io/badge/channel-BETA-F59E0B?style=flat-square)](https://github.com/virtualiqai/viq-releases-beta/releases)
[![Stable](https://img.shields.io/badge/stable-virtualiqai%2Fviq--releases-2563EB?style=flat-square)](https://github.com/virtualiqai/viq-releases)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey?style=flat-square)](https://github.com/virtualiqai/viq-releases-beta/releases)
[![License](https://img.shields.io/badge/license-Proprietary-red?style=flat-square)](./LICENSE)
[![Use](https://img.shields.io/badge/use-Testing%20Only-orange?style=flat-square)](./TERMS_OF_USE.md)

<br/>

[**⬇ Download Latest Beta**](https://github.com/virtualiqai/viq-releases-beta/releases/latest) &nbsp;|&nbsp;
[**📋 Beta Changelog**](./CHANGELOG.md) &nbsp;|&nbsp;
[**🔐 Security & Privacy**](./SECURITY.md) &nbsp;|&nbsp;
[**📜 Terms of Use**](./TERMS_OF_USE.md) &nbsp;|&nbsp;
[**💬 Report a Beta Issue**](https://github.com/virtualiqai/viq-releases-beta/issues)

<br/>

---

</div>

> [!WARNING]
> **This is the beta release channel.** Builds in this repository are pre-release and may contain unfinished features, regressions, or instability. They are intended for testers and early adopters — not for production-adjacent use. For the stable production line, install from the [main release repository](https://github.com/virtualiqai/viq-releases) instead. Beta and stable installers can replace each other in place; your settings and data are preserved.

## Overview

Virtual IQ AI NetOps Toolset is a desktop network operations workbench built by a working network architect, for working network engineers. Every tool addresses a real operational task: discovery, troubleshooting, security verification, performance measurement, or day-to-day reference. The application runs entirely on your workstation, requires no cloud account, and performs all device interactions in real time against the targets you point it at.

It is distributed as a single signed installer for Windows and macOS. Once installed, it launches in your default browser at a private loopback address and exposes its full tool catalog through a clean, dark-themed interface.

---

## Tool Catalog

### 🔍 Discovery

| Tool | Description | Method |
|------|-------------|--------|
| **SNMP Port Mapper** | Walks a switch and correlates IF-MIB, BRIDGE-MIB, Q-BRIDGE-MIB, IP-MIB, LLDP-MIB, and CDP-MIB to produce a full port → MAC → IP → neighbor map; works across Cisco IOS / Catalyst / NX-OS (incl. pure-L2 Nexus access with no SVIs); optional L3 ARP correlation against a separate router (legacy `ipNetToMediaTable` + RFC 4293 fallback); structured diagnostic cards explain L2 / L3 failures with actionable checks | SNMP v1/v2c/v3 GET/GETBULK (read-only) |
| **Ping Sweep** | Sweep a CIDR for live hosts (capped at 254 hosts) | ICMP echo |
| **Nmap Scanner** | Port scan a host using the locally installed nmap binary | TCP/UDP scan |
| **DNS Lookup** | A, AAAA, CNAME, MX, NS, TXT, SOA, PTR records | DNS UDP/53 |
| **WHOIS** | Domain and IP WHOIS lookup | TCP/43 |
| **BGP ASN Lookup** | ASN owner and announced prefixes via RDAP | HTTPS RDAP (ARIN / RIPE / APNIC) |
| **MAC Address Lookup** | OUI vendor identification | Offline OUI table |

### 🛠️ Troubleshooting

| Tool | Description | Method |
|------|-------------|--------|
| **NetDiag Report** | Streaming combined DNS + ping + MTR + port scan + MSS probe + SSL diagnostics for a single host | SSE (Server-Sent Events); mix of ICMP, TCP, DNS, TLS |
| **Ping** | Single-host ICMP ping | System ping |
| **MTR / Traceroute** | Hop-by-hop latency and loss | System mtr or traceroute |
| **fping** | Parallel multi-host ping (capped at 20 hosts) | System fping |
| **Port Scanner** | Per-host TCP port check with banner grab (capped at 100 ports) | TCP connect |
| **Switch Health** | Runs a strict allowlist of `show` commands over SSH and parses CPU, temperature, fans, PSU, and uptime. Hard-blocked from `configure`, `reload`, `clear`, `copy`, `write`, `erase` | SSH TCP/22 (read-only) |

### 🔒 Security

| Tool | Description | Method |
|------|-------------|--------|
| **SSL Inspector** | Certificate chain, expiry, ciphers, SANs | TLS handshake |
| **Config Audit** | Static audit of a pasted device configuration (Cisco / Arista / NX-OS style) for weak SNMP communities, telnet, default credentials, missing logging | Local text analysis (no network call) |
| **Ncat / Netcat** | Reference and command-builder UI | Informational only |
| **SSH Terminal** | In-browser xterm.js terminal to a target device | SSH TCP/22 |
| **SCP / SFTP Server** | Runs a local SFTP/SCP server so devices can push configurations to the workstation; native folder picker on both macOS (Finder) and Windows (Explorer) for the root directory | SSH TCP (configurable port) |

### 📊 Performance

| Tool | Description | Method |
|------|-------------|--------|
| **Internet Diagnostic** | Streaming WAN speed test — multi-CDN download/upload throughput, idle vs loaded latency, DNS/path/MTU probes, a GT3 gauge cluster, and 12 per-workload verdicts (VoIP, video calls, gaming, VDI, streaming, VPN, and more) with root-cause findings | SSE; HTTPS to public speed-test endpoints |
| **SockPerf** | TCP and UDP latency and throughput probe with built-in listener mode | TCP/UDP socket |
| **Bandwidth Calculator** | Throughput, transfer-time, and link-utilization math | Offline |
| **MSS Calculator** | Path MTU/MSS dual-probe (jumbo 9000 and standard 1500, DF-bit) — classifies jumbo, standard, tunnel-overhead, or fragmented paths | ICMP DF-bit probe |

### 🧰 Utilities

| Tool | Description | Method |
|------|-------------|--------|
| **Subnet Calculator** | IP/CIDR math, broadcast, usable range, wildcard mask; enumerates every `/N` block inside a user-selected parent network (Auto = 64 sibling blocks, or pin to `/24`, `/22`, etc.); input subnet highlighted in the table and PDF export | Offline |
| **IP Converter** | Base and format conversions for IPv4 and IPv6 | Offline |
| **IP Info** | Public-IP autodetect, geolocation, and PTR record | HTTPS to api.ipify.org and ipwho.is |
| **Wake-on-LAN** | Send magic packet to wake a host | UDP broadcast or unicast |

### 📚 Reference

| Tool | Description |
|------|-------------|
| **Port Reference** | Searchable port-to-service mapping (offline) |
| **CIDR Table** | CIDR-to-netmask reference (offline) |

### 🧪 Developer

| Tool | Description |
|------|-------------|
| **Activity Log** | Local audit log of every API call — method, path, status, duration. Auto-purges after 14 days. See [SECURITY.md](./SECURITY.md) for details and disclosure |
| **Wishlist** | Submit feature requests stored locally |
| **About / Version** | Build information and update check |

> **Read-only by design.** With the exception of the SSH Terminal (operator-typed commands) and the optional SCP/SFTP Server (accepts inbound file pushes), the toolset issues no writes to target devices — no SNMP SET, no configuration commands, no `reload`, `write`, `erase`, or `clear`.

---

## Why This Toolset

- **Comparable scope to commercial engineer toolsets** — discovery, troubleshooting, security, performance, and reference tools that traditionally require multiple separate utilities or a $1,500+ commercial license, consolidated into one application.
- **Runs entirely local** — no cloud account, no telemetry, no analytics. Outbound connections are limited to a single GitHub version-check at startup plus a small number of opt-in lookups (IP geolocation, ASN/RDAP) that only fire when the user clicks the corresponding tool.
- **Read-only safety by default** — the toolset will not modify a device configuration. Switch Health enforces this in code with a hard allowlist.
- **Real engineering** — built and refined against production enterprise infrastructure, not simulated labs.
- **One installer, no dependencies** — no Python install, no Docker, no Node, no npm. Download, install, launch.

---

## System Requirements

| Component | Requirement |
|-----------|-------------|
| Windows | Windows 10 1809 or later / Windows 11 (x64) |
| macOS | macOS 12 (Monterey) or later (Apple Silicon — arm64) |
| RAM | 512 MB minimum, 1 GB recommended |
| Disk | ~100 MB installed |
| Browser | Latest Chrome, Edge, Firefox, or Safari (auto-launched at startup) |
| Network — outbound | SNMP UDP/161, SSH TCP/22, DNS UDP/53, WHOIS TCP/43, ICMP, plus arbitrary TCP for the port scanner and SSL inspector |
| Network — inbound | Loopback only (`127.0.0.1`). The application binds to localhost; it never accepts external connections |

No Python installation, runtime, or other dependencies are required — everything is bundled.

---

## Installation

### Windows

1. Download `VIQ-Engineer-Toolset-Setup.exe` from the [latest beta release](https://github.com/virtualiqai/viq-releases-beta/releases/latest)
2. Double-click the installer to launch
3. Follow the prompts — the installer registers the application under **Add/Remove Programs** as **VIQ Engineer Toolset**
4. Launch from the Start Menu — your default browser will open automatically

### macOS

1. Download `VIQ-Engineer-Toolset.dmg` from the [latest beta release](https://github.com/virtualiqai/viq-releases-beta/releases/latest)
2. Open the DMG and drag the application to your **Applications** folder
3. Launch from Applications or Spotlight — your default browser will open automatically

> The installer is approximately **27 MB on Windows** and **31 MB on macOS**.

---

## ⚠️ First-Run Security Warnings

This release is distributed as an independent, non-store binary. Because the publisher's binaries have not yet built a reputation with Microsoft SmartScreen, and macOS Gatekeeper requires Apple Developer ID notarization, both operating systems may display a security warning on first launch. **This is expected for any independent software publisher** and does not indicate that the software is malicious. The full source code architecture is documented in [SECURITY.md](./SECURITY.md) for review.

### Windows — Microsoft SmartScreen

On first launch you may see: *"Windows protected your PC."*

1. Click **More info**
2. Click **Run anyway**

After a small number of installs across the user community, SmartScreen will begin recognizing the publisher automatically. A future release will be signed with a code-signing certificate to eliminate this warning entirely.

### macOS — Gatekeeper

On first launch you may see: *"VIQ Engineer Toolset cannot be opened because Apple cannot check it for malicious software"* or *"App is from an unidentified developer."*

**Option 1 — Right-click open:**
1. In Applications, right-click (or Control-click) the app
2. Choose **Open**
3. Click **Open** in the confirmation dialog

**Option 2 — System Settings:**
1. Try to open the app normally (it will be blocked)
2. Open **System Settings → Privacy & Security**
3. Scroll to the bottom — you'll see the blocked app
4. Click **Open Anyway**

A future release will be fully notarized through the Apple Developer Program to eliminate this warning entirely.

---

## Shutting Down Properly

⚠️ **Closing your browser does not stop the application.** The backend continues running in the background.

To shut down cleanly:

- **In-app:** Click the **Shutdown** button in the application header
- **Windows:** Right-click the system-tray icon and choose **Quit**
- **macOS:** Use the in-app Shutdown button (no menu-bar icon)

If you close the browser without shutting down, simply reopen the application's URL (it is printed in the launcher window) and use the Shutdown button.

---

## Privacy & Telemetry

Virtual IQ AI NetOps Toolset is designed to be transparent about every outbound connection. Network traffic from the toolset itself falls into three categories:

1. **Targeted device traffic** — SNMP, SSH, ICMP, and TCP probes go directly to whatever device or host you point the tool at.
2. **Version check (always-on at startup)** — A single HTTPS GET to either `raw.githubusercontent.com/virtualiqai/viq-releases/main/version.json` (stable channel) or `raw.githubusercontent.com/virtualiqai/viq-releases-beta/main/version.json` (beta channel) depending on the channel selected on the About page, to check whether a newer release is available. No system information, IP address, or identifier is sent.
3. **Opt-in third-party lookups (only when you click the tool)** — `api.ipify.org` and `ipwho.is` for the **IP Info** tool, and RDAP queries to `rdap.arin.net`, `rdap.db.ripe.net`, and `rdap.apnic.net` for **BGP ASN Lookup**.

The application performs **no telemetry, no analytics, no crash reporting, and no usage tracking.** Full details are in [SECURITY.md](./SECURITY.md).

---

## Roadmap

Planned for future releases:

- **NetAI (Q4 2026)** — Virtual IQ AI's flagship AI-augmented network operations platform. Predictive incident detection, autonomous Level-1 triage, and natural-language network querying. Currently in active development.
- Signed Windows installer (Authenticode)
- Notarized macOS application (Apple Developer ID)
- Linux build
- Multi-vendor Switch Health parser (Arista EOS, Juniper Junos, PAN-OS)
- Encrypted credential vault for frequently accessed targets
- IPv6 parity across all tools

---

## About the Author

**Muhammad Kashif (Kash)**
Senior Network Architect / AI Engineer · Founder, Virtual IQ AI

15+ years in enterprise network architecture across data centers, healthcare, and hybrid-cloud environments. Holds CCNP Enterprise, CCNP Data Center, CCNP Security, and Microsoft AZ-700 certifications.

Virtual IQ AI is a company developing AI-augmented network operations tooling for working engineers. The NetOps Toolset is the first publicly released product; NetAI follows in Q4 2026.

---

## Legal

This software is proprietary and distributed in compiled-binary form only. The source code is not publicly available.

- **License:** Free for personal and non-commercial use. See [LICENSE](./LICENSE) for full terms.
- **Terms of Use:** Plain-English acceptable use policy in [TERMS_OF_USE.md](./TERMS_OF_USE.md).
- **Third-party components:** Open-source library acknowledgments in [THIRD_PARTY_LICENSES.md](./THIRD_PARTY_LICENSES.md).
- **Trademark:** *Virtual IQ AI*™ is a trademark of Muhammad Kashif.
- **Affiliation disclaimer:** Virtual IQ AI LLC is not affiliated with, endorsed by, or sponsored by Cisco Systems, Arista Networks, Juniper Networks, Palo Alto Networks, Microsoft Corporation, Apple Inc., or any other vendor referenced in this documentation. All product names and trademarks are the property of their respective owners.

© 2026 Virtual IQ AI LLC. All rights reserved.

---

## Contact

- **Email:** info@virtualiqai.com
- **LinkedIn:** [linkedin.com/in/kash-muhammad](https://www.linkedin.com/in/kash-muhammad/)
- **GitHub:** [github.com/virtualiqai](https://github.com/virtualiqai)
- **Beta-channel issues & feature requests:** [github.com/virtualiqai/viq-releases-beta/issues](https://github.com/virtualiqai/viq-releases-beta/issues) — please report beta-specific regressions here so they stay separated from stable bugs
- **Stable-channel issues:** [github.com/virtualiqai/viq-releases/issues](https://github.com/virtualiqai/viq-releases/issues)

---

<div align="center">

**Virtual IQ AI** &nbsp;·&nbsp; Built by engineers, for engineers.

</div>
