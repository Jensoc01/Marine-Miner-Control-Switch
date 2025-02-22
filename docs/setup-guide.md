# Raspberry Pi Setup Guide

This guide will walk you through the steps to set up your Raspberry Pi for controlling the Marine Miner.

## 1. Prepare Your Raspberry Pi

- **Power up and connect your Pi.**
- **Update your system:**
  ```bash
  sudo apt update && sudo apt upgrade -y


Set the correct time zone:

sudo timedatectl set-timezone YOUR_TIMEZONE


Enable SSH

Run sudo raspi-config, go to Interfacing Options > SSH, and enable SSH.

Optionally, set up passwordless SSH by generating an SSH key:

ssh-keygen -t rsa -b 4096 -C "your_email@example.com"


3. Clone Your Repository
Clone your repository to your Raspberry Pi:

git clone git@github.com:YourUsername/Marine-Miner-Control-Switch.git


4. Set Up Cron Jobs
Add your cron jobs for starting and stopping your miner:

crontab -e

Copy
# Stop mining at 6:00 PM daily
00 18 * * * /home/YOUR_USER/E300/stop_mining.sh >> /home/YOUR_USER/E300/cron_test.log 2>&1

# Start mining at 6:00 AM daily
00 06 * * * /home/YOUR_USER/E300/start_mining.sh >> /home/YOUR_USER/E300/cron_test.log 2>&1

5. Additional Configuration
Make sure your scripts use absolute paths.
Verify your SSH connection to GitHub.
Test your scripts manually before relying on cron.
Replace placeholder values with your actual information.


Save the file (in nano, press Ctrl+O, Enter, then Ctrl+X).

Step 3: Commit and Push Your Documentation
Stage the new documentation:

bash
Copy
git add docs/setup-guide.md
Commit your changes:

bash
Copy
git commit -m "Add Raspberry Pi setup guide"
Push to GitHub:

bash
Copy
git push origin main
Step 4: Link to the Guide in Your README
Edit your README.md to include a link to the setup guide. For example, add:

markdown
Copy
## Setup Guide

For detailed instructions on setting up your Raspberry Pi, see the [Setup Guide](docs/setup-guide.md).
Then commit and push those changes:

bash
Copy
git add README.md
git commit -m "Add link to setup guide in README"
git push origin main
That’s it! Now your repository will include a dedicated setup guide that anyone can follow. Let me know if you need any further assistance!






