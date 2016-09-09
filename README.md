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

## Objective of the Project

This open source project is based on to support the automation of installation of OpenStack (for Liberty release on Ubuntu 14.04 LTS) using SaltStack version 2016.3.2, the installation gets completed in very short span of time. 
SaltStack provides an infrastructure management framework which makes task of installation pretty easier. SaltStack maintains a repository of formulas (which are plain sls files having information about steps involved in installation/execution). These sls files contain definite set of formulas for installation and configuration of different OpenStack packages.

## New Features Added

This project supports the installation of OpenStack Liberty release with MariaDB database server.

## How to configure the Project to install OpenStack

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

### Please note:
The above mentioned names could be anything as per the user(s) choice, as we have considered the above mentioned name to easily visualize/identify the OpenStack nodes.

Step5: In order to start salt-minion, execute the following command in terminal on each salt-minion machine (OpenStack nodes):
salt-minion –l debug

Step6: Every minion should be registered on salt-master machine, in order to register the minion execute the following command on salt-master:

    salt-key –a “controller.liberty”
    salt-key –a “compute.liberty”
    salt-key –a “blockstorage.liberty”

    In order to verify the status of minion registration with master, execute the following command
    salt ‘*.liberty’ test.ping (which displays all 3 salt-minions will be shown in green color.)
    
Step7: Updated the file “data_root/openstack_cluster.sls” located in salt-master machine. The fields which are highlighted in the below image should be provided by the user:

### <Image to be added>

Step8: Verify the following values should be in-sync as specified in the below image:

### <Image to be added>

## How to start execution to install OpenStack in three node architecture

We are done with setting of salt-master and salt-minion machines, now this time to start the execution of OpenStack installation. 

In order to start the installation, execute the following command from terminal on salt-master machine:
	salt ‘*.liberty’ state.highstate
	
The following OpenStack components would be installed on respective minions:

### On Controller node (in our case that is represented as controller.liberty with IP address 192.168.174.234)

    1)	Installation and configuration of MariaDB server
    2)	Installation and configuration of RabbitMQ server
    3)	Installation and configuration of Apache server
    4)	Installation and configuration of Memcached
    5)	Installation and configuration of identity service (i.e. Keystone)
    6)	Installation and configuration of image  service (i.e. Glance)
    7)	Installation and configuration of compute service (i.e. Nova)
    8)	Installation and configuration of networking service (i.e. Neutron)
    9)	Installation and configuration of dashboard service (i.e. Horizon)
    10)	Installation and configuration of block storage service (i.e. Cinder)
### On Compute node (in our case that is represented as compute.liberty with IP address 192.168.253.129)

    1)	Installation and configuration of compute service (i.e. Nova)
    2)	Installation and configuration of networking service (i.e. Neutron)

### On Block storage node (in our case that is represented as blockstorage.liberty with IP address 192.168.253.131)

    1)	Installation and configuration of block storage service (i.e. Cinder)

In common the following setting/installation would be performed over all the three OpenStack nodes:

    1)	Update the host file.
    2)	Network interface related changes in interfaces file.

## Changes if Installation needed to setup more than one instance of OpenStack environment

In case if there is requirement to install and configure more than one instance of OpenStack like:

    Instance1: Controller node, Compute node and Block Storage node
    Instance2: Controller node, Compute node and Block Storage node

The following changes which are highlighted in below images would require to be made in the respective files.

### <Image to be added>

### <Image to be added>

## Troubleshooting Steps

Following are the commonly faced problems along with the troubleshooting steps:

Troubleshooting problem: 
During the installation of states, sometimes a message comes from the salt-master saying "Minion did not return.".

Resolution Steps: 
1. This message occurs mainly due to the change in the IP address of salt-minion with respect to salt-master.
2. To resolve this issue, we need to reconfigure IP address of the salt-minion, and update the host file.
3. Execute the test.ping command from salt-master which results in true.
    salt 'controller.liberty' test.ping
4. And then update the openstack_cluster_resources file with the new IP so that after the re run of the states, the minion 	automatically gets the updated IP entry in its host file.

Troubleshooting problem:
When salt command starts execution from salt-master, sometimes a message saying "The Salt state.highstate command is already running at pid 9080" comes on command prompt.

