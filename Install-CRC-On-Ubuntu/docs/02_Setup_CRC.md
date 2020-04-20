# Setup A Locale OpenShift Cluster by CRC Tool

### 1. Setup The Environment
This command will help you to check and setup a local environment to install OpenShift Cluster.
```
$ crc setup
```
[Next Step](./03_Start_CRC.md)

### 2. The Problems what I met
I always believe that nothing is easy. The same things happened on I install the OpenShift. 
```
$ crc setup --log-level error                
FATA Failed to create libvirt 'crc' network
$ crc setup --log-level info            
INFO Checking if oc binary is cached              
INFO Checking if podman remote binary is cached   
INFO Checking if CRC bundle is cached in '$HOME/.crc' 
INFO Checking if running as non-root              
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if libvirt is enabled               
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking for obsolete crc-driver-libvirt     
INFO Checking if libvirt 'crc' network is available 
INFO Setting up libvirt 'crc' network             
FATA Failed to create libvirt 'crc' network 
```

Well, It is command export information is too simple that cannot found out the error position.

Let us confirm the log file of crc. **Remember some important setting info save in .crc folder.** 
```
$ cat .crc/crc.log  
.....
ime="2020-04-17T18:30:22+09:00" level=debug msg="Running '/home/cheng/.crc/bin/crc-driver-libvirt version'"
time="2020-04-17T18:30:22+09:00" level=debug msg="crc-driver-libvirt is already installed in /home/cheng/.crc/bin/crc-driver-libvirt"
time="2020-04-17T18:30:22+09:00" level=info msg="Checking for obsolete crc-driver-libvirt"
time="2020-04-17T18:30:22+09:00" level=debug msg="Checking if an older libvirt driver crc-driver-libvirt is installed"
time="2020-04-17T18:30:22+09:00" level=debug msg="No older crc-driver-libvirt installation found"
time="2020-04-17T18:30:22+09:00" level=info msg="Checking if libvirt 'crc' network is available"
time="2020-04-17T18:30:22+09:00" level=debug msg="Checking if libvirt 'crc' network exists"
time="2020-04-17T18:30:22+09:00" level=debug msg="Running 'virsh --connect qemu:///system net-info crc'"
time="2020-04-17T18:30:22+09:00" level=debug msg="Command failed: exit status 1"
time="2020-04-17T18:30:22+09:00" level=debug msg="stdout: "
time="2020-04-17T18:30:22+09:00" level=debug msg="stderr: error: failed to connect to the hypervisor\nerror: Failed to connect socket to '/var/run/libvirt/libvirt-sock': Permission denied\n"
time="2020-04-17T18:30:22+09:00" level=debug msg="Libvirt network crc not found"
time="2020-04-17T18:30:22+09:00" level=info msg="Setting up libvirt 'crc' network"
time="2020-04-17T18:30:22+09:00" level=debug msg="Creating libvirt 'crc' network"
time="2020-04-17T18:30:22+09:00" level=debug msg="exit status 1 : error: failed to connect to the hypervisor\nerror: Failed to connect socket to '/var/run/libvirt/libvirt-sock': Permission denied\n"
time="2020-04-17T18:30:22+09:00" level=fatal msg="Failed to create libvirt 'crc' network"
```

Real reason `Permission denied`. Good! Understand the problem, the solve out is not far. Let us fixup it.

At first, I tried sudo permission. But RedHat is very smart. They are reject `root` role.

It is very smart, `root` permission always lead some security problem in experience.

```
$ sudo crc setup                          
INFO Checking if oc binary is cached              
INFO Checking if podman remote binary is cached   
INFO Checking if CRC bundle is cached in '$HOME/.crc' 
INFO Checking if running as non-root              
ERRO crc should be ran as a normal user           
FATA crc should be ran as a normal user  -> It is rejected.
``` 

Allocation the exec permission of `libvirt` to current user.
```
sudo usermod -aG libvirt $USER
```

Try again. OH, NO. It is still some error. Why?
```
$ crc setup                                      
INFO Checking if oc binary is cached              
INFO Checking if podman remote binary is cached   
INFO Checking if CRC bundle is cached in '$HOME/.crc' 
INFO Checking if running as non-root              
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if libvirt is enabled               
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking for obsolete crc-driver-libvirt     
INFO Checking if libvirt 'crc' network is available 
INFO Setting up libvirt 'crc' network             
FATA Failed to create libvirt 'crc' network    
```

Of course, for enable the permission setting, I need login again.
```
$ su - cheng
Password: 
```

Run command again. Setup is complete.
```
$ crc setup                                                       
INFO Checking if oc binary is cached              
INFO Checking if podman remote binary is cached   
INFO Checking if CRC bundle is cached in '$HOME/.crc' 
INFO Checking if running as non-root              
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if libvirt is enabled               
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking for obsolete crc-driver-libvirt     
INFO Checking if libvirt 'crc' network is available 
INFO Setting up libvirt 'crc' network             
INFO Checking if libvirt 'crc' network is active  
INFO Starting libvirt 'crc' network               
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf exists 
INFO Writing Network Manager config for crc       
INFO Will use root access: write NetworkManager config in /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf 
INFO Will use root access: execute systemctl daemon-reload command 
INFO Will use root access: execute systemctl stop/start command 
INFO Checking if /etc/NetworkManager/dnsmasq.d/crc.conf exists 
INFO Writing dnsmasq config for crc               
INFO Will use root access: write dnsmasq configuration in /etc/NetworkManager/dnsmasq.d/crc.conf 
INFO Will use root access: execute systemctl daemon-reload command 
INFO Will use root access: execute systemctl stop/start command 
Setup is complete, you can now run 'crc start' to start the OpenShift cluster
```