<div align="center">

# 🍎 macOS on Windows VirtualBox
### *Experimental macOS Virtualization & Testing Documentation*

<p align="center">
  <img src="https://img.shields.io/badge/Host-Windows_10_%7C_11-0078D4?style=for-the-badge&logo=windows11&logoColor=white" alt="Windows" />
  <img src="https://img.shields.io/badge/Hypervisor-VirtualBox_7.x-183A61?style=for-the-badge&logo=virtualbox&logoColor=white" alt="VirtualBox" />
  <img src="https://img.shields.io/badge/Target_OS-macOS_Big_Sur-5856D6?style=for-the-badge&logo=apple&logoColor=white" alt="macOS" />
  <img src="https://img.shields.io/badge/Status-Experimental-FF9500?style=for-the-badge" alt="Status" />
</p>

<p align="center">
  A polished, step-by-step technical guide for developers and enthusiasts experimenting with macOS virtualization on x86-64 hardware.
</p>

---

</div>

<a id="disclaimer"></a>
> [!CAUTION]
> ### ⚠️ Disclaimer & Legal Notice
>
> This repository is an independent, community-created educational and technical documentation project. It is **not affiliated with, sponsored by, or endorsed by Apple Inc.**
>
> **macOS is proprietary software owned by Apple Inc.** The applicable Apple software license may restrict how macOS may be installed or used, including restrictions relating to the hardware on which it may be used.
>
> **This repository:**
> - ❌ **Does not** host or distribute macOS installation images, ISOs, DMGs, or any Apple proprietary software.
> - ❌ **Does not** provide Apple software license keys or activation credentials.
> - ❌ **Does not** claim that installing macOS on non-Apple hardware is permitted by Apple's license.
> - ❌ **Does not** provide instructions intended to bypass Apple's licensing or activation systems.
> - ❌ **Does not** grant users any rights to Apple's software.
>
> Users are responsible for obtaining installation media through a source and method they are legally authorized to use and for complying with Apple's applicable license terms and local laws.
>
> 📖 *For official licensing terms, review:* [Apple Software License Agreements (SLA)](https://www.apple.com/legal/sla/)

---

<a id="navigation-matrix"></a>
## 🧭 Navigation Matrix

<div align="center">

| 📋 [Prerequisites](#prerequisites) | ⚙️ [Step 1: VirtualBox](#step-1) | 💿 [Step 2: Media Prep](#step-2) | 🛡️ [Step 3: Security / Hyper-V](#step-3) |
| :---: | :---: | :---: | :---: |
| <sub>Hardware & RAM Requirements</sub> | <sub>Install Hypervisor & ExtPack</sub> | <sub>Legal Media Guidelines</sub> | <sub>HVCI & Hypervisor Tweaks</sub> |

| 🖥️ [Step 4: Provision VM](#step-4) | 🎛️ [Step 5: Hardware Settings](#step-5) | 💻 [Step 6: EFI / SMC Patch](#step-6) | 🚀 [Step 7: macOS Setup](#step-7) |
| :---: | :---: | :---: | :---: |
| <sub>Base VM Creation</sub> | <sub>EFI, Display & Network Config</sub> | <sub>VBoxManage Command Scripts</sub> | <sub>Disk Utility & OOBE</sub> |

<br>

<p align="center">
  <a href="#bonus"><b>✨ Bonus: Display Resolution Tuning</b></a> &nbsp;•&nbsp;
  <a href="#troubleshooting"><b>🛠️ Troubleshooting Matrix</b></a> &nbsp;•&nbsp;
  <a href="#disclaimer"><b>⚠️ Legal Disclaimer</b></a>
</p>

</div>

---

<a id="prerequisites"></a>
## 📊 System Prerequisites

Ensure your host rig meets the required resource allocations before proceeding:

| Resource Component | Minimum Specification | Recommended Specification |
| :--- | :--- | :--- |
| **System Memory (RAM)** | `8 GB Total` *(4 GB dedicated to VM)* | `16 GB+ Total` *(6–8 GB for VM)* |
| **Processor (CPU)** | `Modern x86-64` *(Intel Core / AMD Ryzen)* | `4+ Cores / 8+ Threads` |
| **Storage Capacity** | `120 GB Available Space` (SSD Recommended) | `200 GB+ NVMe SSD` |
| **Host Operating System** | `Windows 10 / 11 (64-bit)` | `Windows 11 Pro (64-bit)` |
| **Hypervisor Engine** | `Oracle VM VirtualBox 7.0+` | `Latest Stable VirtualBox Release` |

> [!NOTE]
> Hardware virtualization (`Intel VT-x` or `AMD-V / SVM`) must be enabled in your host system's BIOS/UEFI settings.

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-1"></a>
## 📦 Step 1 — VirtualBox & Extension Pack

1. Navigate to the official [Oracle VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads) portal.
2. Download the **Windows hosts** binary installer.
3. On the same page, download the **VirtualBox Oracle VM Extension Pack**.
4. Run the VirtualBox installer and complete standard setup.
5. Double-click the `.vbox-extpack` file to register extension modules with the hypervisor.

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-2"></a>
## 💿 Step 2 — Obtain Installation Media

This repository **does not host, distribute, or provide download links to macOS installation images**.

macOS is proprietary software owned by Apple. Before obtaining or using macOS installation media, review the applicable Apple software license and ensure you have the legal right to obtain and use the software in your circumstances.

> [!WARNING]
> **Do not use unofficial copies, unauthorized torrents, or redistributed installation images unless you have verified that you are legally permitted to obtain and use them.**

You will need a **legally obtained macOS Big Sur (11.7) `.iso` installation image** compatible with virtualization workflows.

*Why Big Sur?* macOS Big Sur (11.7) offers optimal compatibility with standard VirtualBox EFI emulation compared to later releases.

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-3"></a>
## 🛡️ Step 3 — System Virtualization Tweaks

> [!WARNING]
> Disabling Windows Memory Integrity (Hypervisor-Protected Code Integrity / HVCI) is a **security trade-off**. It reduces some host platform protections in order to prevent hypervisor conflicts. Only modify these settings if you accept this trade-off, and consider re-enabling them when your testing concludes.

### 3.1 Disable Memory Integrity (Core Isolation)
1. Open Windows Search (`Win + S`), type **Core Isolation**, and press `Enter`.
2. Toggle **Memory Integrity** to **Off**.
3. Reboot your system when prompted.

### 3.2 Disable Windows Hyper-V
Launch **Command Prompt as Administrator** (`Win + S` → `cmd` → right-click → *Run as Administrator*) and execute:

```cmd
bcdedit /set hypervisorlaunchtype off
```

*Restart your PC once more to enforce configuration changes.*

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-4"></a>
## 🖥️ Step 4 — Create the Virtual Machine

Launch VirtualBox and click **New** (`Ctrl + N`):

<div align="center">

```
┌────────────────────────────────────────────────────────┐
│  VM Name:          macOS-BigSur                        │
│  Type:             Mac OS X                            │
│  Version:          Mac OS X (64-bit) / macOS 11        │
│  Base Memory:      4096 MB – 6144 MB                   │
│  Processors:       2 to 4 vCPUs                        │
│  Hard Disk Size:   100 GB – 120 GB (VDI / Dynamic)     │
└────────────────────────────────────────────────────────┘
```

</div>

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-5"></a>
## 🎛️ Step 5 — Configure VM Parameters

Open your VM's **Settings** (`Ctrl + S`) and apply the following matrix:

<table>
  <thead>
    <tr>
      <th width="20%">Category</th>
      <th width="40%">Configuration Parameter</th>
      <th width="40%">Required Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>⚙️ System</b></td>
      <td>Motherboard / Boot Order<br>Chipset Architecture<br>Extended Features</td>
      <td>Uncheck <code>Floppy</code><br>Set to <code>ICH9</code><br>Enable <code>[x] Enable EFI (special OSes only)</code></td>
    </tr>
    <tr>
      <td><b>🖥️ Display</b></td>
      <td>Video Memory</td>
      <td>Set slider to maximum (<code>128 MB</code>)</td>
    </tr>
    <tr>
      <td><b>🌐 Network</b></td>
      <td>Adapter 1 Attachment</td>
      <td>Set to <code>Bridged Adapter</code> (Select active Wi-Fi/NIC)</td>
    </tr>
    <tr>
      <td><b>🔌 USB</b></td>
      <td>Controller Type</td>
      <td><code>USB 3.0 (xHCI) Controller</code></td>
    </tr>
  </tbody>
</table>

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-6"></a>
## 💻 Step 6 — Apply EFI & SMC Patching

> [!IMPORTANT]
> **Completely close VirtualBox before running these commands.** The background service (`VBoxSVC.exe`) must not be holding VM configuration locks.

> [!NOTE]
> The following are **experimental VirtualBox configuration commands** used to customize the VM hardware environment. They are not Apple-approved methods.

Open **Command Prompt as Administrator** and apply the commands corresponding to your host CPU architecture:

<details open>
<summary><b>🔵 Intel Processor Configuration Script</b></summary>

```cmd
cd "C:\Program Files\Oracle\VirtualBox"

VBoxManage.exe modifyvm "macOS-BigSur" --cpuidset 00000001 000106e5 00100800 0098e3fd bfebfbff
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/efi/0/Config/DmiSystemProduct" "iMac19,1"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/efi/0/Config/DmiSystemVersion" "1.0"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Mac-AA95B1DDAB278B95"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1
```

</details>

<details>
<summary><b>🔴 AMD Ryzen Processor Configuration Script</b></summary>

```cmd
cd "C:\Program Files\Oracle\VirtualBox"

VBoxManage.exe modifyvm "macOS-BigSur" --cpuidset 00000001 000106e5 00100800 0098e3fd bfebfbff
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/efi/0/Config/DmiSystemProduct" "iMac11,3"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/efi/0/Config/DmiSystemVersion" "1.0"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Mac-F2238AC8"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
VBoxManage.exe setextradata "macOS-BigSur" "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1
```

</details>

> 💡 *Replace `"macOS-BigSur"` with your exact VM identifier if you named it differently.*

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="step-7"></a>
## 🚀 Step 7 — macOS Installation Flow

1. **Boot:** Launch VirtualBox and click **Start**. Allow the installer environment to load.
2. **Disk Initialization:**
   - Select your installation language.
   - Choose **Disk Utility** → **Continue**.
   - Select `VBOX HARDDISK Media` in the left hierarchy.
   - Click **Erase**:
     - *Name:* `Macintosh HD`
     - *Format:* `APFS`
     - *Scheme:* `GUID Partition Map`
   - Complete partition creation and exit Disk Utility (`Cmd + Q`).
3. **OS Deployment:**
   - Select **Install macOS Big Sur** → **Continue**.
   - Accept the license terms and select `Macintosh HD` as the target volume.
   - The installation cycle will run for **30–60 minutes** with several automatic reboots.
4. **Out-of-Box Experience (OOBE):**
   - Follow standard setup prompts to configure region, accessibility, local user accounts, and theme preferences.

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="bonus"></a>
## ✨ Bonus — Display Resolution Tuning

VirtualBox defaults virtual displays to **1024×768**. You can inject custom EFI resolutions while the VM is **powered down**.

```cmd
cd "C:\Program Files\Oracle\VirtualBox"

:: Inject standard 1080p Full HD resolution
VBoxManage.exe setextradata "macOS-BigSur" VBoxInternal2/EfiGraphicsResolution 1920x1080

:: (Optional) Expand Video RAM allocation to 256MB
VBoxManage.exe modifyvm "macOS-BigSur" --vram 256
```

| Display Standard | Resolution Setting Value |
| :--- | :--- |
| **HD (720p)** | `1280x720` |
| **FHD (1080p)** | `1920x1080` |
| **QHD (1440p)** | `2560x1440` |
| **MacBook Aspect** | `1440x900` |

> [!TIP]
> Toggle seamless full-screen mode anytime via `Host Key + F` (Default Host Key is Right `Ctrl`).

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="troubleshooting"></a>
## 🛠️ Troubleshooting & Diagnostic Matrix

<details open>
<summary><b>🔍 Expand Common Scenarios & Workarounds</b></summary>
<br>

| Diagnostic Scenario | Root Cause | Remediation Step |
| :--- | :--- | :--- |
| **Stall at Apple Logo / Bootloop** | Hyper-V or HVCI still active | Ensure `hypervisorlaunchtype off` is set and reboot. Verify Memory Integrity is off. |
| **Black Display After EFI Init** | Typo in `VBoxManage` parameter | Re-verify commands in Step 6; ensure VM name casing matches exactly. |
| **"No bootable medium found"** | Unmounted ISO image | Re-insert `.iso` in `VM Settings` → `Storage` → `Optical Drive`. |
| **Stuttering / High Latency** | Resource starvation / Missing VT-x | Enable CPU Virtualization in BIOS; allocate 4+ vCPUs and 6GB+ RAM. |
| **Disk Absent in Installer** | Filter set to volumes only | In Disk Utility, choose **View** → **Show All Devices** from toolbar. |
| **No Network Connectivity** | Host NIC misalignment | In VM Settings → Network, verify adapter is bound to active physical connection. |

</details>

<div align="right"><sub><a href="#navigation-matrix">↑ Back to Navigation Matrix</a></sub></div>

---

<a id="community"></a>
<div align="center">

### 🌟 Community & Collaboration

If this experimental documentation assisted your research or testing workflow, consider giving it a **Star ⭐**!

[Report an Issue](../../issues) • [Submit Documentation Pull Request](../../pulls)

<sub>Crafted for researchers and curious techies exploring OS virtualization boundaries.</sub>

</div>
