# Complete Directory Structure

Here's a comprehensive overview of all files in the production-grade Ansible automation for Helm chart deployments:

```
k8s-helm-automation/
│
├── ansible.cfg                        # Ansible configuration
├── Makefile                           # Automation commands
├── README.md                          # Project documentation
├── .gitignore                         # Git ignore patterns
│
├── inventories/                       # Inventory definitions
│   └── inventory.ini                  # Standard inventory file
│
├── playbooks/                         # Playbook definitions
│   └── deploy-helm-chart.yml          # Main deployment playbook
│
├── roles/                             # Role definitions
│   ├── helm/                          # Helm-related roles
│   │   ├── deploy/                    # Chart deployment role
│   │   │   └── tasks/                 # Deployment tasks
│   │   │       └── main.yml           # Main deployment tasks
│   │   │
│   │   └── repo/                      # Repository management role
│   │       └── tasks/                 # Repo tasks
│   │           └── main.yml           # Main repo tasks
│   │
│   ├── kubernetes/                    # Kubernetes-related roles
│   │   ├── auth/                      # Authentication role
│   │   │   └── tasks/                 # Auth tasks
│   │   │       └── main.yml           # Main auth tasks
│   │   │
│   │   └── namespace/                 # Namespace management role
│   │       └── tasks/                 # Namespace tasks
│   │           └── main.yml           # Main namespace tasks
│   │
│   └── validation/                    # Deployment validation role
│       ├── tasks/                     # Validation tasks
│       │   ├── main.yml               # Main validation tasks
│       │   └── metabase-k8s.yml       # Metabase-specific validation
│       └── defaults/                  # Default variables
│           └── main.yml               # Default validation variables
│
├── vars/                              # Variable definitions
│   ├── cloud_providers/               # Cloud provider specific variables
│   │   ├── aws.yml                    # AWS variables
│   │   ├── azure.yml                  # Azure variables
│   │   └── gcp.yml                    # GCP variables
│   │
│   ├── common.yml                     # Common variables for all environments
│   ├── dev.yml                        # Development environment variables
│   ├── staging.yml                    # Staging environment variables
│   └── prod.yml                       # Production environment variables
│
└── backups/                           # Backup directory for releases (created on first run)
    └── [release-name]/                # Subdirectories for each release
        └── [timestamp].yaml           # Backup files with timestamps

```

## Key Files Description

### Configuration Files
- `ansible.cfg`: Core Ansible configuration with optimized settings
- `Makefile`: Simplifies common operations with make commands
- `.gitignore`: Prevents sensitive data from being committed

### Playbooks
- `playbooks/deploy-helm-chart.yml`: Main playbook orchestrating the entire deployment process

### Core Roles

#### Kubernetes Authentication (`roles/kubernetes/auth/`)
Handles cluster authentication for different cloud providers:
- GCP authentication via gcloud CLI
- AWS authentication via aws CLI
- Azure authentication via az CLI
- Local cluster authentication

#### Kubernetes Namespace Management (`roles/kubernetes/namespace/`)
Manages namespaces with advanced features:
- Namespace creation and configuration
- Resource quota management
- Network policy configuration
- Namespace labels and annotations

#### Helm Repository Management (`roles/helm/repo/`)
Handles Helm chart repositories:
- Adding and updating repositories
- Chart version validation
- Repository caching

#### Helm Deployment (`roles/helm/deploy/`)
Manages the actual chart deployment:
- Helm upgrade/install operations
- Values configuration
- Deployment history
- Rollback preparation
- Dry run capabilities

#### Validation (`roles/validation/`)
Comprehensive post-deployment validation:
- Basic validation of pod status
- Extended validation of logs and endpoints
- Chart-specific validation (Metabase-specific validation included)
- Customizable validation levels

### Variable Files

#### Cloud Provider Variables
- `vars/cloud_providers/gcp.yml`: GCP-specific settings
- `vars/cloud_providers/aws.yml`: AWS-specific settings
- `vars/cloud_providers/azure.yml`: Azure-specific settings

#### Environment Variables
- `vars/common.yml`: Shared settings across environments
- `vars/dev.yml`: Development-specific settings
- `vars/staging.yml`: Staging-specific settings
- `vars/prod.yml`: Production-specific settings

## Automatic Directories
These directories are created automatically as needed:

- `backups/`: Stores pre-upgrade backup files
- `history/`: Deployment history logs