# multipass-docker-init
### Simple cloud-init file for a simple docker ready Multipass instance

**Disclaimer** - This is a setup that works on my system, using Windows 10 Pro version 19041, WSL2 with docker initialized, and hyper-V enabled. This may not work on other systems but I hope it helps someone. I also cannot guarantee that this won't cause other problems and present this "as is" with no warranties. Use at your own risk. 

This cloud-init file may work with multipass on Linux and MacOS also but I haven't tested it. 

#### The basic idea:

Multipass allows the use of a cloud-init file to specify how the vm will be configured when it is built, and the main purpose of my use of Multipass is to create docker swarm and Kubernetes clusters locally for educational use, along with trying out different linux things. This means I occasionaly bork an ubuntu vm and can then easily delete it and start over. Updating and installing docker over and over again was not optimal so I automated it. 

I also need my machine clean and free of VM's for work (which isn't programming) so I needed a simple way to provision and remove them that worked with everything else I have going on. VirtualBox wasn't cutting it but this works great. 

#### To use: 

1. Install Multipass on your system. Visit [the Multipass homepage](https://multipass.run/ "Multipass homepage") and scroll down to the "Install Multipass" header, and click on the installer for windows. 

    - Note that the Multipass home page has some great instructions to get you started with Ubuntu vm's. Spend some time starting them, naming them, deleting and purging, and getting used to using the tooling. 
    - Also note that `multipass help` command on the command line will only give you the top level help, but you can get additional help with dashes on the help flag in subcommands, such as `multipass launch --help`

2. Once installed, test that Multipass installed correctly by opening a terminal and entering the command `multipass version`, which should display the currently installed version. 

3. Copy the **docker.yml** file from this repo into a directory of your choice. (Feel free to clone this repo if that's your thing, or just copy/paste from my file to yours)

4. Run the Multipass Launch and include the cloud-init file: `multipass launch -n node1 --cloud-init [your-docker.yml-file]`, and note the file in brackets needs to point to the *.yml* file you just created. 
    - This should create the new VM, update packages, create a default user named "ubuntu", add the default user to the "docker" group, and install docker. It will also take a few minutes so be patient. 
    - `cloud-init` files can do all sorts of crazy things beside this, but my version is the minimal file I could use to get a docker ready VM. For more on cloud-init [see the docs here](https://cloudinit.readthedocs.io/en/latest/topics/examples.html "cloud-init documentation site")

5. Run `multipass ls` to list active instances and you should see a vm named "node1" in the list. 

6. Run `multipass shell node1` to get an active shell inside the node1 VM and give you a command prompt. The prompt should read "ubuntu@node1:~$"

7. At the prompt from step 6, run `docker version` and you should see the version info for both the client and server (daemon) docker installations. If you get them both, you're all set.  
    - If permission is denied to the daemon, try running `sudo reboot` and waiting for the instance to start up again, and then using `multipass shell node1` to jump in and verify the docker version again.

8. When you are done with the VM, there is a 2 part process to remove it. `multipass delete node1` will stop the machine and delete it from active use, but it's not actually gone yet. (This is a failsafe against deleting things by accident in case you have something you need in a VM). To fully remove the VM you need to run `multipass purge` after the delete command and the VM will be permanently deleted. 


#### Hopefully this helps someone, Have fun and good luck! 
