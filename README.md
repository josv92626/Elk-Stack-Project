# Elk-Stack-Project
# Jon Maguire's Elk Stack Project

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

<img src="https://github.com/SerratedGrin/Elk-Stack-Project/blob/master/ELK%20Diagram.jpg" />

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above, or select portions of the provided files may be used to install specific pieces of the deployment - such as [Filebeat](https://github.com/SerratedGrin/Elk-Stack-Project/blob/master/filebeat-config.yml).


This document contains the following details:
- [Description of the Topology](https://github.com/SerratedGrin/Elk-Stack-Project#description-of-the-topology)
- [Access Policies](https://github.com/SerratedGrin/Elk-Stack-Project#access-policies)
- [ELK Configuration](https://github.com/SerratedGrin/Elk-Stack-Project#elk-configuration)
  - Beats in Use
  - Machines Being Monitored
- [How to Use the Ansible Build](https://github.com/SerratedGrin/Elk-Stack-Project#using-the-playbook)


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application. The usage of which is to test various regular web vulnerability, with various difficultly levels, in a simple UI.

First and foremost - the load balancer's off-loading function, or distribution of traffic across multiple servers, mitigates Denial of Service attacks. This highly valuable security measure alone highlights the benefit of a load balancer.

Load balancing ensures that the application will be highly available, scalable, and reliable - in addition to restricting unsecured access to the network. Load balancers also offer a health probe function, obtaining reports of servers with issues and stops sending traffic to said servers. 

Having multiple virtual machines host the DVWA establishes a wider penetration testing environment, and if one becomes unavailable the others will remain active and the functions of the load balancer further come into play.

The configuration details of each machine may be found below.

| Name                 | Function          | IP Address     | Operating System |
|----------------------|-------------------|----------------|------------------|
| Jump-Box-Provisioner | Gateway           | 52.191.162.187 | Linux            |
| DVWA-LD              | Load balancer     | 40.125.123.181 | N/A              |
| Red-Team-Web-VM-1    | DVWA host machine | 10.10.0.9      | Linux            |
| Red-Team-Web-VM-2    | DVWA host machine | 10.10.0.10     | Linux            |
| Red-Team-Web-VM-3    | DVWA host machine | 10.10.0.11     | Linux            |
| ELK-Server           | ELK host machine  | 52.252.58.97   | Linux            |


The virtual network and allocated security group the DVWA machines have been configured within ensures the filtering of network traffic via security rules, such as which ports are allowed access. This is true for the virtual network and allocated security group for the ELK server as well. 

  - Note that the networks are peering - this allows both networks to seemlessly connect with each other, and simplifies configurations of the resource group containing the entire infrastructure.

Filebeat collects data about the file system, ones that you can specify as may be necessary with a larger organization, and forwards them to Elasticsearch and/or Logstash for indexing.

Metricbeat (an accompanying service to Filebeat) collects server metrics - such as uptime, hardware utilization, peak response time, etc. This is particularly important tool for monitoring services such as Apache2 or MySQL. This data is also forwarded to Elasticsearch and/or Logstash.

 - Note that Filebeat is a separate entity within the diagram, but it is a running service on the DVWA host machines. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems and system metrics. More detail about the ELK configuration [below](https://github.com/SerratedGrin/Elk-Stack-Project#elk-configuration).



### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump-Box-Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from machines that have the SSH key pair used while configuring the server (public key loaded onto the VM, SSH using the id_rsa file containing the private key).

Machines within the network can only be accessed via SSH port 22 through the Docker container loaded on the Ansible machine.
- The ELK server can only be accessed via port 22 (SSH) from the Docker container, which has the key pair, and via HTTP on port 5601 - the latter being a security rule that needs to be established in the configuration process.

A summary of the access policies in place can be found in the table below.

| Name                 | Publicly Accessible | Allowed IP Address(s)               |
|----------------------|---------------------|-------------------------------------|
| Jump-Box-Provisioner | Yes                 | Pub:52.191.162.187 - Priv:10.10.0.8 |
| Red-Team-Web-VM-1    | No                  | 10.10.0.9                           |
| Red-Team-Web-VM-2    | No                  | 10.10.0.10                          |
| Red-Team-Web-VM-3    | No                  | 10.10.0.11                          |
| ELK-Server           | No                  | Pub:52.252.58.97 - Priv:10.0.0.4    |

 - Note that while (as mentioned above) the ELK interface can be accessed via HTTP on port 5601, the host server itself remains entirely private.

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because the process eliminates potential human error and makes the process significantly more efficient - particularly while utilizing a bash script to establish multiple configurations with one command. There are, however, specific elements that will need to be modified to accurately configure according to the parameters of the topology (virtual networks and their respective IP addresses and/or subnets, as was modified in this particular case for the [filebeat-config.yml](https://github.com/SerratedGrin/Elk-Stack-Project/blob/master/filebeat-config.yml)).

The playbook implements the following tasks:
- Installs necessary files for ELK to run as intended.
- Configures the hosting server and the ELK apps themselves.
- Lauches the Docker container in which the ELK Stack has been configured.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the [filebeat-config.yml](https://github.com/SerratedGrin/Elk-Stack-Project/blob/master/filebeat-config.yml) file to the proper directory specified inside said YAML file.
- Update the hosts file within the Ansible directory file to include the webservers (Red Team VMs) and the ELK server. Referenced in [this screenshot]()
- Run the playbook, and navigate to to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
