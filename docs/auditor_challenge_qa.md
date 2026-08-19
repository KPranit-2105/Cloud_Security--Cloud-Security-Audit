# Auditor & Technical Challenge Q&A: Cloud Security Audit
**Role Simulated:** Senior GRC Auditor / Cloud Security Lead  
**Audit Target:** Apex Cloud Financial Systems (ApexPay)  

---

### Q1: Why did you classify unencrypted S3 bucket public access as a Critical finding instead of High?
**Answer:** In ApexPay's environment, `apexpay-customer-documents-prod` stores customer PII, driver's licenses, and identity verification data for KYC compliance. Disabling S3 Block Public Access exposes this bucket directly to unauthenticated internet traffic. Given that data breach fines under GDPR/CCPA and PCI-DSS non-compliance sanctions can exceed millions of dollars and lead to loss of payment processing privileges, the business impact is Severe (5) and likelihood is High (4), placing the Inherent Risk at 20 (Critical).

---

### Q2: Prowler flagged 500+ items in an initial scan. How did you filter signal from noise?
**Answer:** Automated scanners generate large volumes of findings, including low-risk defaults in non-production regions. We prioritized findings using a 3-step triage:
1. **Asset Criticality:** Is the finding on a production asset, data storage holding PCI/PII, or identity entry point?
2. **Exploitability:** Can the issue be exploited remotely without authentication (e.g. 0.0.0.0/0 SSH ingress, public S3 buckets)?
3. **Compensating Controls:** Are there upstream controls (e.g. WAF, SCPs, network VPC boundaries) that mitigate the threat?
This reduced 500+ raw flags to 4 Critical and 14 High action items.

---

### Q3: How do you verify that a remediated control actually operates effectively over time?
**Answer:** Point-in-time audit fixes often regress. We enforce continuous control monitoring by:
1. Enabling **AWS Config Rules** (e.g., `s3-bucket-public-read-prohibited`, `iam-user-mfa-enabled`) with automated remediation.
2. Integrating **Prowler** into nighttime CI/CD pipeline jobs to output weekly compliance trend reports.
3. Requiring quarterly evidence extraction logs verified against our GRC Evidence Repository.

---

### Q4: If management chooses not to fix a High-severity finding due to operational friction, how do you handle it?
**Answer:** Management has the authority to accept business risk, but GRC must ensure formal risk acceptance governance:
1. Require a formal **Risk Acceptance Exception Form** documenting the business rationale.
2. Mandate **Compensating Controls** (e.g. if legacy app cannot support MFA immediately, restrict access to trusted IP ranges via VPN).
3. Require sign-off from the CISO / Business Risk Owner with a maximum 90-day review expiration date.
