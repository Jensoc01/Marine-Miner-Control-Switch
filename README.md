Marine-Miner-Control-Switch README (Manual Setup)

This repository contains scripts and instructions to manually control an E300 FPGA miner using a Raspberry Pi. The Pi manages start/stop commands and monitors the miner via HTTP requests.

Disclaimer: These scripts are specifically intended for E300 FPGA miners. For other models, scripts will need modification, particularly with clock settings and commands. Additionally, the coin being mined can be changed by modifying the script's pool and wallet parameters.

Donations


If you find this project helpful and would like to support its development, consider making a donation. Your support helps me to continue upgrading and maintaining these scripts:

ETH (USDT): 0xFCb752D1dB8f0bD6207685cfaA7902a26A329f3D

BTC: bc1qmplmjz482nl7nv8se9ku3eadl26zjdqmvf6wah

USDT (ERC20 / Binance): 0xFCb752D1dB8f0bD6207685cfaA7902a26A329f3D

Thank you for your support!


Quick Setup Instructions

Prepare Raspberry Pi: Update system and install dependencies.

Create Scripts: Use provided examples for start_miner.sh and stop_miner.sh.

Make Executable: Run chmod +x start_miner.sh stop_miner.sh.

Test Scripts: Execute scripts manually first.

Automate with Cron: Schedule automatic start/stop using cron jobs.

For detailed steps, refer to the full instructions below.


Requirements

Raspberry Pi 4 Model B (or similar)

E300 FPGA miner

Local network connection


Detailed Setup Instructions


Step 1: Raspberry Pi Preparation

Flash Raspberry Pi OS (Lite recommended) onto your SD card.

Boot up the Pi, connect via SSH, and ensure it's updated:

sudo apt update
sudo apt upgrade -y


Step 2: Install Dependencies

Install necessary dependencies (e.g., curl for HTTP requests):

sudo apt install curl -y


Step 3: Create Start and Stop Scripts

Create start_miner.sh

Open a new file using nano editor:

nano start_miner.sh

Copy and paste the script provided below in "Scripts Overview" into this file. Save and exit nano by pressing Ctrl + O to save, then Enter, and Ctrl + X to exit.

Create stop_miner.sh

Open a new file using nano editor:

nano stop_miner.sh

Copy and paste the script provided below in "Scripts Overview" into this file. Save and exit nano by pressing Ctrl + O to save, then Enter, and Ctrl + X to exit.


Step 4: Script Execution

Make both start_miner.sh and stop_miner.sh scripts executable:

chmod +x start_miner.sh stop_miner.sh


Step 5: Test Scripts

Before setting up automation, test the scripts manually:

./start_miner.sh
./stop_miner.sh


Step 6: Automate with Cron

To automate miner start/stop, add cron jobs:

crontab -e

Example cron entries to stop at midnight and start at 6 AM daily:

0 0 * * * /home/pi/Marine-Miner-Control-Switch/stop_miner.sh
0 6 * * * /home/pi/Marine-Miner-Control-Switch/start_miner.sh


Scripts Overview

The following scripts must be copied and pasted into the respective script files created above (start_miner.sh and stop_miner.sh). Use nano to open the file, paste the contents, and save by pressing Ctrl + O, then Enter, and exit with Ctrl + X.

start_miner.sh

#!/bin/bash

Pool="stratum%%2Btcp://YOUR_IP_ADDRESS:PORT"  # Note: %%2B represents the '+' sign
Wallet="YOUR_WALLET_ADDRESS"
WorkerIDPrefix="E300"
Clock="500"
Clock1="500"
Clock2="500"

while IFS= read -r ip; do
    api_endpoint="/cgi-bin/qcmap_web_cgi.cgi"
    api_data_template="Page=setDracaenaMiner&status=1&info_a=os&info_o=${Pool}&info_u=${Wallet}&info_miner=${WorkerIDPrefix}%s&info_fpga_clk_core=${Clock}&info_fpga_clk_core1=${Clock1}&info_fpga_clk_core2=${Clock2}&info_fpga_clk_mem=1100&info_fpga_clk_mem1=1100&info_fpga_clk_mem2=1100"
    ip_part=$(echo "$ip" | awk -F'.' '{print $NF}')
    api_data=$(printf "$api_data_template" "$ip_part")
    api_url="http://${ip}${api_endpoint}"
    curl_command="curl -X POST -d '${api_data}' -H 'Content-Type: application/x-www-form-urlencoded' ${api_url}"
    echo "Performing API call to: ${api_url}"
    echo "With data: ${api_data}"
    eval "${curl_command}"
    sleep 10
    echo "---------------------"
done < "/path/to/ip_list.txt"

echo "Start command completed!"


stop_miner.sh

#!/bin/bash

Pool="stratum%%2Btcp://YOUR_IP_ADDRESS:PORT"  # Note: %%2B represents the '+' sign
Wallet="YOUR_WALLET_ADDRESS"
WorkerIDPrefix="E300"
Clock="500"
Clock1="500"
Clock2="500"

while IFS= read -r ip; do
    api_endpoint="/cgi-bin/qcmap_web_cgi.cgi"
    api_data_template="Page=setDracaenaMiner&status=0&info_a=os&info_o=${Pool}&info_u=${Wallet}&info_miner=${WorkerIDPrefix}%s&info_fpga_clk_core=${Clock}&info_fpga_clk_core1=${Clock1}&info_fpga_clk_core2=${Clock2}&info_fpga_clk_mem=1100&info_fpga_clk_mem1=1100&info_fpga_clk_mem2=1100"
    ip_part=$(echo "$ip" | awk -F'.' '{print $NF}')
    api_data=$(printf "$api_data_template" "$ip_part")
    api_url="http://${ip}${api_endpoint}"
    curl_command="curl -X POST -d '${api_data}' -H 'Content-Type: application/x-www-form-urlencoded' ${api_url}"
    echo "Performing API call to: ${api_url}"
    echo "With data: ${api_data}"
    eval "${curl_command}"
    sleep 10
    echo "---------------------"
done < "/path/to/ip_list.txt"

echo "Stop command completed!"


Troubleshooting

Verify network connection to E300.

Ensure IP addresses in ip_list.txt are correct.

Contributions

Feel free to submit pull requests or open issues for improvements!
