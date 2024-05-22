# Objectives

1. Port Scanning
2. Service Detection
3. OS Detection:
4. Vulnerability Scanning

## Tools and Techniques  

<details>
<summary><b> Linux</b> </summary>

- nmap\
  sudo nmap -sC -sV {IP} -oN {IP-location}/nmap

    ```bash
      nmap -v -sn 192.168.50.1-253 -oG ping-sweep.txt
      grep Up ping-sweep.txt | cut -d " " -f 2
      nmap -p 80 192.168.50.1-253 -oG web-sweep.txt
      grep open web-sweep.txt | cut -d" " -f2
      nmap -sT -A --top-ports=20 192.168.50.1-253 -oG top-port-sweep.txt
      nmap -sT 192.168.50.149 - connected scan
      sudo nmap -sU 192.168.50.149 - UDP 
      sudo nmap -sU -sS 192.168.50.149 - both
      nmap -sT -A --top-ports=20 192.168.50.1-253 -oG top-port-sweep.txt - top ports
      sudo nmap -O 192.168.50.14 --osscan-guess - OS fingeprint
      nmap -sT -A 192.168.50.14 - all 
      nmap --script http-headers 192.168.50.6 - scans with http-headers
    ```

- rustscan?
- nc
  nc -nvv -w 1 -z 192.168.50.152 3388-3390
- Scanless (an open-source network scanning tool)
- Xprobe (an open-source network scanning tool)
  
</details>

<details>
<summary><b> Windows</b> </summary>

- Test-NetConnection -Port 445 192.168.50.151
  
  ```powershell
  1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("192.168.50.151", $*)) "TCP port $* is open"} 2>$null
  ```

</details>

## OS Fingerprinting Tools

+ Nmap (can perform OS detection)
+ Os-fingerprint (an open-source OS fingerprinting tool)
+ OS-Scanner (an open-source OS scanner)

## Vulnerability Scanning Tools

+ nmap
+ Burp Suite (a web application scanning and exploitation tool)

## Other Reconnaissance Techniques:**

+ DNS Rebinding Attacks (can be used to detect potential entry points)
  + host {{ IP }} searches  dns, can specify type with -t (type)
  + dns brute force
        ```bash
        for ip in $(cat list.txt); do host $ip.url.com; done
        ```
  + dnsrecon - python script for dns enumeration
  + dnsenum - cli search dns zones
  + HTTP/HTTPS Scanning (can be used to detect running services and identify potential vulnerabilities)
  + whatweb http://{{ IP }} {IP}
  + wpscan --url http://{{ IP }} --enumerate p --plugins-detection aggressive -o {IP-location}/wpscan
  + go buster on webservers - gobuster dir -u http://{{ IP }}/{{IP}} -w usr/share/wordlists/dirb/common.txt -o {IP-location}/gobuster -x txt,pdf,config
  + sudo nmap -p80 --script=http-enum 1{IP}
+ SMTP Scanning (can be used to detect running email services and identify potential vulnerabilities)
  + nmap smtp can be scanned using nmap
+ SNMP enumeration
  + sudo nmap -sU --open -p 161 192.168.50.1-254 -oG open-snmp.txt
  + onesixtyone -c community -i ips**
  + snmpwalk -c public -v1 -t 10 192.168.50.151
+ FTP Scanning (can be used to detect running file transfer services and identify potential vulnerabilities)

+ SMB Discovery (can be used to detect running file transfer services and identify potential vulnerabilities)
  + smb discovery  nmap -v -p 139,445 --script smb-os-discovery 192.168.50.152
  + nbtscan
    + ex:  nbtscan -r 192.168.50.0/24
  + net-view
  
### Social Engineering Techniques
  
+ Phishing (attempting to trick users into revealing sensitive information)


- <details open>
  <summary><a href="#">Section 1 with a link</a></summary>

  - <details>
    <summary><a href="../src/actions/">subsection 2 with a link</a></summary>

    - a list
    - with some stuff

    > and other things

    - [x] like
    - [ ] a task list 

    </details>

  - <details>
    <summary><b>another subsection</a></summary>

    a. with another list
    b. and some other stuff
    d. [and](),
      [more](),
      [classic](),
      [md]
    e. _no need_ __of html__
    </details>

  - <details>
    <summary>last sub-section</a></summary>

    blablabla

    ```rb
    def some_code
      puts "Rails is so cool"
    end
    ```
    </details>

  - a random not collapsable section
    > legacy. Should be restructured.

    ```js
    console.log("look what I found, a new js framework. Still no real alternative to rails though")
    ```

  - <details>
    <summary>and another collapsable section</summary>

    ...
  </details>

- <details>
  <summary>section 2</summary>
      
  - some parent content

  - and another list

  - <details>
    <summary>section 2.1</summary>

      and some content
    </details>

  - <details>
    <summary>section 2.2</summary>

      and some content
    </details>
  
  - section 2.3
    and some no collapsed content

</details>








