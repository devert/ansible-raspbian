# ansible-raspbian
Install and configuration for a Raspberry Pi running Raspbian using Ansible

# Setup
1. Fresh [install](https://www.raspberrypi.org/documentation/installation/installing-images/README.md) of lastest Raspbian on SSD
1. Fire up Raspberry Pi and [enable SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)
    ```
    sudo raspi-config
    ```
1. Change the Raspberry Pi SSH password to something other than the default `raspberry`
    ```
    passwd
    ```
1. Grab the [IP address](https://www.raspberrypi.org/documentation/remote-access/ip-address.md) of your Pi on the network
    ```
    hostname -I
    ```
1. [Install Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html) and [Git](https://git-scm.com/book/en/v1/Getting-Started-Installing-Git) on control machine
    ```
    brew install ansible git
    brew install http://git.io/sshpass.rb
    ```
1. Clone this repo to your control machine
    ```
    git clone git@github.com:devert/ansible-raspbian.git
    cd ansible-raspbian
    ```
1. Copy the hosts file and replace `<IP_ADDRESS>` with the IP of your Pi and `<SSH_PASSWORD>` to what you changed it to earlier
    ```
    cp templates/hosts.example hosts
    ```
1. Install project dependent roles with [Ansible Galaxy](https://galaxy.ansible.com/)
    ```
    ansible-galaxy install -r requirements.yml
    ```
1. Run the playbook
    ```
    ansible-playbook playbook.yml -i hosts
    ```

# VNC Setup
1. `sudo raspi-config` > Interface Options > VNC > Enable. Reboot.

2. Generate the password you wish to use for VNC sessions with `vncpasswd -print`
    ```
    vncpasswd -print

    Password:
    Verify:
    Password=40cca9718ffb8e91d55cf290c3db86d5
    ```

3. Copy the output of that command (eg. `Password=40cca9718ffb8e91d55cf290c3db86d5`) for the config file.

4. Create and edit the following file here: `/etc/vnc/config.d/common.custom`. Enter the following config:
    ```
    Encryption=PreferOn
    Authentication=VncAuth
    Password=40cca9718ffb8e91d55cf290c3db86d5
    ```

5. Restart the vnc service:
    ```
    sudo systemctl restart vncserver-x11-serviced
    ```

6. In OSX Finder `Go > Connect to Server` and enter address `vnc://pi@<IP_ADDRESS>:5900`. Use the password you provided to `vncpasswd` to login.

# Plex Setup

1. Update and upgrade everything
    ```
    sudo apt-get update
    sudo apt-get upgrade
    ```

2. Ensure you have the HTTPs transport package

    ```
    sudo apt-get install apt-transport-https
    ```

3. Add the dev2day repository to your package source list.

    ```
    wget -O - https://dev2day.de/pms/dev2day-pms.gpg.key | sudo apt-key add -
    echo "deb https://dev2day.de/pms/ stretch main" | sudo tee /etc/apt/sources.list.d/pms.list
    ```

4. Update the package list again.

    ```
    sudo apt-get update
    ```

5. Install Plex Service Installer

    ```
    sudo apt-get install -t stretch plexmediaserver-installer
    ```

6. Change some permissions

    ```
    # Change to PLEX_MEDIA_SERVER_USER=pi
    sudo nano /etc/default/plexmediaserver.prev

    # Change ownership of /var/lib/plexmediaserver if necessary
    chown -R plex:plex /var/lib/plexmediaserver

    # Restart plexmediaserver
    sudo service plexmediaserver restart
    sudo service plexmediaserver status
    ```

7. Ensure Pi has a static IP

    ```
    hostname -I
    # Add ip=<IP_ADDRESS> to the bottom of this file
    sudo nano /boot/cmdline.txt
    ```

8. Reboot with `sudo reboot`

9. Go to `localhost:32400/web` or `<IP_ADDRESS>:32400/web` to setup Plex Media Server