Resolution Steps:
1. This message occurs when a salt-minion is stopped by user by pressing Ctrl+C command to terminate it, and eventually it is still running.
2. In that case when salt command is again executed from salt-master so this message comes and no states completion summary is displayed on salt-master.
3 To avoid such a situation it is better to kill the salt-master and salt-minion demon by executing following command from salt-master and salt-minion.
pkill salt-master(From salt-master node)
pkill salt-minion(From salt-minion node)
4. So again freshly start the salt-master and salt-minion demon in the debug mode, and wait for connection to be established.
salt-master -l debug
salt-minion -l debug
5. As the connection gets established, just execute the salt command by specifying the minion.
salt '*.liberty' state.highstate

Troubleshooting problem:
When salt-minion is showing infinite jobs sequence for a individual states and is acquiring more time to complete the process.

Resolution Steps:
1. Terminate the salt-minion by Ctrl+C command.
2. Check the individual states files, as if there is some logical error which causes salt-minion to start the infinite loop of jobs.
3. After revalidating the states files for any logical errors, restart the salt-minion daemon.
 salt-minion -l debug
4. And then start the execution of salt command from salt-master for a specific or all the minions.

Troubleshooting problem:
When there is compile time error saying NoneType object is not iterate-able comes on salt-master node.

Resolution Steps:
1. This problem occurs when there is no element in the dict to iterate.
2. To resolve this error make sure, no sls is commented under a specific role in openstack_cluster_resources.sls file.
3. Even if a user wants to run the individual state on individual minion, he needs to specify at least one state under each role.
4. And can execute the salt-command by specifying that minion node.
5. Let’s say he wants to install mariadb on controller node, then in cluster resources he needs to at least specify a individual sls under every node either it is compute or storage, but can execute the salt-command for a individual node.
salt 'controller.liberty' state.highstate

Troubleshooting problem:
If a user wants to install an individual package on an individual minion.

Resolution Steps:
1. In such a case he needs to comment out all other states in the openstack _cluster_resources.sls file for that particular minion.
2. And then execute the following command from the salt-master for a specific node.
3. Let say for a controller node, apache module is needed.
	3.1 So firtsly comment out other sls (by putting # at the begining of line) in openstack _cluster_resources.sls file.
	3.2 And then execute the following command from salt-master.
	salt 'controller.liberty' state.highstate.

Troubleshooting problem:
If a user wants to install all services on all nodes.

Resolution Steps:
1. In that case user needs to remove all the commented lines form the openstack _cluster_resources.sls if there any.
2. And execute the following command from salt-master.
Salt '*.liberty' state.highstate
3. As the command execution begins, he further cross validates the response of installation on all salt-minions node by visualizing the salt-minion daemon.
4. All the states summary will be displayed on salt-master after the installation process is completed on each minion.

## Version Information

This project is tested on the following combination of versions:

1. SaltStack Master version 2016.3.2
2. SaltStack Minion version 2016.3.2
3. OpenStack Liberty Release
4. Ubuntu 14.04 LTS x86-64 operating system

## Current Limitations

For OpenStack Neutron Networking, this project supports only installation and configuration of Networking Option 2: Self-service networks. 

##Future Plan

We are in planning phase to provide the automation for installation of OpenStack Mitaka release and installation and configuration of Object Storage (Swift module).

<table>
  <tbody>
    <tr>
      <th>Tables</th>
      <th align="center">Are</th>
      <th align="right">Cool</th>
    </tr>
    <tr>
      <td>col 3 is</td>
      <td align="center">right-aligned</td>
      <td align="right">$1600</td>
    </tr>
    <tr>
      <td>col 2 is</td>
      <td align="center">centered</td>
      <td align="right">$12</td>
    </tr>
    <tr>
      <td>zebra stripes</td>
      <td align="center">are neat</td>
      <td align="right">$1</td>
    </tr>
    <tr>
      <td>
        <ul>
          <li>item1</li>
          <li>item2</li>
        </ul>
      </td>
      <td align="center">See the list</td>
      <td align="right">from the first column</td>
    </tr>
  </tbody>
</table>
