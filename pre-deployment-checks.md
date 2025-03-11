# Install ansible-lint if not already installed
pip install ansible-lint

# Run lint on the entire project
cd k8s-helm-automation
ansible-lint playbooks/deploy-helm-chart.yml