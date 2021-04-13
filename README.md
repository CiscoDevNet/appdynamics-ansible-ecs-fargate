> NOTE: Waiting for pull request to be completed and released to enable full functionality: https://github.com/ansible-collections/community.aws/pull/534. Although underlying Boto3 library supports `depends_on` functionality, current ansible module does not have and option to force one container execution completion (containing agent binaries) before another container starts (monitored application). This collection can be run, as it creates and destorys resources in AWS as expepcted, however, container execution fails and as a result application is not visible in the controller.

# AppDynamics Agents in AWS ECS with Ansible

AWS Elastic Container Service (ECS) Fargate is a service used to run containers without having to manage servers or clusters, by defining tasks we describe requirements for our applications running in containers. For more information on this refer to the official [AWS documentation](https://aws.amazon.com/ecs/).

With this Ansible collection, ECS Fargate Cluster that allows you to define Tasks that describe your containers, defined in Task Definition can be created. Tasks can be scaled in and out with ECS Cluster Services. 

All of the supporting resources are going to be created as well. Secrets, namely controller access key, is going to be stored in AWS Secrets Manager, CloudWatch Logs created, and networking handled - specifically create a Virtual Private Cloud (VPC), VPC subnets, open firewall rules in Security Groups and enable access to internet via Internet Gateway mapped to a VPC using Route Table.


## Prepare

Start by copying playbook's `/vars` files, and adding prefix `.local.` to their name. 

Copy the playbook’s variables file: `playbooks/vars/cloud-provider.yml` appending `.local.` at the beginning, so it’s ignored by version control. In this newly created file the true variable values should be stored.

Similar approach is to be followed for providing controller connection, copying `playbooks/vars/controller.yml` and agent configuration from `playbooks/vars/agent.yml`.

> i.e. based on `controller.yml` create `.local.controller.yml`.

Note that ideally secret values are stored in an Ansible vault.

After populating files prefixed with `.local.` with true variable values - proceed to running playbooks.

## Run Playbooks to Create Resources

### Create AWS User (optional)

Create a user in AWS to be used by Ansible. Use this user's access and secret key to connect to AWS from ansible (refer to content of `playbooks/vars/cloud-provider.yml`). 

If there is already a user on AWS account that can be used for this purpose, skip this step (optional).

```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_ansible_user_create.yml
```

### Run ECS Fargate Create Playbook

Create ECS containers and supporting resources.
```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_fargate_create.yml
```

## Run Playbooks to Remove Created Resources

As there is no default way to “remove” or “rollback”, a set of playbooks is defined to delete resources.

### Run ECS Fargate Remove Playbook

```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_fargate_remove.yml
```

### Remove AWS User (optional)

```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_ansible_user_remove.yml
```


