Template Version: 2025.04.21
### Versions:
- [ ] 
##### Exploit Search:

##### Findings:

---
### Mountable Directories:
###### Commands:
- List: `showmount -e <IP>`
- Mount: `mount -t nfs <IP>:<remote_folder> <local_folder> -o nolock` (Use `-o vers=2` for no auth)
- Unmount: `umount <local_folder>` (Use `-f` for force)

---
- [ ] Exploit search