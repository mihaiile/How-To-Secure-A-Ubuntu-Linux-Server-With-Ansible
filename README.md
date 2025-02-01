# How To Secure A (Ubuntu) Linux Server With Ansible
Ansible playbooks collection of ["How To Secure A Linux Server"](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server).
This repository is forked from the original [moltenbit/How-To-Secure-A-Linux-Server-With-Ansible](https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible) and adapted towards Ubuntu Linux as that is what was used to test it although probably still applies to many other recent flavours of Linux. On top of that, some of the changes from different forks were added/adapted along with some personal preferences changes. 

These Ansible playbooks are made to help install secure Linux servers faster.

## How to get started
1. Download a Linux ISO (for example from [Ubuntu](https://ubuntu.com/))
2. burn/copy/transfer it to your install medium (e.g. a CD or USB stick) or just plainly use it as source for a VirtualBox installation
3. As part of installation process (available in Ubuntu), you can create already a user and also enable SSH access

From the machine used to connect remotely:
1. Install [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
2. git clone this repository
  ```
  git clone https://github.com/mihaiile/How-To-Secure-A-Ubuntu-Linux-Server-With-Ansible
  cd How-To-Secure-A-Ubuntu-Linux-Server-With-Ansible
  ```
4. [Create SSH-Public/Private-Keys](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server#ssh-publicprivate-keys)
  ```
  ssh-keygen -t ed25519
  ```

5. Change all variables in *group_vars/variables.yml* according to your needs.
7. Recommended: configure static IP address on your system.
8. Add your systems IP address to *hosts.yml*.

&nbsp;

Run the requirements playbook using the user you specified while installing the server:

    ansible-playbook --inventory hosts.yml --ask-pass --ask-become-pass requirements-playbook.yml

&nbsp;

Run the main playbook with the same user above, you specified in the *variables.yml* file:

    ansible-playbook --inventory hosts.yml --ask-pass --ask-become-pass main-playbook.yml

&nbsp;

If you need to run the playbooks multiple times remember to use the SSH key and the new SSH port:

    ansible-playbook --inventory hosts.yml -e ansible_ssh_port=SSH_PORT --key-file /PATH/TO/SSH/KEY main-playbook.yml

&nbsp;

Tested on Ubuntu 24.04.1 LTS.

&nbsp;

## Configurations
The playbook uses most of the settings from ["How To Secure A Linux Server"](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server) / my choices if the guide has more than one option to do something.

### Requirements
- sudo installed

&nbsp;

## Changes made to the original installation after running the ansible playbooks
- groups created for *sshusers*, *sudousers* and *suusers*; user added to groups
- use of sudo limited to sudousers group
- use of su limited to suusers group
- passwordless sudo enabled for the user if specified in the *variables.yml* 
- SSH public key added to authorized_keys file

### Packages
Installed packages are:
- apt-transport-https
- ca-certificates
- host
- kbtin
- ntp
- libpam-pwquality
- unattended-upgrades
- apt-listchanges
- apticron
- ufw
- psad
- fail2ban
- msmtp
- msmtp-mta
- mailutils
- bsd-mailx
- clamav
- clamav-freshclam
- clamav-daemon
- rkhunter
- auditd
- audispd-plugins

### auditd
Uses best practice rules from [Neo23x0](https://github.com/Neo23x0) 

### ClamAV
ClamAV is set to run everyday at 3 AM to scan the full system, exluding sys folders.

### Firewall: UFW
UFW is set to default deny in and out. 
The SSH-Port is set to *limit in*, allowed outgoing ports by default are 53 (DNS), 123 (NTP), 80 (http), 443 (https) and the mail port specified in *variables.yml*.

### Firewall: PSAD and Fail2Ban
PSAD and Fail2Ban is configured according to "How To Secure A Linux Server" guide.

### Lynis
Lynis is configured according to "How To Secure A Linux Server" guide and will run an audit + send the report as an attachment to your mail address configured in *variables.yml*.
Current Lynis rating is 77.

### Mail
For mailing I chose msmtp with the help from [Decatec's guide](https://decatec.de/linux/linux-einfach-e-mails-versenden-mit-msmtp/). This will send a testmail.

### Password quality
Password quality is done via pam_pwquality according to "How To Secure A Linux Server" guide.

### Rkhunter
Rkhunter is configured according to "How To Secure A Linux Server" guide.

### SSH
SSH is configured according to "How To Secure A Linux Server" guide.

### Unattended upgrades
Unattended upgrades is configured to only upgrade security upgrades automatically. Automatic restarts are enabled.

## Plans / ToDos
- [ ] 

## Warning!
Read all tasks carefully and make sure they do not break your system before using these playbooks!
Do not rely solely on the Ansible playbooks for security! This is just a starting point!
It is your responsibility to make sure all settings you need have been set and are working.
Depending on your needs and goals make sure to further secure your system.

## Credits
- [moltenbit/How-To-Secure-A-Linux-Server-With-Ansible](https://github.com/moltenbit/How-To-Secure-A-Linux-Server-With-Ansible) who initially setup the ansible structure
- [imthenachoman](https://github.com/imthenachoman) for creating the great [How To Secure A Linux Server](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server) guide
- [Neo23x0](https://github.com/Neo23x0) for the auditd best practice rules
- [movd/emails_from_ubuntu.md](https://gist.github.com/movd/7a9e3db63d076f85d16c7dcde62fe401) for the guide on setting up mail for Ubuntu
