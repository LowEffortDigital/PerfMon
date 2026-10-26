# PerfMon for macOS — Distribution & Releases

Official binary releases and installer packages for **PerfMon (Task Manager for macOS)**, created by [Low Effort Development Studios](https://loweffort.digital).

> 📖 **Feature Guide**: Looking for a detailed walkthrough with screenshots? Check out the [**Complete Feature Guide**](FEATURE_GUIDE.md).

---

## ⬇️ Downloads

| Release | Version | macOS Requirement | Architecture | Package |
|:---|:---|:---|:---|:---|
| **Latest** | **v1.1** | macOS 14.0+ (Sonoma, Sequoia) | Universal (Apple Silicon & Intel) | [**Download PerfMon.dmg**](https://github.com/LowEffortDigital/PerfMon/releases/latest/download/PerfMon.dmg) |
| **Previous** | **v1.0.0** | macOS 14.0+ | Universal | [Download PerfMon v1.0.0](https://github.com/LowEffortDigital/PerfMon/releases/tag/v1.0.0) |

---

## 🚀 Installation Guide

1. Download [**`PerfMon.dmg`**](https://github.com/LowEffortDigital/PerfMon/releases/latest/download/PerfMon.dmg).
2. Double-click the `.dmg` file to mount it.
3. Drag **PerfMon** to your **Applications** folder.
4. Launch **PerfMon** from Launchpad, Spotlight, or Applications.

> **Note on Gatekeeper**: If macOS prompts that the application cannot be checked on first launch:
> - Right-click **PerfMon.app** in Applications ➔ Click **Open** ➔ Click **Open Anyway** (or go to *System Settings ➔ Privacy & Security ➔ Open Anyway*).
> - Alternatively, run this command in Terminal:
>   ```bash
>   xattr -cr "/Applications/PerfMon.app"
>   ```

---

## 🌟 Key Features

- **Modular Desktop HUD Widgets**: High-frequency (0.5s–1s) desktop-layer and floating widgets.
- **Process-Locked Widgets**: Pin widgets to single processes or entire process trees surviving app restarts and PID changes.
- **Menu Bar Item Designer**: Drag-and-drop token strip for menu bar stats with custom label/value colors and PM monogram.
- **Disk Access Topology Map**: Interactive 3-column topology flow with single-process file isolation and process hierarchy trees.
- **Hardware Bus Architecture**: Real-time device tree detecting Thunderbolt, USB4, PCIe, Apple Fabric, and SATA link speeds.
- **Network Tracing & Topology**: Active socket inspection with Geo-IP, reverse DNS, and process connection maps.
- **Low Effort Design Suite**: Multiple sleek themes (Dark Mode, Vivid Purple, Hot Pink, Amber Gold, Cyber Cyan, Emerald Green, Monochrome, Light Mode).
- **Anonymous Runtime Telemetry**: On-device tracking of launches, session duration, and update downloads with full user opt-out and raw JSON export.

---

## 📄 License & Terms of Use

PerfMon is distributed under the MIT License with anonymous runtime telemetry disclosure. See [LICENSE](LICENSE) and [PRIVACY.md](PRIVACY.md).

---

## 💬 Support & Contact

- **Website**: [loweffort.digital](https://loweffort.digital)
- **Support & FAQs**: [loweffort.digital/support.html](https://loweffort.digital/support.html)
- **Bug Reports**: [GitHub Issues](https://github.com/LowEffortDigital/PerfMon/issues)
- **Email**: `support@loweffort.digital`
