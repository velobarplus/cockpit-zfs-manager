# ZFS manager

### WARNING!

ZFS manager is currently pre-release software. Use at your own risk!

## Requirements

 * Cockpit: 201+
 * NFS (Optional)
 * Samba: 4+ (Optional)
 * ZFS: 0.8+
 
 <details>
  <summary>Tested Distributions</summary>
  
  * Arch Linux
  * CentOS Linux 8
  * Debian 10
  * Fedora 31
  * Oracle Linux Server 8
  * Proxmox VE 6
  * Red Hat Enterprise Linux 8
  * Ubuntu 18.04 LTS
  * Ubuntu 19.10
  
</details>

## Screenshots
<img src="https://user-images.githubusercontent.com/58222345/80313061-69dff180-881b-11ea-98d4-e245ed8faffd.png" width="425" /> <img src="https://user-images.githubusercontent.com/58222345/80313062-6b111e80-881b-11ea-9d0e-d4bcac251bf1.png" width="425" />
<img src="https://user-images.githubusercontent.com/58222345/80313064-6c424b80-881b-11ea-812f-ab3a99edef75.png" width="425" />
<img src="https://user-images.githubusercontent.com/58222345/80313065-6d737880-881b-11ea-8bad-a37506c2ce12.png" width="425" />
<img src="https://user-images.githubusercontent.com/58222345/80313066-6f3d3c00-881b-11ea-859d-a47cf1e85177.png" width="425" />
<img src="https://user-images.githubusercontent.com/58222345/80313067-7106ff80-881b-11ea-9688-0c94f8088b3b.png" width="425" />

<sup>Screenshots using Cockpit 217 on Oracle Linux 8.1 with UEK R6.</sup>

## Installation

Copy zfs folder to cockpit

```bash
$ git clone https://github.com/optimans/cockpit-zfs-manager.git
$ sudo cp -r cockpit-zfs-manager/zfs /usr/share/cockpit
```

#### Samba

Auto generated snapshot names are created in YYYY.MM.DD-HH.MM.SS format.

It is recommended to add the following properties to the Samba configuration file to allow access to Previous Versions in Windows Explorer:

```bash
$ sudo nano /etc/samba/smb.conf
```

Append to [global] section or individual share sections

```
shadow: snapdir = .zfs/snapshot
shadow: sort = desc
shadow: format = %Y.%m.%d-%H.%M.%S
shadow: localtime = yes	
vfs objects = acl_xattr shadow_copy2
```

#### SELinux

If using SELinux in enforcing mode, it is recommended to enable the boolean states for Samba:

```bash
$ sudo setsebool -P samba_export_all_ro=1 samba_export_all_rw=1
```

## Using ZFS Manager

Login as a privileged user and click ZFS from the navigation list.

A Welcome to ZFS manager modal will display and allow you to configure initial settings.

Note: Inline help is currently available in modals. Documentation will be created at a later date.



## Caveats

#### Storage Pools

New storage pools are created with the following properties set (not visible in Create Storage Pool modal):

 * aclinherit=passthrough
 * acltype=posixacl
 * casesensitivity=sensitive
 * normalization=formD
 * sharenfs=off
 * sharesmb=off
 * utf8only=on
 * xattr=sa

#### File Systems

New file systems are created with the following properties set (not visible in Create File System modal):

 * normalization=formD
 * utf8only=on

Passphrase is currently supported for encrypted file systems.

If SELinux contexts for Samba is selected, the following properties are set:

 * context=system_u:object_r:samba_share_t:s0
 * fscontext=system_u:object_r:samba_share_t:s0
 * defcontext=system_u:object_r:samba_share_t:s0
 * rootcontext=system_u:object_r:samba_share_t:s0

#### Samba

ZFS always creates shares in /var/lib/samba/usershares folder when ShareSMB property is enabled. This is also the case even if ZFS manager is managing the shares. To avoid duplicate shares of the same file system, it is recommended to configure a different usershares folder path if required or to disable usershares in the Samba configuration file.

Note: Newer versions of Samba may require the usershares folder to be set to a new path instead of [disabled in configuration](https://github.com/optimans/cockpit-zfs-manager/issues/12):

```bash
$ sudo mkdir /var/lib/samba/usershares2
$ sudo nano /etc/samba/smb.conf
```

Append/Change to [global] section

```
usershare path = /var/lib/samba/usershares2
```

If enabled, ZFS manager manages shares for the file systems only. Samba global configuration will need to be configured externally.

## More Information

* [ServeTheHome and ServeThe.Biz Forums](https://forums.servethehome.com/index.php?threads/25668/)

## Guides

 * [CentOS 8 as an Active Directory Domain Services (AD DS) Member](guides/CentOS-8.md)
