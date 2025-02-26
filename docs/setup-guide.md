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

## 📜 Detailed Setup Instructions

### Step 1: Raspberry Pi Preparation
1. Flash Raspberry Pi OS (Lite recommended) onto your SD card.
2. Boot up the Pi, connect via SSH, and ensure it's updated:

   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

### Step 2: Install Dependencies
Install necessary dependencies (e.g., `curl` for HTTP requests):

```bash
sudo apt install curl -y
```

### Step 3: Create Start and Stop Scripts

#### Create `start_miner.sh`
Open a new file using nano editor:

```bash
nano start_miner.sh
```

Copy and paste the script provided below in **"Scripts Overview"** into this file. Save and exit nano by pressing `Ctrl + O`, then `Enter`, and `Ctrl + X` to exit.

#### Create `stop_miner.sh`
Open a new file using nano editor:

```bash
nano stop_miner.sh
```

Copy and paste the script provided below in **"Scripts Overview"** into this file. Save and exit nano by pressing `Ctrl + O`, then `Enter`, and `Ctrl + X` to exit.

---

## 📝 Scripts Overview
The following scripts must be copied and pasted into the respective script files created above (`start_miner.sh` and `stop_miner.sh`). 

### `start_miner.sh`
```bash
#!/bin/bash

# Set your mining pool, wallet, and other parameters.
Pool="stratum%%2Btcp://xxx:xxx"  # Note: %%2B represents the '+' sign
Wallet="YOUR WALLET ADDRESS"
WorkerIDPrefix="E300"  # WorkerID will be formed by appending the last part of the IP address.
Clock="500"   # FPGA clock settings (for fpga0)
Clock1="500"  # FPGA clock settings (for fpga1)
Clock2="500"  # FPGA clock settings (for fpga2)

# The API call is constructed to instruct the FPGA to start mining.
# "status=1" is assumed to be the command for starting mining.
while IFS= read -r ip; do
    # Define the API endpoint on the FPGA.
    api_endpoint="/cgi-bin/qcmap_web_cgi.cgi"

    # Build the POST data; note that %s will be replaced with the last part of the IP address.
    api_data_template="Page=setDracaenaMiner&status=1&info_a=os&info_o=${Pool}&info_u=${Wallet}&info_miner=${WorkerIDPrefix}%s&info_fpga_clk_core=${Clock}&info_fpga_clk_core1=${Clock1}&info_fpga_clk_core2=${Clock2}&info_fpga_clk_mem=1100&info_fpga_clk_mem1=1100&info_fp>

    # Extract the last segment (octet) of the IP address.
    ip_part=$(echo "$ip" | awk -F'.' '{print $NF}')

    # Insert the IP segment into the API data.
    api_data=$(printf "$api_data_template" "$ip_part")

    # Construct the full URL for the API call.
    api_url="http://${ip}${api_endpoint}"

    # Build the curl command to send the POST request.
    curl_command="curl -X POST -d '${api_data}' -H 'Content-Type: application/x-www-form-urlencoded' ${api_url}"

    echo "Performing API call to: ${api_url}"
    echo "With data: ${api_data}"

    # Execute the API call.
    eval "${curl_command}"

    # Pause 10 seconds before processing the next IP.
    sleep 10
    echo "---------------------"
done < "/path/to/ip_list.txt"

echo "Start command completed!"
```

---

### `stop_miner.sh`
```bash
#!/bin/bash

# Set your mining pool, wallet, and other parameters.
Pool="stratum%%2Btcp://xxxx:xxxx"  # Note: %%2B represents the '+' sign
Wallet="Your Wallet Address"
WorkerIDPrefix="E300"  # WorkerID will be formed by appending the last part of the IP address.
Clock="500"   # FPGA clock settings (for fpga0)
Clock1="500"  # FPGA clock settings (for fpga1)
Clock2="500"  # FPGA clock settings (for fpga2)

# The API call is constructed to instruct the FPGA to stop mining.
# "status=0" is assumed to be the command for stopping mining.
while IFS= read -r ip; do
    # Define the API endpoint on the FPGA
    api_endpoint="/cgi-bin/qcmap_web_cgi.cgi"

    # Build the POST data; note that %s will be replaced with the last part of the IP address.
    api_data_template="Page=setDracaenaMiner&status=0&info_a=os&info_o=${Pool}&info_u=${Wallet}&info_miner=${WorkerIDPrefix}%s&info_fpga_clk_core=${Clock}&info_fpga_clk_core1=${Clock1}&info_fpga_clk_core2=${Clock2}&info_fpga_clk_mem=1100&info_fpga_clk_mem1=1100&info_fp>

    # Extract the last segment (octet) of the IP address.
    ip_part=$(echo "$ip" | awk -F'.' '{print $NF}')

    # Insert the IP segment into the API data.
    api_data=$(printf "$api_data_template" "$ip_part")

    # Construct the full URL for the API call.
    api_url="http://${ip}${api_endpoint}"

    # Build the curl command to send the POST request.
    curl_command="curl -X POST -d '${api_data}' -H 'Content-Type: application/x-www-form-urlencoded' ${api_url}"

    echo "Performing API call to: ${api_url}"
    echo "With data: ${api_data}"

    # Execute the API call.
    eval "${curl_command}"

    # Pause 10 seconds before processing the next IP.
    sleep 10
    echo "---------------------"
done < "/path/to/ip_list.txt"

echo "Stop command completed!"
```

---

for the Script to push to your E300 address, you need to create a IP list within the same folder as your Stop / Start Script. 

To create and edit ip_list.txt, use:

nano (Yourfolder_where_script_is)/ip_list.txt
Then, add the IPs of all your E300 devices (one IP per line):

192.xxx

192.xxx

192.XXX

Ensure Your Script Uses This File: Your script should be reading from this file to loop through each IP. If you're using something like while IFS= read -r ip, it will pull each IP address and use it in the API request.

## 🛠️ Troubleshooting
- Verify network connection to **E300**.
- Ensure **IP addresses** in `ip_list.txt` are correct.

---

## 🤝 Contributions
Feel free to submit **pull requests** or open **issues** for improvements!


