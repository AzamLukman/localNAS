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



Option 1: Use Windows Built-in VPN (L2TP/IPsec)
Step 1: Enable VPN Server on Windows

Open Control Panel → Network and Sharing Center → Change adapter settings.

Press Alt → select File → New Incoming Connection.

Select the user account for VPN access (e.g., nasuser) → Next.

Check Through the Internet → Next.

Select Internet Protocol Version 4 (TCP/IPv4) → Properties.

Assign IP range for VPN clients (e.g., 192.168.50.10 – 192.168.50.20).

Finish the wizard.

Step 2: Configure Router for VPN

Login to your router.

Forward the VPN ports to your PC:

L2TP/IPsec: UDP 500, UDP 1701, UDP 4500

Ensure your router supports VPN passthrough.

Step 3: Connect from iPhone

Open Settings → VPN → Add VPN Configuration.

Select Type: L2TP.

Fill in details:

Field	Value
Description	My Home VPN
Server	Your public IP or DDNS name
Account	nasuser
Password	VPN password
Secret	Same as “IPSec pre-shared key”
Send All Traffic	ON

Tap Done, then Connect.

Once connected, your iPhone is virtually on your home network → you can use FTPManager / PhotoSync with your local IP or hostname to access NAS.

Option 2: Use a Third-Party VPN (Easier)

Install WireGuard or OpenVPN on your PC or router.

Configure a VPN profile for your iPhone.

Once connected, your iPhone can access NAS securely over the internet.

✅ Advantages: Much safer than port forwarding; encrypted traffic; works on mobile networks.
