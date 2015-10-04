# OWASP WebGoat Ansible playbook(Vagrant, Azure)
To use this playbook you need to have installed [Ansible](http://docs.ansible.com/ansible/intro_installation.html).

## Install OWASP WebGoat on the VirtualBox with Vagrant

You need to install [vagrant](http://www.vagrantup.com/downloads)and [virtualbox](https://www.virtualbox.org/wiki/Downloads)

Then just go into project directory and run:
```
vagrant up
```

After everything is ready, you should be able to see WebGoat's webpage by the following link: http://192.168.1.100/webgoat

## Install OWASP WebGoat on the Azure VM
In this guide we are assuming that you are already have valid Azure subscription.

In order to start launching and provision Azure VM you need to do next preparations:

* Install ```sudo pip install azure==0.11.1``` (newest azure version > 1.0 not compatible with ansible azure module yet. Here is relevant github issue https://github.com/ansible/ansible-modules-core/pull/2114)

* Install Azure Command Line tools
```
yum install npm ## or apt-get install npm
npm install azurecli
```

* Login into your azure subscription:
   * Generate link for Azure Subscription Key
   
```azure account download```
   * Open link in Web Browser and download key

   * Import key via Azure cli
```
azure account import <path to downloaded file>
```

* Now we need to create valid Storage Account for our VM:
```
azure storage account create --location "East US 2" webgoatlab --type GRS
```
Location of storage account and VM should be the same.

* Generate ssh key for VM
```
openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out ~/.ssh/webgoatlab.pem
```

* Give owner-only permissions for generated key
```
chmod 600 ~/.ssh/webgoatlab.pem
```

* Generate management certificate from existing ssh key:
```
openssl x509 -inform pem -in ~/.ssh/webgoatlab.pem -outform der -out ~/.ssh/manage.cer
```

* Export generated Management Certificate to the azure account:
```
azure account cert export -f ~/.ssh/manage.cer
```

* Set environment variables with your Azure Subscription ID and Path to Management Certificate
```
export AZURE_SUBSCRIPTION_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CERT_PATH=~/.ssh/manage.cer
export ANSIBLE_HOST_KEY_CHECKING=False
```

* Run playbook with command:
```
ansible-playbook -i "localhost," azure_goat.yml
```
