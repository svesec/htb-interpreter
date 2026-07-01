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

# Vulnerability Discovery

## Objective

The objective of this phase was to identify publicly known vulnerabilities affecting the exposed technologies and determine whether they were applicable to the target environment.

## Version Identification

Following the initial reconnaissance, additional enumeration was performed to determine the exact version of the exposed Mirth Connect instance.

The version was successfully identified as **Mirth Connect 4.4.0** through multiple independent sources.

Version validation was performed using:

- Java Web Start deployment descriptor (`webstart.jnlp`)
- Public OpenAPI endpoint (`/api/server/version`)

Validating the version through multiple independent methods increased confidence in the accuracy of the identified software release before vulnerability research began.

### Mirth Connect Version

![03 - Mirth Connect Version](evidence/screenshots/03_mirth_connect_version.png)

**Evidence Files**

- `evidence/files/02_webstart_jnlp.txt`
- `evidence/files/03_api_server_version.txt`

---

## Public Vulnerability Research

Once the application version had been confirmed, publicly available security advisories were reviewed to determine whether the deployed version was affected by any known vulnerabilities.

Research identified **CVE-2023-43208**, a critical unauthenticated Remote Code Execution vulnerability affecting Mirth Connect versions prior to **4.4.1**.

The advisory confirmed that the identified target version (**4.4.0**) fell within the vulnerable range.

Additional technical analysis published by Horizon3 described the vulnerability as an unsafe XML deserialization issue caused by insecure processing of attacker-controlled XML data, ultimately allowing arbitrary command execution without authentication.

The vulnerability received a **CVSS v3.1 score of 9.8 (Critical)**.

### CVE Research

![04 - CVE-2023-43208](evidence/screenshots/04_cve_2023_43208.png)

### Horizon3 Technical Analysis

![05 - Horizon3 Technical Writeup](evidence/screenshots/05_horizon3_writeup.png)

The public research phase confirmed that:

- The target version was vulnerable.
- Exploitation did not require authentication.
- Public technical research describing the attack path was available.
- The identified vulnerability represented a realistic path toward full system compromise.

At this stage the assessment transitioned from vulnerability research into controlled exploitation.
