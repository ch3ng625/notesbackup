Last update: 2025.11.14
# WordPress
### WPScan:
`wpscan --api-token $WPSCAN_API --url <URL> --disable-tls-checks`
### PHP Plugin RCE:
- Plugins => Add Plugins
- Upload PHP web shell
- Locate file under Media

---
# DotNetNuke (DNN)
TODO
https://angelica.gitbook.io/hacktricks/network-services-pentesting/pentesting-web/dotnetnuke-dnn

---
# Grafana
### Common Exploits:
- LFI (CVE-2021-43798 https://nusgreyhats.org/posts/writeups/a-not-so-deep-dive-in-to-grafana-cve-2021-43798/)
### Sensitive Files:
- `/etc/grafana/grafana.ini`
- `/var/lib/grafana/grafana.db` (SQLite)
### Grafana Database:
- `user` table:
	- `login`, `password`, `salt`
	- https://github.com/iamaldi/grafana2hashcat
- `data_source` table:
	- `basic_auth`, `basic_auth_user`, `basic_auth_password`, `json_data`, `secure_json_data`
	- Ref: https://github.com/jas502n/Grafana-CVE-2021-43798/blob/main/AESDecrypt.go

---