````md
# Qualcomm Atheros QCA9377 Wireless (ath10k)

A **modern, up-to-date setup guide** to get the **Qualcomm Atheros QCA9377 (ath10k)**
wireless adapter working reliably on **new Linux distributions**.

---

## ✅ Supported Systems

Tested and working on:

- Ubuntu 20.04 / 22.04 / 24.04
- Debian 11 / 12
- Linux Mint 21+
- Fedora 36+
- Arch Linux (latest)

**Recommended kernel:** `5.4+`

---

## 🔍 Verify Hardware

Confirm your wireless adapter:

```bash
lspci | grep -i network
````

Expected output:

```text
Qualcomm Atheros QCA9377 802.11ac Wireless Network Adapter
```

If your device is different, this guide will **not apply**.

---

## 🔄 System Update

Update your system and firmware packages:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install --reinstall linux-firmware
```

Reboot after completion.

---

## 📁 Firmware Installation

### ✔ Check Existing Firmware

```bash
ls /lib/firmware/ath10k/QCA9377/hw1.0/
```

Required files:

```text
board.bin
firmware-5.bin
```

---

### ❌ Firmware Missing or Wi-Fi Still Broken

Manually install the latest official firmware:

```bash
git clone https://github.com/kvalo/ath10k-firmware.git
sudo cp -r ath10k-firmware/QCA9377 /lib/firmware/ath10k/
```

Rename firmware file if needed:

```bash
cd /lib/firmware/ath10k/QCA9377/hw1.0/
sudo mv firmware-5.bin_* firmware-5.bin
```

---

## ⚙️ Driver Configuration

Some QCA9377 cards fail OTP calibration.
Enabling `skip_otp` greatly improves stability.

Create configuration file:

```bash
sudo nano /etc/modprobe.d/ath10k.conf
```

Add:

```text
options ath10k_core skip_otp=y
```

Save and exit.

---

## 🔁 Reload Driver

Reload ath10k without reboot:

```bash
sudo modprobe -r ath10k_pci
sudo modprobe ath10k_pci
```

Or reboot:

```bash
sudo reboot
```

---

## 📶 Verification

Confirm driver is loaded:

```bash
lsmod | grep ath10k
```

Check kernel logs:

```bash
dmesg | grep ath10k
```

Check network interfaces:

```bash
ip link
nmcli device
```

Wi-Fi networks should now appear in Network Manager.

---

## 🛠 Optional Fixes

### 🔹 Disable PCIe Power Saving (Highly Recommended)

```bash
sudo nano /etc/default/grub
```

Change:

```text
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

To:

```text
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pcie_aspm=off"
```

Apply and reboot:

```bash
sudo update-grub
sudo reboot
```

---

### 🔹 Disable Wi-Fi Power Save

Temporary:

```bash
sudo iw dev wlan0 set power_save off
```

Persistent:

```bash
sudo nano /etc/NetworkManager/conf.d/wifi-powersave.conf
```

```text
[connection]
wifi.powersave = 2
```

Restart NetworkManager:

```bash
sudo systemctl restart NetworkManager
```

---

## 🐞 Troubleshooting

When asking for help, include:

```bash
dmesg | grep ath10k
lspci -nn
uname -r
```

---

### ⭐ Maintained for modern Linux users

If this guide helped you, consider starring the repo.

```

---

