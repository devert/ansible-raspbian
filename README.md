# ansible-raspbian
Install and configuration for a Raspberry Pi running Raspbian using Ansible

# Setup
1. Fresh [install](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) of lastest Raspbian on SSD
1. Fire up Raspberry Pi and [enable SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)
    ```
    sudo raspi-config
    ```
1. Grab the [IP address](https://www.raspberrypi.org/documentation/remote-access/ip-address.md) of your Pi on the network
    ```
    hostname -I
    ```
1. [Install Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html) and [Git](https://git-scm.com/book/en/v1/Getting-Started-Installing-Git) on control machine
    ```
    brew install ansible git
    ```
1. Clone this repo to your control machine
    ```
    git clone git@github.com:devert/ansible-raspbian.git
    cd ansible-raspbian
    ```
1. Copy the hosts file and replace `<IP_ADDRESS>` with the IP of your Pi
    ```
    cp templates/hosts.example hosts
    ```
1. Install project dependent roles with [Ansible Galaxy](https://galaxy.ansible.com/)
    ```
    ansible-galaxy install -r requirements.yml
    ```
1. Run the playbook
    ```
    ansible-playbook playbook.yml -i hosts --ask-pass
    ```