# BASICS

## ANSIBLE (2.0) INSTALLATION FROM SOURCE:

	[root] cd /opt

	[root] git clone git://github.com/ansible/ansible.git --recursive

	[root] cd ./ansible

	[root] source ./hacking/env-setup

	[root] pip install paramiko PyYAML Jinja2 httplib2 six


## UPDATE FROM SOURCE:

	[root] cd /opt/ansible

	[root] git pull --rebase

	[root] git submodule update --init --recursive


## MACHINES INVENTORY:

	$ echo "127.0.0.1" > ~/ansible_hosts

	$ export ANSIBLE_INVENTORY=~/ansible_hosts

	$ echo "export ANSIBLE_INVENTORY=~/ansible_hosts" >> ~/.bashrc


## CHECK CONNECTION TO THE REMOTE MACHINES:

	$ ansible all -m ping

	$ ansible mt-scrapy -m ping

Note:  If you want to specify a different inventory file, specify it through "-i". E.g.:

	$ ansible -i ~/ansible_hosts all -m ping


## E.G. OF AN INVENTORY FILE (with IPs)

	$ vim ~/ansible_hosts


```
# NOTES:
# 1) As a private key, you can add a normal ssh key (e.g., "id_rsa"), or a ".pem" key (used e.g. by Amazon AWS).
#
# 2) When adding new machines below, make sure to manually ssh into them the first time (for the trust to be established).
#     E.g.:
#         $ ssh -i ~/.ssh/precifica-NV-crawler.pem centos@172.16.0.215

# [localhost]
# 127.0.0.1

[mt-scrapy]
172.16.0.216  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.22  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.157  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.171  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.166  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.170  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.31  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.6  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.43  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.20  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.15  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
172.16.0.215  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
```


## E.G. OF AN INVENTORY FILE (with alias to IPs)

	$ vim ~/ansible_hosts


```
# NOTES:
# 1) As a private key, you can add a normal ssh key (e.g., "id_rsa"), or a ".pem" key (used e.g. by Amazon AWS).
#
# 2) When adding new machines below, make sure to manually ssh into them the first time (for the trust to be established).
#     E.g.:
#         $ ssh -i ~/.ssh/precifica-NV-crawler.pem centos@172.16.0.215
#

# [localhost]
# 127.0.0.1

[mt-scrapy-active]
scrapy-americanas ansible_ssh_host=172.16.0.216  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-extra ansible_ssh_host=172.16.0.22  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-others01 ansible_ssh_host=172.16.0.157  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-others02 ansible_ssh_host=172.16.0.171  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-others03 ansible_ssh_host=172.16.0.166  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-others04 ansible_ssh_host=172.16.0.170  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-pneus01 ansible_ssh_host=172.16.0.31  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-pneus02 ansible_ssh_host=172.16.0.6  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-pontofrio ansible_ssh_host=172.16.0.43  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-ricardo_eletro ansible_ssh_host=172.16.0.20  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-shoptime ansible_ssh_host=172.16.0.15  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
scrapy-submarino ansible_ssh_host=172.16.0.215  ansible_connection=ssh ansible_ssh_private_key_file=/home/centos/.ssh/precifica-NV-crawler.pem
```


## RUN A COMMAND ON A GROUP OF MACHINES:

	$ ansible mt-scrapy -m shell -a 'echo $TERM'

	$ ansible mt-scrapy -m shell -a 'sudo wc -l /opt/precifica/precifica-scrapy/scraped/*scraped.csv'

(Attention: single and double quotes matter on that case). Use exactly as above.
It can also handle running as another user, sudo, etc. Reference: http://docs.ansible.com/ansible/intro_adhoc.html#parallelism-and-shell-commands

## COPYING FILES TO A GROUP:

### Simple transfer:

	$ ansible mt-scrapy -m copy -a "src=/etc/hosts dest=/tmp/hosts"

### The "file" module: dealing with groups, permissions, recursively create and delete

	$ ansible mt-scrapy -m file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"

#### Below is equivalent to "mkdir -p":

	$ ansible mt-scrapy -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"

#### Remove a directory recursively:

	$ ansible mt-scrapy -m file -a "dest=/path/to/c state=absent"
