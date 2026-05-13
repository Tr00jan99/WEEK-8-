<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:00FF00,100:000000&height=250&section=header&text=Network%20Enumeration%20Lab&fontSize=50&fontColor=ffffff&animation=fadeIn&fontAlignY=38&desc=Comprehensive%20Security%20Analysis%20Report&descAlignY=61&descAlign=62"/>

  <p align="center">
    <a href="#"><img src="https://img.shields.io/badge/Author-Syafiq%20Ridzuan-00FF00?style=for-the-badge&logo=github&logoColor=white" alt="Author"></a>
    <a href="#"><img src="https://img.shields.io/badge/Target-Metasploitable%202-DC382D?style=for-the-badge&logo=kalilinux&logoColor=white" alt="Target"></a>
    <a href="#"><img src="https://img.shields.io/badge/Tools-Nmap%20%7C%20Zenmap%20%7C%20Enum4linux-0078D6?style=for-the-badge&logo=kali-linux&logoColor=white" alt="Tools"></a>
    <a href="#"><img src="https://img.shields.io/badge/Status-Completed-success?style=for-the-badge" alt="Status"></a>
  </p>
</div>

> [!WARNING]  
> ### 🛑 DISCLAIMER BY SYAFIQ RIDZUAN
> **This report and its contents are for EDUCATIONAL AND AUTHORIZED TESTING PURPOSES ONLY.** All enumeration techniques and scans demonstrated here were executed in a closed, locally hosted, and strictly controlled lab environment (`VMware` / `Metasploitable 2`). Do not use these techniques on any systems or networks without explicit, written permission. **Use this knowledge responsibly.**

---

