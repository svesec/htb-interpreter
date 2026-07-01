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
