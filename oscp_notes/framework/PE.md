# Privilege Escalation

## Objectives

1. Gain elevated privileges on the target system or network.
2. Obtain access to sensitive data or systems.
3. Establish a stable and persistent presence within the target system's network.

### Tools and Techniques

- **Exploit frameworks:** Metasploit, Core Impact, Immunity Debugger
- **Privilege escalation tools:** Pwnage, Privix, PrivEsc
- **System exploitation tools:** Windows Exploitation Toolkit (WET), Linux Exploitation Toolkit (LET)
- **Lateral movement tools:** PowerSpade, Cobalt Strike

Frameworks:

### Exploit Frameworks

- **Windows Exploitation Toolkit (WET):** A Windows-specific exploit framework that includes a wide range of exploits for various Windows vulnerabilities.
- **Linux Exploitation Toolkit (LET):** A Linux-specific exploit framework that provides a comprehensive set of exploits for various Linux vulnerabilities.

#### Privilege Escalation Tools

- **Pwnage:** A tool that uses a combination of exploits and privilege escalation techniques to gain elevated privileges on Windows systems.
- **Privix:** A tool that uses a combination of exploits and privilege escalation techniques to gain elevated privileges on Linux systems.
- **PrivEsc:** A tool that uses a variety of techniques, including exploiting vulnerabilities, using misconfigured permissions, and leveraging system flaws, to escalate privileges.

### Lateral Movement Tools

- **PowerSpade:** A tool that uses a combination of exploits, privilege escalation techniques, and lateral movement tactics to gain access to sensitive data or systems.
- Cobalt Strike: A tool that provides a set of features and functions for conducting advanced persistent threats (APTs) and other types of attacks.

### Other Techniques

+ Using misconfigured permissions: Identify and exploit misconfigured permissions on the target system to gain elevated privileges.
+ Exploiting Windows services: Use exploits or vulnerabilities in Windows services, such as SMB or RPC, to escalate privileges.
+ Using Linux kernel flaws: Identify and exploit flaws in the Linux kernel to gain elevated privileges.
+ Using SUID binaries: Identify and exploit misconfigured SUID binaries on the target system to gain elevated privileges.

- 
- 

whoami

whoami /groups

Get-LocalUser

Get-LocalGroup

Get-LocalGroupMember adminteam

Get-LocalGroupMember Administrators

systeminfo

ipconfig /all 

netstat -ano

icacls

Power up?  

whoami /priv