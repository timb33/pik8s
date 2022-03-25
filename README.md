# pik8s
Micok8s on Ubuntu on a raspberry pi cluster

## Install OS and setup cards
<br/>(using Windows) and using Raspberry Pi imager, install 64bit Ubuntu2004lts image via: "other general-purpose OS>Ubuntu>Ubuntu server 2004lts"
<br/>Once complete, unplug the SD card and immediately plug it back in.
<br/>sdcard shows up as drive.
<br/>Adjust and drag in the following two files from this repo, 
1. user-data, which changes ubuntu's default password and sets hostname to e.g. piubuntu91
2. network-config, which connects to a named access point, and sets a static IP (e.g. 192.168.1.91) for the sdcard
<br/>plug in the sdcard into the (headless) pi, start the pi, and leave it alone for a few minutes (it doesn't show up when I scan my nw, I don't know why)
<br/>restart the same pi and this time after it's booted up, it shows up on your lan on the expected static IP.
<br/>This first pi is now running.

## Install MicroK8s master - do once
<br/>ssh onto it using the ubuntu account, the password's in your user-data file
<br/>follow [this guide](https://ubuntu.com/tutorials/how-to-kubernetes-cluster-on-raspberry-pi#5-master-node-and-leaf-nodes) to install microk8s, but in short...
<br/><code>sudo echo "cgroup_enable=memory cgroup_memory=1 net.ifnames=0 dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=LABEL=writable rootfstype=ext4 elevator=deadline rootwait fixrtc" > /boot/firmware/cmdline.txt</code>
<br/> or manually edit existing file
<br/><code>sudo vi /boot/firmware/cmdline.txt</code>
<br/><code>sudo reboot</code>
<br/><code>sudo snap install microk8s --classic</code>
<br/>then you can start adding worker nodes (on other pi's) via: sudo microk8s.add-node on this one

## Install MicroK8s worker - do once per node
<br/>Repeat the master setup, but put this worker node on its own different IP (e.g. 192.168.1.92), so tweak the two files
<br/>then... 
<br/><code>sudo nano /boot/firmware/cmdline.txt</code>   <- do same as before
<br/><code>sudo reboot</code>
<br/><code>sudo snap install microk8s --classic</code>
<br/><code>sudo microk8s join 192.168.1.91:25000/yourGuidFromYourMaster/04ce47cd112c --worker</code>
<br/>and repeat for as many worker nodes as you want (I've got 2 workers so I can pull power to one node and watch my K8s hi-availability work)

## Configure MicroK8s
I used Portainer, which is available from the MicroK8s menu 
TBC
