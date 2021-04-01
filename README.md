# AppDynamics on the Cloud with Ansible

## Prepare

Set variables by copying playbook's `/vars` files, and adding prefix `.local.`. Newly created files are ignored by version control. 
> e.g. make a copy of a file `controller.yml` in the same `/vars`  directory and name it `.local.controller.yml`

Populate `.local.` files with variable values and proceed to running playbooks.

## Run playbooks

### Run AWS ECS Fargate playbook to create resources

`ansible-playbook playbooks/aws_ecs_fargate_create.yml`

### Run AWS ECS Fargate playbook to remove resources

`ansible-playbook playbooks/aws_ecs_fargate_remove.yml`

