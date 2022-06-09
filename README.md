# Infrastructure as Code (IaC)

Infrastructure as code is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools. (It is the managing and provisioning of infrastructure - networks, virtual machines, load balancers, and connection topology - through code instead of through manual processes.)

For cloud computing: A process that describes and provisions all the infrastructure resources in a cloud environment using a simple text file. Used to model and provision all the resources needed for your applications across all regions and accounts.

# Benefits of IaC

## IaC boosts productivity through automation

One of the first and most obvious benefits of an infrastructure model like IaC is that it improves the productivity of your operations teams across the IT sector, and allows you to automate all infrastructure processes and changes to save time, money, and minimize the risk of human error. Up until the creation of IaC, infrastructure changes had to be handled and managed through extensive and complex manual work, which would invariably drain resources and sometimes cause setbacks to occur.

## Consistency in configuration and setup

The definition files are a single source of truth. There’s never any confusion about what they do. You execute them repeatedly and get predictable results every time.

## Low Costs

You don’t have to hire a team of professionals to routinely manage resource provisioning, configuration, troubleshooting, hardware setup, and so on. It saves time and money.

## Speed 

IaC automates resource provisioning across environments, from development to deployment, by simply running a script. It drastically accelerates the software development life-cycle and makes your organization more responsive to external challenges.

## Accountability

When you need to trace changes to definition files, you can do it with ease. They are versioned, and therefore all changes are recorded for your review at a later point. So, once again, there’s never any confusion on who did what.

# Tools for IaC

## Terraform

HashiCorp Terraform is the most popular and open-source tool for infrastructure automation. It helps in configuring, provisioning, and managing the infrastructure as code.

## Ansible

Ansible is considered the simplest way to automate the provision, configuration, and management of applications and IT infrastructure. Ansible enables users to execute playbooks to create and manage the required infrastructure resources. It does not use agents and can connect to servers and run commands over SSH. Its code is written in YAML as Ansible Playbooks, making it easy to understand and deploy the configurations. You can even expand the features of Ansible by writing your own Ansible modules and plugins.

## AWS CloudFormation

The models and templates for CloudFormation are written in YAML or JSON format. You just need to code your desired infrastructure from scratch with the suitable template language and use the AWS CloudFormation to provision and manage the stack and resources defined in the template.

# Configuration Orchestration vs. Configuration Management

* Configuration orchestration tools, which include Terraform and AWS CloudFormation, are designed to automate the deployment of servers and other infrastructure.

* Configuration management tools like Chef, Puppet, and Ansible help configure the software and systems on this infrastructure that has already been provisioned.

* Configuration orchestration tools do some level of configuration management, and configuration management tools do some level of orchestration. Companies can and many times use both types of tools together.

### Example

Nightly backups - or commits, is a management task that might be automated using a variety of technologies, including command line scripts or external network management systems. Automating the provisioning of the infrastructure services needed to support an app moving into production – in the right order – is orchestration.

# Use Cases of IaC

There are three major cases where IaC approach can be applied: software development, infrastructure management and cloud monitoring.

* When the environments are uniform across the whole cycle, the chances of bugs arising are much lower, as well as the time required for deployment and configuration of all the required environments. Build, testing, staging and production environment deployments will be repeatable, predictable and error-free.

* Cloud infrastructure management using IaC means that all actions that can be automated will be automated. In this case, multiple scenarios emerge, where provisioning and configuring the system components with Terraform and Kubernetes helps save time, money and effort. All kinds of tasks, from database backups to new feature releases can be done faster and better.

* Finally, cloud monitoring, logging and alerting tools also need to run in some environments and deliver new system components. Solutions like ELK stack, FluentD, SumoLogic, Datadog, Prometheus + Grafana — all of these can be quickly provisioned and configured for your project using IaC best practices.

# Ansible

Ansible can actually do both orchestration and management - but we also learn Terraform because some companies use it.

* Very simple to use.

* Agentless - because everything you need to install only needs to be installed on Ansible. The other server does not need to have Ansible installed. 

A provisioning.sh script can do multiple things in **one** server only. But with Ansible, the yaml scripts can have numerous instructions for multiple servers.

## Use case

There are 500 servers running, and you need to run an update command for half of those servers. In this scenario, if you were not using IaC, you would have to run the script for every server individually. But by using ansible, you could run such a command on all these servers without manually going into every instance and running sudo apt-get update.

Sonar Cube: a tool that looks for any potential threats (IPs), and places these IPS into a CSV or JSON file. This file is then used in the security groups of these servers to block these IPs. But how do we block these IPs in 1000s of servers (yes, through security groups - but it would take a very long time to manually carry out this task). But with ansible, it's one single command. You pick up the file (in a playbook), and run a script to block the hosts.