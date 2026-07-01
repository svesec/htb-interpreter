# Hack The Box – Interpreter

Penetration testing assessment of the Hack The Box **Interpreter** machine using a structured six-phase security assessment methodology.

---

# Overview

This report documents a security assessment of the Hack The Box machine **Interpreter**. The objective of the assessment was to identify and validate security weaknesses across the exposed services and application components, determine their potential impact, and demonstrate the resulting attack path.

The assessment identified multiple weaknesses throughout the target environment, beginning with reconnaissance of an exposed Mirth Connect deployment. Subsequent analysis led to application-level compromise, extraction of credential material, GPU-assisted password recovery, authenticated SSH access, local service enumeration, source code analysis, identification of an unsafe Python `eval()` implementation, and successful privilege escalation resulting in root-level code execution.

The complete attack chain progressed from external reconnaissance to full system compromise while following a structured six-phase penetration testing methodology.

---

# Scope

| Item | Details |
|------|---------|
| **Target** | Interpreter |
| **Platform** | Hack The Box |
| **Operating System** | Debian Linux |
| **Assessment Type** | Black Box Security Assessment |
| **Primary Technologies** | Jetty, Mirth Connect, Java, Python, PostgreSQL |
| **Objective** | Identify and validate security weaknesses leading to full system compromise |
| **Out of Scope** | Denial of Service (DoS), brute-force attacks, attacks against external infrastructure |

---

# Methodology

The assessment followed a structured six-phase security assessment methodology designed to provide a repeatable and systematic approach for evaluating the security of web applications and supporting infrastructure.

The testing process consisted of the following phases:

1. Preparation and Scope Definition
2. Reconnaissance and Information Gathering
3. Vulnerability Discovery
4. Controlled Exploitation
5. Risk Assessment
6. Security Recommendations

Each finding was validated through controlled testing and supported by technical evidence collected during the assessment.

---

# Reconnaissance

Initial reconnaissance identified three publicly accessible services exposed by the target host.

| Port | Service | Version |
|------|---------|---------|
| 22 | SSH | OpenSSH 9.2p1 |
| 80 | HTTP | Jetty |
| 443 | HTTPS | Jetty |

The HTTP and HTTPS services hosted the **Mirth Connect Administrator** interface, immediately identifying the primary application under assessment.

The presence of both HTTP and HTTPS interfaces suggested a Java-based enterprise application. Initial enumeration therefore focused on technology identification, deployment artifacts, and available application resources.

### Nmap Scan

![01 - Initial Nmap Scan](evidence/screenshots/01_nmap_initial_scan.png)

**Evidence File**

`evidence/files/01_nmap_initial_scan.txt`

---

# Attack Surface Analysis

Further enumeration confirmed that the exposed application was **Mirth Connect**, exposing both the administrative interface and deployment artifacts commonly associated with the platform.

Inspection of the available resources identified the Java Web Start launcher (`webstart.jnlp`), providing valuable information regarding the application deployment model.

These findings significantly reduced the attack surface by positively identifying the underlying technology stack and enabling targeted security research during the following assessment phases.

### Mirth Connect Administrator

![02 - Mirth Connect Login](evidence/screenshots/02_mirth_connect_login_page.png)

**Evidence File**

`evidence/files/02_webstart_jnlp.txt`
---

---

# Vulnerability Discovery

Public vulnerability research identified **CVE-2023-43208**, a critical unauthenticated Remote Code Execution vulnerability affecting Mirth Connect versions prior to **4.4.1**.

The target was confirmed as **Mirth Connect 4.4.0**, which falls within the vulnerable range. The vulnerability research phase established that exploitation did not require authentication and represented a realistic path toward remote command execution.

### CVE Research

![03 - CVE-2023-43208 NVD Reference](evidence/screenshots/03_cve_2023_43208_nvd.png)
---

# Controlled Exploitation

After confirming that the target was running a vulnerable version of Mirth Connect, controlled exploitation was performed to validate the impact of **CVE-2023-43208**.

The exploitation process was performed incrementally. Instead of immediately attempting to obtain an interactive shell, a simple ICMP-based validation was used first. This confirmed that arbitrary command execution was possible while keeping the initial test low-impact and easy to verify.

### Remote Code Execution Validation

![04 - RCE Ping Validation](evidence/screenshots/04_rce_ping_validation.png)

**Evidence File**

`evidence/files/03_rce_ping_validation.txt`

---

# Initial Access

After validating remote command execution, the payload was extended to obtain an interactive shell on the target system.

The shell confirmed that the vulnerability could be used to gain execution on the underlying operating system through the exposed Mirth Connect service.

### Initial Shell

![05 - Initial Shell](evidence/screenshots/05_initial_shell.png)

The initial shell provided the foothold required for local enumeration, credential discovery, and further privilege escalation analysis.
---

# Local Enumeration

Following initial access, local enumeration was performed to identify the execution context, available services, sensitive files, and potential privilege escalation vectors.

The compromised shell was running with limited privileges under the Mirth Connect service account. Initial enumeration focused on understanding the environment before attempting privilege escalation.

### User Context

![06 - User Context](evidence/screenshots/06_user_context.png)

The enumeration confirmed the current user context and provided the starting point for further privilege escalation analysis.

---

## Mirth Installation Analysis

Enumeration of the application directories revealed the Mirth Connect installation structure and several configuration files containing sensitive information.

### Mirth Installation Directory

![07 - Mirth Installation Directory](evidence/screenshots/07_mirth_installation_directory.png)

Reviewing the application configuration files exposed credentials used by the backend PostgreSQL database.

### Database Credentials

![08 - Database Credentials](evidence/screenshots/08_database_credentials.png)

The recovered credentials enabled authenticated access to the database, allowing further enumeration of stored application data.
## Database Enumeration

Using the recovered configuration credentials, authenticated access to the PostgreSQL database was established.

Database enumeration identified application tables containing user account information and password hashes.

### Database Access

![09 - Database Access](evidence/screenshots/09_database_access.png)

Further inspection revealed the stored credential records for privileged application users.

### Database User Credentials

![10 - Database User Credentials](evidence/screenshots/10_database_user_credentials.png)

The extracted password hashes were preserved for offline password recovery.
