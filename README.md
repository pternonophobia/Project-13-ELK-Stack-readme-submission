## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Network Diagram](~/Project-13-ELK-Stack-readme-submission/Diagram/Project 13 Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the **_YML and config_** file may be used to install only certain pieces of it, such as Filebeat.

  - _filebeat-playbook.yml_
  - _install-elk.yml_

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be **_highly Functional and available_**, in addition to restricting **_traffic_** to the network.
- _What aspect of security do load balancers protect? What is the advantage of a jump box?_
  - **_Load Balancers make the network more resilient by routing traffic elswhere when the server is unavailable or unusable._**

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the **_network_** and system **_system logs_**.
- _What does Filebeat watch for?_
  - **_Filebeat monitors log files and specific locations, collects data, and sends data off to be indexed in either Elasticsearch or Logstash._**
- _What does Metricbeat record?_
  - **_Metricbeat records metrics and statistics and pushes them to a specific, pre-set output._**

The configuration details of each machine may be found below.

| **_Name_** | **_Function_** | **_IP Address_** | **_Operating System_** |
|------------|----------------|------------------|------------------------|
| Jump Box   | Provisioner    | 10.0.0.4         | Linux Ubuntu 18.04     |
| Web-1      | Web Server     | 10.0.0.5         | Linux Ubuntu 18.04     |
| Web-2      | Web Server     | 10.0.0.6         | Linux Ubuntu 18.04     |
| Web-3      | Web Server     | 10.0.0.7         | Linux Ubuntu 18.04     |
| ELK server | Monitoring     | 10.1.0.4         | Linux Ubuntu 18.04     |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the **_Jump-Box Provisioner_** machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- **_Workstation Public IP through TCP 5601, IP: 70.113.32.23_**

Machines within the network can only be accessed by **_Your personal workstation and Jump-Box Provisioner through a SSH to the Jump-Box_**.
- _Which machine did you allow to access your ELK VM? What was its IP address?_
  - **_Jump-Box Provisioner IP:  10.0.0.4 via SSH port 22._**


A summary of the access policies in place can be found in the table below.

| **_Name_** | **_Publicly Accessible_** | **_Allowed IP addresses_**    |
|------------|---------------------------|-------------------------------|
| Jump Box   | YES                       | 70.113.32.23 (Workstation IP) |
| Web-1      | NO                        | 10.1.0.4 via SSH port 22      |
| Web-2      | NO                        | 10.1.0.4 via SSH port 22      |
| Web-3      | NO                        | 10.1.0.4 via SSH port 22      |
| ELK server | NO                        | Workstation IP via TCP 5601   |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _What is the main advantage of automating configuration with Ansible?_
  - **_Although there are multiple advantages, the most obvious is the ability to quickly and easily deploy YAML playbooks, containing multi-tier applications._**
  - **_ You also dont need to write custom coding to automate your system features._**

The playbook implements the following tasks:
- _In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Specify a different group of machines:
      ```yaml
        - name: Config elk VM with Docker
          hosts: elk
          become: true
          tasks:
      ```
  - Install Docker.io
      ```yaml
        - name: Install docker.io
          apt:
            update_cache: yes
            force_apt_get: yes
            name: docker.io
            state: present
      ``` 
  - Install Python-pip
      ```yaml
        - name: Install python3-pip
          apt:
            force_apt_get: yes
            name: python3-pip
            state: present

          # Use pip module (It will default to pip3)
        - name: Install Docker module
          pip:
            name: docker
            state: present
            `docker`, which is the Docker Python pip module.
      ``` 
  - Increase Virtual Memory
      ```yaml
       - name: Use more memory
         sysctl:
           name: vm.max_map_count
           value: '262144'
           state: present
           reload: yes
      ```
  - Download and Launch ELK Docker Container (image sebp/elk)
      ```yaml
       - name: Download and launch a docker elk container
         docker_container:
           name: elk
           image: sebp/elk:761
           state: started
           restart_policy: always
      ```
  - Published ports 5044, 5601 and 9200 were made available
      ```yaml
           published_ports:
             -  5601:5601
             -  9200:9200
             -  5044:5044   
      ```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

! **_Screenshots were lost when my laptop broke, was unable to re-aquire pictures/screenshots_**

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _Web-1: 10.1.0.5_
- _Web-2: 10.1.0.6_
- _Web-3: 10.1.0.7_

We have installed the following Beats on these machines:
- _Filebeat & Metricbeat_

These Beats allow us to collect the following information from each machine:
- _Filebeat will be used to collect selected log files from other files such as Apache, MySQL Databases, and Microsoft Azure web services and tools._
- _Metricbeat is used to monitor Virtual Machine Statistics, such as CPU stats, filesystem stats, and memory._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the **_YAML_** file to **_ansible folder_**.
- Update the **_Config_** file to include **_remote users and ports._**
- Run the playbook, and navigate to **_Kibana ((70.113.32.23):5601_) to check that the installation worked as expected.

- _Which file is the playbook? Where do you copy it?_
  - **_there are two playbooks, one being filebeat_config.yml is copied to /etc/ansible, the other being Metricbeat-config.yml also copied to /etc/ansible_**
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
  - **_Update both config files to include the ELK private IP address 10.2.0.4 using nano /etc/ansible/metricbeat.yml and filebeat.yml_**
- _Which URL do you navigate to in order to check that the ELK server is running?
  - **_http://70.113.32.23:5601/app/kibana_**
