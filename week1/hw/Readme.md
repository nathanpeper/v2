# HW 1: Installing Jetpack and Docker 

## 1. Jetpack 3.2.1
Jetpack is a SDK that basically contains everything needed for deep learning and AI applications in a handy package for the Jetson. Installation on the Jetson requires downloading and installing both on the Jetson (the target) and an Ubuntu computer (the host).
All of the following must be done in an Ubuntu OS. If a command is ever permission denied, try adding "sudo" at the beginning.
Refer to https://docs.nvidia.com/jetpack-l4t/index.html#jetpack/3.2.1/install.htm for more detailed instructions.

### Host (Computer) Installation

Open a terminal window and create a new directory to store installation packages. Now download the latest version of Jetpack from the Nvidia webiste (you need to make a Nvidia Developer's account first). The download is a Linux executable file (.run file type). Move the file into the directory you just made, then cd into that directory. Add exec permission for this Jetpack file (replace the <> with the name of the file):
```
chmod +x <Jetpack file name>
```
Run the file (remember to replace the <>):
```
./<Jetpack file name>
```
The install wizard window should open, click "next". The next window displays the installation locations, which should already be the directory you are currently in. Next window, select the correct Jetson version (TX2 as of now, maybe Jetson Xavier?). 

The next window is the components manager, where you can see what actions will be performed to the host and target. You can troubleshoot here for errors if needed. For now, click "next" at the bottom right and then check "accept all" in the following terms and conditions popup. You'll be taken back to the components manager and everything will begin to download. There may be an error message during download; just dismiss it and click "next" again. A message will prompt you to monitor the computer during installation, and click "ok" to begin installation. After installation is done, the wizard will now do target hardware setup (the Jetson). 

### Target (Jetson) Installation

Boot up the Jetson and ensure it is connected to the internet through the same router as the host computer. Both the Jetson and host computer must be running at the same time.

On the host computer, select the "Device accesses Internet via router/switch" option. The next window prompts you to select the correct network interface corresponding to the router that the Jetson is also connected to. To view these, open up another terminal window and bring up a list of the interfaces with this command:
```
ifconfig
```
Find the one with "inet addr: 192.168.<somenumbers>" in the second line of the description, which points to the router. The name often looks like "wlp<somenumbers>". Select that interface from the drop-down menu in the install wizard. The next window displays the actions the install will take, click "next". 

Follow the instructions on the new window to put the Jetson into Force USB Recovery Mode. The FORCE RECOVERY button is the button next to the power button, and the RESET button is the button on the opposite end of the row. Afterwards, check if the Jetson is on Force USB Recovery Mode by listing the USB buses in the host computer:
```
lsusb
```
You should see "NVidia Corp" in the list of devices. Go back to the window with instructions and press "enter" to begin flashing the OS on the Jetson. 

After flashing, the host computer will install Jetpack modules onto the Jetson via ssh. If the host computer can't find the IP address of the Jetson, it will give you options to try again or enter the IP address manually. Keep trying again, but if this keeps failing then check the Jetson's IP address and enter it manually. When installation on the Jetson is done, close the window as prompted and you're done.

### Testing Jetpack on the Jetson

Ensure the Jetson is on and running Ubuntu. Use ls to check the directory name where the CUDA samples are (it looks likes "NVIDIA_CUDA-<version>" replacing the <> with the CUDA version you have). Then cd into it and run the oceanFTT sample:
```
cd NVIDIA_CUDA-<version>_Samples/bin/aarch64/linux/release/
./oceanFTT
```

##2. Docker 
Docker is a platform that allows you to create, deploy, and run applications in containers. The application and all its dependecies are packaged into one container that is easy to ship out and uses the same Linux kernel as the system it's running on, unlike a virtual machine. This makes it especially useful for compact platforms such as the Jetson.

### Downloading Docker onto the Jetson

Navigate to the /etc/apt directory. There should be a executable file there called "sources.list". Open it and add the following to the bottom:
```
deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable
```
If the file is read-only (it won't let you save changes), open a terminal and give yourself permission to edit the file:
```
sudo chmod a+rwx sources.list
```
Now update the package lists and install Docker. If Docker is already on the Jetson, a message in the terminal will tell you it's "already installed".
```
sudo apt-get update
sudo apt-get install docker.io
```
Let's test Docker to see if it can run containers. The following command will attempt to run the hello-world image by creating a container from it. Notice the image is based on arm64v8. Since the Jetson doesn't have the image yet, Docker will automatically pull it online from the official repository:
```
sudo docker run arm64v8/hello-world
```

### Linking Docker to an External Drive

The Jetson has limited storage, so linking Docker to an external drive is a good choice to store all your Docker work. A SSD is reccommended to speed up processes. The Docker files on the Jetson are in /var/lib/docker. Plug an external drive into the Jetson and check its location and name (usually /media/nvidia/<drivename>). Now move the Docker directory stored on the Jetson (in /var/lib/docker) to the external drive. You might want to back up this directory first somewhere in case of errors.
```
mv /var/lib/docker /media/nvidia/<drivename>
```
Create a symbolic link between the Docker directory you just moved onto on the external drive to a new softlink that Docker on the Jetson will refer to when pulling files:
```
sudo ln -s /media/nvidia/<drivename>/docker /var/lib/docker
```
Now your Docker work will automatically be stored on this external drive. The new "docker" directory in /var/lib is a softlink: every time Docker calls on that, it'll actually be pulling from the original "docker" directory in the external drive. Just remember to hook it up when working with Docker.
















