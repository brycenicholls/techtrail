---
hide:
  - navigation
---

# INSTALLING THE OPENSTACK CLIENT

In order to interact with the openstack API you will need to install the client.
In this example, I am going to install the client in a python virtual environment. This gives me the option of installing and switching between different versions of the client.

## PREREQUISITES

!!! Warning
    The openstack client only supports python 2.7 at the moment.

```title="Install python and pip on centos"

sudo yum -y install epel-release
sudo yum -y install python python-devel python-pip gcc
sudo pip install --upgrade pip
sudo pip install virtualenv
```

Once the packages are installed, create a folder for your virtual environments.
I am naming the folder ‘venvs’ and changing into that directory.

```bash
mkdir venvs && cd venvs
```

### Create the virtual environment

```bash
[centos@osp-client venvs]$ python -m virtualenv openstack_client
  No LICENSE.txt / LICENSE found in source
New python executable in /home/centos/venvs/openstack_client/bin/python2
Also creating executable in /home/centos/venvs/openstack_client/bin/python
Installing setuptools, pip, wheel...
done.
[centos@osp-client venvs]$ ll
total 0
drwxrwxr-x. 5 centos centos 56 Jan 28 12:47 openstack_client
```

Activate the virtual environment by running the below command.
Notice that the prompt changes once activated. I will create a separate post on how you can configure your bash and zsh environments to give you more features and functionality.

```bash
[centos@osp-client venvs]$ source openstack_client/bin/activate
(openstack_client) [centos@osp-client venvs]$
```

### Install the all-in-one client

```python
(openstack_client) [centos@osp-client venvs]$ pip install python-openstackclient
To deactivate the virtual environment just type ‘deactivate’.
```

You will notice that the prompt changes again once the virtual environment is deactivated.

```bash
(openstack_client) [centos@osp-client venvs]$ deactivate
[centos@osp-client venvs]$
```

Using the above process, you can create multiple virtual environments and install different versions of the client as per your requirements.
