# ---

**NetBird Backup & Restore Playbook**

System Type: Docker / Self-Hosted  
Service Name: Management Service (bbaluyut-management-1)  
Data Path: /var/lib/netbird/ inside the container

## ---

**🟢 Part 1: How to Backup**

Run these steps to save your current configuration and database.

### **1\. Create a Backup Directory**

Create a folder to store your snapshot.

mkdir \-p backup

### **2\. Backup Configuration Files**

Copy the essential text configuration files from your current directory.  
(Note: We skip openid-configuration.json as your setup does not use it.)

cp docker-compose.yml management.json turnserver.conf backup/

### **3\. Backup the Database**

You must briefly stop the management service to ensure the database isn't being written to while copying.

1. **Stop the service:**  
   sudo docker compose stop management

2. Copy the data:  
   (This pulls the database files from inside the Docker container to your local folder.)  
   sudo docker compose cp management:/var/lib/netbird/ backup/

3. **Restart the service:**  
   sudo docker compose start management

### **4\. Archive (Optional but Recommended)**

Compress the backup folder into a single file for easy download or storage.

tar \-czvf netbird-backup.tar.gz backup/

## ---

**🔴 Part 2: How to Restore**

Follow these steps if you move to a new server or need to recover from a crash.

### **1\. Restore Configuration**

Place your backed-up configuration files into the folder where you plan to run NetBird.

cp backup/docker-compose.yml .  
cp backup/management.json .  
cp backup/turnserver.conf .

### **2\. Initialize Containers**

Docker needs to create the containers first before we can put data into them.

sudo docker compose up \-d

### **3\. Stop Management Service**

**Immediately** stop the management service to prevent it from creating a new, empty database.

sudo docker compose stop management

### **4\. Restore Database**

Copy your saved data back into the container.  
(Note: The . after netbird/ ensures we copy the contents, not the folder itself.)

sudo docker compose cp backup/netbird/. management:/var/lib/netbird/

### **5\. Start Service**

Bring the management dashboard back online.

sudo docker compose start management

### **6\. Verify Restoration**

Check the logs to ensure the database loaded correctly (look for "loaded store" or similar success messages).

sudo docker compose logs \--tail=50 management

### ---

**⚠️ Important Note on Container Names**

Your current installation uses a unique container prefix (bbaluyut...).  
If you ever reinstall on a fresh machine using the standard NetBird installer, the container name might revert to just netbird-management.

* The docker compose commands above (e.g., docker compose stop management) are smart—they will work regardless of the prefix.  
* However, if you ever use docker inspect or manual docker commands, check the name first using docker ps.