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

