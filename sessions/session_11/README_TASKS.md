# Your turn now: Security!

## 1) Perform a Security Assessment

The following general steps will guide you through a security assessment. Consider using them as steps in a report. The report will become a section in your final project report.

### A. Risk Identification

1. Identify assets (e.g. web application)
1. Identify threat sources (e.g. SQL injection)
1. Construct risk scenarios (e.g. Attacker performs SQL injection on web application to download sensitive user data)

### B. Risk Analysis

1. Determine likelihood
1. Determine impact
1. Use a Risk Matrix to prioritize risk of scenarios
1. Discuss what are you going to do about each of the scenarios


## 2) Improve the Security of Your System

Based on your security assessment, take steps to improve the security of your system. Briefly report on the steps you took. At a minimum, you should address the following:

### A. Firewall

Set up `ufw` on your server so that only the ports you actually need are open.

1. Enable `ufw` and deny all incoming traffic by default
2. Allow only the ports your application needs (typically SSH, HTTP, HTTPS)
3. Verify with `sudo ufw status` that no unnecessary ports are exposed
4. Be aware of the Docker/UFW issue discussed in the lecture — if you use Docker's port mapping (`-p`), Docker bypasses `ufw` and opens ports directly via `iptables`. Test that your firewall actually blocks what you think it blocks.

### B. TLS

Set up TLS so your application is served over HTTPS.

1. Get a domain name (e.g. via the [GitHub Student Developer Pack](https://education.github.com/pack))
2. Install Nginx as a reverse proxy in front of your application
3. Use Certbot with Let's Encrypt to obtain a TLS certificate
4. Verify that your site is accessible over HTTPS and that HTTP redirects to HTTPS

See the [TLS Tutorial](TLSTutorial.md) for a step-by-step guide.

### C. Harden Your Containers

1. **Don't run as root**: make sure your application containers use a non-root user (see the `USER` directive in Dockerfile)
2. **Scan your images for vulnerabilities**: run `docker scout` or `snyk container test` on your images and address critical/high findings
3. **Keep base images up to date**: make sure you're not using outdated base images with known vulnerabilities (e.g. `python:3.9.2-buster` vs `python:3.12`)

### D. Enhance your CI Pipelines with multiple security related static analysis tools

Add at least one static analysis tool that scans for security vulnerabilities in your application code.
That is, add a tool like [Security Code Scan](https://security-code-scan.github.io/), [CodeQL](https://codeql.github.com/docs/codeql-overview/about-codeql/), or [Semgrep](https://semgrep.dev/) with enabled security rules to your CI pipeline.

At a later build stage, add at least one other Docker image vulnerability scanner like [Trivy](https://trivy.dev/docs/latest/getting-started/), [Docker Scout](https://docs.docker.com/scout/), or [Snyk](https://docs.snyk.io/) to your CI pipeline.

Integrate such tools, so that build, delivery, and deployment get aborted as early as possible in a meaningful way, i.e., shift-left security.


