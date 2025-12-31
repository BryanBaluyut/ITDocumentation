1. Immediate Setup  

# Update and Upgrade
# Enable Automatic Updates: Install unattended-upgrades to ensure security patches are applied without manual intervention

sudo apt install unattended-upgrades
sudo dpkg-reconfigure -plow unattended-upgrades

2. Secure User Access

adduser <your-username>
usermod -aG sudo <your-username>

3. Use SSH Keys: Never use passwords for SSH. Generate a key pair on your local machine and upload it to the server.
https://github.com/BryanBaluyut/ITDocumentation/blob/db2d9b1c141a7ee4ec7e867a7a0e776a6c16a2e3/SSH%20Key%20Pair%20Setup/Setup%20Windows%20SSH%20Private%20Key%20and%20Ubuntu%20Public%20Key.md

4. Disable Root Login & Password Auth: 

# Edit /etc/ssh/sshd_config to include:

sudo nano /etc/ssh/sshd_config

PermitRootLogin no
PasswordAuthentication no

# Before restarting the service, it is a best practice to test the configuration for syntax errors.

sudo sshd -t

# If there is no output, the configuration is valid. Now, restart the SSH service to apply the changes:

sudo systemctl restart ssh

# By default, your SSH client might try your SSH keys automatically. To truly test PasswordAuthentication no, you must tell your client to ignore your keys and only attempt a password.

ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no username@your_server_ip

4. Configure UFW (Uncomplicated Firewall) Ubuntu comes with UFW, which is simple to manage.

# Set Defaults: 
sudo ufw default deny incoming 
sudo ufw default allow outgoing.

# Allow SSH: 
sudo ufw allow ssh

# Allow Web Traffic: 
sudo ufw allow 80/tcp 
sudo ufw allow 443/tcp.

# Enable: 
sudo ufw enable.

5. Install Fail2Ban

sudo apt update
sudo apt install fail2ban -y

# After installation, the service should start automatically. You can check its status:

sudo systemctl status fail2ban

# Fail2Ban comes with a default configuration file at /etc/fail2ban/jail.conf. Never edit this file directly, as it will be overwritten during system updates. Instead, create a .local copy where you will apply your custom settings.

sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local

# After saving your changes

sudo systemctl restart fail2ban

# Fail2Ban Commands

# Check overall status	
sudo fail2ban-client status

# Check SSH jail (see banned IPs)	
sudo fail2ban-client status sshd

# Manually unban an IP	
sudo fail2ban-client set sshd unbanip <IP-ADDRESS>

# Manually ban an IP	
sudo fail2ban-client set sshd banip <IP-ADDRESS>