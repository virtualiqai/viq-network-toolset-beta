# Changelog — Beta Channel

All notable changes to **Virtual IQ AI NetOps Toolset — Beta channel** are documented here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) and adheres to [Semantic Versioning](https://semver.org/) with the pre-release identifier extension (e.g., `2.6.21-beta.1`).

Beta builds are produced from the `beta` branch of the private source repository by the same CI pipeline that produces stable builds. When a beta has been validated, it is promoted to the stable channel by dropping the `-beta.N` suffix and tagging on `main`; promoted versions appear under their clean number in the [stable channel changelog](https://github.com/virtualiqai/viq-releases/blob/main/CHANGELOG.md).

The stable-channel history below is mirrored here so testers have full context about what each beta is built on top of. Beta-specific entries (e.g., `[2.6.21-beta.1]`) are added above the stable history as betas ship.

---

## [2.6.22-beta.6] — 2026-06-08

### 📶 Internet Diagnostic — multi-CDN download fixes under-reported throughput

The download test ran four parallel streams against a **single** CDN, so that one server's per-IP rate limit capped the reading well below line rate (≈60 % low on some corporate links, where a browser multi-CDN speed test pulled far more on the same circuit). Download now runs **eight streams with an asymmetric split**: four stay on the probe-race-winning CDN — preserving the previous number as a floor, so a proxy that blocks the alternate CDNs can never make the result *worse* — and four fan out across the rest of the CDN pool for the additional headroom. A blocked alternate contributes zero rather than dragging the total down. Per-CDN, per-stream results are now written to the log for field diagnosis.

### 🩺 Switch Health — environment parsing fixed across Cisco platforms

Fan, power, and temperature checks frequently returned **"Unknown — output could not be parsed"**. Two root causes, both addressed:

- **Wrong command for the device.** The platform was a manual NX-OS / IOS-XE selection; choosing the wrong one ran a command the device rejects (e.g. `show environment fan` on IOS-XE → `% Invalid input`), and the rejection text reached the parser as "unparseable" data. The tool now **auto-detects the platform from `show version`** and runs the correct universal command — `show environment all` on IOS-XE, `show environment` on NX-OS.
- **Cross-contaminated parsing.** The combined environment output was fed whole to every section parser, so the power parser counted temperature-sensor rows as power supplies (a Nexus chassis reported six supplies instead of two). Output is now **section-split** so the fan, power, and temperature parsers each see only their own block, and CLI rejection lines are stripped so an unsupported command can't masquerade as bad sensor data. Verified against Catalyst 9600 (IOS-XE 17.10) and Nexus 9000 output, including full sensor-name preservation.

## [2.6.22-beta.5] — 2026-06-06

### 🪟 Internet Diagnostic — gauge needle no longer freezes at the start (Windows)

On Windows the live download/upload gauge needles could sit frozen at the beginning of a test while easing smoothly on macOS. Cause: a leftover inline `transition:transform` on all five needle elements fought the `requestAnimationFrame` follower that rewrites the SVG transform every frame — inert under the macOS WebKit engine but honored by Chromium / WebView2, which restarted a fresh 1.4 s ease on every frame and never converged. The stale CSS transition was removed from all five needles; the rAF follower now owns all needle easing.

## [2.6.22-beta.4] — 2026-06-04

### 📶 Internet Diagnostic — throughput diagnostics

Added structured download/upload result logging (target CDN, strategy, per-stream Mbps, peak, and sample count) to root-cause the under-reported download throughput, and removed the startup instrument sweep on the gauges.

### 🛰️ SNMP Port Mapper — larger routers

The Layer-3 ARP-table walk timeout was raised from 22 s to 90 s for large distribution routers whose `ipNetToMediaTable` is slow to enumerate, with elapsed-time logging added so a slow walk is visible in the log.

### 🧾 Logging

All successful (2xx / 3xx) Werkzeug HTTP access lines are now dropped from `netops.log`; only real tool actions and 4xx / 5xx responses remain.

## [2.6.22-beta.3] — 2026-06-03

### 🔐 TLS — trust the OS certificate store for corporate TLS inspection

On networks that re-sign TLS at an inspecting proxy, every Internet Diagnostic HTTPS request failed certificate verification because the app trusted only its bundled Mozilla CA set — uploads failed outright and downloads were degraded. The app now injects `truststore` at startup so TLS verifies against the **operating-system trust store** (Windows SChannel / macOS keychain), where corporate CAs are installed. `/api/health` now reports `has_truststore` and `tls_trust` (`os-store` vs `certifi`) so the fix can be confirmed on a target machine.

### 📶 Internet Diagnostic

The startup instrument sweep on the gauges is now routed through the easing path instead of snapping.

### 🧾 Logging

High-frequency health and status-poll access lines are filtered out and log rotation was tightened to bound the file size.

## [2.6.22-beta.2] — 2026-06-01

### 🪟 SCP / SFTP — Windows folder picker repaired and made diagnosable

The "Browse" button's native Windows folder dialog returned no path in the frozen build. The `-NonInteractive` flag (which made the dialog return *Cancel* in the windowless process) was removed, the console is now hidden via `CREATE_NO_WINDOW` instead of `-WindowStyle Hidden`, an owner form is activated so the dialog takes foreground, and the helper emits explicit `PICK_OK` / `PICK_CANCEL` / `PICK_ERR` sentinels so an empty result surfaces as a real error instead of a silent cancel.

### 🛰️ SNMP — Windows UDP event-loop policy

Forced `WindowsSelectorEventLoopPolicy` for pysnmp's UDP transport on Windows (the default Proactor loop has fragile datagram support), with `set_event_loop` applied per call. A dedicated Windows CI job and an snmpsim-backed Layer-3 / ARP reproduction test were added; the live test is marked expected-fail pending the deeper SNMP execution-model fix.

### 📶 Internet Diagnostic — connection reuse & needle easing (first pass)

Download workers now reuse one TCP/TLS connection across back-to-back fetches (per-fetch re-handshakes were inflating the rate denominator), and the live download/upload gauge needles ease toward each sample instead of snapping.

### 🧪 Testing infrastructure

Added a local protocol lab (snmpsim, OpenSSH, SFTP, a bandwidth-throttled Caddy, Unbound DNS, ICMP target), a frozen-binary smoke suite, and a Playwright end-to-end scaffold; CI checkout/setup actions were updated.

## [2.6.22-beta.1] — 2026-05-29

### 🛰️ SNMP Port Mapper — L3 enrichment hardened across every Cisco platform pair

The Port Mapper now produces a usable port → MAC → IP → hostname result on every realistic combination of Cisco IOS Catalyst and NX-OS as the L2 access and L3 gateway pair. Previous betas left whole classes of switches with an empty MAC tab and zero L3 matches; this build addresses every code path that was producing those empty results.

- **Nexus VLAN discovery now unions three sources** (SVI interface names ∪ `vmVlan` per-port access VLAN ∪ `vtpVlanState`). The pre-fix logic walked only SVI names, so a pure-L2 access Nexus without any SVIs returned an empty VLAN list and Strategy E (community-context FDB polling) was silently skipped. The MAC table came back empty on those switches even though the device was fully reachable.
- **Layer 3 ARP correlation** continues to walk the legacy `ipNetToMediaTable`, with automatic fallback to RFC 4293 `ipNetToPhysicalTable` on devices that only expose the newer table. The MAC normalization between the L2 FDB and the L3 ARP table is now uniform across both sources, so case and format differences can never cause a false-negative match.
- **Structured `snmp:` log lines** are emitted across the full scan path (entry parameters, L2 device platform, FDB strategy selected, L3 device probe + `sysDescr`, ARP walk row counts, RFC 4293 fallback if used, ARP/MAC correlation result, and a sample mismatch warning when zero MACs overlap). The log file at `~/Library/Application Support/VIQ Engineer Toolset/netops.log` (macOS) or `%LOCALAPPDATA%\VIQ Engineer Toolset\netops.log` (Windows) now contains enough evidence to diagnose any unsuccessful scan without rerunning it.
- **Uplink ports no longer report dozens of "endpoints."** A switch's FDB legitimately learns the *chassis MAC of the upstream device* on its uplink port; when the L3 ARP table maps that MAC to forty SVI / management IPs (perfectly normal for a distribution switch terminating many subnets), the previous build attached all forty IPs to the uplink row, suggesting the uplink had forty hosts behind it. The per-MAC `ip_addresses` list is still populated (so the MAC tab continues to show the correlated identities) but the per-port aggregation and the `endpoints` array now skip ports whose role is `uplink`. The frontend Port Inventory export honors the same rule.

### 🩺 Diagnostic panel — every scan now tells you what worked and what to fix

Scan responses include a structured `diagnostics` array that the UI renders as colour-coded cards (error / warning / info) next to the port-mapper summary. Each card includes a title, a one-paragraph explanation, and a bulleted list of suggested checks. The categories cover:

- **Layer 3 device unreachable** (sysDescr empty): suggested `snmpwalk` invocation, community confirmation, version mismatch, SNMP ACL on the device, UDP/161 filter.
- **Layer 3 reachable but ARP table empty**: VRF context guidance (NX-OS ARP is per-VRF), SNMP view exclusion of the IP-MIB subtree, and the "did you point at a device that hosts SVIs for these VLANs?" check.
- **ARP collected but zero overlap with the switch FDB**: gateway-ownership check, ping-sweep to warm the ARP cache, and a side-by-side sample of MACs from each side so OUI mismatches become obvious at a glance.
- **L2 FDB empty on NX-OS**: VLAN discovery breakdown, community-context verification, BRIDGE-MIB view check, and an L3-fallback suggestion.
- **L2 FDB empty on Catalyst / non-Nexus**: BRIDGE-MIB exposure probe and per-VLAN community-suffix guidance.
- **Disabled / successful states** are also captured as `info` cards so the panel always describes the outcome.

The cards never echo the community string or any other secret in plaintext (see the security section below).

### 🧮 Subnet Calculator — parent block enumeration is finally anchored to your input

The "All Possible Subnets In Parent Block" section used to enumerate `/N` blocks starting at the classful parent of the first octet, so an input of `10.93.139.224/26` produced 64 rows starting at `10.0.0.0/26`, `10.0.0.64/26`, … unrelated to the user's actual address space. The redesigned tool exposes a new **Parent Prefix** input next to the existing CIDR field:

- **Auto** (default) picks `input_prefix − 6` and produces 64 sibling blocks centered on a real supernet of the input. For `/26` input that becomes a `/20` parent containing 64 `/26` rows; the input's own subnet is highlighted in the table and in the exported PDF.
- Explicit prefixes from `/8` through `/26` are available for users who already know their allocation boundary (e.g., picking `/24` for the example above produces exactly the four `/26` blocks within `10.93.139.0/24`).
- The parent network is computed by masking the **input IP** with the parent prefix, so the enumeration always contains the user's own subnet. Row count is capped at 256 with a "truncated" indicator if a very broad parent is picked. The PDF export's section heading shows the resolved parent label (e.g., "All /26 Subnets Inside Parent 10.93.128.0/20 (64)").

### 🪟 SCP / SFTP — folder picker now works on Windows

The "Browse" button next to **Root Directory** on the SCP / SFTP Server tool was previously macOS-only and returned a hard `501` on Windows. It now spawns a native Windows folder selection dialog via PowerShell + `System.Windows.Forms.FolderBrowserDialog` (with `-STA` apartment threading, hidden console, and a topmost owner form so the dialog appears above the app window). The macOS code path (osascript `choose folder`) is unchanged. Cancellation maps to `{cancelled: true}` on both platforms and leaves the Root Directory input untouched.

### 🔒 Security — credential redaction audit

A full audit of every log statement, API response field, persisted activity record, and PDF export confirmed that credentials (SNMP community strings, license activation JWT, SSH passwords, private keys, API tokens) are not exposed in plaintext anywhere a user-controlled file ends up. A small number of leaks were introduced earlier in the `v2.6.22-beta` series and are now eliminated:

- The "L3 device unreachable" log warning was emitting the L3 community string in cleartext. It now goes through a new `_redact_secret()` helper that prints `<redacted:len=N>` — enough to verify a value was provided without disclosing it.
- The diagnostic cards' suggested `snmpwalk` commands no longer embed the community. The placeholder `<community>` is shown instead, accompanied by a sentence telling the user to re-enter the value if they suspect a typo.
- `/api/license/activate` body persistence is now disabled outright (added to `_SKIP_BODY_LOG_PREFIXES`). The previous behavior redacted the `key` field via `_redact_body()`, but pre-redaction commits had let cleartext JWTs through; the activation attempt is still captured via the endpoint / status / timestamp.
- A new `_purge_legacy_unredacted_bodies()` runs on every startup. It nulls out any `request_body` in `activity_log` that still mentions a sensitive key in plaintext (`community`, `password`, `private_key`, `token`, etc.) or that targets `/api/license/`. Existing installs that were used before the redaction commit landed will see their legacy plaintext rows wiped on the next launch.

### 🧪 Per-tool pre-build smoke harness

`tests/smoke_tools.py` is a new pre-build gate that exercises every user-facing tool's `/api` endpoint with a benign payload (or a mocked subprocess for OS-specific dialogs) and reports a one-row pass/fail summary. The full harness covers 32 tools in under three seconds when online (it auto-skips the live-network tools when there's no internet). It runs as part of every local DMG build and every release tag.

### 🚦 CI / build pipeline

`actions/checkout` was bumped from `@v4` to `@v5` and `actions/setup-python` from `@v5` to `@v6` so the release workflow runs on Node.js 24 ahead of GitHub's June 2026 default switch.

---

## [2.6.21-beta.2] — 2026-05-26

### 🌐 Internet Diagnostic — multi-CDN routing + sustained-rate display

- Live download and upload throughput now shows the sustained rate (cumulative bits / wall-clock elapsed) instead of a peak-of-windowed-average. The displayed number converges to the final result, never overshoots, never end-drops.
- The test start timestamp is now captured before workers launch (was: on first callback, which over-counted bits arriving during the worker stagger window).
- Download path adds five LibreSpeed CDNs (Clouvider NYC / ATL / LA, Sharktech Chicago, plus the existing default), spread across the configured worker pool so a single overloaded edge can't cap the result.
- Jitter calibration now reports the standard-deviation of inter-packet arrival times measured during the steady-state phase only, excluding the ramp-up window.

### 🛠️ Infrastructure

- `gh release create --latest=true` is forced on prereleases so the `/releases/latest` pointer resolves correctly on the beta channel (previously the README's "Download Latest Beta" badge landed on an empty listing).

---

## Stable Channel History

The entries below mirror the stable channel's changelog and are included here for context. They describe the production line this beta channel is built on top of.

---

## [2.6.19] — 2026-05-22

### 🪵 `netops.log` Now Lands Where Users Can Find It

The previous build's diagnostic logging never produced a file on Windows because the rotating file handler was writing into `%PROGRAMFILES%\VIQ Engineer Toolset\assets\` — an admin-only path. Python's `RotatingFileHandler` silently swallows `PermissionError` on first open, so the log appeared to be working with no on-disk evidence to debug from.

v2.6.19 routes the log (and future runtime state) through a per-platform user-writable data directory:

- **Windows:** `%LOCALAPPDATA%\VIQ Engineer Toolset\netops.log`
- **macOS:** `~/Library/Application Support/VIQ Engineer Toolset/netops.log`
- **Linux:** `$XDG_DATA_HOME/viq-engineer-toolset/netops.log` (default `~/.local/share/…`)
- Override available via the `NETOPS_DATA_DIR` environment variable.
- Falls back to the original install-bundle path if the user dir can't be created.

The resolved log path is now emitted on startup and surfaced through `/api/health` alongside the existing `data_dir` and `base_dir` so it's discoverable from the UI. The diagnostic wrappers around the SCP file path (`open` / `seek` / `read` / `write` / `close`) added in v2.6.18 finally have somewhere to write to.

### ✨ UI — Glow Survives `prefers-reduced-motion: reduce`

Windows 11's Accessibility → Visual Effects → "Animation effects" setting (often Off on managed installs) makes Chrome/Edge advertise `prefers-reduced-motion: reduce`. The toolset's media query honors that by stripping every `animation:` rule — but the brand mark (header top-left) and the About page avatar derived their entire sapphire glow from the `kGlow` keyframe's `box-shadow`, with no base shadow on the static class. Animation off therefore meant glow off.

A static `box-shadow` fallback now lives inside the same media query so the brand identity stays visible even when animations are disabled by the OS.

---

## [2.6.18] — 2026-05-22

### 🩺 Diagnostic Build — Windows SCP Write Path

A targeted observability release for an open issue where Cisco IOS `copy running-config scp:` to a Windows VIQ Toolset host fails with `%Error writing scp://… (Unknown error -1)` even though the same client uploads successfully to a macOS VIQ host. asyncssh's SCP sink catches only `(OSError, SFTPError)` — any other exception propagates as a generic fatal error and Cisco renders it as `Unknown error -1`, with no server-side trace.

This build adds:

- `asyncssh` logger raised to **INFO** at startup so its SCP/SFTP events flow into the existing `netops.log` rotating file at the install root.
- Explicit `log.exception()` wrappers around the SFTP server's `open()` and the tracked-transfer file's `seek` / `read` / `write` / `close` so a full traceback (with `local_path`, `flags`, `pflags`, `parent_writable`, `bytes_so_far`) lands in `netops.log` regardless of which step throws.
- A new explicit `seek()` method on the tracked file wrapper — previously `seek` slipped through `__getattr__`, hiding any seek-stage failure from the existing error-marking path.

No behavior changes for users whose SCP/SFTP already works. After reproducing the Cisco error on Windows, send the most recent `netops.log` from `%PROGRAMFILES%\VIQ Engineer Toolset\netops.log` for diagnosis.

---

## [2.6.17] — 2026-05-21

### 🔧 SSH Stack Restored for Legacy Network Gear

Older Cisco, Arista, Juniper, Huawei, and MikroTik devices became unreachable in v2.6.16 because the upstream `paramiko` 4.x line removed the `DSSKey` class and tightened the default preferred-algorithm lists (ssh-rsa, ssh-dss, SHA-1 DH KEX). v2.6.17 pins `paramiko>=3.4,<4` so legacy host-key types, KEX algorithms, and ciphers are back in the default negotiation set without monkey-patching. SSH Terminal, Switch Health, and Config Audit now work against the same hardware they did in v2.6.1.

### 🧪 SSH Compatibility Test Suite

A new `tests/test_ssh_tools.py` pytest suite spins up an in-process paramiko mock SSH server (RSA + Ed25519 host keys) and exercises `_ssh_open_temp_shell`, `api_ssh_connect`, and the `/api/health` dependency diagnostic end-to-end. CI now runs this suite on both macOS and Windows runners *before* the PyInstaller build — if SSH is broken on either platform, the release is blocked.

### 🩺 `/api/health` Dependency Diagnostic

The health endpoint now reports a `dependencies` map indicating which Python libraries loaded successfully (`paramiko`, `asyncssh`, `cryptography`, `pysnmp`, …) and which feature blocks are affected when any are missing. SSH-related errors are also enriched with the offered algorithm lists from the failing handshake, making "Incompatible ssh peer" failures actionable instead of opaque.

---

## [2.6.16] — 2026-05-22

### Highlights

This release consolidates ~15 iterative beta builds (v2.6.2 through v2.6.16) into the new production line. The headline improvements are in **SCP/SFTP**, **Config Audit**, and **Switch Health**, plus a number of UX and reliability fixes the user community asked for.

### 🔐 SCP/SFTP Server

- **Port-22 forwarding via `pfctl` rdr** on macOS. The embedded SCP/SFTP listener still binds an unprivileged port (default 2222) so no part of the toolset runs as root continuously. Clicking "Install Port Forwarding" pops a single macOS admin-password prompt, after which a pf rdr rule redirects external port-22 traffic to the listener — letting network devices push to the standard SCP port without disabling macOS Remote Login, and without `sudo`-launching the whole app. Rule targets the live interface IP (via the pf `(iface)` macro) so packets stay routable; an earlier 127.0.0.1 redirect hit BSD's "martian packet" anti-spoofing drop. Removal is a single click that restores `/etc/pf.conf`.
- **Listener Host dropdown** — a Wireshark-style interface picker. The SCP/SFTP page enumerates every live IPv4 interface (Wi-Fi, Ethernet, USB tether, VPN tunnel, bridge) and lets you pick which one(s) to bind on. Port forwarding rules are installed only on the selected interface, so an active corporate VPN tunnel isn't accidentally exposed.
- **Native folder picker** for the Root Directory field (`📂 Browse…` button opens macOS's standard Finder folder-selection dialog). Plus a "Quick pick" dropdown for common paths (`~/Documents/VIQ-SFTP-Root`, `~/Downloads`, etc.). The text input still accepts any absolute path manually.
- **Default Root Directory** moved from inside the .app bundle to `~/Documents/VIQ-SFTP-Root` so files survive reinstalls and aren't hidden inside the application package.
- "Forwarding port 22 → 2222 on: en0 (10.93.139.213)" status line surfaces which interface is actually forwarded, so you don't have to drop to `sudo pfctl -s nat` to check.

### 🔎 Config Audit

- **Side-by-side aligned diff** replaces the bottom unified-diff dump. Startup config left, running config right, scroll-synced, with green/red/amber row colors and word-level highlighting on changed lines. New "Added / Removed / Changed" tile counts.
- **Whitespace-agnostic comparison** — config lines are normalized for the diff alignment (collapsed internal whitespace, stripped trailing whitespace) but the original device formatting is preserved in the display panes. False-positive diffs from pager artifacts and spacing inconsistencies are gone.
- **`--More--` pager handling** — when a Cisco device pauses output with `--More--`, the SSH read loop now sends a space character to advance. Earlier versions only stripped the literal text and let the device sit at the pager indefinitely, so `show running-config` returned ~60 lines (one PTY page) instead of the full config.
- **Boilerplate filtering** — lines like `Building configuration…`, timestamp comments, and bare `!` separators are excluded from the alignment so the diff focuses on real configuration drift.
- **Drift Status tile** shows the running count of differences (`14 diffs` instead of the previous "Difference Found" that didn't fit).

### 📡 Switch Health

- **SSE streaming with a 7-phase progress stepper** — connect, prep, version & inventory, CPU & resources, power · fan · temperature, interface errors, score health. Each phase reports completion text as it happens instead of one big spinner-and-wait.
- **Cancel button** appears on running operations; converts to "Dismiss" after failure so the post-failure stepper can be cleared.

### 🛡️ SSH connectivity

- **Legacy host-key + KEX algorithms** re-enabled for older Cisco / Arista / MikroTik / Huawei gear (`ssh-rsa`, `ssh-dss`, `diffie-hellman-group14-sha1`, group1-sha1, group-exchange-sha1, `aes128-cbc`, `3des-cbc`, `hmac-sha1`). Paramiko 3.x disables these by default, which produced "Incompatible ssh peer (no acceptable host key)" against the kind of equipment most engineers actually have to work with.
- Same legacy treatment applied to the system-`ssh` fallback path via `HostKeyAlgorithms=+ssh-rsa,ssh-dss`, `KexAlgorithms=+...`, `Ciphers=+aes128-cbc,3des-cbc`.

### 🪟 Windows

- **PyInstaller spec now uses `collect_all()`** for `paramiko`, `cryptography`, `cffi`, `bcrypt`, `nacl`, `asyncssh`, `pysnmp`, `pyasn1` — pulls in the full native-extension and DLL trees that the static heuristic analysis was missing. Earlier Windows builds silently shipped without working SSH; v2.6.16 fixes Config Audit, Switch Health, and SSH Terminal on Windows.

### 🔔 Error log + diagnostics

- **Bell icon in the header** opens a session-scoped error log panel showing recent errors with timestamps, sources (`SCP/SFTP`, `Switch Health`, `Config Audit`, `About`, etc.), and detail strings. Red badge with the unread count. Clear button at the top of the panel.
- **`/api/health` exposes a dependency map** showing which optional native deps loaded successfully and what features each blocks if it fails. Future Windows-vs-macOS bugs diagnose in 5 seconds.

### ℹ️ About / Version

- **GitHub link** in the Developer card now actually points at the public release repo.
- **"Check for Updates"** now re-fetches the upstream `version.json` on each click instead of returning a startup-cached value. Uses proper numeric version comparison (so `2.6.10 > 2.6.9` is correctly recognized as an update — string compare previously got it wrong because `'9' > '1'` as characters).
- About page shows "Last Checked" timestamp and any check error inline.

### 🔧 CI / build pipeline

- **Workflow refactored to upload directly to `viq-releases`** — no intermediate GitHub Actions artifact storage. The free-tier 500 MB artifact quota was exhausted during the v2.6.x rapid-iteration cycle, blocking releases until the cache recalculated. The new pipeline has four jobs (`pre-release` → `build-macos` / `build-windows` in parallel → `finalize`) and is immune to the quota cliff.
- **SHA-256 checksums** are now generated in the `finalize` step from the actual uploaded artifacts (not intermediates), so they always match what users actually download.

### 🐛 Bug fixes / polish

- Stepper UI no longer auto-hides on failure; cancel/dismiss button persists so users can read the error before clearing.
- Per-tool error attribution in the bell log (errors show as "SCP/SFTP" or "Switch Health" instead of generic "Toolset").
- Activity-log middleware redacts credentials (community strings, passwords, SSH keys) before persistence — no-op on a public release but the protection has been there since v2.4.2 and applies to this release.

---

## [2.6.1] — 2026-05-21

### Brand Identity v1.0

This release introduces the official Virtual IQ AI brand identity across the application and repository.

- **New app icon.** The Windows installer, macOS DMG, taskbar/Dock icon, and system-tray glyph all use the new IQ ligature mark.
- **In-app header brand.** The dashboard header now displays the IQ mark inside a small dark card with the same pulsing sapphire glow used on the About avatar, alongside the "Virtual IQ AI" wordmark and "by Muhammad Kashif (KASH)" author attribution.
- **About / Version page.** Developer card now uses the brand mark in place of the previous lettermark; About copy updated to "Virtual IQ AI · USA".
- **Web favicons.** Full favicon set added — SVG, ICO multi-res, 16/32/192/512 PNGs, and Apple touch icon — so browser tabs and bookmarks reflect the new brand.
- **Theme color.** Added `theme-color` meta tag (`#0A0E1A`) so mobile and desktop chrome blend with the dark UI.
- **README banner.** GitHub repo home page now leads with the production brand banner instead of the placeholder Shields.io badge.

No functional changes to any tool. All read-only safety guarantees, rate limits, credential redaction, and SHA-256 checksum publishing carry over unchanged from v2.5.x.

---

## [2.5.1] — 2026-05-21

### UI Polish

- **Header attribution.** Split the dim single-line "Built by KASH" into three legible stacked lines so the author name is fully visible. Brand color and developer card refreshed with full bio and certifications (CCNP Enterprise / Data Center / Security, Microsoft AZ-700).
- Patch over v2.5.0 — no functional or security changes.

---

## [2.5.0] — 2026-05-21

### Verifiable Release Artifacts

This release introduces published download integrity verification.

- **SHA-256 checksums published with every release.** A `SHA256SUMS.txt` file is now attached to each GitHub release alongside the DMG and Setup.exe. Users can verify the integrity of downloaded artifacts before installation:
  - macOS / Linux: `shasum -a 256 -c SHA256SUMS.txt`
  - Windows: `Get-FileHash -Algorithm SHA256 VIQ-Engineer-Toolset-Setup.exe`
- All other functionality unchanged from v2.4.2.

---

## [2.4.2] — 2026-05-21

### Initial Public Release

This is the first publicly available release of Virtual IQ AI NetOps Toolset. The application has been under continuous development against production enterprise infrastructure prior to this public availability. Version 2.4.2 reflects the application's internal release history; the product debuts publicly at this version.

### 🚀 Application Platform

- Cross-platform desktop application packaged as native installers for **Windows (NSIS)** and **macOS (DMG)**
- Bundled **Python 3.13** runtime — no separate Python installation required
- **Flask + flask-cors** backend bound to loopback only (`127.0.0.1`); never exposed externally
- Automatic port selection: tries `5001`, `7421`, `8765`, `9080`, `9090` in order, with ephemeral fallback
- Automatic browser launch on startup
- **Windows system-tray icon** with Quit menu (pystray)
- **Graceful shutdown** via in-app Shutdown button, system-tray menu, or SIGINT/SIGTERM
- **AES-256-GCM source-code protection** with native loader extension
- **Per-IP, per-tool rate limiting** with hard host and port caps to prevent accidental overrun:
  - SNMP: 6 rpm / 1 concurrent
  - Sweep: 3 rpm / 1 concurrent, max 254 hosts
  - Port scan: 5 rpm / 1 concurrent, max 100 ports
  - NetDiag: 4 rpm / 1 concurrent
  - fping: max 20 hosts
- **Loopback-only API** with optional PBKDF2-SHA256 (600k iterations) local-user authentication

### 🔐 Privacy & Credential Protection

- **No telemetry, no analytics, no crash reporting, no usage tracking**
- Only outbound HTTPS request on startup is the GitHub version check
- Third-party lookups (IP Info, BGP ASN) are strictly opt-in per tool invocation
- **Device credentials are never persisted between runs** — no master credential vault exists
- **Activity log automatically redacts credential fields before insert.** A walker traverses each JSON request body and replaces values for approximately 20 credential-related field names (SNMP communities, SSH passwords, private keys, auth tokens) with `***REDACTED***`. Matching is case-insensitive and recursive into nested objects/arrays
- **`/api/ssh/*` and `/api/filetransfer/user/*` request bodies are excluded from activity logging entirely** as defense in depth
- Six unit tests cover the redaction logic across SNMP, password, private-key, nested-object, non-JSON, empty, and case-insensitivity scenarios

### 🔍 Discovery Tools

- **SNMP Port Mapper** — Walks IF-MIB, BRIDGE-MIB, Q-BRIDGE-MIB, IP-MIB, LLDP-MIB, and CDP-MIB; correlates port-to-MAC-to-IP-to-neighbor mappings with optional L3 router correlation
- **Ping Sweep** — ICMP sweep across a CIDR
- **Nmap Scanner** — Wraps the locally installed nmap binary
- **DNS Lookup** — Full record-type support (A, AAAA, CNAME, MX, NS, TXT, SOA, PTR) via dnspython
- **WHOIS** — Domain and IP WHOIS lookups against registrar whois servers
- **BGP ASN Lookup** — ASN ownership and announced prefixes via RDAP (ARIN/RIPE/APNIC)
- **MAC Address Lookup** — Offline OUI vendor identification

### 🛠️ Troubleshooting Tools

- **NetDiag Report** — Streaming combined diagnostic bundle (DNS + ping + MTR + port scan + MSS probe + SSL) over Server-Sent Events for a single host
- **Ping** — Single-host ICMP probe
- **MTR / Traceroute** — Hop-by-hop latency and loss analysis
- **fping** — Parallel multi-host ICMP
- **Port Scanner** — TCP connect with optional banner grab
- **Switch Health** — Vendor-neutral CPU/temperature/fan/PSU/uptime parsing over SSH. **Strict allowlist** enforced in code — blocks `configure`, `reload`, `clear`, `copy`, `write`, `erase`, and other write commands

### 🔒 Security Tools

- **SSL Inspector** — TLS certificate chain, validity, ciphers, and SAN inspection
- **Config Audit** — Local-only static audit of pasted device configurations for weak SNMP, telnet, default credentials, and missing logging
- **Ncat / Netcat** — Command builder and reference UI
- **SSH Terminal** — In-browser xterm.js terminal session via paramiko (with system-ssh fallback). Supports password, RSA, Ed25519, ECDSA, and DSS authentication
- **SCP / SFTP Server** — Local SFTP/SCP server (asyncssh) for inbound configuration pushes from devices

### 📊 Performance Tools

- **SockPerf** — TCP/UDP latency and throughput probe with built-in listener mode
- **Bandwidth Calculator** — Throughput, transfer-time, and utilization math
- **MSS Calculator** — Dual-probe path MTU/MSS (jumbo 9000 + standard 1500, DF-bit) with jumbo/standard/tunnel/fragmented classification

### 🧰 Utilities

- **Subnet Calculator** — CIDR, broadcast, usable range, wildcard mask
- **IP Converter** — Base and format conversions for IPv4 and IPv6
- **IP Info** — Public IPv4 autodetect, geolocation, and PTR resolution
- **Wake-on-LAN** — Magic packet sender (UDP broadcast or unicast)

### 📚 Reference

- **Port Reference** — Offline searchable port-to-service mapping
- **CIDR Table** — Offline CIDR-to-netmask reference

### 🧪 Developer Tools

- **Activity Log** — Local SQLite audit log of every API call with 14-day auto-purge and automatic credential redaction
- **Wishlist** — Local feature-request submission
- **About / Version** — Build info and update check

### ⚠️ Known Limitations

- Windows installer is not yet code-signed; SmartScreen may warn on first launch
- macOS DMG signing depends on CI build secrets; verify with `spctl --assess` on your downloaded DMG
- No SHA-256 checksums published yet
- No Linux build available
- Switch Health output parser supports Cisco IOS / IOS-XE / NX-OS syntax only
- Nmap, MTR, and fping tools require the corresponding binary installed locally — they are not bundled
- IPv6 end-to-end support is partial — host validators accept IPv6 but several tools assume IPv4 paths
- Banner-grab in Port Scanner may trigger IDS/IPS on hardened networks

---

## Upcoming Releases

Tracked, in no particular order:

- **NetAI Platform** *(Q4 2026)* — Flagship AI-augmented network operations platform from Virtual IQ AI. Predictive incident detection, autonomous Level-1 alert triage, and natural-language network querying. Currently in active development.
- **Signed Windows installer** (Authenticode)
- **Notarized macOS application** (Apple Developer ID + notarization)
- **Encrypted credential vault** for frequently accessed targets
- **Linux build** (deb / AppImage)
- **Multi-vendor Switch Health** parsers (Arista EOS, Juniper Junos, PAN-OS)
- **Full IPv6 parity** across all tools
- **Light/dark theme toggle** (currently dark only)

---

*© 2026 Virtual IQ AI LLC. All rights reserved.*
