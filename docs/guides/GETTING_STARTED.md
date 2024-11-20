# Getting Started with GCP Team

This guide helps new team members get set up with the necessary tools, access, and knowledge to work with a-d-a-p-t.ai's GCP infrastructure.

## Prerequisites

1. Google Cloud SDK
```bash
# Install Google Cloud SDK
curl https://sdk.cloud.google.com | bash
exec -l $SHELL
gcloud init
```

2. Development Tools
```bash
# Install required tools
sudo apt-get update && sudo apt-get install -y \
    terraform \
    kubectl \
    docker.io \
    python3-pip

# Install Python packages
pip3 install --user \
    google-cloud-storage \
    google-cloud-monitoring \
    google-cloud-logging
```

## Access Setup

1. GCP Console Access
   - Request access through IT Support
   - Set up 2FA with Google Authenticator
   - Complete security training

2. Service Account Setup
```bash
# Create service account key file
gcloud iam service-accounts keys create key.json \
    --iam-account=your-sa@project.iam.gserviceaccount.com

# Set environment variable
export GOOGLE_APPLICATION_CREDENTIALS="$HOME/key.json"
```

3. Configure gcloud CLI
```bash
# Set project
gcloud config set project adapt-ai-prod

# Set compute zone
gcloud config set compute/zone us-central1-a

# Set compute region
gcloud config set compute/region us-central1
```

## Project Structure

```
adapt-ai-prod/
├── networking/
│   ├── vpc/
│   ├── subnets/
│   └── firewall/
├── compute/
│   ├── gke/
│   ├── instances/
│   └── functions/
├── storage/
│   ├── buckets/
│   └── databases/
└── security/
    ├── iam/
    └── kms/
```

## Common Tasks

### 1. Deploying Infrastructure

```bash
# Clone infrastructure repository
git clone https://github.com/TeamADAPT/gcp-infrastructure.git

# Initialize Terraform
cd gcp-infrastructure
terraform init

# Plan changes
terraform plan

# Apply changes
terraform apply
```

### 2. Accessing GKE Clusters

```bash
# Get cluster credentials
gcloud container clusters get-credentials cluster-name

# View nodes
kubectl get nodes

# View pods
kubectl get pods --all-namespaces
```

### 3. Managing Cloud Storage

```bash
# Create bucket
gsutil mb gs://bucket-name

# Copy files
gsutil cp local-file gs://bucket-name/

# Set bucket policy
gsutil iam ch user:user@adapt.ai:objectViewer gs://bucket-name
```

### 4. Monitoring and Logging

```bash
# View logs
gcloud logging read "resource.type=gce_instance"

# Create metric
gcloud beta monitoring metrics create custom.googleapis.com/metric-name

# Set up alert
gcloud alpha monitoring policies create --policy-from-file=policy.yaml
```

## Best Practices

1. Security
   - Use least privilege access
   - Rotate credentials regularly
   - Enable audit logging
   - Use Cloud KMS for secrets

2. Cost Management
   - Use preemptible instances when possible
   - Set up budget alerts
   - Clean up unused resources
   - Use committed use discounts

3. Resource Management
   - Use labels for resources
   - Follow naming conventions
   - Document all changes
   - Use Infrastructure as Code

4. Monitoring
   - Set up uptime checks
   - Configure alerts
   - Use custom dashboards
   - Monitor costs

## Development Workflow

1. Local Development
   - Use Cloud Code in VS Code
   - Test with Cloud Build locally
   - Use emulators when possible

2. Testing
   - Use separate test projects
   - Run integration tests
   - Validate IAM policies
   - Test disaster recovery

3. Deployment
   - Use CI/CD pipelines
   - Follow change management
   - Update documentation
   - Monitor deployments

## Troubleshooting

1. Access Issues
   - Check IAM permissions
   - Verify service account
   - Check OAuth scopes
   - Review audit logs

2. Deployment Issues
   - Check Cloud Build logs
   - Verify quotas
   - Review error messages
   - Check dependencies

3. Performance Issues
   - Review monitoring metrics
   - Check resource utilization
   - Analyze trace data
   - Review logs

## Support and Resources

1. Internal Resources
   - Team documentation in `/docs`
   - Slack: #gcp-team
   - Wiki: internal.adapt.ai/gcp

2. External Resources
   - [GCP Documentation](https://cloud.google.com/docs)
   - [Terraform GCP Provider](https://registry.terraform.io/providers/hashicorp/google/latest/docs)
   - [GKE Documentation](https://cloud.google.com/kubernetes-engine/docs)

3. Support Channels
   - Emergency: #gcp-oncall
   - General: #gcp-team
   - Email: gcp-support@adapt.ai

## Next Steps

1. Complete access setup
2. Review architecture documentation
3. Set up local development environment
4. Join team communication channels
5. Schedule onboarding sessions with team lead
