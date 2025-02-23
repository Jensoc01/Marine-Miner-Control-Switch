Marine-Miner-Control-Switch README (Manual Setup)
This repository contains scripts and instructions to manually control an E300 FPGA miner using a Raspberry Pi. The Pi manages start/stop commands and monitors the miner via HTTP requests.
Disclaimer: These scripts are specifically intended for E300 FPGA miners. For other models, scripts will need modification, particularly with clock settings and commands. Additionally, the coin being mined can be changed by modifying the script's pool and wallet parameters.

Donations
If you find this project helpful and would like to support its development, consider making a donation. Your support helps me to continue upgrading and maintaining these scripts:

ETH (USDT): 0xFCb752D1dB8f0bD6207685cfaA7902a26A329f3D

BTC: bc1qmplmjz482nl7nv8se9ku3eadl26zjdqmvf6wah

USDT (ERC20 / Binance): 0x4D0F0717Ba9623eeAD6a0429C1CB16F70CEa2F66

Thank you for your support!

Quick Setup Instructions
1.	Prepare Raspberry Pi: Update system and install dependencies.
2.	Create Scripts: Use provided examples for start_miner.sh and stop_miner.sh.
3.	Make Executable: Run chmod +x start_miner.sh stop_miner.sh.
4.	Test Scripts: Execute scripts manually first.
5.	Automate with Cron: Schedule automatic start/stop using cron jobs.
For detailed steps, refer to the full instructions below.

Requirements
•	Raspberry Pi 4 Model B (or similar)
•	E300 FPGA miner
•	Local network connection

# 🔌 How to Connect to a Raspberry Pi via SSH

## 📝 Prerequisites
Before connecting to your Raspberry Pi using SSH, make sure you have:
- A **Raspberry Pi** (any model)
- A **MicroSD card** with **Raspberry Pi OS** installed
- A **network connection** (Ethernet or Wi-Fi)
- A **PC, Mac, or another device** with an SSH client

---

## 1️⃣ Enable SSH on the Raspberry Pi

### Option 1: Enable SSH Before Booting (Headless Setup)
If you **don’t** have a monitor or keyboard connected to the Raspberry Pi, you can enable SSH **before booting** by following these steps:

1. Remove the **SD card** from the Raspberry Pi and insert it into your computer.
2. Open the **boot partition** of the SD card.
3. Create an empty file named **`ssh`** (with no extension) inside the boot partition:
   - **Windows:** Use Notepad, and save an empty file as `ssh` (select "All Files" in the Save dialog).
   - **Mac/Linux:** Open a terminal and run:
     ```bash
     touch /Volumes/boot/ssh
     ```
4. Eject the SD card, insert it into the **Raspberry Pi**, and power it on. SSH will now be enabled.

---

### Option 2: Enable SSH Using the Raspberry Pi Desktop
If you have a monitor and keyboard attached to the Pi:
1. Open **Raspberry Pi Configuration**:
   ```bash
   sudo raspi-config
   ```
2. Navigate to:
   ```
   Interfacing Options → SSH → Enable
   ```
3. Exit and **reboot** the Raspberry Pi:
   ```bash
   sudo reboot
   ```

---

## 2️⃣ Find the Raspberry Pi’s IP Address
To connect via SSH, you need the **IP address** of your Raspberry Pi.

### Method 1: Using Your Router (Recommended)
- Log into your **router’s admin panel** and find the **connected devices list**.
- Look for a device named **raspberrypi**.

### Method 2: Using Terminal (If Pi Has a Screen)
Run this command on the Raspberry Pi:
```bash
hostname -I
```
This will output an IP like `192.168.1.100` (your IP may be different).

---

## 3️⃣ Connect to Raspberry Pi via SSH

### On **Windows** (Using PuTTY)
1. Download and install **PuTTY** from [putty.org](https://www.putty.org/).
2. Open **PuTTY** and enter the Raspberry Pi’s IP address (e.g., `192.168.1.100`).
3. Set the port to `22` and click **Open**.
4. When prompted, log in with:
   ```
   Username: pi
   Password: raspberry
   ```
   *(If you changed the password, use your new one.)*

---

### On **Mac/Linux** (Using Terminal)
1. Open **Terminal** and type:
   ```bash
   ssh pi@192.168.1.100
   ```
   *(Replace `192.168.1.100` with your Raspberry Pi’s actual IP address.)*
2. When prompted, enter the password (`raspberry` by default).

---

## 4️⃣ (Optional) Change the Default Password
For security, change the default password immediately:
```bash
passwd
```
Enter a new password and confirm.

---

## 5️⃣ (Optional) Set a Static IP for Easier SSH Access
To avoid changing IPs after each reboot, set a **static IP**:
1. Open the DHCP settings in your **router** and assign a **static lease** to your Pi’s MAC address.
2. Alternatively, configure a static IP in the **Raspberry Pi’s network settings** (`/etc/dhcpcd.conf`).

---

## 🎉 Done! You Can Now SSH into Your Raspberry Pi!
From now on, just run:
```bash
ssh pi@192.168.1.100
```
or connect using PuTTY.

🚀 **Enjoy remote access to your Raspberry Pi!**
