# Ansible: Let's Encrypt LAN `example`
Uses [Ansible Role: Let's Encrypt private networks](https://github.com/rembik/ansible-role-letsencrypt) to:
* **Setup** Let's Encrypt ACME client on local Debian/Ubuntu Linux server `control.example.com` and sign/renew **certificate**s.
* **Deploy** them to local Linux or Windows proxy-/webservers: `utm.example.com`, `nginx.example.com`, `msx.example.com` and `iis.example.com`.
* **Automate** certificate cycle on local Debian/Ubuntu Linux server `control.example.com` as Ansible Control Machine.

## Requirements
* Ansible >= 2.3, ([Docs](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-via-apt-ubuntu))
* [`letsencrypt` role requirements](https://github.com/rembik/ansible-role-letsencrypt#requirements) 

#### Pre-setup check
* Make sure all Windows servers are pre-configured for remote management with Ansible ([Wiki](https://github.com/rembik/ansible-letsencrypt-example/wiki/Configure-Windows-Server)).
* Make sure Sophos UTM is pre-configured for Ansible Role: Let's Encrypt ([Wiki](https://github.com/rembik/ansible-letsencrypt-example/wiki/Configure-Sophos-UTM)).
* Make sure Windows Server which running Spiceworks Network Monitor is pre-configured ([Wiki](https://github.com/rembik/ansible-letsencrypt-example/wiki/Configure-Spiceworks-Monitor)).

## Setup
```
$ sudo su
mkdir /opt/letsencrypt
cd /opt/letsencrypt
mkdir certs
chmod 0700 certs
git clone --recursive https://github.com/rembik/ansible-letsencrypt-example.git provision
cd provision
```
After getting repository and setting local certificate-exchange directory, create a file which contains your Ansible vault password and create an `vault` file for every needed `vars` file ([Docs](http://docs.ansible.com/ansible/playbooks_vault.html)).
```
$ touch .vault
chmod 0600 .vault
$ ansible-vault create /host_vars/control_example_com/vault
$ ansible vault create ...
```
Run the playbook to setup the Let's Encrypt ACME clients and sign/renew all certificates: 
```
$ ansible-playbook letsencrypt.yml --vault-password-file .vault -t setup,certificate
```
Now initial import the certificates which should be deployed to Sophos UTM and get there needed references `ref` and `ref_ca` (see [Wiki](https://github.com/rembik/ansible-letsencrypt-example/wiki/Configure-Sophos-UTM#4-get-certificate-references)). 
Finally, when the config of all the inventory files is completed, run the certificate deployment process with the playbook: 
```
$ ansible-playbook letsencrypt.yml --vault-password-file .vault -t deploy
```
After initial plays the automated certificate cycle for your private network is finished.

## Troubleshooting
See [ansible-letsencrypt-example Wiki](https://github.com/rembik/ansible-letsencrypt-example/wiki#troubleshooting) for troubleshooting.
