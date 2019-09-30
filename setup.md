# Instruction manual to set up a newly flashed Neutis N5
The username and password for the first time log in are username: **root**, password: **root**
After you login, you will be prompted to change your username and password.

The current password in your Neutis is : `rootneutis`

## Add New User
Proceed to add a new user by running the command

Add new user using the `adduser` command
~~~
adduser <name of new user>
~~~
**NOTE**: You can leave the room number , work phone, home phone fields, etc blank.


Use the `usermod` command to add the user to the `sudo` group.
~~~
usermod -aG sudo <username>
~~~
Then switch to the new user using the `su` command
```
su - <username>
```
**NOTE:** After you have switched to the new user, it is impossible to switch back to root user.(Idk why)
if you receive this error :
>sudo must be owned by uid 0 and have the setuid bit 

It means`sudo` was not run with root privileges. The `sudo` binary does not have the correct owner or permissions. It must be owned by the root user and have the Set-user-ID bit set. Then do this:

```
chown root:root /usr/bin/sudo && chmod 4755 /usr/bin/sudo
```
## Connect to WIFI:
Run this command
```
nmtui
```
![enter image description here](https://i.stack.imgur.com/Cmyo5.png)
Press `Activate Connection`and you will see the page above. Select the wifi network you want and enter the password. TADA it is done.

## Using multiple terminals without GUI
use `alt`+ `<any F1 to F12>`to have multiple terminals


##  Install ROS
Follow the instructions on ROS website. Install `ros-kinetic-desktop` version. We use this version because we want the robot-generic libraries.

## Install MAVROS
```
sudo apt-get install ros-kinetic-mavros ros-kinetic-mavros-extras
```
Then install [GeographicLib](https://geographiclib.sourceforge.io/) datasets by running the `install_geographiclib_datasets.sh` script:
```
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
./install_geographiclib_datasets.sh
```
##  Use SSH for convenience
Because there is no GUI in Neutis, you are not able to copy and paste commands from websites. A workaround would be to SSH into Neutis from another Ubuntu machine.
### Installation
But first you have to install SSH server in Neutis,
~~~
sudo apt install -y openssh-server
~~~
### SSH
Then you can SSH into Neutis using:
```
ssh {username in Neutis}@{ip address of Neutis}
```
*Example*: `ssh performancerotors@192.168.1.103`

You will be prompted to enter the password.

### Transferring files between client and remote
```
scp <source> <destination>

```

To copy a file from  `B`  to  `A`  while logged into  `B`:

```
scp /path/to/file username@a:/path/to/destination

```

To copy a file from  `B`  to  `A`  while logged into  `A`:

```
scp username@b:/path/to/file /path/to/destination
```
### Cannot set LC_CTYPE to default locale: No such file or directory 
You may encounter errors like this when using SSH
>perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
        LANGUAGE = (unset),
        LC_ALL = (unset),
        LC_TIME = "zh_CN.UTF-8",
        LC_MONETARY = "zh_CN.UTF-8",
        LC_ADDRESS = "zh_CN.UTF-8",
        LC_TELEPHONE = "zh_CN.UTF-8",
        LC_NAME = "zh_CN.UTF-8",
        LC_MEASUREMENT = "zh_CN.UTF-8",
        LC_IDENTIFICATION = "zh_CN.UTF-8",
        LC_NUMERIC = "zh_CN.UTF-8",
        LC_PAPER = "zh_CN.UTF-8",
        LANG = "en_US.UTF-8"
        are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory

This is because your SSH client forwards your locale environment variables from your local computer to the remote Linux server which doesn’t have the needed locale generated.

We can fix this by disabling SSH locale environment variable forwarding to fix this error. Open the SSH client configuration file on your local computer.
```
sudo nano /etc/ssh/ssh_config
```
Find this line:
```
SendEnv LANG LC_*
```
Add a `#` sign at the beginning to comment it out. Save and close the file.
## Install screen
When you SSH into Neutis, it is more convenient if you are able to open multiple terminals in the same session. `screen` allows you to do that

Start by installing `screen` in Neutis:
```
sudo apt-get install screen
```
### How to use
After you have SSH -ed into Neutis, enter this command:
```
screen
```
* To open a new shell within the same session, press **Ctrl + A ** followed by **Ctrl+ C**  
* To switch between two windows quickly, use **Ctrl+A** followed by **Ctrl+A** again  
**Ctrl+A, Ctrl+D** will detach from the screen session. 
* You can disconnect from the host and log on again later and use **screen -r** to resume your session.  
* To go to a specific screen window, press **Ctrl+A**  then wait for while and press a number (It won't work if you press Ctrl , A and the number at the same time). Screen will exit when all active windows are closed (or the shells within have exited).

## Install usbutils
You need to install usbutils to be able to use the command `lsusb`
```
sudo apt-get install usbutils
```
## Install usb_cam ROS package & image_common ROS packages
The following driver `usb_cam` is required to operate the logitech c170 webcam:
### Install usb_cam
```
cd catkin_ws/src
git clone https://github.com/ros-drivers/usb_cam.git
catkin build
```
### Install image_common
image-common stack is required for `usb_cam` to run:
```
sudo apt-get install ros-kinetic-image-common
```
### Testing the package
```
roslaunch usb_cam usb_cam-test.launch
```
**Edit the launch file's `video_device` parameter according to what `/dev/video*` path your camera is using. eg. /dev/video0 or /dev/video1