## 📑 Table of Contents
1. [Introduction & Preparation](#-introduction--preparation)
2. [Challenge 1 — NetBIOS Enumeration](#1️⃣-challenge-1--netbios-enumeration)
3. [Challenge 2 — Fast Nmap Scan](#2️⃣-challenge-2--fast-nmap-scan)
4. [Challenge 3 — DNS Records](#3️⃣-challenge-3--dns-records)
5. [Challenge 5 — TTL OS Fingerprinting](#4️⃣-challenge-5--ttl-os-fingerprinting)
6. [Challenge 9 — FTP Banner](#5️⃣-challenge-9--ftp-banner)
7. [Challenge 10 — Anonymous FTP Login](#6️⃣-challenge-10--anonymous-ftp-login)
8. [Challenge 12 — Enum4linux](#7️⃣-challenge-12--enum4linux)
9. [Challenge 16 — Version Detection](#8️⃣-challenge-16--version-detection)
10. [Challenge 17 — OS Detection](#9️⃣-challenge-17--os-detection)
11. [Challenge 19 — RPC Info](#🔟-challenge-19--rpc-info)
12. [Conclusion](#-conclusion)

---

## 🛠️ Introduction & Preparation

Before diving into the challenges, the Security Operations (SecOps) environment was prepared to ensure a smooth enumeration process. 

| Role | Details |
| :--- | :--- |
| **Attacker Machine** | Windows 10 & Kali Linux (VMware) |
| **Victim Machine** | Metasploitable 2 |
| **Victim IP Address** | `192.168.135.135` |
| **Tools Used** | CMD, Terminal, Zenmap, Nmap, Netcat, Enum4linux |

> [!TIP]
> The connection was first verified by sending a simple `ping 192.168.135.135` from the Windows 10 machine to the Metasploitable 2 machine to ensure they could communicate.

---

## 1️⃣ Challenge 1 — NetBIOS Enumeration

> [!NOTE]
> **Objective:** To find the computer name, domain/workgroup, and active services using NetBIOS over the network.

- **Command Used:** `nbtstat -a 192.168.135.135` (Executed on Windows 10 CMD)

**🔍 Key Findings:**
* **`<00>`:** The machine is named `METASPLOITABLE` and belongs to the `WORKGROUP` domain.
* **`<20>`:** The File and Print Sharing service is active. This is a big indicator that network shares are accessible.
* **`<01> / <1D>`:** The target acts as the Master Browser, meaning it maintains the list of other computers on the network.

> 📸 <img width="1496" height="348" alt="img1" src="https://github.com/user-attachments/assets/f8d2826b-9a8d-471b-a87b-14b8e7806af1" />
><img width="1454" height="791" alt="img2" src="https://github.com/user-attachments/assets/1bb466ba-e1f8-4e5f-9870-2ad6c1b11824" />
><img width="1134" height="564" alt="img3" src="https://github.com/user-attachments/assets/c871654d-815f-4736-a48f-d439703438d1" />
><img width="1134" height="564" alt="img3" src="https://github.com/user-attachments/assets/c871654d-815f-4736-a48f-d439703438d1" />
>



---

## 2️⃣ Challenge 2 — Fast Nmap Scan

> [!NOTE]
> **Objective:** To perform a fast scan of the most common ports to see what services are running and exposed.

- **Command Used:** `nmap -F 192.168.135.135` (Executed on Zenmap)

**🔍 Key Findings:**
The scan revealed multiple high-risk open ports on the target machine:
* **Port 21 (FTP):** Often allows anonymous or weak-credential access.
* **Port 23 (Telnet):** Highly insecure remote access because it transmits passwords in plain text.
* **Port 139/445 (SMB):** Allows file sharing, which can lead to severe SMB vulnerabilities.
* **Databases Exposed:** MySQL (`3306`) and PostgreSQL (`5432`) are open to the network.

> 📸 <img width="1200" height="1077" alt="img5" src="https://github.com/user-attachments/assets/6a292ff1-de99-4ce7-8179-84e655300a20" />


---

## 3️⃣ Challenge 3 — DNS Records

> [!NOTE]
> **Objective:** To extract Domain Name System (DNS) records and understand how the domain `loranet.my` routes traffic and emails.

- **Commands Used:** 
  * `nslookup loranet.my` (Windows CMD)
  * `dig ANY loranet.my` (Kali Linux)
  * `dig MX loranet.my` (Kali Linux)

**🔍 Key Findings:**
* **NSLookup:** Successfully resolved `loranet.my.localdomain` to the IP address `91.107.211.163`.
* **DIG ANY:** The server returned an `RFC8482` response. This means the server blocked the "ANY" query for security reasons (known as Query Minimization) to prevent DNS amplification attacks.
* **DIG MX:** Successfully found the Mail Exchanger (MX) record, which tells us which server is responsible for handling emails for the domain.

> 📸 <img width="1174" height="652" alt="img6" src="https://github.com/user-attachments/assets/fba3d1c8-6ea0-41f4-8f98-8651d64d4199" />
><img width="1533" height="949" alt="img7" src="https://github.com/user-attachments/assets/11ee0150-4184-49e3-a6d8-436980837991" />
><img width="1622" height="1016" alt="img8" src="https://github.com/user-attachments/assets/ac450c8f-78d4-4b28-9875-32405e872794" />




---

## 4️⃣ Challenge 5 — TTL OS Fingerprinting

> [!NOTE]
> **Objective:** To guess the target's Operating System (OS) simply by looking at the Time-To-Live (TTL) value from a basic ping command.

- **Command Used:** `ping 192.168.135.135` (Executed on Kali Linux)

**🔍 Key Findings:**
* The ping reply showed a **TTL = 64**.
* Based on standard networking rules, a TTL of 64 strongly indicates that the target is running a **Linux or Unix** based Operating System.

> 📸 <img width="1649" height="648" alt="img25" src="https://github.com/user-attachments/assets/1fa1e96b-4ce4-4b88-b1b3-402a2df8fc3b" />


---

## 5️⃣ Challenge 9 — FTP Banner

> [!NOTE]
> **Objective:** To grab the "banner" (greeting message) of the FTP service to find out the exact software version it is running.

- **Command Used:** `nc 192.168.135.135 21` (Executed on Kali Linux)

**🔍 Key Findings:**
* Using Netcat, we successfully connected to the FTP port (21).
* The server responded with the banner: `220 (vsFTPd 2.3.4)`.
* This confirms the target is running **vsFTPd version 2.3.4**, which is famously known in the cybersecurity world for having a severe malicious backdoor.

> 📸<img width="699" height="177" alt="img9" src="https://github.com/user-attachments/assets/6346d4e7-50f7-4b38-9143-02990a62c162" />


---

## 6️⃣ Challenge 10 — Anonymous FTP Login

> [!NOTE]
> **Objective:** To test if the FTP server allows anyone to log in without a valid user account.

- **Command Used:** `ftp 192.168.135.135` (Executed on Kali Linux)

**🔍 Key Findings:**
* We attempted to log in using the username `anonymous` and left the password completely blank.
* The server responded with `230 Login successful.`.
* **Security Risk:** This is a massive vulnerability. Anyone on the network can access the FTP server files without needing a real password.

> 📸 <img width="1002" height="614" alt="img10" src="https://github.com/user-attachments/assets/fe87688f-a801-4d75-b53d-4d7ac8594cdb" />


---

## 7️⃣ Challenge 12 — Enum4linux

> [!NOTE]
> **Objective:** To extract deep and detailed information from the target's SMB service, such as user lists, shares, and password policies.

- **Command Used:** `enum4linux -a 192.168.135.135` (Executed on Kali Linux)

**🔍 Key Findings:**
* Connected successfully to the target without requiring a password (null session).
* Retrieved extensive details about the system's network configuration and hidden shares. This information is a goldmine for planning further exploitation.

> 📸 <img width="458" height="642" alt="image" src="https://github.com/user-attachments/assets/224d158c-382b-48f3-bdda-cd472633fa16" />
><img width="462" height="803" alt="image" src="https://github.com/user-attachments/assets/b8ed39f0-3d44-47ad-8357-2033702d0423" />
><img width="466" height="352" alt="image" src="https://github.com/user-attachments/assets/395ba03f-f251-4046-a689-430dba09cc59" />
<img width="358" height="352" alt="image" src="https://github.com/user-attachments/assets/c2829d05-df07-453b-8a0e-0f55ba18a355" />
><img width="553" height="817" alt="image" src="https://github.com/user-attachments/assets/75575f71-0887-4a53-875c-bac151949af1" />




---

## 8️⃣ Challenge 16 — Version Detection

> [!NOTE]
> **Objective:** To probe the open ports and determine the exact software versions of the services running on them.

- **Command Used:** `nmap -sV 192.168.135.135` (Executed on Zenmap)

**🔍 Key Findings:**
* The scan successfully pinpointed specific service versions:
  * `OpenSSH 4.7p1`
  * `vsftpd 2.3.4`
* **Why this matters:** Knowing the exact software versions makes it incredibly easy for an attacker to search for known public exploits (like searching Exploit-DB) to compromise the machine.

> 📸 <img width="1201" height="770" alt="image" src="https://github.com/user-attachments/assets/4d8e2595-defe-42d6-9ea0-565a8c8aea13" />


---

## 9️⃣ Challenge 17 — OS Detection

> [!NOTE]
> **Objective:** To use Nmap's advanced fingerprinting techniques to confidently confirm the target's Operating System.

- **Command Used:** `nmap -O 192.168.135.135` (Executed on Zenmap)

**🔍 Key Findings:**
* Nmap analyzed the network responses and identified the OS as **Linux 2.6.9 - 2.6.33**.
* This perfectly matches and verifies our earlier OS guess from the TTL fingerprinting in Challenge 5.

> 📸 <img width="988" height="797" alt="image" src="https://github.com/user-attachments/assets/91594573-6dce-4165-9af2-aa7aef9c4ec9" />


---

## 🔟 Challenge 19 — RPC Info

> [!NOTE]
> **Objective:** To enumerate Remote Procedure Call (RPC) services and find out what network file systems are being shared.

- **Command Used:** `rpcinfo -p 192.168.135.135` (Executed on Kali Linux)

**🔍 Key Findings:**
* Discovered an active **NFS (Network File System)** Server running on Port 2049.
* The Portmapper service is active on port 111, advertising these services to the network.
* **Security Risk:** If sensitive directories like `/root` or `/home` are shared with weak permissions, an attacker could mount them locally and steal data or upload SSH keys to gain full control.

> 📸 <img width="330" height="398" alt="image" src="https://github.com/user-attachments/assets/cb6a7198-a516-49c0-ba6d-a9f01b658ec1" />


---

## 🏁 Conclusion

The enumeration phase was highly successful in mapping out the attack surface of the `192.168.135.135` (Metasploitable 2) machine. By following a structured approach, we uncovered several critical vulnerabilities that could be easily exploited, including:

1. **Anonymous FTP Access:** The `vsFTPd 2.3.4` service allows password-less login.
2. **Insecure Protocols:** Services like Telnet (23) transmit data in plain text.
3. **Exposed File Shares:** SMB and NFS services are open and could leak sensitive system files.

This report highlights the absolute necessity of disabling unused services, enforcing strong authentication, and keeping software up-to-date.

---
<div align="center">
  <p><b>🛡️ Executed and Documented by Syafiq Ridzuan</b></p>
  <p><a href="https://github.com/SyafiqRidzuan">GitHub Profile</a></p>
</div>
![Uploading img1.png…]()
