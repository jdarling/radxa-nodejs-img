Radxa Rock Node.js
==================

This is here for anyone who is interested in having a working Ubuntu 14.04 with
Node.js pre-installed and working.

The linked image is for a 16GB Class 10 MicroSD card. Doing this with any slower
or smaller device just doesn't make sense.  As its setup only about 7% of the
drive is in use, so you have lots of space for things like Logs and Applications.

Build-essentials is already installed on this image as well, so you can utilize
native extensions.

Tested to work with a simple hapi webapp server and using my
[Radxa-rock GPIO Node.js Library](https://github.com/jdarling/radxa-rock)

For storage reasons the image file is NOT here on GitHub, but I'm placing the
readme here to make versioning and following easy.

To burn it to a MicroSD card (Linux)
====================================

NOTE: This can take a REALLY long time to burn, on my machine it takes about 30
minutes going from a Sata 3 (6Gbs) drive to a USB 3.0 MicroSD reader/writer.

```
sudo dd of=/dev/sdb if=radxa_nodejs_16gb.img
```

Change /dev/sdb to your MMC drive above.  Mine is /dev/sdc normally.

Download
========

[Download the image from](https://drive.google.com/file/d/0B4r7sllGEcnVOGt3dkZ5NXVzQmc/edit?usp=sharing) then extract the .tar.gz file to get the .img file.

Users
=====

User: rock / Password: rock

Root password: root

Recreate it yourself
====================

Creating the above image wasn't too difficult.  I started off using the 2GB
[Ubuntu 14.04 server image](http://dl.radxa.com/rock/images/ubuntu/sd/radxa_rock_ubuntu_14.04_server_140820_sdcard.zip) and burning it to my MicroSD card using:

NOTE: If the above link doesn't work then browse [the ubuntu sd images](http://dl.radxa.com/rock/images/ubuntu/sd/)
to find the latest server image.

```
sudo dd of=/dev/sdb if=radxa_rock_ubuntu_14.04_server_140820_sdcard.img
```

From there I put the MicroSD card into my Radxa Rock and powered the device on.

Since that image doesn't have a display out that I can make use of (says out of
range on my monitor) I then used [Angry IP Scanner](http://angryip.org/) to find
the IP of the rock.

Next SSH into the rock using:

```
ssh -l rock <ip>
```

And login using "rock" as the password (no double quotes).

Once your successfully logged into the device switch to the root user:

```
su root
```

Use "root" as the password.

[The following taken from this askubuntu question](http://askubuntu.com/questions/24027/how-can-i-resize-an-ext-root-partition-at-runtime)

**Note this must either be done from su or you must sudo every command!

```
fdisk /dev/mmcblk0p1

Command (m for help): p

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     9437183     4717568   83  Linux
```
^^ Make note of that Start value

```
Command (m for help): d
Selected partition 1

Command (m for help): p

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
```

For First sector use the Start value from above, this is IMPORTANT, if you don't
it won't work and you will have to start over.

```
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
```

Just hit <enter> to use whatever the default is.

```
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

Now your drive has been allocated from the original 2GB up to whatever your
disk size actually is, minus a bit for offset.

Restart the rock:

```
shutdown -r now
```

Give a few minutes for the device to power back up and then ssh back into the
rock:

```
ssh -l rock <ip>
```

Finally resize the disk to take up the entire space:

```
sudo resize2fs /dev/mmcblk0p1
```

Next we have to install Node.js on the machine.  We do this by first installing
a few tools (curl and build-essentials) on the rock and then using the
[Joyent instructions for installing Node](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager).

```
sudo apt-get install -y build-essential curl
```

Lots of stuff goes on.

Now install Node.js:

```
curl -sL https://deb.nodesource.com/setup | sudo bash -
```

Once that completes:

```
sudo apt-get install -y nodejs
```

And your done!

Create a backup
===============

Once you have your projects installed on your MicroSD card you might want to
create a backup of the drive so you can restore at any time.  This is again done
using the dd command, something like as follows:

```
sudo dd of=your_backup_name.img if=/dev/sdb
```

Again, this will take quite a bit of time, be patient and you will be rewarded
with an .img file that you can directly write to any disk and have it be the
exact same.

Wireless Networking
===================

Still working on getting this working... Send me a note if you figure it out
or a Pull Request on this document with instructions :)
