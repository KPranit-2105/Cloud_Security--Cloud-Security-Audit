# Simulated Audit Evidence Pack (Prowler Scan Verification)
**Target:** Apex Cloud Financial Systems (`Account 123456789012`)  
**Audit Context:** Evidence gathered during Prowler v3.12.0 security assessment  
**Classification:** **SIMULATED EVIDENCE FOR PORTFOLIO DEMONSTRATION**  

---

## Evidence Item EVD-001: IAM Root Account MFA Audit

### CLI Verification Output
```bash
$ aws iam get-account-summary --query "SummaryMap.AccountMFAEnabled"
0
```
> **Audit Finding:** Returns `0` (Disabled). Fails CIS AWS Benchmark 1.1.

---

## Evidence Item EVD-002: S3 Public Access Block Configuration

### CLI Verification Output
```bash
$ aws s3api get-public-access-block --bucket apexpay-customer-documents-prod
An error occurred (NoSuchPublicAccessBlockConfiguration) when calling the GetPublicAccessBlock operation: The public access block configuration was not found.
```
> **Audit Finding:** Public access block is missing. Fails CIS AWS Benchmark 2.1.51 and PCI-DSS 1.3.1.

---

## Evidence Item EVD-003: Database Security Group Ingress Rules

### Security Group Ingress Query
```json
{
    "SecurityGroups": [
        {
            "GroupId": "sg-0a1b2c3d4e5f67890",
            "GroupName": "apexpay-app-db-sg",
            "IpPermissions": [
                {
                    "FromPort": 22,
                    "ToPort": 22,
                    "IpProtocol": "tcp",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0",
                            "Description": "Legacy Admin SSH Access"
                        }
                    ]
                }
            ]
        }
    ]
}
```
> **Audit Finding:** Ingress rule permits SSH from `0.0.0.0/0`. Fails CIS AWS Benchmark 5.2.

---

## Evidence Item EVD-004: Post-Remediation Verification Proof

### S3 Public Access Block Enabled (Simulated Verification Output)
```json
{
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    }
}
```
> **Remediation Status:** Verified remediated via AWS CLI inspection.
