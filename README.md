# NFS
Network File System in Cent OS
### What is NFS ?
Network File System(NFS) is a distributed file system protocol, to share the files and folders between the Linux/Unix systems.

### How to setup NFS ?
Since it's like a client-server model, we need to setup server and client individually.

### Setup NFS-server

1. Installing nfs-utils

```
sudo su
```
```
yum install nfs-utils
```

2. Choose the directory to share. If not present create one.

```
mkdir /var/nfs_share_dir
```

3. Add permissions and ownwership privilages to the shared directory.

```
chmod -R 755 /var/nfs_share_dir
```
```
chown nfsnobody:nfsnobody /var/nfs_share_dir

```
4. Start the nfs services.

```
systemctl enable rpcbind
systemctl enable nfs-server
systemctl enable nfs-lock
systemctl enable nfs-idmap
systemctl start rpcbind
systemctl start nfs-server
systemctl start nfs-lock
systemctl start nfs-idmap

```
5. Configuring the exports file for sharing.

Open the exports file and add these lines.

```
vi /etc/exports
```

Fill in the the file-shared path and clients details in /etc/exports. 10.16.48.20 - Client's IP

```
/var/nfs_share_dir 10.16.48.20(rw,sync,no_root_squash)
```

6. Restart the service

```
systemctl restart nfs-server
```
7. Only for Centos 7,NFS service override.

```
firewall-cmd --permanent --zone=public --add-service=nfs
firewall-cmd --permanent --zone=public --add-service=mountd
firewall-cmd --permanent --zone=public --add-service=rpc-bind
firewall-cmd --reload
```
## Setup NFS-Client(s)

1. Installing nfs-utils
```
sudo su 
```
```
yum install nfs-utils
```
2. Create a mount point
```
mkdir -p /mnt/nfs/var/nfs_share_dir
```

3. Mounting the filesystem
```diff
mount -t nfs 10.16.48.20:/var/nfs_share_dir /mnt/nfs/var/nfs_share_dir

+ -t type of filesystem
+ 10.16.48.20 server's IP

```
4. Verify if mounted.
```
$ df -kh
```
5. Mounting permanently.

Now if the client is rebooted, we need to remount again. So, to mount permanently,we
need to configure /etc/fstab file.
Append this to /etc/fstab
```
10.16.48.20:/var/nfs_share_dir /mnt/nfs/var/nfs_share_dir nfs defaults 0 0
```
To verify, create a file in the Client-side, and open in server-side.
### Client-side(12.68.8.1)
```
echo "Client Hello" >> /mnt/nfs/var/nfs_share_dir/testing.txt
```

### Server-side(10.16.48.20)
```
$ cat /var/nfs_share_dir/testing.txt
```
```
Client Hello
```
