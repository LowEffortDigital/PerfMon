# PerfMon for macOS — Distribution & Releases

Official binary releases and installer packages for **PerfMon (Task Manager for macOS)**, created by [Low Effort Development Studios](https://loweffort.digital).

---

## ⬇️ Downloads

| Release | Version | macOS Requirement | Architecture | Package |
|:---|:---|:---|:---|:---|
| **Latest** | **v1.0.0** | macOS 14.0+ (Sonoma, Sequoia) | Universal (Apple Silicon & Intel) | [**Download PerfMon.dmg**](https://github.com/LowEffortDigital/PerfMon/releases/latest/download/PerfMon.dmg) |

---

## 🚀 Installation Guide

1. Download [**`PerfMon.dmg`**](https://github.com/LowEffortDigital/PerfMon/releases/latest/download/PerfMon.dmg).
2. Double-click the `.dmg` file to mount it.
3. Drag **Task Manager** to your **Applications** folder.
4. Launch **Task Manager** from Launchpad, Spotlight, or Applications.

> **Note on Gatekeeper**: If macOS prompts that the application cannot be checked on first launch:
> - Right-click **Task Manager.app** in Applications ➔ Click **Open** ➔ Click **Open Anyway** (or go to *System Settings ➔ Privacy & Security ➔ Open Anyway*).
> - Alternatively, run this single command in Terminal:
>   ```bash
>   xattr -cr "/Applications/Task Manager.app"
>   ```

---

## 🌟 Key Features

- **Windows 11-Style Task Manager**: Recreated natively in Swift/AppKit with negligible overhead (< 1.5% CPU load).
- **Near Real-Time Desktop Widgets**: High-frequency (0.5s–1s) floating HUD ribbon and pop-out monitors for CPU, RAM, Disk, and Network.
- **Darwin/Mach Telemetry**: Native kernel counters, per-core CPU, APFS disk throughput, network bandwidth, and Apple Silicon GPU utilization.
- **macOS Menu Bar Extra**: Live system stats directly in your menu bar.
- **Dynamic Theming**: Multiple color themes (Dark Mode, Vivid Purple, Hot Pink, Monochrome, Light Mode) with real-time Dock icon switching.

---

## 📄 License & Terms of Use

Copyright © 2026 Low Effort Development Studios / James Humphrey. All rights reserved.

**Proprietary Freeware License**:
* **Permitted Use**: You are granted a free, non-exclusive license to download, install, and use PerfMon for personal, educational, and commercial purposes on compatible Apple macOS devices.
* **Restrictions**:
  * The software is provided in compiled binary form. The source code is proprietary and closed-source.
  * You may not decompile, reverse engineer, disassemble, decrypt, modify, or create derivative works of this software.
  * You may not sell, rent, lease, or sub-license the binary packages.
  * You may not redistribute altered copies or misrepresent the origin of this software.
* **Disclaimer**: This software is provided "AS IS", without warranty of any kind, express or implied. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability.

---

## 💬 Support & Contact

- **Website**: [loweffort.digital](https://loweffort.digital)
- **Support & FAQs**: [loweffort.digital/support.html](https://loweffort.digital/support.html)
- **Bug Reports**: [GitHub Issues](https://github.com/LowEffortDigital/PerfMon/issues)
- **Email**: `support@loweffort.digital`
