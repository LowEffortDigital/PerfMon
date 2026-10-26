# Privacy Policy for Task Manager for macOS (PerfMon)

Last updated: October 26, 2026

**Task Manager for macOS ("PerfMon")** is designed and engineered by Low Effort Design Studios with privacy, transparency, and user sovereignty as foundational principles.

### 1. Information Collection & Telemetry Overview

Task Manager is built around local hardware inspection and transparent, privacy-first software distribution.

#### A. Local On-Device Performance Telemetry
- All hardware performance telemetry (CPU, GPU, memory, disk throughput, network bandwidth, process listings, active sockets, and file access maps) is acquired exclusively from local macOS Darwin kernel and Mach subsystem APIs (`host_statistics`, `IOBlockStorageDriver`, `IOAccelerator`, `proc_pidinfo`, `proc_pidfdinfo`).
- **No Payload or Content Inspection**: Local telemetry measures aggregate usage metrics only. PerfMon never reads file contents, browser histories, document contents, keystrokes, or network packet data.
- **Local In-Memory Execution**: Real-time hardware performance metrics remain strictly in memory and are never transmitted to any external server.

#### B. Anonymous Download & Runtime Usage Telemetry
To improve application stability, understand macOS hardware architecture distribution (Apple Silicon vs Intel), verify release delivery, and track software distribution downloads, PerfMon collects non-identifiable runtime telemetry:
- **Application Launches & Runtime**: Cumulative launch counts, session duration, and aggregate application uptime.
- **Distribution & Downloads**: Software update checks, release download completion status, downloaded file sizes, and download duration.
- **Platform Architecture**: General macOS version (e.g., macOS 15.1), CPU architecture (Apple Silicon `arm64` or Intel `x86_64`), core counts, and system memory tiers.
- **No Personal Identifiers (Zero PII)**: We do NOT collect names, email addresses, user account details, persistent hardware serial numbers, MAC addresses, IP addresses, or location data.
- **No Third-Party Analytics SDKs**: PerfMon does not include any commercial tracking frameworks, advertising identifiers, or third-party analytics libraries. All telemetry logic is open, native Swift.

### 2. User Controls & Opt-Out

You maintain complete control over runtime and download telemetry:
- **Inspection**: You can inspect every telemetry event in real time and copy the complete raw telemetry payload in JSON format from the application settings (`Settings -> General -> Runtime Usage & Download Telemetry -> Copy Telemetry JSON`).
- **Reset**: You can permanently purge all stored telemetry history and counters on your machine at any time using the "Reset Telemetry" button.
- **Opt-Out**: You can disable runtime usage and download telemetry at any time by toggling off "Track Anonymous Runtime & Download Telemetry" in `Settings -> General`. When disabled, no telemetry events or beacon heartbeats are logged or transmitted.

### 3. Permissions

Task Manager requests only the minimal local macOS system privileges necessary to inspect running processes and system load:
- Standard user-space process inspection via Darwin kernel APIs.
- Optional elevated system privileges only if the user explicitly requests to terminate protected background processes via standard macOS authentication prompts.

### 4. License & Open Source Transparency

The terms of telemetry collection are codified in Section 1 of the PerfMon [LICENSE](LICENSE).

### 5. Contact Us

If you have any questions or feedback regarding this Privacy Policy or telemetry handling, open an issue or pull request at:  
`https://github.com/Facelesswhite/PerfMon/issues`
