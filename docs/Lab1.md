# Lab 1:   Setup: VMWare Pro, Multipass, AWS EC2

## Setup VMWare Workstation Pro + Obtain Personal Use Licence

Disclaimer: this setup require creating account on Broadcom.com Website. It's recommended to download the VMWare Workstation Pro over the VirtualBox due to it's higher performance and product quality; although if you are not willing to use VMWare software, VirtualBox is an alternative. Please ask your instructor if you chose this path.

Please Navigate to the address below and Click to donwload the VMWare software base on your OS:
- [Download Address](https://blogs.vmware.com/workstation/2024/05/vmware-workstation-pro-now-available-free-for-personal-use.html)
- For MacOS: VMware Fusion Pro Download
- For Windows and Linux: VMware Workstation Pro Download

1. Sign up on Broadcom.com website and login
![broadcom.com login page](images/lab1-fig1.jpeg)
2. Search for the VMWare Workstation Pro product
![search VMWare Workstation Pro name](images/lab1-fig2.jpeg)
3. Download the Software base on your OS
![Choose VMWare base on your OS](images/lab1-fig3.jpeg)
4. On the upcoming page, Download the lastest version of the software.
5. After it's downloaded, install the VMWare software.

## Install Ubuntu Desktop OS on VMWare Workstation Pro

1. Download the latest LTS version of Ubuntu from [Ubuntu.com](https://ubuntu.com/download/desktop)
2. After download of Ubuntu `.ios` file.
3. Move over to VMWare Workstation Pro. Proceed to create a new Virtual Machine. (Note: your VMWare dashboard/UI most probably are different)
![Create New VM](images/lab1-fig4.png)
4. Choose Ubuntu 64 bit as the type of the OS you want to install on your new VM.
![Choose Ubuntu-64](images/lab1-fig5.png)
5. Choose Lagacy BIOS as the Firmware Type:
![Firmware Type](images/lab1-fig6.png)
6. Create a new Virtual Disk. (20 GB should be enough)
![Create a virtual Disk](images/lab1-fig7.png)
7. You can leave the rest of the setting as default and Finilize the setup.
8. Before you start our VM for the first time. you need to connect the .iso file as an external drive such as CD/DVD Drive. (this would be similar when you have inserted a DVD disk of Ubuntu installation into the machine)
![Attach ISO file](images/lab1-fig8.png)
9. Start/Run the VM you just created. Select Install Ubuntu Option.
![Install Ubuntu](images/lab1-fig9.png)
10. Select next multiple time to get to the page below; select Intractive Installation.
![Ubuntu Installation](images/lab1-fig10.png)
11. Hit Next and accept the default for the next 2 more pages till you get to the page below. Choose to Erase the Disk and Install the Ubuntu.
![Erase the Disk and Install](images/lab1-fig11.png)
12. Create a user account.
![create a user account](images/lab1-fig12.png)
13. Hit Next, and choose your Time Zone and hit next again.
14. Last page before the installatio begin you can review your installation configurations. Hit Install.
![review page](images/lab1-fig13.png)
15. After Installation is done. Login to your VM and update.
```sh
sudo apt update && sudo apt upgrade -y
```

## Installing and Using Multipass on Your Computer
Multipass is a lightweight VM manager for Linux, Windows, and macOS that allows you to create, manage, and configure Ubuntu instances with ease. Follow these steps to install Multipass on your computer:
1. Download and Install Multipass:
    - Go to the Multipass download page (https://multipass.run/install).
    - Download the installer and follow the installation instructions.
2. Verify the Installation:
- Open a terminal or command prompt and type:
multipass version
- This command should return the version of Multipass installed, confirming the installation was successful.
Using Multipass
1. Launch an Ubuntu Instance:
- To create and start a new Ubuntu instance, run:
multipass launch --name my-ubuntu
- Replace `my-ubuntu` with a name of your choice. This command will download the default Ubuntu image (usually the latest LTS version) and start the instance.
2. Access the Ubuntu Instance:
- To access the Ubuntu instance, use:
multipass shell my-ubuntu
- This command will drop you into a shell session within your Ubuntu instance, where you can start running commands as if you were using a regular Ubuntu machine.
3. Managing Instances:
- List Instances:
multipass list
This will show you all the instances you have created, along with their statuses.
 
ACIT3475
- Stop an Instance:
multipass stop my-ubuntu
- Delete an Instance:
multipass delete my-ubuntu
After deleting an instance, run the following command to free up space:
multipass purge
Multipass also allows you to configure networking, mount directories from your host machine, and more. For additional commands and options, consult the Multipass documentation.
