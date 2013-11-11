mkinitcpio-squashfs
===================

This hook allows the user to mount SquashFS image as root.

Image can be stored on local (or remote - read about NBD) block device
or mounted/downloaded from remote location.

List of supported protocols:
  * HTTP
  * HTTPS
  * FTP
  * NFS

Kernel parameters:
------------------

* squashfs=\<image-source\>
* squashfs\_copy=\<image-copy\>
* squashfs\_source\_opts=\<image-source-options\>

*\<image-source\>* can be remote or local location.

For remote HTTP(s) or FTP location just use URL for example:

* squashfs=http://192.168.1.1/images/archlinux.squashfs

For NFS location use following syntax:

* (nfs|nfs4)://HOST:HOST\_PATH:IMAGE\_PATH

Examples:

* squashfs=nfs://192.168.1.1:/images:archlinux.squashfs
* squashfs=nfs4://192.168.1.1:/images:/gnu-linux/archlinux.squashfs
* squashfs=nfs://192.168.1.1:/images:auto

For local location use **DEVICE:IMAGE\_PATH** syntax for example:

* squashfs=/dev/sda1:/images/archlinux.squashfs
* squashfs=LABEL=SquashFSImages:/images/archlinux.squashfs
* squashfs=UUID=3c1d5e55-(...):/images/archlinux.squashfs
* squashfs=PARTLABEL=SquashFSImages:/images/archlinux.squashfs
* squashfs=PARTUUID=3c1d5e55-(...):/images/archlinux.squashfs

For both NFS and local locations, it is also possible to type *AUTO* as *IMAGE\_PATH*, then script will search for
**first file** which name ends with .sfs or .squashfs

*\<image-copy\>* can be one of {true, 1, 0, false} default is false.

If *\<image-copy\>* is set to true or 1, then *\<image-source\>* will be copied
to RAM. This option is forced to true while you use remote location.

Example with squashfs\_copy:

* squashfs=/dev/sda1:/images/archlinux.squashfs squashfs\_copy=true

**NOTE**: After copying, you can remove device from your computer.


Default mount options
---------------------

Default mount options for all mounts performed by squashfs hook:

* ro

You can change this with **squashfs\_source\_opts** parameter.

mkinitcpio.conf
---------------

If you are going to use remote locations, add *net* (from mkinitcpio-nfs-utils)
before *squashfs* for example:

* HOOKS="base udev modconf keyboard **block net** squashfs filesystems"

It is also required to place *block* before squashfs if you will use
block devices as image source.

For NFS support, you need to add **/usr/bin/mount.nfs** and **/usr/bin/mount.nfs4** into BINARIES variable.

License
=======

Copyright (C) 2013 Patryk Jaworski \<regalis@regalis.com.pl\>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see http://www.gnu.org/licenses.
