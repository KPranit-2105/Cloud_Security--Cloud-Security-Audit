# Remediation Guide: Fixing High & Critical Audit Findings

This guide details step-by-step AWS CLI and IaC commands to remediate the critical findings identified in the ApexPay Prowler Audit.

---

## 1. Enable Account-Level S3 Block Public Access (FIND-002)

### AWS CLI Command
```bash
aws s3control put-public-access-block \
    --account-id 123456789012 \
    --public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```

### Terraform HCL Fix
```hcl
resource "aws_s3_account_public_access_block" "account_wide" {
  account_id              = "123456789012"
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

---

## 2. Restrict Inbound SSH Ingress Rules (FIND-004)

### Revoke 0.0.0.0/0 Ingress on Security Group
```bash
aws ec2 revoke-security-group-ingress \
    --group-id sg-0a1b2c3d4e5f67890 \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0
```

---

## 3. Enable Multi-Region CloudTrail Logging (FIND-005)

### AWS CLI Command
```bash
aws cloudtrail update-trail \
    --name apexpay-global-audit-trail \
    --is-multi-region-trail \
    --enable-log-file-validation \
    --kms-key-id arn:aws:kms:us-east-1:123456789012:key/logging-key-id
```
