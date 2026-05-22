<div align="center">

<img src="./assets/banners/github-readme-1280.png" alt="Virtual IQ AI NetOps Toolset — Beta" width="100%"/>

<br/>
<br/>

# Virtual IQ AI NetOps Toolset — Beta Channel

### Pre-release builds for testing

**This repository hosts pre-release builds of the Virtual IQ AI NetOps Toolset. Beta builds are functional but may contain unfinished features, regressions, or instability. For the stable production line, use the [main release repository](https://github.com/virtualiqai/viq-releases) instead.**

<br/>

[![Channel](https://img.shields.io/badge/channel-BETA-F59E0B?style=flat-square)](https://github.com/virtualiqai/viq-releases-beta/releases)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey?style=flat-square)](https://github.com/virtualiqai/viq-releases-beta/releases)
[![Stable](https://img.shields.io/badge/stable-virtualiqai%2Fviq--releases-2563EB?style=flat-square)](https://github.com/virtualiqai/viq-releases)
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

## What This Repository Is

The Virtual IQ AI NetOps Toolset ships through two parallel release channels:

| Channel | Repository | Audience | Stability |
|---------|------------|----------|-----------|
| **Stable** | [`virtualiqai/viq-releases`](https://github.com/virtualiqai/viq-releases) | Everyone — recommended default | Production-ready, validated against real network gear |
| **Beta** *(this repo)* | [`virtualiqai/viq-releases-beta`](https://github.com/virtualiqai/viq-releases-beta) | Testers and early adopters | Pre-release; features and fixes in validation |

Every beta build that proves itself in this channel is eventually promoted to the stable channel — the version-number suffix is the only difference (`v2.6.20-beta.3` here graduates to `v2.6.20` on the stable repo). Beta builds and stable builds are produced from the same source tree by the same CI pipeline; the only divergence is the tag suffix and which release page receives the artifact.

## Should I Use the Beta Channel?

Use the **beta** channel if any of these apply:

- You want to try a fix or feature before it lands in the stable line.
- You volunteered to help validate releases against your own environment.
- You're investigating a specific issue with the maintainer and were asked to install a particular beta build.

Use the **[stable channel](https://github.com/virtualiqai/viq-releases)** instead if:

- You're running this in a production-adjacent capacity.
- You need predictable behavior week-to-week.
- You don't want to hand-pick which build to download.

## How to Install a Beta Build

1. Open the [**latest release page**](https://github.com/virtualiqai/viq-releases-beta/releases/latest).
2. Download the installer for your platform:
   - **macOS** — `VIQ-Engineer-Toolset.dmg`
   - **Windows** — `VIQ-Engineer-Toolset-Setup.exe`
3. (Optional but recommended) Verify the download:
   ```bash
   # macOS / Linux
   shasum -a 256 -c SHA256SUMS.txt

   # Windows PowerShell
   Get-FileHash -Algorithm SHA256 VIQ-Engineer-Toolset-Setup.exe
   ```
4. Install. If you already have a stable build installed, the beta installer replaces it in place — your settings, activity log, and SFTP host key are preserved (they live in your user-writable data directory, not in the install folder).

To switch back to the stable channel later, simply install the latest build from [`viq-releases`](https://github.com/virtualiqai/viq-releases) over the top — same installer, same install path.

## In-App Channel Selector

The toolset's **About / Version** page exposes a channel toggle. When you set it to **Beta**, the in-app update check polls this repository's `version.json` for new beta builds. When set to **Stable**, it polls the stable repo. The channel choice persists between launches.

## Reporting Beta Issues

If a beta build misbehaves, please open an issue on this repository (not on the stable one) so beta-specific reports stay separated. Include:

- The beta tag you're running (visible on the About page — e.g., `v2.6.20-beta.2`).
- Your OS and version.
- Steps to reproduce and the expected vs. actual behavior.
- If applicable, the tail of `netops.log` from your data directory.

Stable-channel bugs continue to be tracked at [virtualiqai/viq-releases/issues](https://github.com/virtualiqai/viq-releases/issues).

## What's Retained Here

This repository keeps **up to three** of the most recent beta builds at any time. Older betas in the same release cycle are pruned as new ones ship; the last three are retained so testers can compare behavior across iterations or downgrade if a new beta introduces a regression. When a beta cycle promotes to stable, that version's betas may be removed as part of the cycle's cleanup.

## Code, Source, and License

The application source is **proprietary**. Both this repository and the stable release repository contain only the user-facing documentation and installer artifacts — the Python source tree is private and is shipped to end users only in AES-256-GCM encrypted form inside the installer bundle. See [SECURITY.md](./SECURITY.md) for the integrity and credential-handling details that apply identically to both channels.

Beta builds carry the same [License](./LICENSE) and [Terms of Use](./TERMS_OF_USE.md) as stable builds — personal and non-commercial use only, no warranty.

---

*© 2026 Virtual IQ AI · USA*
