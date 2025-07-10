# DevSecOps

DevSecOps integrates security practices and considerations throughout the entire Software Development Lifecycle (SDLC).

> The "shift left" philosophy is central to DevSecOps, meaning security is addressed early and continuously, rather than being a late-stage afterthought.

## Main Principles

1. **Shared Responsibility**
   - Security is everyone's job, not just a dedicated security team. Developers, Ops, QA, and security all collaborate.
2. **Automation**
   - Automate security tasks as much as possible to ensure consistency, speed, and reduce human error.
3. "**Shift Left**"
   - Integrate security earlier in the SDLC (design, coding) to catch vulnerabilities when they are cheaper and easier to fix.
4. **Learning + Improvement**
   - Regularly review security processes, incidents, and vulnerabilities to adapt and improve.
5. **Transparency + Feedback**
   - Open communication about security findings and quick feedback loops to development teams.
6. Security as Code
   - Define security policies and configurations in code, enabling version control and automated enforcement.

## Terminologies

Refer to this source: [The Enchiridion of Impetus Exemplar](https://shellsharks.com/threat-modeling)

| **Stage**   | **Category**                            | **Purpose**                                                          | **Tools**                                                                                            |
| ----------- | --------------------------------------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Plan**    | **Threat Modeling & Design Security**   | Identify threats early in the planning phase                         | OWASP Threat Dragon, IriusRisk, Microsoft Threat Modeling Tool                                       |
| **Plan**    | **Policy as Code**                      | Define and enforce rules on infra, CI/CD, APIs                       | OPA (Open Policy Agent), Rego                                                                        |
| **Plan**    | **Security Awareness & Training**       | Build a security-first engineering culture                           | OWASP Top 10, SANS Top 25, Security Champions Programs                                               |
| **Code**    | **SAST (Static App Security Testing)**  | Analyze source code for vulnerabilities without running it           | SonarQube, Checkmarx, Veracode, Fortify, Semgrep, Bandit, ESLint                                     |
| **Code**    | **Secrets Scanning**                    | Detect hardcoded credentials in source code                          | GitGuardian, Spectral, Trufflehog                                                                    |
| **Code**    | **IDE Plugins & Pre-Commit Hooks**      | Provide real-time/static feedback to developers                      | Snyk Plugin, SonarLint, Pre-Commit                                                                   |
| **Code**    | **Version Control & Code Review**       | Enable secure collaboration and peer review                          | Git, GitHub, GitLab, Bitbucket                                                                       |
| **Code**    | **SCA (Software Composition Analysis)** | Detect vulnerable third-party libraries                              | Snyk, Trivy, Dependabot, OWASP Dependency-Check, Black Duck                                          |
| **Build**   | **Container & Artifact Scanning**       | Scan container images and build artifacts for CVEs                   | Trivy, Clair, Anchore Engine, Aqua, Docker Scout                                                     |
| **Build**   | **SBOM (Software Bill of Materials)**   | Generate list of software components                                 | Syft, SPDX Tools                                                                                     |
| **Build**   | **CI/CD Orchestration**                 | Automate security in build and deploy pipelines                      | Jenkins, GitLab CI/CD, GitHub Actions, Azure DevOps, CircleCI, Travis CI                             |
| **Build**   | **IaC Security Scanning**               | Detect misconfigurations in infrastructure-as-code                   | Checkov, KICS, Terrascan, tfsec, Infracost                                                           |
| **Test**    | **DAST (Dynamic App Security Testing)** | Simulate external attacks on running apps                            | OWASP ZAP, Burp Suite, Acunetix, Netsparker                                                          |
| **Test**    | **IAST (Interactive App Security)**     | Analyze app behavior during testing                                  | Contrast Security, Veracode                                                                          |
| **Test**    | **Fuzz & API Testing**                  | Discover hidden bugs, malformed input issues, and insecure endpoints | AFL, Peach Fuzzer, OWASP ZAP (API), Postman, SoapUI                                                  |
| **Test**    | **Vulnerability Scanning & Management** | Identify, prioritize, and manage vulnerabilities                     | Tenable.io, Qualys, Nessus, OpenVAS, Kenna Security                                                  |
| **Release** | **Secrets Management (Runtime)**        | Secure runtime secrets injection                                     | HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, Kubernetes Secrets        |
| **Release** | **Cloud Posture Management (CSPM)**     | Monitor cloud security configurations                                | AWS Security Hub, Azure Security Center, GCP SCC, Prisma Cloud, Wiz                                  |
| **Release** | **IAM & PAM**                           | Enforce identity-based least-privilege access                        | AWS IAM, Azure AD, Google Cloud IAM, HashiCorp Boundary, CyberArk                                    |
| **Operate** | **Runtime Protection (RASP)**           | Block threats from inside the app during runtime                     | Contrast Security, Signal Sciences                                                                   |
| **Operate** | **Web App Firewalls (WAF)**             | Filter and monitor HTTP traffic                                      | AWS WAF, Cloudflare, Nginx ModSecurity, Akamai                                                       |
| **Operate** | **Network Security & Detection**        | Detect and prevent network-based threats                             | Snort, Suricata, Palo Alto, Fortinet                                                                 |
| **Operate** | **SIEM & Observability**                | Collect, analyze, and alert on logs, metrics, and traces             | Splunk, ELK Stack, Sumo Logic, Microsoft Sentinel, Prometheus, Grafana, OpenTelemetry, Loki, Graylog |
| **Operate** | **Container Runtime Security**          | Monitor container activity in production                             | Falco, Sysdig Secure                                                                                 |
