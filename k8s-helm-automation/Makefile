# Makefile for Helm chart deployment automation

.PHONY: setup lint deploy-dev deploy-staging deploy-prod dry-run-dev dry-run-prod validate help

# Default environment
ENV ?= dev
CHART ?= metabase-k8s
CLUSTER ?= dev-cluster
NAMESPACE ?= metabase-$(ENV)
RELEASE ?= metabase-$(ENV)
EXTRA_ARGS ?=

# Directories
PLAYBOOK_DIR = playbooks
VAR_DIR = vars

# Ansible commands
ANSIBLE = ansible-playbook
ANSIBLE_LINT = ansible-lint

# Main playbook
DEPLOY_PLAYBOOK = $(PLAYBOOK_DIR)/deploy-helm-chart.yml

# Help command
help:
	@echo "Helm Chart Deployment Automation"
	@echo ""
	@echo "Usage:"
	@echo "  make [command] [ENV=dev|staging|prod] [CHART=chart-name] [CLUSTER=cluster-name] [NAMESPACE=namespace] [RELEASE=release-name] [EXTRA_ARGS='extra ansible args']"
	@echo ""
	@echo "Commands:"
	@echo "  setup              Install required dependencies"
	@echo "  lint               Lint the Ansible playbooks"
	@echo "  deploy-dev         Deploy to development environment"
	@echo "  deploy-staging     Deploy to staging environment"
	@echo "  deploy-prod        Deploy to production environment"
	@echo "  dry-run-dev        Perform a dry-run on development environment"
	@echo "  dry-run-prod       Perform a dry-run on production environment"
	@echo "  validate           Validate an existing deployment"
	@echo "  clean              Clean temporary files"
	@echo ""
	@echo "Examples:"
	@echo "  make deploy-dev CHART=metabase-k8s CLUSTER=dev-cluster"
	@echo "  make deploy-prod CHART=metabase-k8s CLUSTER=prod-cluster EXTRA_ARGS='--skip-tags validation'"
	@echo "  make dry-run-prod CHART=metabase-k8s NAMESPACE=metabase-test"

# Setup dependencies
setup:
	@echo "Installing dependencies..."
	pip install ansible ansible-lint
	ansible-galaxy collection install kubernetes.core
	@echo "Dependencies installed successfully."

# Lint playbooks
lint:
	@echo "Linting playbooks..."
	$(ANSIBLE_LINT) $(DEPLOY_PLAYBOOK)

# Deploy to development
deploy-dev:
	@echo "Deploying $(CHART) to development environment..."
	$(ANSIBLE) $(DEPLOY_PLAYBOOK) -e "environment=dev chart_name=$(CHART) cluster_name=$(CLUSTER) namespace=$(NAMESPACE) release_name=$(RELEASE)" $(EXTRA_ARGS)

# Deploy to staging
deploy-staging:
	@echo "Deploying $(CHART) to staging environment..."
	$(ANSIBLE) $(DEPLOY_PLAYBOOK) -e "environment=staging chart_name=$(CHART) cluster_name=$(CLUSTER) namespace=$(NAMESPACE) release_name=$(RELEASE)" $(EXTRA_ARGS)

# Deploy to production
deploy-prod:
	@echo "Deploying $(CHART) to production environment..."
	$(ANSIBLE) $(DEPLOY_PLAYBOOK) -e "environment=prod chart_name=$(CHART) cluster_name=$(CLUSTER) namespace=$(NAMESPACE) release_name=$(RELEASE)" $(EXTRA_ARGS)

# Dry run in development
dry-run-dev:
	@echo "Performing dry-run in development environment..."
	$(ANSIBLE) $(DEPLOY_PLAYBOOK) -e "environment=dev chart_name=$(CHART) cluster_name=$(CLUSTER) namespace=$(NAMESPACE) release_name=$(RELEASE) dry_run=true" $(EXTRA_ARGS)

# Dry run in production
dry-run-prod:
	@echo "Performing dry-run in production environment..."
	$(ANSIBLE) $(DEPLOY_PLAYBOOK) -e "environment=prod chart_name=$(CHART) cluster_name=$(CLUSTER) namespace=$(NAMESPACE) release_name=$(RELEASE) dry_run=true" $(EXTRA_ARGS)

# Validate deployment
validate:
	@echo "Validating deployment in $(ENV) environment..."
	$(ANSIBLE) $(DEPLOY_PLAYBOOK) -e "environment=$(ENV) chart_name=$(CHART) cluster_name=$(CLUSTER) namespace=$(NAMESPACE) release_name=$(RELEASE)" --tags validation $(EXTRA_ARGS)

# Clean temp files
clean:
	@echo "Cleaning temporary files..."
	rm -rf *.retry ansible_cache
	find . -name "*.pyc" -delete
	find . -name "__pycache__" -delete
	@echo "Clean completed."