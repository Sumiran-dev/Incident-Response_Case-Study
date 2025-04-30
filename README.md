# 🛡️ Incident Response Case Study

---
## 🧭 Overview

This case study showcases a hands-on incident response investigation using Splunk, performed as part of the Blue Team Lab Online (BTLO) labs. The scenario simulated a targeted attack on a web application, and I took on the role of a Tier 1 SOC Analyst tasked with detecting the attack, tracing the adversary's steps, and identifying the techniques used.

Each screenshot included in this investigation provides a forensic snapshot of critical events: from identifying the attacker’s IP, to detecting CVE exploitation, and uncovering lateral movement. This walkthrough is designed to be understood by both cybersecurity professionals and non-technical readers who are interested in how real-world investigations unfold.

---

## 🔍 Phase 1: Identifying the Attacker’s IP

Using Splunk, I queried logs related to HTTP requests. In the screenshot (Image 1), the IP address `113.89.232.157` stands out due to its repeated interaction with sensitive endpoints.

- **Key Observation:** Suspicious access patterns from `113.89.232.157` trying to reach `/api/upload`
- **Log Evidence:** Shows POST requests that align with known reverse shell behavior

> *This IP was flagged as the attacker’s origin based on behavior consistent with web exploitation.*
![image](https://github.com/user-attachments/assets/ad1fe5bc-932e-4091-8fff-208fd97ae418)

![image](https://github.com/user-attachments/assets/7b034ff7-4463-4c14-9210-7252d27bcc47)

---

## 🔐 Phase 2: Reconnaissance and Internal Endpoint Discovery

In this phase, I investigated the structure of the website using information from the `robots.txt` file (Screenshot 2).

- **Discovered URLs:**
  - `/admin`
  - `/admin/file-upload`

> *These endpoints, hidden from search engines, hinted at sensitive admin functionality that could be abused for file uploads or privilege escalation.*

![image](https://github.com/user-attachments/assets/3f99fc8c-0060-41d7-8c3c-824ddc3623b9)

![image](https://github.com/user-attachments/assets/3e9939da-3903-426c-938a-1a80dce71f56)

![image](https://github.com/user-attachments/assets/3a048f6e-cf20-4b29-8fd1-f8d8e2b75585)

---

## 🧠 Phase 3: CVE Exploitation via Header Abuse

The attacker leveraged a known vulnerability in the middleware layer, confirmed by a specific HTTP header found in the SIEM logs:

- **Header Detected:** `x-middleware-subrequest`
- **Exploited Endpoint:** `/api/upload`
- **Screenshot 3:** Shows the exact header captured in the log event

> *The presence of this header indicates that the attacker bypassed authorization checks by tricking the server into treating the request as internal.*

This action led to the successful upload of a **reverse shell**, allowing the attacker remote access to the server.

![image](https://github.com/user-attachments/assets/9a171757-5b4b-4955-918b-27e6d91d7028)

![image](https://github.com/user-attachments/assets/0304c9bc-00ea-4bf1-9c60-7576ca30c6b3)

![image](https://github.com/user-attachments/assets/87da7bf5-760c-4818-a53e-e98f681fe348)

---

## 📡 Phase 4: Reverse Shell Callback Detection

After uploading the malicious file, the attacker attempted to initiate a connection back to their machine.

- **Callback Target:**
  - **IP:** `113.89.232.157`
  - **Port:** `31337`
- **Screenshot 4:** Captures the payload's connection attempt

> *This stage confirms command-and-control activity, where the compromised server would act as a backdoor to the attacker.*
![image](https://github.com/user-attachments/assets/7ddd17bb-fdd3-4e4c-a3bb-b365efb54906)

---

## 🔁 Phase 5: Lateral Movement Attempt

Using Splunk, I reviewed Windows event logs (specifically `Event ID 4648`) to analyze credential use from the web server to another internal host:

- **Source Machine:** WebApp Server
- **Target Machine:** `172.217.164.174`
- **Technique Used:** `SSH Brute Force`
- **Screenshot 5 & 6:** Shows both failed and successful login attempts, plus `Accepted password` events

> *The logs confirm the attacker guessed credentials and successfully logged into another server via SSH.*
![image](https://github.com/user-attachments/assets/82a13552-c1a8-4e0e-9dc3-1e95da815f5a)


---

## 👤 Phase 6: User Account Compromise

- **Compromised Account:** `dbserv`
- **Access Method:** Credential reuse with SSH
- **Event Code:** 4648 followed by 4624 (successful logon)
- **Screenshot 7:** Displays the login trail and mapping to internal lateral movement

> *This shows how lateral movement can be achieved through weak or reused passwords.*
![image](https://github.com/user-attachments/assets/680171bc-e253-4a95-81a6-67944cc10870)

---

## 🧩 MITRE Techniques Mapped

| Tactic             | Technique                        | ID         |
|--------------------|----------------------------------|------------|
| Initial Access     | Exploit Public-Facing App        | T1190      |
| Execution          | Command & Scripting Interpreter  | T1059      |
| Credential Access  | Password Guessing (Brute Force)  | T1110.001  |
| Lateral Movement   | SSH                              | T1021.004  |

---

## 📈 Summary & Human Insight

This investigation wasn't just about ticking checkboxes — it was about **thinking critically** and **connecting the dots** across systems, logs, and behavior patterns. Each log told a part of the story, and by following the evidence, I was able to reconstruct the entire attack chain.

> “As someone from a neurodivergent background, focusing on patterns and logic is a strength I embrace. This investigation allowed me to apply that strength to a real-world scenario — and it reminded me why I love cybersecurity.”

---

## 🛠️ Skills Demonstrated

- Splunk SIEM investigation
- Log analysis and event correlation
- MITRE ATT&CK mapping
- Threat detection and incident reporting
- Real-world simulation of SOC Tier 1 duties

---

## 🧠 Final Thoughts

This was more than a lab — it was a chance to simulate what real SOC analysts do every day:
- Pay attention to small anomalies
- Correlate different data sources
- Understand the attacker’s mindset

> *In cybersecurity, we protect not just systems, but people. This lab made me feel one step closer to being ready for that mission.*
