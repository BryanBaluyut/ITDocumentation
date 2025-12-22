1.  Generate Private Key on your Windows Machine

# On your Windows Machine open Powershell
ssh-keygen -t ed25519 -C "your_email@example.com"

# File location: Press Enter to save it in the default location (~/.ssh/id_ed25519).
# Passphrase: (Optional but recommended) Type a passphrase to add an extra layer of security to your local key file.

# Rename the private key
Rename-Item -Path "$HOME\.ssh\id_ed25519" -NewName "id_yourUbuntuServer"

# Rename the public key
Rename-Item -Path "$HOME\.ssh\id_ed25519.pub" -NewName "id_yourUbuntuServer.pub"

# Edit config
edit "$HOME\.ssh\config"

Host UbuntuHostname
    HostName UbuntuHostname
    User username
    IdentityFile ~/.ssh/id_ubuntuprivatekey
    IdentitiesOnly yes

2. Transfer your public to your Ubuntu Server

type $HOME\.ssh\id_yourUbuntuServer.pub | ssh username@UbuntuHostname "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# If your prefer the manual way or if this command isn't working
# View your public key in your Windows PC $HOME\.ssh\id_yourUbuntuServer.pub

Get-Content "$HOME\.ssh\id_yourUbuntuServer.pub"

# Copy the text that starts with ssh-ed25519...
# Log into your Ubuntu Server
# Create the directory and file:

mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys

# Paste your key into the file, save (Ctrl+O, Enter), and exit (Ctrl+X).

# Set permissions: 
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys

3. Test the Connection
# Try logging in from your local machine. You should no longer be asked for your VPS account password (though you will be asked for your key passphrase if you set one).

# Open a command prompt and type. It should use the username you setup in "$HOME\.ssh\config"

SSH UbunterServerHostname

# Your Ubuntu server keeps a detailed record of every login. You can check the most recent entries in the authentication log to see the method used.

sudo journalctl -u ssh -n 20 --no-pager

# If you used a key: You will see a line that says: Accepted publickey your username from IP