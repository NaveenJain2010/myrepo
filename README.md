---
Title: openstack-automation-saltstack
Description: This project has been created by HCL Tech System Software team to support the installation automation of OpenStack using SaltStack.
Contributor: HCL Tech System Software Team
MailTo: hcl_ss_oss@hcl.com
Tags: Automation of openstack, openstack automation using salt stack
Created:  2016 Sep 09
Modified: 2016 Sep 09

---

openstack-automation-saltstack
=========

This project has been created by HCL Tech System Software team to support the installation automation of OpenStack using SaltStack.

# Objective of the Project

This open source project is based on to support the automation of installation of OpenStack (for Liberty release on Ubuntu 14.04 LTS) using SaltStack version 2016.3.2, the installation gets completed in very short span of time. 
SaltStack provides an infrastructure management framework which makes task of installation pretty easier. SaltStack maintains a repository of formulas (which are plain sls files having information about steps involved in installation/execution). These sls files contain definite set of formulas for installation and configuration of different OpenStack packages.

# New Features Added

This project supports the installation of OpenStack Liberty release with MariaDB database server.

# How to configure the Project to install OpenStack

The following steps will be required by end users to their workstations to configure/use this project:
Step1: Setup the project on Physical/virtual machine with Ubuntu 14.04 LTS x86-64 operating system (this machine would trigger the installation of OpenStack liberty release over salt-minion(s)):

1)	Install the latest version 2016.3.2 of salt-master.

•	Run the following command to import the SaltStack repository key:
  wget -O - https://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add –

•	Save the following line 
  deb http://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest trusty main
  to /etc/apt/sources.list.d/saltstack.list

•	Run the following command:
  sudo apt-get update

•	Run the following command to install salt-master
  sudo apt-get install salt-master

2)	Clone the project from git to local machine.

3)	Update the salt-master configuration file at "/etc/salt/master" which would hold the below contents:
    pillar_roots:
      liberty:
        - /openstack_liberty/data_root
    file_roots:
      liberty:
        - /openstack_liberty/component_root

Step2: For best configuration as per OpenStack 3-node setup is needed in which one is Controller node, second is Compute node and third is Block storage node.

Setup three physical or virtual machines with Ubuntu 14.04 LTS x86-64 operating system, these machines would be used for setting up the OpenStack in three node architecture.

1)	Install the latest version 2016.3.2 of salt-minion on all three nodes.

•	Run the following command to import the SaltStack repository key:
  wget -O - https://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add –

•	Save the following line 
  deb http://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest trusty main
  to /etc/apt/sources.list.d/saltstack.list

•	Run the following command:
  sudo apt-get update

•	Run the following command to install salt-master
  sudo apt-get install salt-minion

2)	On every minion update the “/etc/hosts” file on every minion by adding the IP address of salt-master.

3)	On every minion update the “/etc/salt/minion” file with the IP address of salt-master against “master:” field.

Step3: In order to start salt-master, execute the following command in terminal on salt-master machine 
      salt-master –l debug

Step4: Update the name of all three minions by executing the following commands:

1)	For Controller Node:
    echo “controller.liberty” > /etc/salt/minion_id

2)	For Compute Node:
    echo “compute.liberty” > /etc/salt/minion_id

3)	For Block Storage Node:
    echo “blockstorage.liberty” > /etc/salt/minion_id

4)	For each minion (OpenStack node) the same name should be updated into the “/etc/hostname”.

5)	Reboot all three minions.

