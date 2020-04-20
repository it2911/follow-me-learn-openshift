# Start CRC
##### I am still fighting with DNS problem 

### 1. Start the OpenShift Cluster
If you follow the official document which tell us run `crc start -p pull-secret`.

[Next Step](./04_First_Use_OpenShift.md)

Yes, it looks very easy. But it lead some problems on my PC.

### 2. The Problems what I met
#### 2.1 Cloud not to access the internet.

When I finished the `crc start` running. I meet the below error info. 
Meanwhile, I restarted my PC for some reasons.
```
$ crc start
INFO Checking if oc binary is cached              
INFO Checking if podman remote binary is cached   
INFO Checking if running as non-root              
INFO Checking if Virtualization is enabled        
INFO Checking if KVM is enabled                   
INFO Checking if libvirt is installed             
INFO Checking if user is part of libvirt group    
INFO Checking if libvirt is enabled               
INFO Checking if libvirt daemon is running        
INFO Checking if a supported libvirt version is installed 
INFO Checking if crc-driver-libvirt is installed  
INFO Checking if libvirt 'crc' network is available 
INFO Checking if libvirt 'crc' network is active  
INFO Checking if NetworkManager is installed      
INFO Checking if NetworkManager service is running 
INFO Checking if /etc/NetworkManager/conf.d/crc-nm-dnsmasq.conf exists 
INFO Checking if /etc/NetworkManager/dnsmasq.d/crc.conf exists 
? Copy it from https://cloud.redhat.com/openshift/install/crc/installer-provisioned
? Image pull secret **************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************************

INFO Extracting bundle: crc_libvirt_4.3.8.crcbundle ... 
INFO Checking size of the disk image /home/cheng/.crc/cache/crc_libvirt_4.3.8/crc.qcow2 ... 
INFO Creating CodeReady Containers VM for OpenShift 4.3.8... 
INFO Verifying validity of the cluster certificates ... 
INFO Check internal and public DNS query ...      
INFO Check DNS query from host ...                
ERRO Failed to query DNS from host: lookup api.crc.testing: no such host
```

But when I login again, I found that I cannot access Internet.
I try to command `ping www.google.com`, no response. 

Try to ping gateway, the response is OK.

I try to access the router `Manager UI Page`. It is working too.

I read the `crc start` log again. I found that crc will write `dnsmssq` on my PC. I think the changed DNS is reason which lead I cannot access Internet.

Another way of saying, I could access Internet, just domain name resolution failed. Follow this feeling I try to ping my server public IP. *The response returned.*

I solve this problem by temporary way. I add the google dns server info `nameserver 8.8.8.8` to `/run/systemd/resolve/stub-resolv.conf`.

Try to access the `google.com` again. The Page is show too.

#### 2.2 Add domain to /etc/hosts

The dns problem is temporary solve, but `ERRO Failed to query DNS from host: lookup api.crc.testing: no such host` still left.

My target is start OpenShift Cluster. So I memo the `dnsmasq not work` problem, add `api.crc.testing` and `foo.crc.testing` to `/etc/hosts`

```
$ crc ip
192.168.130.11
$ sudo echo "192.168.130.11 api.crc.testing" >> /etc/hosts
$ sudo echo "192.168.130.11 foo.crc.testing" >> /etc/hosts
``` 

Before start OpenShift, delete the cache info first.
```
$ crc delete
Do you want to delete the OpenShift cluster? [y/N]: y
Deleted the OpenShift cluster
$ crc start -p pull-secret --log-level debug   
......
INFO                                              
INFO To access the cluster, first set up your environment by following 'crc oc-env' instructions 
INFO Then you can access it by running 'oc login -u developer -p developer https://api.crc.testing:6443' 
INFO To login as an admin, run 'oc login -u kubeadmin -p kKdPx-pjmWe-b3kuu-jeZm3 https://api.crc.testing:6443' 
INFO                                              
INFO You can now run 'crc console' and use these credentials to access the OpenShift web console 
Making call to close driver server
(crc) Calling .Close
Successfully made call to close driver server
Making call to close connection to plugin binary
Making call to close driver server
(crc) Calling .Close
Successfully made call to close driver server
Making call to close connection to plugin binary
Started the OpenShift cluster
WARN The cluster might report a degraded or error state. This is expected since several operators have been disabled to lower the resource usage. For more information, please consult the documentation 
```

**Successful!**