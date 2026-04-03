# Write-up: Network Exploration and Lateral Movement
**Machine:** Cyberheroes (TryHackme)  
**Author:** JH0W-Security 
**Date:** April 03, 2026

---

## 1. Introduction
This report documents a controlled exploitation process in a Linux environment. The objective was to identify misconfigurations in common services (SMB/SSH) and perform lateral movement between system users to gain access to sensitive data.

---

## 2. Methodology
I followed the standard Penetration Testing methodology:
1. **Reconnaissance:** Mapping active ports and services.
2. **Enumeration:** Collecting specific attack vectors (users and files).
3. **Exploitation:** Gaining initial access via brute force.
4. **Post-Exploitation:** Lateral movement between accounts and password cracking.

---

## 3. Technical Development

### I. Service Enumeration (SMB)
I began the analysis by checking the **Samba (445/tcp)** service to identify open shares.
* **Tool:** `smbclient`
* **Finding:** The `//Anonymous` directory allowed access without a password. Inside, I found a file named `staff.txt`, which revealed two valid users: **jan** and **kay**.

### II. Initial Access (SSH Brute Force)
With the mapped users, I performed a dictionary attack against the **SSH (22/tcp)** service.
* **Tool:** `Hydra`
* **Wordlist:** `rockyou.txt`
* **Success:** Credentials found for user **jan** (`jan:armando`).

### III. Lateral Movement (SSH Key Cracking)
While exploring the system as `jan`, I identified that the `/home/kay` directory had improper read permissions for other users.

1. **Exfiltration:** I located the SSH private key (`id_rsa`) inside the `.ssh` directory of user **kay**.
2. **Cryptographic Analysis:** The key was protected by a passphrase. I used the `ssh2john.py` script to extract the hash.
3. **Offline Cracking:** I used `John the Ripper` to crack the key's protection.
   * **Result:** Passphrase identified: `beeswax`.

---

## 4. Proof of Concept (PoC)
Using the key and the passphrase, I performed the final login:

```bash
# Connecting using the exfiltrated private key
ssh -i kay_id_rsa kay@10.64.191.131

# Authenticating with the cracked passphrase
Enter passphrase for key 'kay_id_rsa': beeswax
```

---

## 5. Skills Demonstrated
SMB Enumeration (smbclient)

Password Cracking (John the Ripper, Hydra)

Linux Permission Analysis

SSH Key Exfiltration and Exploitation
