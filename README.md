# localNAS# Windows SFTP NAS for iPhone

This guide explains how to set up a simple **SFTP NAS on Windows** so you can access a folder (`D:\NAS`) from your iPhone securely. Only the NAS folder will be visible — no system folders like `Users`.

---

## Requirements

- Windows 10/11 with **OpenSSH Server installed**
- Administrator privileges
- iPhone with an SFTP app (e.g., **FE File Explorer**, **FileBrowser**, **Transmit**)
- LAN or Wi-Fi connection

---

## Step 1: Prepare the NAS folder

1. Create the folder:

D:\NAS

markdown
Copy code

2. Set **ownership** of `D:\NAS` to **Administrators**:

- Right-click → Properties → Security → Advanced → Change owner → Administrators → OK  
- Disable inheritance → Convert inherited permissions → OK

3. Create a **writable subfolder**:

D:\NAS\files

pgsql
Copy code

- Right-click → Properties → Security → Edit → Add user `nasuser` → Full Control → OK

---

## Step 2: Create SFTP user

Open **PowerShell as Administrator**:

```powershell
# Create a new user
net user nasuser YourPassword123 /add
net user nasuser /active:yes
Replace nasuser and YourPassword123 with your preferred username and password.

Step 3: Configure OpenSSH
Open the SSH config file:

powershell
Copy code
notepad "C:\ProgramData\ssh\sshd_config"
Add at the end:

pgsql
Copy code
Match User nasuser
    ChrootDirectory D:/NAS
    ForceCommand internal-sftp
    AllowTcpForwarding no
Notes: Use forward slashes /.
ForceCommand internal-sftp ensures SFTP-only access.

Save and close.

Step 4: Restart SSH service
powershell
Copy code
Restart-Service sshd
Step 5: Find your PC’s LAN IP
powershell
Copy code
ipconfig
Look for the IPv4 address under Ethernet or Wi-Fi.

Example: 192.168.0.50

Step 6: Connect from iPhone
Open an SFTP client (FE File Explorer, FileBrowser, Transmit)

Add a new server with:

Field	Value
Host	Your PC LAN IP
Port	22
Username	nasuser
Password	YourPassword123
Protocol	SFTP

Accept the host key if prompted.

You should see only files folder inside D:\NAS.

Notes
Writable subfolder: Upload files here (D:\NAS\files)

SFTP-only access: User cannot access full Windows shell

Permissions: Only Administrators and nasuser have access

If you see “Users” folder, check:

D:\NAS is owned by Administrators

SSH service has been restarted

SFTP client cleared cached session

Optional
You can use LAN or Wi-Fi.

You can create multiple SFTP users with separate subfolders.

yaml
Copy code

---

If you want, I can also make a **`setup.ps1` script** that automatically creates `nasuser`, sets folder permissions, and configures SSH, so you can just run it and connect your iPhone immediately.  

Do you want me to create that script too?
