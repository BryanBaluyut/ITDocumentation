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

# View your public key in your Windows PC ~/.ssh/id_your_key.pub
# Copy the text that starts with ssh-ed25519...
# Log into your Ubuntu Server

# Create the directory and file:

Bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys

# Paste your key into the file, save (Ctrl+O, Enter), and exit (Ctrl+X).

# Set permissions: 
Bash
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys

3. Test the Connection
# Try logging in from your local machine. You should no longer be asked for your VPS account password (though you will be asked for your key passphrase if you set one).

