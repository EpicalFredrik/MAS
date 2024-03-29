# Podman

## Installing Podman for Windows
If you do not have docker installed, Podman is an alternative. I prefer Podman over Docker since that is what RedHat recommends for OpenShift, keep in mind it’s a personal preference.

To install Podman, use the following link,  
https://github.com/containers/podman/blob/main/docs/tutorials/podman-for-windows.md

After the install,  
`Podman machine init`

To keep your Podman up to date,  
`Podman machine ssh sudo dnf upgrade -y`

I recommend installing an easy to use text editor, so I ran  
`podman machine ssh sudo dnf install nano -y`  
First time you use Podman need to turn off a feature in WSL that will update your DNS to your local computer.  
Enter your machine,  
`podman machine ssh`  
Edit the resolv.conf file,  
`sudo vi /etc/resolv.conf`   
Change Nameserver to 8.8.8.8 or 9.9.9.9  

```
\# This file was automatically generated by WSL. To stop automatic generation of this file, add the following entry to /etc/wsl.conf:  
\# [network]  
\# generateResolvConf = false  
nameserver 9.9.9.9  
```

To save the file hit CTRL-o and then exit with CTRL-x.  
This will not stick until you edit the wsl.conf file so let’s fix it,  
`sudo nano /etc/wsl.conf`  
Add the two rows and save, CTRL-o and CTRL-x,  

```
[user]  
default=user  
[network]  
generateResolvConf = false  
```

When done, just type `exit` to exit out of the Podman machine.
That should be it, Podman is up and running, whenever you restart your computer, you need to start podman, “Podman machine start”.

---

If you want a GUI like Docker Desktop, you can try the Podman-Desktop,  
https://podman-desktop.io/
