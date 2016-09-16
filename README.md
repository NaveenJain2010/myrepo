---
Title: openstack-automation-saltstack
Description: This project is owned  by HCL Tech System Software team to support the automated installation of OpenStack using SaltStack.
Owner of the Project: HCL Tech System Software Team
Contributor: HCL Tech System Software Team
Mail To: hcl_ss_oss@hcl.com
Tags: Automation of OpenStack, OpenStack automation using saltstack, HCL in OpenStack automation, Installation support for OpenStack
Created:  2016 Sep 09
Modified: 2016 Sep 09
---

openstack-automation-saltstack
==============================

This project has been created by HCL Tech System Software team to support the automated installation of OpenStack using SaltStack.

## Objective of the Project

Objective of the Project
========================

This open source project is based on to support the automated installation of OpenStack (for Liberty release on Ubuntu 14.04 LTS) using SaltStack version 2016.3.2, the installation gets completed in very short span of time. 
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

![Image1](https://github.com/NaveenJain2010/myrepo/raw/master/img/image1.png)

Step8: Verify the following values should be in-sync as specified in the below image:

![Image2](https://github.com/NaveenJain2010/myrepo/raw/master/img/image2.png)

Step9: The following file as displayed in below image contains the value for the parameters which would be specified while executing the commands for every service to create users, services and endpoints etc. Before proceeding to the installation please review and update the values as per your preferences:

![Image3](https://github.com/NaveenJain2010/myrepo/raw/master/img/image3.png)

## How to start execution to install OpenStack in three node architecture

We are done with setting of salt-master and salt-minion machines, now this time to start the execution of OpenStack installation. 

#### In order to start the installation, execute the following command from terminal on salt-master machine:

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

![Image4](https://github.com/NaveenJain2010/myrepo/raw/master/img/image4.png)

![Image5](https://github.com/NaveenJain2010/myrepo/raw/master/img/image5.png)

## Troubleshooting Steps

Following are the commonly faced problems along with the troubleshooting steps:

<table class=MsoNormalTable border=0 cellspacing=0 cellpadding=0 width=768
 style='width:8.0in;margin-left:-.8in;border-collapse:collapse;mso-yfti-tbllook:
 1184;mso-padding-alt:0in 5.4pt 0in 5.4pt'>
 <tr style='mso-yfti-irow:0;mso-yfti-firstrow:yes;height:15.0pt'>
  <td width=51 nowrap valign=bottom style='width:38.0pt;border:solid windowtext 1.0pt;
  mso-border-alt:solid windowtext .5pt;background:#D9D9D9;padding:0in 5.4pt 0in 5.4pt;
  height:15.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><b><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>Sl. No.<o:p></o:p></span></b></p>
  </td>
  <td width=207 nowrap valign=bottom style='width:155.5pt;border:solid windowtext 1.0pt;
  border-left:none;mso-border-top-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;background:
  #D9D9D9;padding:0in 5.4pt 0in 5.4pt;height:15.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><b><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>Troubleshooting problems <o:p></o:p></span></b></p>
  </td>
  <td width=510 nowrap valign=bottom style='width:382.5pt;border:solid windowtext 1.0pt;
  border-left:none;mso-border-top-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;background:
  #D9D9D9;padding:0in 5.4pt 0in 5.4pt;height:15.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><b><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>Resolution Steps<o:p></o:p></span></b></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:1;height:128.65pt'>
  <td width=51 valign=top style='width:38.0pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-left-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;padding:
  0in 5.4pt 0in 5.4pt;height:128.65pt'>
  <p class=MsoNormal align=right style='margin-bottom:0in;margin-bottom:.0001pt;
  text-align:right;line-height:normal'><span style='mso-ascii-font-family:Calibri;
  mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'>1<o:p></o:p></span></p>
  </td>
  <td width=207 valign=top style='width:155.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:128.65pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>During the installation of states, sometimes a message comes
  from the salt-master saying &quot;Minion did not return.&quot;<o:p></o:p></span></p>
  </td>
  <td width=510 valign=top style='width:382.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:128.65pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>1. This message occurs mainly due to the change in the IP
  address of salt-minion with respect to salt-master.<br>
  2. To resolve this issue, we need to reconfigure IP address of the
  salt-minion, and update the host file.<br>
  3. Execute the <span class=SpellE>test.ping</span> command from salt-master
  which results in true.<br>
  <span class=GramE><b style='mso-bidi-font-weight:normal'><i>salt</i></b></span><b
  style='mso-bidi-font-weight:normal'><i> '<span class=SpellE>controller.liberty</span>'
  <span class=SpellE>test.ping</span></i></b><br>
  4. And then update the <span class=SpellE>openstack_cluster_resources</span>
  file with the new IP so that after the re run of the states, the minion
  automatically gets the updated IP entry in its host file.<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:2;height:211.0pt'>
  <td width=51 nowrap valign=top style='width:38.0pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-left-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;padding:
  0in 5.4pt 0in 5.4pt;height:211.0pt'>
  <p class=MsoNormal align=right style='margin-bottom:0in;margin-bottom:.0001pt;
  text-align:right;line-height:normal'><span style='mso-ascii-font-family:Calibri;
  mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'>2<o:p></o:p></span></p>
  </td>
  <td width=207 valign=top style='width:155.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:211.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>When salt command starts execution from salt-master, sometimes a
  message saying &quot;The Salt <span class=SpellE>state.highstate</span>
  command is already running at <span class=SpellE>pid</span> 9080&quot; comes
  on command prompt.<o:p></o:p></span></p>
  </td>
  <td width=510 valign=top style='width:382.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:211.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>1. This message occurs when a salt-minion is stopped by user by
  pressing <span class=SpellE>Ctrl+C</span> command to terminate it, and
  eventually it is still running.<br>
  2. In that case when salt commands are re-executed from salt-master so this
  message comes and no states completion summary has been displayed on
  salt-master.<br>
  3 To avoid such a situation it is better to kill the salt-master and
  salt-minion daemon by executing following command on salt-master and
  salt-minion.<br>
  <span class=SpellE><span class=GramE><b style='mso-bidi-font-weight:normal'><i>pkill</i></b></span></span><b
  style='mso-bidi-font-weight:normal'><i> salt-master</i></b><i>(From
  salt-master node)</i><br>
  <span class=SpellE><b style='mso-bidi-font-weight:normal'><i>pkill</i></b></span><b
  style='mso-bidi-font-weight:normal'><i> salt-minion</i></b><i>(From
  salt-minion node)</i><br>
  4. Restart the salt-master and salt-minion daemon in the debug mode, and wait
  for connection to be established.<br>
  <span class=GramE><b style='mso-bidi-font-weight:normal'><i>salt-master</i></b></span><b
  style='mso-bidi-font-weight:normal'><i> -l debug</i><br>
  <i>salt-minion -l debug</i></b><i> <br>
  5. </i>As the connection gets established, just execute the salt command by specifying
  the minion.<br>
  <b style='mso-bidi-font-weight:normal'><i>salt '*.liberty' <span
  class=SpellE>state.highstate</span></i></b><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:3;height:108.4pt'>
  <td width=51 nowrap valign=top style='width:38.0pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-left-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;padding:
  0in 5.4pt 0in 5.4pt;height:108.4pt'>
  <p class=MsoNormal align=right style='margin-bottom:0in;margin-bottom:.0001pt;
  text-align:right;line-height:normal'><span style='mso-ascii-font-family:Calibri;
  mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'>3<o:p></o:p></span></p>
  </td>
  <td width=207 valign=top style='width:155.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:108.4pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>When salt-minion is showing infinite jobs sequence for an
  individual states and is acquiring more time to complete the process.<o:p></o:p></span></p>
  </td>
  <td width=510 valign=top style='width:382.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:108.4pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>1. Terminate the salt-minion by <span class=SpellE>Ctrl+C</span>
  command.<br>
  2. Check the individual states files, as if there is some logical error which
  causes salt-minion to start the infinite loop of jobs.<br>
  3. After revalidating the states files for any logical errors, restart the
  salt-minion daemon.<br>
  <i><span style='mso-spacerun:yes'> </span><span class=GramE><b
  style='mso-bidi-font-weight:normal'>salt-minion</b></span><b
  style='mso-bidi-font-weight:normal'> -l debug</b> <br>
  </i>4. And then start the execution of salt command from salt-master for a particular
  or for all the minions.<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:4;height:142.6pt'>
  <td width=51 nowrap valign=top style='width:38.0pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-left-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;padding:
  0in 5.4pt 0in 5.4pt;height:142.6pt'>
  <p class=MsoNormal align=right style='margin-bottom:0in;margin-bottom:.0001pt;
  text-align:right;line-height:normal'><span style='mso-ascii-font-family:Calibri;
  mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'>4<o:p></o:p></span></p>
  </td>
  <td width=207 valign=top style='width:155.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:142.6pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>When there is <span class=GramE>compile</span> time error saying
  <span class=SpellE>NoneType</span> object is not iterate-able comes on
  salt-master node.<o:p></o:p></span></p>
  </td>
  <td width=510 valign=top style='width:382.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:142.6pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>1. This problem occurs when there is no element in the <span
  class=SpellE>dict</span> to iterate.<br>
  2. To resolve this error make sure, no <span class=SpellE>sls</span> is
  commented under a specific role in <span class=SpellE>openstack_cluster_resources.sls</span>
  file.<br>
  3. Even if a user wants to run the individual state on individual minion, he/she
  needs to specify at least one state under each role.<br>
  4. And can execute the salt-command by specifying that minion node.<br>
  5. Let’s say he/she wants to install <span class=SpellE>MariaDB</span> on
  controller node, then in cluster resources he needs to at least specify an
  individual <span class=SpellE>sls</span> under every node either it is
  compute or storage, but can execute the salt-command for an individual node.<i><br>
  <b style='mso-bidi-font-weight:normal'>salt '<span class=SpellE>controller.liberty</span>'
  <span class=SpellE>state.highstate</span></b></i><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:5;height:108.4pt'>
  <td width=51 nowrap valign=top style='width:38.0pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-left-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;padding:
  0in 5.4pt 0in 5.4pt;height:108.4pt'>
  <p class=MsoNormal align=right style='margin-bottom:0in;margin-bottom:.0001pt;
  text-align:right;line-height:normal'><span style='mso-ascii-font-family:Calibri;
  mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'>5<o:p></o:p></span></p>
  </td>
  <td width=207 valign=top style='width:155.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:108.4pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>If a user wants to install an individual package on an
  individual minion.<o:p></o:p></span></p>
  </td>
  <td width=510 valign=top style='width:382.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:108.4pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>1. In such a case he/she needs to comment out all other states
  in the <span class=SpellE>openstack</span> _<span class=SpellE>cluster_resources.sls</span>
  file for that particular minion.<br>
  2. And then execute the following command from the salt-master for a specific
  node.<br>
  3. Let say for a controller node, apache module is needed.<br>
  3.1. So he/she should comment out other <span class=SpellE>sls</span> in <span
  class=SpellE>openstack</span> _<span class=SpellE>cluster_resources.sls</span>
  file.<br>
  3.2. And then execute the following command from salt-master.<br>
  <span class=GramE><b style='mso-bidi-font-weight:normal'><i style='mso-bidi-font-style:
  normal'>salt</i></b></span><b style='mso-bidi-font-weight:normal'><i
  style='mso-bidi-font-style:normal'> '<span class=SpellE>controller.liberty</span>'
  <span class=SpellE>state.highstate</span>.</i></b><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:6;mso-yfti-lastrow:yes;height:112.0pt'>
  <td width=51 nowrap valign=top style='width:38.0pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-left-alt:solid windowtext .5pt;mso-border-bottom-alt:
  solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;padding:
  0in 5.4pt 0in 5.4pt;height:112.0pt'>
  <p class=MsoNormal align=right style='margin-bottom:0in;margin-bottom:.0001pt;
  text-align:right;line-height:normal'><span style='mso-ascii-font-family:Calibri;
  mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'>6<o:p></o:p></span></p>
  </td>
  <td width=207 valign=top style='width:155.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:112.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>If a user wants to install all services on all nodes.<o:p></o:p></span></p>
  </td>
  <td width=510 valign=top style='width:382.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-bottom-alt:solid windowtext .5pt;mso-border-right-alt:solid windowtext .5pt;
  padding:0in 5.4pt 0in 5.4pt;height:112.0pt'>
  <p class=MsoNormal style='margin-bottom:0in;margin-bottom:.0001pt;line-height:
  normal'><span style='mso-ascii-font-family:Calibri;mso-fareast-font-family:
  "Times New Roman";mso-hansi-font-family:Calibri;mso-bidi-font-family:"Times New Roman";
  color:black'>1. In that case user needs to remove all the commented lines (i.e.
  a # sign in the beginning of line) form the <span class=SpellE>openstack</span>
  _<span class=SpellE>cluster_resources.sls</span> if there is any.<br>
  2. And execute the following command from salt-master.<br>
  </span><span class=GramE><b style='mso-bidi-font-weight:normal'><i
  style='mso-bidi-font-style:normal'>salt</i></b></span><b style='mso-bidi-font-weight:
  normal'><i style='mso-bidi-font-style:normal'> '*.liberty' <span
  class=SpellE>state.highstate</span></i></b><span style='mso-ascii-font-family:
  Calibri;mso-fareast-font-family:"Times New Roman";mso-hansi-font-family:Calibri;
  mso-bidi-font-family:"Times New Roman";color:black'><br>
  3. As the command execution begins, he/she further cross validates the
  responses of installation on all salt-minions node by seeing the salt-minion daemon.<br>
  4. All the states summary will be displayed on salt-master after the
  installation process has been completed on each minion.<o:p></o:p></span></p>
  </td>
 </tr>
</table>

## Version Information

This project is based and tested on the following versions:

1. SaltStack Master and Minion version 2016.3.2
2. OpenStack Liberty Release
3. Ubuntu 14.04 LTS x86-64 operating system

## Current Limitations

For OpenStack Neutron Networking, this project supports only installation and configuration of Networking Option 2: Self-service networks. 

##Future Plan

We are in planning phase to provide the automation for installation of OpenStack Mitaka release and installation and configuration of Object Storage (Swift module).
