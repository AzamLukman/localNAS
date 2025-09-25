# Windows SFTP NAS for iPhone

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

```
D:\NAS
```

2. Set **ownership** of `D:\NAS` to **Administrators**:

- Right-click → Properties → Security → Advanced → Change owner → Administrators → OK  
- Disable inheritance → Convert inherited permissions → OK

3. Create a **writable subfolder**:

```
D:\NAS\files
```

- Right-click → Properties → Security → Edit → Add user `nasuser` → Full Control → OK

---

## Step 2: Create SFTP user

Open **PowerShell as Administrator**:

```powershell
# Create a new user
net user nasuser YourPassword123 /add
net user nasuser /active:yes
```

> Replace `nasuser` and `YourPassword123` with your preferred username and password.

---

## Step 3: Configure OpenSSH

1. Open the SSH config file:

```powershell
notepad "C:\ProgramData\ssh\sshd_config"
```

2. Add at the end:

```
Match User nasuser
    ChrootDirectory D:/NAS
    ForceCommand internal-sftp
    AllowTcpForwarding no
```

> Notes: Use forward slashes `/`.  
> `ForceCommand internal-sftp` ensures **SFTP-only access**.

3. Save and close the file.

---

## Step 4: Restart SSH service

```powershell
Restart-Service sshd
```

---

## Step 5: Find your PC’s LAN IP

```powershell
ipconfig
```

- Look for the **IPv4 address** under Ethernet or Wi-Fi.  
- Example: `192.168.0.50`

---

## Step 6: Connect from iPhone

1. Open an SFTP client (FE File Explorer, FileBrowser, Transmit)  
2. Add a new server with:

| Field       | Value                     |
|------------|---------------------------|
| Host       | Your PC LAN IP            |
| Port       | 22                        |
| Username   | nasuser                   |
| Password   | YourPassword123           |
| Protocol   | SFTP                      |

3. Accept the host key if prompted.  
4. You should see **only `files` folder inside `D:\NAS`**.  

---

## Notes

- **Writable subfolder**: Upload files here (`D:\NAS\files`)  
- **SFTP-only access**: User cannot access full Windows shell  
- **Permissions**: Only `Administrators` and `nasuser` have access  
- **If you see “Users” folder**, check:  
  1. `D:\NAS` is owned by Administrators  
  2. SSH service has been restarted  
  3. SFTP client cleared cached session  

---

## Optional

- You can use LAN or Wi-Fi.  
- You can create multiple SFTP users with separate subfolders.
