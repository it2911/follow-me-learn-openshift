# Donwload CRC

### 1. Ready RedHat Account 
You need the RedHat Account to download the crc command tools and `pull secret`.

### 2. Download CRC Command
I am going to install OpenShift on Ubuntu System. So I download crc linux command.

### 3. Download Pull Secret
This file will be used when you run `crc start` command.

[crc-download-page](../images/Screenshot_20200419_234045.png)

### 4. Download kubectl Command tools

You can find the install document by this [link](`https://kubernetes.io/ja/docs/tasks/tools/install-kubectl/`).

But for convenience I copied the download command for you.
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```
