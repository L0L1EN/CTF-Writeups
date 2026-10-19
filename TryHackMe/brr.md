# TryHackMe: Brr

- **Level:** Easy
- **Category:** OT / ICS (SCADA, Modbus)
- **Target IP:** 10.128.185.45
- **My IP:** 192.168.129.228

## 1. Reconnaissance — Nmap scan

```
22/tcp   open  ssh          OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http         WebSockify Python/3.12.3
5020/tcp open  zenginkyo-1?
5901/tcp open  vnc          VNC (protocol 3.8)
8080/tcp open  http         Apache Tomcat/Coyote JSP engine 1.1
```

A few things stand out:

- **Port 8080** exposes a web server with the title *"ScadaBR CTF"* — ScadaBR is an open-source SCADA/HMI platform running on Tomcat. This is our main entry point.
- **Port 5020** is labeled `zenginkyo-1?` by nmap, but that is just a default entry in `nmap-services` (the trailing `?` means no probe actually matched). Port 5020 is a classic alternate port for **Modbus/TCP** in OT/ICS environments — a strong hint that this is the PLC.
- Ports 80 (`WebSockify`) and 5901 (VNC) together indicate that a browser-based VNC session (noVNC) is available, likely to interact with the HMI panel.

## 2. Gaining access to ScadaBR

Browsing to `http://10.128.185.45:8080/ScadaBR/login.htm` leads to the ScadaBR login page (version 1.11.0).

ScadaBR ships with **default credentials**, and they have not been changed here:

- **Username:** `admin`
- **Password:** `admin`
- **Email:** `admin@yourMangoDomain.com`

We are now logged into the SCADA dashboard.

## 3. Finding the PLC — data source configuration

ScadaBR is not hiding the flag itself. It is just a supervision panel that tells us where the real process data lives: a **PLC speaking Modbus**, reachable on port **5020**.

In the ScadaBR administration panel, the **Data Sources** section reveals a data source named **`secret`**:

- **Type:** Modbus IP
- **Host:Port:** `plc:5020`

So the flag is not in the dashboard — we have to follow the panel and chase the "chill" all the way down to the PLC's registers.

## 4. Reading the flag from the PLC registers

The data points configured under the `secret` data source don't show anything useful by default. The trick is to **edit the data point settings** and set the number of registers to read to **20**.

Once the change is applied, the values returned by the PLC appear — but they are **hexadecimal values**. Each 16-bit holding register stores one ASCII character of the flag (high byte `0x00`, low byte the character itself), for example `0054` = `T`, `0048` = `H`, `004D` = `M`, `007B` = `{` …

Decoding the whole register table gives the flag.

## 5. Flag

```
THM{modbus_hid}
```

## Takeaways

- **Default credentials** on a SCADA dashboard (admin/admin) are a direct open door to the OT environment description.
- The ScadaBR panel itself never held the flag — its **data source configuration** simply leaked where the real process data lives: a Modbus PLC on port 5020.
- **Modbus/TCP has no authentication**: anyone who can reach port 5020 has exactly the same access as the HMI. The login screen on 8080 protected the dashboard, never the controller.
- Don't blindly trust nmap service labels for OT protocols: Modbus, DNP3, BACnet and S7comm routinely come back unlabeled or misidentified because nmap's probes are tuned for IT protocols.
