 # Summary Report: Secure Deployment & Threat Modeling of a Python Web Application

## Overview

The objective of this project was to securely containerize and deploy a simple Python Flask web application using Docker and Docker Compose. The purpose of this project was to find and mitigate vulnerabilities in the code and deployment structure. I used a threat modeling framework, STRIDE, and the MITRE ATT&CK framework to analyze the attack surface. I also used mitigations aligned with NIST 800-53, CIS Docker Benchmark, and OWASP Top 10.

---

## Steps Taken to Secure the Application

1. **Containerization with Docker**
   - Used `python:3.13-alpine` to minimize base image size and reduce the attack surface.
   - Created a non-root user (`appuser`) to follow the principle of least privilege.
   - Configured `WORKDIR`, copied code safely, and avoided copying secrets into the image.
   - Implemented a `HEALTHCHECK` for runtime visibility.

2. **Secure Docker Compose Setup**
   - Segregated services into separate frontend and backend Docker networks.
   - Used environment variables for credentials via `.env` file (instead of hardcoding).
   - Defined dependency order using `depends_on`.

3. **Code-Level Hardening**
   - Removed insecure `eval()` usage.
   - Implemented input validation on endpoints like `/ping`.
   - Planned for secure token-based authentication for future endpoint protection.

4. **Environment & Runtime Security**
   - Eliminated running containers as root.
   - Avoided mounting host volumes unnecessarily.
   - Ensured that development-mode behavior (e.g., Flask debug server) is not used in production.

---

## = Vulnerabilities Found & Remediated

| Category                | Vulnerability                                   | Remediation                                                                 |
|-------------------------|--------------------------------------------------|------------------------------------------------------------------------------|
| Code Injection          | `/ping` endpoint allowed unsafe user input      | Added input validation and disabled `eval()` usage                          |
| Credential Exposure     | Hardcoded credentials in the code               | Migrated to environment variables stored in `.env` file                     |
| Privilege Escalation    | Container ran as root                           | Introduced `appuser` with no root privileges                               |
| Lack of Logging         | No access or error logs                         | Implemented logging best practices for traceability                         |
| No Rate Limiting        | DoS risk via open endpoints                     | Planned use of Flask-Limiter or reverse proxy-level rate limiting           |
| Network Exposure        | All services shared the same network            | Split into isolated Docker Compose networks (`frontend` and `backend`)      |
| Insecure Deployment     | Used Flask’s built-in dev server                | Noted recommendation to use Gunicorn for production                         |

---

## How the New Architecture Improves Security

- **Principle of Least Privilege**: Containers no longer run as root, limiting atack surface from container compromise.
- **Network Segmentation**: Frontend and backend services are isolated by Docker network boundaries.
- **Secure Secrets Handling**: Environment variables are used in place of hardcoded credentials.
- **Smaller Attack Surface**: Alpine-based image and removal of dangerous code paths reduce entry points.
- **Built-In Health Monitoring**: Docker `HEALTHCHECK` provides observability for automated restarts and monitoring.
- **Compliance Alignment**: Controls map directly to NIST, CIS, and OWASP recommendations, enabling a clear path to compliance.

---

## Lessons Learned & Reflections

- **Security Must be First**: Secure-by-design decisions during containerization and deployment prevent vulnerabilities that would be expensive to fix later.
- **Containers need constant Hardening**: A working deployment is not necessarily a secure one. Hardening steps like user permissions, input validation, and network separation are necessary during all aspects of creation.
- **Threat Modeling Provides Focus**: STRIDE and MITRE ATT&CK helped pinpoint realistic attacker behaviors and prioritize defenses, this enabled being able to move beyond a security checklist.
- **Documentation increases visibility**: Mapping controls to frameworks like NIST and OWASP can align with enterprise standards when designed correctly.

---

**Conclusion**:  
This project reinforced that securing an application is a multi-layered process. Throughout each step of this processes there were many aspects of security that needed to be focused on. By combining containerization best practices, code-level hardening, and systematic threat modeling, I was able to transform an insecure Flask app into a deployment-ready, security-conscious system. Although most of these security solutions were provided The experience deepened my understanding of DevSecOps and gave me practical understanding of applications I do not regulalry used as a security practitioner. 
