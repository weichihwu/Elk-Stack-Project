# Elk-Stack-Project
Elk-Stack-Project 27052021 Monash Cybersecurity Boot Camp

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Images/Elk-Stack-Project_Network-Diagram.png](Images/Elk-Stack-Project_Network-Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook_ file may be used to install only certain pieces of it, such as Filebeat.

  - _TODO: Enter the playbook file._
---
- name: Config ELK VM with Docker
  hosts: elk
  become: true
  tasks:
  - name: Use more memory
    sysctl:
      name: vm.max_map_count
      value: '262144'
      state: present
      reload: yes

  - name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    apt:
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present

   # Use command module
  - name: Increase virtual memory
    command: sysctl -w vm.max_map_count=262144

  - name: download and launch a docker web container
    docker_container:
      name: elk
      image: sebp/elk:761
      state: started
      restart_policy: always
      published_ports:
        -   5601:5601
        -   9200:9200
        -   5044:5044

   # Use systemd module
  - name: Enable service docker on boot
    systemd:
      name: docker
      enabled: yes

  - name: Enable docker service
    systemd:
      name: docker

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available_, in addition to restricting access_ to the network.
- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_
Load balancers protects the system from DDoS attacks by shifting attack traffic. The advantage of a jump box is to give access to the user from a single node that can be secured and monitored.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the jumpbox_ and system network_.

- _TODO: What does Filebeat watch for?_
Filebeat is a lightweight shipper for forwarding and centralizing log data. Installed as an agent on your servers, Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.

- _TODO: What does Metricbeat record?_
Metricbeat is a lightweight shipper that you can install on your servers to periodically collect metrics from the operating system and from services running on the server. Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    | WebServer| 10.0.0.5   | Linux            |
| Web-2    | WebServer| 10.0.0.7   | Linux            |
| Web-3    | WebServer| 10.0.0.8   | Linux            |
| VM-1     | ElkServer| 10.2.0.5   | Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box_ machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- _TODO: 122.106.117.236_

Machines within the network can only be accessed by _Jump Box_.
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address?_122.106.117.236

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 122.106.117.236      |
| Web-1    | No                  | 10.2.0.5             |
| Web-2    | No                  | 10.2.0.5             |
| Web-3    | No                  | 10.2.0.5             |
| VM-1(Elk)| No                  | 10.2.0.5             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _The primary benefit of Ansible is it allows IT administrators to automate away the drudgery from their daily tasks

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- 1. Create a VM
- 2. Download and configer the container
- 3. Launch and expose the container
- 4. Implement identity and access management

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Images/docker_ps_output.png](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_
- 10.0.0.5 (Web-1)
- 10.0.0.7 (Web-2)
- 10.0.0.8 (Web-3)

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
- Filebeat - used to collects data about the file system
- Metricbeat - used to collects machine metrics data

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook_ file to Ansible Control Node_.
- Update the hosts_ file to include.webserver and elk
- Run the playbook, and navigate to Kibana_ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? /etc/ansible/roles/filebeat-playbook.yml Where do you copy it?_/etc/ansible
- _Which file do you update to make Ansible run the playbook on a specific machine? Yes, in the playbook file filebeat-playbook.yml we specify 'hosts: elk' which means we only run this playbook on the hosts machine [elk] group. How do I specify which machine to install the ELK server on versus which to install Filebeat on?_On the Ansible hosts fille we add [elk] hosts group with machine 10.2.0.5 ansible_python_interpreter=/usr/bin/python3
- _Which URL do you navigate to in order to check that the ELK server is running?
- http://20.36.45.183:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._