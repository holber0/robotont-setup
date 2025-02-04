# Setting up software for Robotont
## Prerequisites:
    * Robotont
    * Mouse
    * Empty memory stick (at least 8Gb)
    * Keyboard
    * Internet connection (WiFi or cable)
## Installation instructions:
1) Create ubuntu installation memory stick and plug it into the computer on the robot
2) Power on the computer and enter the boot menu (for Intel NUC press F10 on the keyoard)
3) In the appeared menu select the boot device: USB
4) Install ubuntu:
   * Connect to the internet over WIFI or LAN
   * Setup your username as **peko**. If you want to use a different one also follow the additional step after step 6
   * Create a password and don't forget it
   * Follow installation steps
5) Download installation repository on the robot. For that open terminal with **Ctrl+Alt+T** and paste there line by line: 
```
sudo apt install git
```
```
git clone https://github.com/robotont/robotont-setup
```
6) Adjust the file **hosts** in the ansible folder of the repository. Repository is the **robotont-setup** folder you've downloaded with a command. Edit the second line and enter the password of the access point you would like to have **wifi_pass=** ***your_password***. The robot will become a WiFi access point after configuration is finished with the password specified by you.
   * Additional step for custom username(if you are using peko skip it). Modify the first line in hosts file **ansible_user=peko** and replace peko with your username
   * You must also specify the WiFi interface used for the access point. to find out which interfaces are available, use the following command.

      ```
      nmcli --get-values GENERAL.DEVICE,GENERAL.TYPE device show
      ```
      replace the wifi_interface=wlo1 with the interface name you want to use.
   
7) Run the following command to install ansible:
<!-- ```
sudo apt install python3-pip
```
```
python3 -m pip install --user ansible
``` -->
```
sudo apt install ansible
```
8) Change the terminal working directory to the one containing the configuration files:
```
cd robotont-setup/ansible
```
9) Run ansible and enter user password in the BECOME field when it appears:
```
ansible-playbook robots-local.yaml -K
```
10) If there is an error in the last reboot step then reboot manually and your robot is ready.
### Truobleshooting:
  * If there is an error at some point repeat step 9
  * If the computer is frozen then reboot the computer and then open the terminal with **Ctrl+Alt+T** repeat steps 8-9 but first
  * If there's an error on the Apply connection step run:
  ```
  nmcli connection up netplan-wlp58s0-robotont-1
  ```
  * If there is an error activating or starting the Wifi Check if the Ubuntu desktop Manager if the Wifi Interface is enabled. The Ansible installation also installs on a server installation a minimal Desktop environment. 
# Laptop setup: 2 laptops and two memory sticks are needed
#### Boot linux in trial mode from the installation memory stick
#### Then copy scripts from main laptop to the one which installs ubuntu
```
scp -r ~/robotont-setup/partitioning/* ubuntu@<ip addr>:.
```
#### Then first run partitioning
It prompts for disk name. If something fails unplug target memory stick and plug back again.
```
sudo ./pa
```
#### After that install ubuntu and select not to reboot
During installation, when the Installation type window comes up choose Something else, then choose your device, change its type to Ext4 journaling file system with mount point /. In the same window for Device for boot loader installation field choose the same device.
#### And then run copying script after which it can be rebooted
```
sudo ./copying
```
### After that run ansible for laptops(with the correct laptop ip in hosts file)
```
ansible-playbook laptops.yaml -K
```

## SSH part outdated
### Installing ssh on robot
```
sudo apt install -y openssh-server
```
### Generating keys
```
ssh-keygen -t ed25519 -f ~/.ssh/robotont_ed25519
```
### Copying ssh keys
```
ssh-copy-id -i ~/.ssh/robotont_ed25519 peko@[ip_address]
```
### First configure ip in hosts file and then run
```
ansible-playbook robots.yaml -K
```


# Old robotont-setup
Scripts and other stuff that does not belong to the catkin workspace

# How to update robotont

## Connecting to internet
Before updating we need to connect a LAN cable to the robot.
Open a terminal and ssh into robot. Use the command in form of
```bash
ssh <USERNAME>@<ROBOT_IP_ADDRESS>
```
For example:
```bash
ssh clearbot@192.168.200.1
```

Obtain an IP address for the LAN interface:
```bash
sudo dhclient -v
```

Check for connection:
```bash
ping google.com
```

## Updating the system 
To update all ubuntu packages run:
```bash
sudo apt update
sudo apt upgrade -y
```
## Getting the latest catkin workspace for the robot
Make a backup of the existing workspace:
```bash
mv ~/catkin_ws ~/backup_ws
```

Create a new workspace
```bash
mkdir -p catkin_ws/src
cd catkin_ws
catkin build
```
Download and build all the latest robotont ROS packages for the robot.

```bash
cd ~/catkin_ws
wstool init src https://raw.githubusercontent.com/robotont/robotont-setup/melodic-devel/ansible/resources/.rosinstall
catkin build
```



