# A SSH Key Deploy to Multiple Regions.
[![Builds](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

---
## Description
This is a simple localhost playbook and this playbook works with your AWS IAM user access/secret key. Furthermore, the playbook's actual working is like deploying the same SSH key to all or multiple regions. So, then whether we can use the same key which we used regions so no need to store multiple keys.

---
## Features:
- Easy to Deploy an SSH-Key to multiple regions in AWS
- SSH-Key is creating with the same script.
- Multiple regions were using the same key so we don't store multiple key's
- If your installing ansible along with python2 you don't need to install any dependencies to connect AWS from local

---
## Pre-Requests 
- Install Ansible on your Master Machine (_localhost_)
- Create an IAM user role under your AWS account and please enter the values once the playbook running time
##### Installation
[Ansible2](https://docs.ansible.com/ansible/2.3/index.html) (For your reference visit [How to install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))
##### IAM Role Creation
[IAM Role Creation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)
##### Ansible Modules used
- [yum](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html) 
- [pip](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html)
- [openssh_keypair](https://docs.ansible.com/ansible/latest/collections/community/crypto/openssh_keypair_module.html)
- [shell](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html)
- [ec2-key](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_key_module.html)
---

### How To Use
Ansible Installation article is in the pre-request section so please check out the pre-request section.
```sh
amazon-linux-extras install -y ansible2
yum install git -y
git clone https://github.com/yousafkhamza/deploy-key.git
cd deploy-key
# --------------------------------------
# --- Please-Change-Your-Credentials ---
# --------------------------------------
ansible-playbook main.yml
```
---
## Behind the Playbook.
I just pasted the main.yml 

```sh
---
- name: Creating key on multiple location
  hosts: localhost
  vars_files:
    - cred.vars
  vars:
    key_name: "id_rsa"    <-------------------------- Key Name.
  tasks:
  # ---------------------------------------------------------
  # Lets Start the Task
  # ---------------------------------------------------------
    - name: "Creating New SSH-Kay Meterial"
      run_once: True
      openssh_keypair:
          path: "{{ key_name }}"
          type: rsa
          size: 4096
          state: present

    - name: "Renaming Local Private Key" 
      run_once: True
      delegate_to: localhost
      shell: "mv ./{{ key_name }}  ./{{ key_name }}.pem"
          
    - name: "Creating Key pair"
      ec2_key:
        access_key: "{{access_key}}"
        secret_key: "{{secret_key}}"
        region: "{{item}}"
        name: "{{key_name}}"
        key_material: "{{ lookup('file', './{{key_name}}.pub') }}"
        state: present
      with_items:           <----------------- Please add which you needed regions 
        - ap-south-1
        - us-east-2
        - us-west-2
```
Please change the credential with your AWS IAM Access key.
```sh
access_key: "<Your access key>"
secret_key: "<Your secret key>"
```
---
## Additional Reference 
AWS Region List. [Region List](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.RegionsAndAvailabilityZones.html)

---
# Conclusion
The playbook's actual working is like deploying the same SSH key to all or multiple regions. So, then whether we can use the same key which we used regions so no need to store multiple keys.

_By_
_Yousaf K Hamza_
