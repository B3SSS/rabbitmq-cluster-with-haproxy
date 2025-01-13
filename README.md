# RabbitMQ cluster (3 hosts) with HAProxy and without DNS server

### System
1. CPU - 4
2. Memory - 4Gb
3. Disk - 30Gb 

### Strategy
1. Create user with name "ansible" and set administrator role for him
```bash
# sudo su
# vi /etc/sudoers.d/ansible
ansible  ALL=(ALL:ALL)  NOPASSWD: ALL
```
2. Rename your virtual machines. 
For example: rabbitmq-1, rabbitmq-2, rabbitmq-3, haproxy-server. 
```bash
sudo hostnamectl set-hostname rabbitmq-1
sudo systemctl restart systemd-hostnamed
sudo reboot
```
3. Change source for YUM repositories (only for CentOS 7)
```bash
sudo sed -i s/mirror.centos.org/vault.centos.org/g /etc/yum.repos.d/CentOS*.repo
sudo sed -i s/^#.*baseurl=http/baseurl=http/g /etc/yum.repos.d/CentOS*.repo
sudo sed -i s/^mirrorlist=http/#mirrorlist=http/g /etc/yum.repos.d/CentOS*.repo
sudo yum update -y
```
4. Create and copy SSH public key to hosts
```bash
# Create SSH key
ssh-keygen -t rsa
# Copy SSH public key
ssh-copy-id -i ~/.ssh/id_rsa ansible@ip-address-rabbitmq-1
ssh-copy-id -i ~/.ssh/id_rsa ansible@ip-address-rabbitmq-2
ssh-copy-id -i ~/.ssh/id_rsa ansible@ip-address-rabbitmq-3
ssh-copy-id -i ~/.ssh/id_rsa ansible@ip-address-rabbitmq-haproxy
```
5. Rename file "hosts_example.yaml" and change IP addresses for all hosts and username in hosts.yaml
```
1. ansible_host: 192.168.22.2 -> ansible_host: <your-ip-address>
2. ansible_ssh_private_key_file: /home/user/.ssh/id_rsa -> ansible_ssh_private_key_file: /home/<your-username>/.ssh/id_rsa
```
6. Create a virtual environment for Python in the root directory
```bash
python3 -m venv .venv
source .venv/bin/activate
pip3 install -r requirements.txt
```
7. Start playbook file
```bash
ansible-playbook playbook.yaml

# If want to check host states
# ansible-playbook playbook.yaml -CD
```
8. Enjoy!

