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
