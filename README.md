Presented By
===

* [Romain Pelisse](https://github.com/rpelisse)

Requirement: This lab assume that attendees are familliar with JBoss EAP, especially its installation and how to configure the server.

Connecting to the system
===

Go request your lab environnment on [GUID Grabber](https://www.opentlc.com/gg/gg.cgi?profile=generic_pc)
Lab Activation key: ansible-middleware

Use the browser on your sytem to allocate one instance of this lab for you. Once this is done, you'll get a DNS hostname (or an IP) to get to the remote system provided for you.

If you are not familliar with SSH, please let me (the instructor) know!

How to edit file?
===

You'll be connecting to the lab system using a SSH connection so to edit the files you have to create and/or modify for the lab you need to use a text editor such as 'vi', 'emacs' or 'pico'. If you are unfamilliar with those text editor, simply use 'pico'. All three have been made available on the system. Feel free to install an other text editor on the system if you prefer - just don't break the system ;)


Lab 0 - Setting up Your Environment (15')
====

Before starting the lab, please check that:
* Ansible is installed by executing on the lab system:
```$ ansible-playbook --version```
* Double check that you can indeed install package on the remote system:
```$ sudo dnf install nginx```

Ansible Overiew (15' to 25' depending on attendees familiarity with Ansible):

This part of the lab will cover the basics of Ansible. You will learn its syntax, but also how to organize its files, and create a playbook. This first lab will then focus on installing and configuring, using Ansible, a HTTP server (nginx). The playbook will also ensure that the service, like the firewall, is running and that the HTTP server can indeed be reached using the public IP of the system. We will even go one step further, by already configuring the HTTP server to proxy request to a local instance of Wildfly (that we will install in the next lab).

Lab 1 - Basic install of Wildlfy using Ansible (25')
===

If you are not familiar with Ansible, we are going to start with simple tutorial. If you are already fluent with Ansible and that the exercice below does not provide any challenges to you, go to the next lab.

We will implement together a playbook that will automate the installation of an instance of Wildfly (the upstream version of JBoss EAP).

* Ansible helloworld, copy the content below into a file called playbook.yml:
```
---
- name: Network Getting Started First Playbook
  gather_facts: false
  hosts: localhost
  tasks:

    - name: Hello Ansible
      debug:
        msg: "Hello!"
```
* Execute it using the ansible-playbook command:
```
# ansible-playbook playbook.yml
```
* Warning: As Ansible uses YAML as a format, respecting the Yaml constraint is important! Be careful of your identation!
* Download Wildlfy's zipfile from the project website
    * Hint: look at the Ansible module called [unarchive](https://docs.ansible.com/ansible/latest/modules/unarchive_module.html)
	* Advanced Question: Makes this idempotent
* Prepare system to run Widlfy which includes:
    * create required directory structure
    * create group and user for 'widlfy'
    * create a symlink bewteen the init script provided with Wildfly and the /etc/init.d directory.
* Ensure Wildfly service is running as a service

Lab 2 - Introducing JCliff (20')
===

We will start this lab by a short presentation of JCliff and its integration within Ansible, then you'll have to implement the following requirements:

* Tweak memory setting and JAVA_OPTS (reduce memory usage by a factor 2)
* Create admin user for the Console
* Install jcliff:
    * Add a yum repo:
```
$ cat /etc/yum.repos.d/jcliff.repo
[jcliff]
baseurl = http://people.redhat.com/~rpelisse/jcliff.yum/
gpgcheck = 0
name = JCliff repository
```
    * Install the tool
```
# yum install jcliff
```
* Install jcliff role for Ansible using Ansible Galaxy
```
$ ansible-galaxy install redhat-cop.jcliff
- downloading role 'jcliff', owned by redhat-cop
- downloading role from https://github.com/redhat-cop/automate-jcliff/archive/master.tar.gz
- extracting redhat-cop.jcliff to /home/rpelisse/.ansible/roles/redhat-cop.jcliff
- redhat-cop.jcliff (master) was installed successfully
```
* Add properties to the Widlfy Configuration (using JCliff) (myPropery/myValue)
    * Hint: Looks at the example provided on [automate-jcliff](https://github.com/rpelisse/automate-jcliff/blob/master/tune-wildfly-with-jcliff.yml#L30)
* Use the jboss-cli to check that properties has been created:
```
/opt/wildfly/wildfly-16.0.0.Final/bin/jboss-cli.sh  --connect --command='/system-property=myProperty:read-resource'
{
    "outcome" => "success",
    "result" => {"value" => "myValue"}
}
```

Lab 3 - Using Ansible to deploy a JDBC driver(15')
===

Enhance your playbook to automate the following actions:
* Download the latest jdbc driver for Postgresql
* Create the required directory structure to deploy a module
* Deploy the required artefacts to deploy a new JDBC driver (for Postgresql) inside Wildfly
* Use JCliff to ensure the new driver is available to apps hosted on Wildfly

Lab 4 - Deploy application with JCliff (15')
===

Add the deployment of a WebApp in your playbook, by automating the following actions:
* Download the [simple-webapp.war](people.redhat.com/~rpelisse/jcliff.yum/)
* Use JCliff to deploy the WebApp

Lab 5 - Write a JCliff custom rule (30')
===

* Use JCliff to enable logging all the incoming HTTP request (acccess.log)
