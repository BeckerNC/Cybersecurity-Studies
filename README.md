## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://github.com/BeckerNC/Cybersecurity-Studies/blob/main/Diagrams/Azure-ELK

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Playbook file may be used to install only certain pieces of it, such as Filebeat.

  - pentest.yml
  https://github.com/BeckerNC/Cybersecurity-Studies/blob/main/Ansible/pentest.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- A load balancer increases availability and capacity by evenly distributing user loads, as well as protectecting the network from DDoS (Distribbutted Denial of Service) attacks. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system information.
- Filebeat collates, ships and centralises Log data, which can be viewed in Kibana (Via Elasticsearch) for easy analyses oss specific log types (eg: Failed Logins, etc)
- Metricbeat is used to collect metric data from target servers, such as CPU or memory data used by the services on a VM. It can be also be used to monitor ELK stacks or other beats.

The configuration details of each machine may be found below.


| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    | DVWA Container         | 10.0.0.5     |       Linux           |
| Web-2    | DVWA Container          | 10.0.0.6           | Linux                 |
| Web-3    | DVWA Container         |  10.0.0.7          | Linux                 |
| ELK-Server| ELK Monitoring | 10.1.0.4 | Linux
### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 218.215.247.105 (My IP)

Machines within the network can only be accessed by the Jumpbox.
- The Jumbox's IP is 52.226.119.128

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes              |     218.215.247.105|
| Web-1         |No                     | 10.0.0.4                     |
|     Web-2     | No                    | 10.0.0.4
Web-3                     |No | 10.0.0.4
|ELK-Server | Yes | 10.0.0.4  20.70.32.50:5601

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- We can configure multiple containers/virtual machines consistently and quickly. It also reduces the chance of human error.

The playbook implements the following tasks:
- First, be sure to add your VM to the Ansible hosts file
- Create a new playbook called ELK.yml. Add the below commands: 
 (Add --- in the first line)
- name: Config elk VM with Docker
  hosts: ELK
  remote_user: azdmin
  become: true
  tasks:

  - name: Use more memory
    sysctl:
     name: vm.max_map_count
     value: '262144'

  - name: docker.io
    apt:
     update_cache: yes
     name: docker.io
     state: present

  - name: Install pip3
    apt:
     force_apt_get: yes
     name: python3-pip
     state: present

  - name: Install Python Docker module
    pip:
     name: docker
     state: present

  - name: download and launch a docker web container
    docker_container:
     name: elk
     image: sebp/elk:761
     state: started
     restart_policy: always
     published_ports:
            - 5601:5601
            - 9200:9200
            - 5044:5044

  - name: Enable docker service
    systemd:
     name: docker
     enabled: yes
- After running the playbook SSH to your ELK container and run 'sudo docker ps' to double check your ELK docker is running. 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance:

https://github.com/BeckerNC/Cybersecurity-Studies/blob/main/Diagrams/docker-ps.png

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1: 10.0.0.5
- Web-2: 10.0.0.6
- Web-3: 10.0.0.7 

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat colllects Log data with wide range of information. When veiwed through Kibana it is a quick and easy way to track important Log information, such as succesful or failed login attempts.
- Metricbeat allows you to monitor a range of information relating to CPU and RAM usage on a target server, as well as network traffic. Usseful for spotting spikes in demand on resources that may indicate a bad actor at work.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the .yml file to /etc/ansible.
- Update the /ansible/hosts file to include the IP of the container
- Run the playbook, and navigate to sudo docker ps to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- The playbook will always end in .yml. Eg: ELK.yml
- Update the hosts file in Ansible to include the IP's of any machine you want to install the playbook on. If you want to install certain applications on a machine you can change the configure files (eg metricbeat-config.yml) to include specific IP's.  
- You can check your ELK server is running by visiting http://[your.VM.IP]:5601/app/kibana in your browser. 

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._