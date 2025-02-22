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
•	
Detailed Setup Instructions
Step 1: Raspberry Pi Preparation
•	Flash Raspberry Pi OS (Lite recommended) onto your SD card.
•	Boot up the Pi, connect via SSH, and ensure it's updated:
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

# Set your mining pool, wallet, and other parameters.
Pool="stratum%%2Btcp://YOUR IP"  # Note: %%2B represents the '+' sign
Wallet="UR ADDRESS"
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

echo "Completed!""

echo "Start command completed!"

stop_miner.sh
#!/bin/bash

# Set your mining pool, wallet, and other parameters.
Pool="stratum%%2Btcp://YOUR IP"  # Note: %%2B represents the '+' sign
Wallet="UR ADDRESS"
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

echo "Completed! “Troubleshooting
•	Verify network connection to E300.
•	Ensure IP addresses in ip_list.txt are correct.
Contributions
Feel free to submit pull requests or open issues for improvements!

