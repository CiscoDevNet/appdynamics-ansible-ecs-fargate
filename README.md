# AppDynamics Agents in AWS ECS with Ansible

AWS Elastic Container Service (ECS) Fargate is a service used to run containers without having to manage servers or clusters, by defining tasks we describe requirements for our applications running in containers. For more information on this refer to the official [AWS documentation](https://aws.amazon.com/ecs/).

With this Ansible collection, ECS Fargate Cluster that allows you to define Tasks that describe your containers, defined in Task Definition can be created. Tasks can be scaled in and out with ECS Cluster Services. 

All of the supporting resources are going to be created as well. Secrets, namely controller access key, is going to be stored in AWS Secrets Manager, CloudWatch Logs created, and networking handled - specifically create a Virtual Private Cloud (VPC), VPC subnets, open firewall rules in Security Groups and enable access to internet via Internet Gateway mapped to a VPC using Route Table.

## 1. Prepare

### Create AWS User (optional)

Create a user in AWS to be used by Ansible. Use this user's access and secret key to connect to AWS from ansible (refer to content of `playbooks/vars/cloud-provider.yml`). 

If there is already a user on AWS account that can be used for this purpose, skip this step (optional).

```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_ansible_user_create.yml
```

### Variables

Start by copying playbook's `/vars` files, and adding prefix `.local.` to their name. 

Copy the playbook’s variables file: `playbooks/vars/cloud-provider.yml` appending `.local.` at the beginning, so it’s ignored by version control. 
In this newly created file the _true_ variable values should be stored.

> i.e. based on `cloud-provider.yml` create `.local.cloud-provider.yml`.

Similar approach is to be followed for providing controller connection, copying `playbooks/vars/controller.yml`, agent configuration from `playbooks/vars/agent.yml`, and application details from `playbooks/vars/application.yml`.

After populating files prefixed with `.local.` with true variable values - proceed to running playbooks.

> Note that ideally secret values are stored in an Ansible vault.

## 2. Run Playbooks to Create Resources

### Run ECS Fargate Create Playbook

Create ECS containers and supporting resources.
```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_fargate_create.yml
```

## 3. Run Playbooks to Remove Resources

As there is no default way to “remove” or “rollback”, a set of playbooks is defined to delete resources.

### Run ECS Fargate Remove Playbook

```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_fargate_remove.yml
```

> In case errors happen during destroying resources, try re-running the command after couple of seconds - for resources like IGW or ECS Services, it may take a bit longer to get removed from the AWS.

### Remove AWS User (optional, if created)

```console
foo@bar:~$ ansible-playbook playbooks/aws_ecs_ansible_user_remove.yml
```

## 4. How to contribute?

* Open an issue/feature request/bug report
* Improve documentation, enhance testing, create and share demo scenarios, ...
* Implement a feature (e.g. add support for .NET Core, NodeJS, Python, ...)
    * Fork a repo https://docs.github.com/en/github/getting-started-with-github/fork-a-repo
    * Create a feature branch
    * Merge changes back to original repo through PullRequest
* Get in touch: #