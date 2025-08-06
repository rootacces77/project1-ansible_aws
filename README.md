This project creates 3 servers.
Bastion server ( Ubuntu ) with reverse proxy that forwards connections to webserver.
It has Nginx,Suricata,Fail2ban,AppArrmor,Firewall,Aide installed and set up.

Webserver ( RedHat ) has some basic web application set up which I found online (Its not working fully but it does the job).
Application is hosted with httpd and connects to database.

Database ( RedHat ) server has mariadb set up with sakila database.

Connection is done with selfsigned certificates.Both webserver and database server have firewall,selinux and sysctl set up. 

Step 1.
It is assumed that you have 4 updated virtual machines with two interfaces.One with internet connection and one Internal Network
One machine is controller on which you have ansible installed and set up
Database virutal machine has to have storage device with minimum of 15G size
Bastion server uses Ubuntu.Webserver and Database server use RedHat ( with subscription-manager set up ) 
You have to have root access to these machines with ssh key 

Step 1.
On controller machine set up python virtual environment with ansible-builder and ansible-navigator installed.
Build an image using ansible-builder located in ansible-ee
ansible-builder build --tag project1-env:1.0 --container-runtime docker -f execution-environment.yaml
You will also have to change ansible-navigator.yaml to use container-engine specified and image name

Step 3.
Open inventory/hosts file and change ip addresses with Internal Network addresses.
Open plabooks/vars/main.yaml.You can modify ports,user names etc.
Required changes in playbooks/vars/main.yaml are 
db_device_path: "/dev/sdb"   -> name of the database storage device 
bastion_pub_iface: "enp0s3"  -> bastion public interface
bastion_priv_iface: "enp0s8" -> bastion private interface
database_ip: '172.16.0.7'    ->
webserver_ip: '172.16.0.3'   -> Your private network ip addresses
bastion_ip: '172.16.0.2'     ->

Step 4.
Use ansible-navigator on cotroller node to execute playbooks/main.yaml ( you have to be in root of project dir )
ansible-navigator run plabooks/main.yaml

After ansible finishes you will only be able to log in to servers using user specified in playbook/vars/main.yaml with key ./project-key
You will only be albe to access webserver and database through bastion server.Also website will only be available through bastion server

#Things to do:
Sysctl
Suricata
Youtube


README
TASK [storage-role : Create Logical Volume] *********************************************************************************************
fatal: [ip-10-0-3-201.eu-north-1.compute.internal]: FAILED! => {"changed": false, "err": "  Not enough free memory for VDO target. 449.00 MiB RAM is required, but only 378.00 MiB RAM is available.\n", "msg": "Creating logical volume 'database-vdo' failed", "rc": 5}


ansible-ee
