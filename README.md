# pik8s
Micok8s on Ubuntu on a raspberry pi cluster

## Install OS and setup cards
* (using Windows) and using Raspberry Pi imager, install 64bit Ubuntu2004lts image via: "other general-purpose OS>Ubuntu>Ubuntu server 2004lts"
* Once complete, unplug the SD card and immediately plug it back in.
* sdcard shows up as drive.
* Adjust and drag in the following two files from this repo, 
1. user-data, which changes ubuntu's default password and sets hostname to e.g. piubuntu91
2. network-config, which connects to a named access point, and sets a static IP (e.g. 192.168.1.91) for the sdcard
* plug in the sdcard into the (headless) pi, start the pi, and leave it alone for a few minutes (it doesn't show up when I scan my nw, I don't know why)
* restart the same pi and this time after it's booted up, it shows up on your lan on the expected static IP.
* This first pi is now running.

## Install MicroK8s master - do once
* ssh onto it using the ubuntu account, the password's in your user-data file
* follow [this guide](https://ubuntu.com/tutorials/how-to-kubernetes-cluster-on-raspberry-pi#5-master-node-and-leaf-nodes) to install microk8s, but in short...
* sudo echo "cgroup_enable=memory cgroup_memory=1 net.ifnames=0 dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=LABEL=writable rootfstype=ext4 elevator=deadline rootwait fixrtc" > /boot/firmware/cmdline.txt
* sudo vi /boot/firmware/cmdline.txt
* sudo reboot
* sudo snap install microk8s --classic
* then you can start adding worker nodes (on other pi's) via: sudo microk8s.add-node on this one

## Install MicroK8s worker - do once per node
* Repeat the master setup, but put this worker node on its own different IP (e.g. 192.168.1.92), so tweak the two files
* then... 
* sudo nano /boot/firmware/cmdline.txt   <- do same as before
* sudo reboot
* sudo snap install microk8s --classic
* sudo microk8s join 192.168.1.91:25000/yourGuidFromYourMaster/04ce47cd112c --worker
* and repeat for as many worker nodes as you want (I've got 2 workers so I can pull power to one node and watch my K8s hi-availability work)

## Configure MicroK8s
I used Portainer, which is available from the MicroK8s menu 
TBC
