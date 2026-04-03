# Write-up: TryHackMe - Dig Dug
**Date:** April 03, 2026  
**Author:** JH0W-Security
**Category:** Network / DNS Enumeration  

---

## 1. Machine Description
The **Dig Dug** room on TryHackMe is a challenge focused on the exploration and enumeration of the DNS (Domain Name System) service. The primary objective is to query a specific DNS server to extract hidden information within its records, simulating a sensitive data exposure vulnerability.

* **Target IP:** `10.66.147.193`
* **Target Domain:** `givemetheflag.com`
* **Difficulty:** Easy (Info)

---

## 2. Methodology and Execution

### 2.1 Port Scanning (Nmap)
I initiated the process with a service mapping to confirm host availability and identify potential attack vectors:

```bash
nmap -sCV -A 10.66.147.193 -Pn
```
Scan Observations:

Port 22/tcp (SSH): Open (OpenSSH 8.2p1 Ubuntu).

DNS Service: Identified operating via UDP (port 53). Since the machine's focus is DNS, I used specific tools to query the service directly.

2.2 DNS Enumeration with ```dig```
I used the ```dig``` (Domain Information Groper) utility to perform targeted queries against the target server.

```TXT``` Record Query
TXT records are frequently used to store text strings, domain verification keys, or, in CTF scenarios, the challenge flags.

```Bash
dig @10.66.147.193 givemetheflag.com TXT
```
Server Response:

```DNS Zone file
;; ANSWER SECTION:
givemetheflag.com.  0   IN  TXT  "flag{0767ccd06e79853318f25aeb08ff83e2}"
```

Validating Other Records
Tests were also conducted on ```MX``` (Mail Exchange) and ```NS``` (Name Server) records to validate the exposure surface:

```Bash
# MX Query (Mail Servers)
dig @10.66.147.193 givemetheflag.com MX

# NS Query (Name Servers)
dig @10.66.147.193 givemetheflag.com NS
```

The server presented a fixed response configuration, returning the flag for multiple query types directed at the ```givemetheflag.com``` domain.

---

## 3. Challenges and Key Learnings
Network Troubleshooting: I overcame initial timeout issues by validating VPN connectivity and correcting the target's IP address.

Transport Protocols: I reinforced the concept that DNS operates primarily over UDP, requiring tools like ```dig``` for precise analysis when a conventional TCP scan is insufficient.

---

## 4. Conclusion
The successful exploration demonstrated the importance of exhaustive enumeration. In real-world audits, this technique allows for the discovery of subdomains, email infrastructures, and other data that should not be publicly accessible.

Captured Flag: ```flag{0767ccd06e79853318f25aeb08ff83e2}```

---

## 5. Tools Used
Nmap: Network reconnaissance and version detection.

Dig: Detailed interrogation of DNS records.

Kali Linux: Operational testing environment.

---

## 5. Skills Demonstrated
* **DNS Enumeration & Reconnaissance (dig):** Querying DNS servers to extract sensitive records.
* **Network Service Identification (Nmap):** Port mapping and service version detection in Linux environments.
* **Protocol Analysis (UDP vs TCP):** Practical understanding of how infrastructure services behave across different transport protocols.
* **Troubleshooting & Connectivity:** Diagnosing network failures and configuring VPN tunnels for testing environments.
