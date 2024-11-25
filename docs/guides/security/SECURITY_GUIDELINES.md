# GCP Security Guidelines

This document outlines the security policies, procedures, and best practices for a-d-a-p-t.ai's GCP infrastructure.

## Core Security Principles

1. Least Privilege Access
   - Grant minimum required permissions
   - Regular access reviews
   - Time-bound access when possible
   - Use service accounts appropriately

2. Defense in Depth
   - Multiple security layers
   - Comprehensive monitoring
   - Regular security testing
   - Incident response readiness

3. Zero Trust Architecture
   - Identity-based access
   - Network segmentation
   - Encryption everywhere
   - Continuous verification

## Identity and Access Management (IAM)

### Role Assignment

1. User Roles
```yaml
# Example role structure
roles:
  viewer:
    - roles/viewer
    - roles/monitoring.viewer
  developer:
    - roles/editor
    - roles/container.developer
  admin:
    - roles/owner
    - roles/security.admin
```

2. Service Account Roles
```yaml
# Service account naming convention
{service}-{environment}-sa@{project}.iam.gserviceaccount.com

# Required permissions only
roles:
  app-engine-sa:
    - roles/appengine.appViewer
    - roles/cloudsql.client
```

### Access Patterns

1. Human Access
   - Require 2FA
   - Use corporate identity
   - Regular access review
   - Break-glass procedures

2. Service Access
   - Use service accounts
   - Rotate keys regularly
   - Monitor usage
   - Audit access

## Network Security

### VPC Configuration

1. Network Segmentation
```hcl
# Terraform example
resource "google_compute_network" "vpc" {
  name                    = "prod-vpc"
  auto_create_subnetworks = false
  routing_mode           = "GLOBAL"
}

resource "google_compute_subnetwork" "subnet" {
  name          = "private-subnet"
  ip_cidr_range = "10.0.0.0/24"
  network       = google_compute_network.vpc.id
  private_ip_google_access = true
}
```

2. Firewall Rules
```hcl
resource "google_compute_firewall" "allow_internal" {
  name    = "allow-internal"
  network = google_compute_network.vpc.id

  allow {
    protocol = "tcp"
    ports    = ["0-65535"]
  }

  source_ranges = ["10.0.0.0/8"]
}
```

### Cloud Armor

1. WAF Rules
```yaml
security_policies:
  default:
    rules:
      - priority: 1000
        action: "deny(403)"
        match:
          expr:
            - "evaluatePreconfiguredExpr('xss-stable')"
      - priority: 2000
        action: "deny(403)"
        match:
          expr:
            - "evaluatePreconfiguredExpr('sqli-stable')"
```

2. DDoS Protection
   - Enable Cloud Armor
   - Set rate limiting
   - Configure adaptive protection
   - Monitor attacks

## Data Security

### Encryption

1. At Rest
   - Use Cloud KMS
   - Customer-managed keys
   - Regular key rotation
   - Access auditing

2. In Transit
   - TLS 1.3 minimum
   - Managed certificates
   - Certificate automation
   - Regular validation

### Data Classification

1. Categories
   - Public
   - Internal
   - Confidential
   - Restricted

2. Handling Requirements
```yaml
data_handling:
  public:
    encryption: optional
    access: any
  internal:
    encryption: required
    access: authenticated
  confidential:
    encryption: required
    access: authorized
    audit: required
  restricted:
    encryption: required
    access: strictly controlled
    audit: required
    backup: required
```

## Monitoring and Auditing

### Security Monitoring

1. Cloud Audit Logs
```yaml
audit_configs:
  - service: allServices
    audit_log_configs:
      - log_type: ADMIN_READ
      - log_type: DATA_READ
      - log_type: DATA_WRITE
```

2. Alert Policies
```yaml
alert_policies:
  - name: "iam-changes"
    conditions:
      - filter: "resource.type=project AND protoPayload.methodName=SetIamPolicy"
  - name: "vpc-changes"
    conditions:
      - filter: "resource.type=gce_network AND operation.type=insert"
```

### Security Scanning

1. Container Scanning
   - Vulnerability scanning
   - Base image updates
   - Policy compliance
   - SBOM generation

2. Code Scanning
   - Static analysis
   - Dependency scanning
   - Secret detection
   - License compliance

## Incident Response

### Response Procedures

1. Detection
   - Monitor alerts
   - Analyze logs
   - Verify incidents
   - Initial assessment

2. Containment
   - Isolate affected resources
   - Block attack vectors
   - Preserve evidence
   - Document actions

3. Remediation
   - Fix vulnerabilities
   - Update configurations
   - Verify fixes
   - Document changes

4. Recovery
   - Restore services
   - Verify functionality
   - Monitor closely
   - Update documentation

## Compliance Requirements

### Standards

1. Industry Standards
   - SOC 2
   - ISO 27001
   - HIPAA
   - GDPR

2. Implementation
   - Regular audits
   - Policy updates
   - Training
   - Documentation

## Security Tooling

### Required Tools

1. Infrastructure Security
   - Security Command Center
   - Cloud Asset Inventory
   - Binary Authorization
   - Container Analysis

2. Application Security
   - Cloud Armor
   - Identity-Aware Proxy
   - VPC Service Controls
   - Secret Manager

## Best Practices

1. Development
   - Secure coding guidelines
   - Code review requirements
   - Security testing
   - Dependency management

2. Operations
   - Change management
   - Access reviews
   - Incident response
   - Disaster recovery

3. Monitoring
   - Log analysis
   - Alert management
   - Performance monitoring
   - Security scanning

## Support

For security-related assistance:
- Emergency: #security-oncall
- General: #gcp-security
- Email: security@adapt.ai
- Documentation: /docs/security
