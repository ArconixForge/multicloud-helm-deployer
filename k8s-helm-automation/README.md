# Kubernetes Helm Chart Deployment Automation

Production-grade Ansible playbooks for automating the deployment of Helm charts to Kubernetes clusters across various cloud providers (GCP, AWS, Azure).

## Features

- **Cloud Provider Agnostic**: Works with GKE, EKS, AKS, and others
- **Environment Management**: Easily switch between development, staging, and production
- **Modular Design**: Role-based architecture for maintainability and extensibility
- **Comprehensive Validation**: Built-in deployment verification with basic and full modes
- **Security-First**: Designed with security best practices for credentials handling
- **Deployment History**: Automatic tracking of deployments and optional backups

## Directory Structure

```
.
├── ansible.cfg                 # Ansible configuration
├── inventories/                # Inventory definitions
│   └── inventory.ini           # Standard inventory file
├── playbooks/                  # Playbook definitions
│   └── deploy-helm-chart.yml   # Main deployment playbook
├── roles/                      # Role definitions
│   ├── helm/                   # Helm-related roles
│   │   ├── deploy/             # Chart deployment role
│   │   └── repo/               # Repository management role
│   ├── kubernetes/             # Kubernetes-related roles
│   │   ├── auth/               # Authentication role
│   │   └── namespace/          # Namespace management role
│   └── validation/             # Deployment validation role
├── vars/                       # Variable definitions
│   ├── cloud_providers/        # Cloud provider specific variables
│   │   ├── aws.yml             # AWS variables
│   │   ├── azure.yml           # Azure variables
│   │   └── gcp.yml             # GCP variables
│   ├── common.yml              # Common variables for all environments
│   ├── dev.yml                 # Development environment variables
│   ├── staging.yml             # Staging environment variables
│   └── prod.yml                # Production environment variables
└── README.md                   # This file
```

## Prerequisites

- Ansible 2.10+
- Kubernetes CLI (kubectl)
- Helm 3.x
- Cloud provider CLI tools (as needed):
  - GCP: `gcloud`
  - AWS: `aws`
  - Azure: `az`

## Usage

### Basic Deployment

```bash
# Deploy to development environment
ansible-playbook playbooks/deploy-helm-chart.yml -e "environment=dev"

# Deploy to production environment
ansible-playbook playbooks/deploy-helm-chart.yml -e "environment=prod"
```

### Advanced Usage

```bash
# Deploy with additional overrides
ansible-playbook playbooks/deploy-helm-chart.yml \
  -e "environment=dev" \
  -e "release_name=metabase-test" \
  -e "chart_version=1.0.0" \
  -e "set_values.replicaCount=2"
  
# Deploy with full validation
ansible-playbook playbooks/deploy-helm-chart.yml \
  -e "environment=prod" \
  -e "validation_level=full"
  
# Deploy with dry run first
ansible-playbook playbooks/deploy-helm-chart.yml \
  -e "environment=prod" \
  -e "dry_run=true"
  
# Deploy using specific cloud provider
ansible-playbook playbooks/deploy-helm-chart.yml \
  -e "environment=dev" \
  -e "cloud_provider=aws" \
  -e "cluster_name=my-eks-cluster"
```

### Using Tags

```bash
# Only run authentication tasks
ansible-playbook playbooks/deploy-helm-chart.yml -e "environment=dev" --tags auth

# Skip validation
ansible-playbook playbooks/deploy-helm-chart.yml -e "environment=dev" --skip-tags validation

# Run everything except Helm operations
ansible-playbook playbooks/deploy-helm-chart.yml -e "environment=dev" --skip-tags helm
```

## Configuration

### Environment Variables

The following environment variables are supported:

#### GCP Environment Variables
- `GCP_PROJECT`: GCP project ID
- `GCP_REGION`: GCP region (defaults to us-central1)
- `GCP_ZONE`: GCP zone (defaults to us-central1-a)
- `GCP_KEY_FILE`: Path to GCP service account key file
- `GCP_STORAGE_CLASS`: Storage class for GCP persistent volumes

#### AWS Environment Variables
- `AWS_ACCESS_KEY_ID`: AWS access key ID
- `AWS_SECRET_ACCESS_KEY`: AWS secret access key
- `AWS_SESSION_TOKEN`: AWS session token (if using temporary credentials)
- `AWS_REGION`: AWS region (defaults to us-east-1)
- `AWS_PROFILE`: AWS CLI profile name
- `AWS_STORAGE_CLASS`: Storage class for AWS persistent volumes

#### Azure Environment Variables
- `AZURE_APP_ID`: Azure service principal ID
- `AZURE_PASSWORD`: Azure service principal password
- `AZURE_TENANT`: Azure tenant ID
- `AZURE_RESOURCE_GROUP`: Azure resource group for AKS cluster

### Variable Files

The playbook uses variable files in the `vars/` directory:

- `common.yml`: Common settings for all environments
- `dev.yml`, `staging.yml`, `prod.yml`: Environment-specific settings
- `cloud_providers/gcp.yml`, `cloud_providers/aws.yml`, etc.: Cloud provider settings

## Customizing Deployments

### Adding Support for New Charts

1. Create chart-specific validation tasks:
   ```bash
   touch roles/validation/tasks/your-chart-name.yml
   ```

2. Add any chart-specific variables to your environment files.

### Customizing Validation

The validation role supports two levels:

- `basic`: Fast validation checking only for running pods and services
- `full`: Comprehensive validation with health checks, log analysis, etc.

Custom validation steps can be added in `roles/validation/tasks/` for specific charts.

## Best Practices

### Security

- Never store credentials directly in variable files
- Use environment variables or Ansible Vault for sensitive information
- Consider using service accounts with limited permissions
- Enable network policies in production environments

### Reliability

- Always use `atomic: true` in production to ensure automatic rollback
- Set appropriate timeouts for different environments
- Enable comprehensive validation for production deployments
- Use the backup feature before upgrading critical deployments

### Workflow Integration

This solution can be integrated with CI/CD platforms by:

1. Adding the repository as a submodule or dependency
2. Configuring environment-specific credentials securely
3. Creating pipeline scripts that execute the playbook with appropriate parameters

## Troubleshooting

### Common Issues

- **Authentication Failures**: Ensure cloud provider credentials are correctly set
- **Validation Timeouts**: Increase `validation_timeout` for complex applications
- **Helm Repository Issues**: Check network connectivity and repository URLs
- **Resource Limitations**: Verify that your cluster has sufficient resources

### Debugging

Enable verbose output:

```bash
ansible-playbook playbooks/deploy-helm-chart.yml -e "environment=dev" -vvv
```

Check logs for detailed information:

```bash
cat ansible.log
```

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

Please adhere to:
- Keep the role-based structure
- Add documentation for new features
- Include validation for new chart types
- Follow security best practices

## License

This project is licensed under the Apache License 2.0.