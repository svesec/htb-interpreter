# htb-interpreter
Penetration testing assessment of the Hack The Box "Interpreter" machine using a structured six-phase security assessment methodology.

## Executive Summary

This repository documents penetration testing assessment of the Hack The Box **Interpreter** machine following a structured six-phase security assessment methodology.

The assessment demonstrates a complete attack path beginning with external reconnaissance of a Mirth Connect deployment, followed by application analysis, credential extraction, GPU-assisted password recovery, authenticated SSH access, local service enumeration, source code review, identification of an unsafe Python `eval()` implementation, and successful privilege escalation resulting in root-level code execution.

The objective of this assessment is not only to demonstrate exploitation, but also to document the methodology, technical reasoning, evidence, risk evaluation, and remediation recommendations expected in a professional penetration testing engagement.
---

## Scope

### Target

- **Platform:** Hack The Box
- **Machine:** Interpreter
- **Operating System:** Debian Linux
- **Primary Technologies:** Jetty, Mirth Connect, Java, Python, PostgreSQL

### Assessment Objective

The objective of this assessment was to identify, validate, and document security weaknesses across the exposed attack surface while following a structured six-phase penetration testing methodology.

Testing included reconnaissance, vulnerability discovery, controlled exploitation, privilege escalation, risk assessment, and remediation recommendations.
---

## Methodology

This assessment follows a structured six-phase penetration testing methodology developed to provide a repeatable and systematic approach for evaluating the security of web applications and supporting infrastructure.

1. Preparation and Scope Definition
2. Reconnaissance and Information Gathering
3. Vulnerability Discovery
4. Controlled Exploitation
5. Risk Assessment
6. Security Recommendations

Each phase builds upon the previous one to ensure that every finding is properly validated, documented, and supported with technical evidence.
---

# Phase 1 – Preparation and Scope Definition

## Objective

The objective of this assessment was to evaluate the security posture of the target system by following a structured six-phase penetration testing methodology.

The assessment focused on identifying vulnerabilities, validating their exploitability under controlled conditions, assessing the associated security risks, and providing practical remediation recommendations.

## Scope

The assessment included:

- External reconnaissance
- Web application enumeration
- Technology identification
- Source code analysis
- Credential analysis
- Controlled exploitation
- Privilege escalation
- Risk assessment
- Security recommendations

## Rules of Engagement

The assessment was conducted against the designated Hack The Box target within an isolated laboratory environment.

Only actions required to validate identified vulnerabilities were performed. Exploitation was intentionally limited to the minimum level necessary to demonstrate security impact while avoiding unnecessary post-exploitation activity.
---

# Phase 2 – Reconnaissance and Information Gathering

## Objective

The objective of this phase was to identify exposed services, technologies, application components, and potential entry points that could contribute to the overall attack surface.

## External Reconnaissance

Initial network reconnaissance was performed to identify publicly accessible services exposed by the target host.

The scan revealed three primary services:

- TCP/22 – OpenSSH
- TCP/80 – Jetty HTTP
- TCP/443 – Jetty HTTPS

The HTTP and HTTPS services hosted the Mirth Connect Administrator interface, immediately identifying the primary application under assessment.

**Evidence**

- `evidence/screenshots/01_nmap_initial_scan.png`
- `evidence/files/01_nmap_initial_scan.txt`
  
### Technology Identification

Further enumeration confirmed that the target application was based on **Mirth Connect**, exposing both the administrative interface and deployment artifacts commonly associated with the platform.

Additional inspection of the available resources identified the Java Web Start launcher (`webstart.jnlp`), providing valuable information regarding the application deployment model.

The availability of these resources significantly improved technology fingerprinting and enabled targeted research during the following assessment phases.

**Evidence**

- `evidence/screenshots/02_mirth_connect_login_page.png`
- `evidence/files/02_webstart_jnlp.txt`
