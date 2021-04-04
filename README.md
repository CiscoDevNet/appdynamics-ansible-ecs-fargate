# AppDynamics Agents in AWS ECS with Ansible

AWS Elastic Container Service (ECS) Fargate is a service used to run containers without having to manage servers or clusters, by defining tasks we describe requirements for our applications running in containers. 

Creating ECS Fargate Cluster allows you to define Tasks that describe your containers, defined in Task Definition. Tasks can be scaled in and out with ECS Cluster Services. 

With this Ansible collection, in an automated manner all of the supporting resources during this process are going to be created. Secrets, namely controller access key, is going to be stored in AWS Secrets Manager, CloudWatch Logs created, and networking handled - specifically create a Virtual Private Cloud (VPC), VPC subnets, open firewall rules in Security Groups and enable access to internet via Internet Gateway mapped to a VPC using Route Table.


## Prepare

Start by copying playbook's `/vars` files, and adding prefix `.local.` to their name. These newly created files are ignored by version control. 

Copy the playbook’s variables file: `playbooks/vars/cloud-provider.yml` appending `.local.` at the beginning, so it’s ignored by version control, where the true variable values should be stored.

Similar approach is to be followed for providing controller connection, copying `playbooks/vars/controller.yml` and agent configuration from `playbooks/vars/agent.yml`.

> i.e. based on `controller.yml` create `.local.controller.yml`.

Note that ideally secret values are stored in an Ansible vault.

After populating files prefixed with `.local.` with true variable values - proceed to running playbooks.

## Run Playbooks to Create Resources

### Create AWS User 

Create a user in AWS to be used by Ansible. Use this user's access and secret key to connect to AWS from ansible (refer to content of `playbooks/vars/cloud-provider.yml`).

```console
ansible-playbook playbooks/aws_ecs_ansible_user_create.yml
```

### Run ECS Fargate Create Playbook

Create ECS containers and
```console
ansible-playbook playbooks/aws_ecs_fargate_create.yml
```

## Run Playbooks to Remove Created Resources

As there is no default way to “remove” or “rollback”, we re running set of playbooks to delete resources.

### Run ECS Fargate Remove Playbook

```console
ansible-playbook playbooks/aws_ecs_fargate_remove.yml
```

### Remove AWS User 

```console
ansible-playbook playbooks/aws_ecs_ansible_user_remove.yml
```


