# Red Hat Applier in Tower Inventory Example


## Overview
This is an ansible inventory which identifies all application, including CI/CD components, to be built and deploy 
in an OpenShift environment. The ansible layer is very thin. It simply provides a way to orchestrate the application of [OpenShift templates](https://docs.openshift.com/container-platform/3.7/dev_guide/templates.html) across one or more [OpenShift projects](https://docs.openshift.com/container-platform/3.7/architecture/core_concepts/projects_and_users.html#projects). All configuration for the applications should be defined by an OpenShift template and the corresponding parameters file. 

Currently, the following components are included in this inventory:

* Ruby Application, Infrastructure OpenShift Templates and corresponding parameter files
* Tower configuration for the Ruby project, inventory, credentials, and job templates

## Prerequisites
* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)
* [OpenShift CLI Tools](https://docs.openshift.com/container-platform/3.7/cli_reference/get_started_cli.html)
* Access to the OpenShift cluster 

## Usage
1. Log on to an OpenShift server `oc login -u <user> https://<server>:<port>/`
    1. User needs permissions to deploy ProjectRequest objects
2. Clone this repository
3. Install the required [casl-ansible](https://github.com/redhat-cop/casl-ansible) dependency
    1. `[rh-applier-tower]$ ansible-galaxy install -r requirements.yml`
4. Run the ansible playbook provided by the casl-ansible
    1. `[rh-applier-tower]$ ansible-playbook deploy.yml -i inventory/ -e "target=bootstrap" --connection local --skip-tags="login"`
5. To run the ansible playbook on Ansible Tower omit the skip tags, look at the `tower/setup.yml` for more details

## Layout
- `inventory`: a standard [ansible inventory](http://docs.ansible.com/ansible/latest/intro_inventory.html).   
  -  the `hosts` file reflects the fact that the playbook will use the OpenShift CLI on your localhost to interact with the cluster
- `vars`: a standard [ansible inventory](http://docs.ansible.com/ansible/latest/intro_inventory.html).
  - the vars files are written according to [the convention defined by the openshift-applier role](https://github.com/redhat-cop/casl-ansible/tree/master/roles/openshift-applier#sourcing-openshift-object-definitions).
- `templates/`: a set [OpenShift templates](https://docs.openshift.com/container-platform/3.7/dev_guide/templates.html) to be sourced from the inventory (and then vars files). OpenShift provides a lot of templates out of the box, and [the Labs team curates a repository](https://github.com/rht-labs/labs-ci-cd/tree/master/templates) as well. These should be favored before writing custom/new templates to be kept here.
- `params/`: a set of [parameter files](https://docs.openshift.com/container-platform/3.7/dev_guide/templates.html#templates-parameters) to be processed along with their respective OpenShift template. the convention here is to group files by their application.
- `projectrequests`: processing templates for `ProjectRequest` objects in OpenShift requires elevated privileges, so we process `ProjectRequests` without templates because we want normal users to be able to run these playbooks. this directory contains the object definitions.
