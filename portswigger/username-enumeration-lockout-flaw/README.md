# Technical Report: PortSwigger Lab - Username Enumeration via Logical Flaw in Account Lockout

## 1. General Information
* **Date:** April 3, 2026
* **Target:** PortSwigger Academy (Authentication Module)
* **Vulnerability:** Username Enumeration (Logical Flaw in Account Lockout)
* **Level:** Practitioner
* **Tools Used:** Burp Suite (Proxy, Intruder)

---

## 2. Executive Summary
This report describes the exploitation of a logical flaw in an authentication portal's defense mechanism. The brute-force protection system (account lockout) was implemented inconsistently, allowing an attacker to identify valid usernames through the analysis of differentiated error messages and server behavior under stress.

---

## 3. Vulnerability Description
The application implements a security rule that locks accounts after multiple failed login attempts. However, this rule contains a logical flaw: it is only triggered when the **username entered exists in the database**.

* **Behavior with an invalid username:** The server always returns the standard error message, regardless of the number of attempts.
* **Behavior with a valid username:** After 5 failed attempts, the message specifically changes to: `"You have made too many incorrect login attempts"`.

This discrepancy in behavior allows for **Username Enumeration**.

---

## 4. Exploitation Methodology

### Step 1: Username Enumeration (Intruder)
1.  Intercept the `POST /login` request and send it to **Burp Intruder**.
2.  Set the attack type to **Cluster Bomb**.
3.  Define two Payload sets:
    * **Payload 1 (username):** Use the candidate list provided by the lab.
    * **Payload 2 (null):** Set type to "Null payloads" and configure it to generate 5 attempts per user.
4.  **Analysis:** Sort the results by **Length**. The target user will present a response with a different size, containing the account lockout warning.

### Step 2: Password Brute-Force (Sniper)
1.  Wait for the lockout timer to reset (approx. 1 minute).
2.  Configure a new attack in **Sniper** mode.
3.  Fix the identified username and place the payload marker `§` only on the `password` parameter.
4.  Load the candidate password list.
5.  **Result:** One request will result in an **HTTP 302** status, confirming a successful login and redirecting to the account page.

---

## 5. Proof of Concept (PoC)
* **Vulnerable Endpoint:** `/login`
* **Trigger Payload (Valid):** `username=§user§&password=invalid§§` (repeated 5x to force lockout).
* **Success Evidence:** **HTTP 302** redirect to the `/my-account` page.

---

## 6. Mitigation Recommendations
* **Uniform Messages:** Error and lockout messages must be generic and identical, regardless of whether the user exists in the system or not.
* **IP-based Protection:** Implement temporary blocking based on the source IP address (Rate Limiting).
* **Response Sanitization:** Ensure that HTTP status codes and server response times are constant across all authentication attempts.

---

## 7. Skills Demonstrated

* **Authentication Flaws Exploitation:** Identification and exploitation of vulnerabilities in login and session management mechanisms.
* **Automated Web Attacks (Burp Intruder):** Advanced configuration of Cluster Bomb and Sniper attacks for security testing automation.
* **Logic Bug Identification:** Ability to deduce flaws in security logic flows (Account Lockout) beyond common code vulnerabilities.
* **HTTP Protocol Analysis:** Detailed analysis of headers, status codes (302 vs 200), and response length for data extraction.
* **Information Gathering & Enumeration:** Reconnaissance techniques to map attack surfaces and identify valid users on remote systems.

---

### References
* [PortSwigger: Authentication Vulnerabilities](https://portswigger.net/web-security/authentication)
* [OWASP: Testing for Account Enumeration](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Accounts)
