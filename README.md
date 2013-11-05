mkinitcpio-squashfs
===================

This hook allows the user to mount SquashFS image as root.

Image can be stored on local (or remote - read about NBD) block device
or downloaded from remote location.

List of supported protocols:
  * HTTP
  * HTTPS
  * FTP

Finally, to get a writable system, it is possible to keep the device you
have booted from (this feature is disabled for remote locations).

This makes it possible for instance to bind directories and get a
somewhat writeable system.

Kernel parameters:
------------------

* squashfs=\<image-source\>
* squashfs\_copy=\<image-copy\>
* keep\_device=\<location\>

*\<image-source\>* can be remote or local location.

For remote location just use URL for example:

* squashfs=http://192.168.1.1/images/archlinux.squashfs

for local location use **DEVICE:PATH** syntax for example:

* squashfs=/dev/sda1:/images/archlinux.squashfs
* squashfs=LABEL=SquashFSImages:/images/archlinux.squashfs
* squashfs=UUID=3c1d5e55-(...):/images/archlinux.squashfs

It is also possible to type *AUTO* as *PATH*, then script will search for
**first file** which name ends with .sfs or .squashfs

*\<image-copy\>* can be one of {true, 1, 0, false} default is false

If *\<image-copy\>* is set to true or 1, then *\<image-source\>* will be copied
to RAM. This option is forced to true while you use remote location.

Example with squashfs\_copy:

* squashfs=/dev/sda1:/images/archlinux.squashfs squashfs\_copy=true

*\<keep-device\>* must be a valid path inside the squashed system and defaults
to /mnt/origin

If *\<keep-device|>* is set, then the device from which you booted will still be
accessible at this location.

Moreover, some automatic bind mounts may be performed (useful for instance to
get access to a writeable /etc). Normally, only /etc needs to be mounted, and
then the fstab it contains should be able to perform the rest of the bindings
you wish to have, but this feature allows you to have 'early' mounts if you
need.

To do so, just create a file called '.binds' at the root of the block device.
This file must contain one line for each location you want to bind early.

Each of those line consists in the relative path to a location from the root of
the booted system, that is without the '/' at the begining. If the directory's
name on the block device is different from the name of the directory where you
want it bound, add '=' followed by the path relative to the root of the block
device.

Example:

You want your device kept under /origin (which you must have created in the
squashed filesystem).

* keep\_device=/origin

The block device you're trying to boot contains the /etc directory, a /data
directory that you want to use as /home under the booted-up system and, of
course, a squashed filesystem, say root.sfs.

From the service shell right after root system was mounted in initramfs you
would see this:

```
$ ls /image\_source
etc/    data/    root.sfs
```

If it also has a .binds file that reads:

```
$ cat /image\_source/.binds
etc
home=data
```

Then once the system is booted up you'll see the content of

* /origin/etc in /etc
* /origin/data in /home

**NOTE**: After copying, if access to device was not kept, you can
remove it from your computer

mkinitcpio.conf
---------------

If you are going to use remote locations, add *net* (from mkinitcpio-nfs-utils)
before *squashfs* for example:

* HOOKS="base udev modconf keyboard **block net** squashfs filesystems"

It is also required to place *block* before squashfs if you will use
block devices as image source.


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
