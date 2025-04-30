# Build a Yocto image for Raspberry Pi 4

This script builds a Yocto image for Raspberry Pi 4 using the poky repository and the meta-raspberrypi layer. It builds a full command line image with SSH support and installs the mosquitto MQTT broker and Python 3.

## Download and Install Dependencies

First install the required packages for building Yocto images. The following command installs the necessary dependencies on fedora:

```bash
git clone -b kirkstone git://git.yoctoproject.org/poky.git 

git clone -b kirkstone git://git.yoctoproject.org/meta-raspberrypi 

git clone -b kirkstone git://git.openembedded.org/meta-openembedded  
```


## Adding layers

After cloning the repositories, run the following command to install the required packages:

```bash
source poky/oe-init-build-env build 
```

See all layers in the `conf/bblayers.conf` file:

```bash
cat conf/bblayers.conf
```
or

```bash
bitbake-layers show-layers

NOTE: Starting bitbake server...
layer                 path                                      priority
==========================================================================
meta                  /home/son/Projects/yocto_pi4/poky/meta    5
meta-poky             /home/son/Projects/yocto_pi4/poky/meta-poky  5
meta-yocto-bsp        /home/son/Projects/yocto_pi4/poky/meta-yocto-bsp  5

```
We need to add lyers to the `conf/bblayers.conf` file. Open the file in a text editor and add the following lines:

```bash
vi conf/bblayers.conf
```

And add paths to the layers, or you can use the following command to add layers:

```bash
bitbake-layers add-layer ../meta-raspberrypi
bitbake-layers add-layer ../meta-openembedded
bitbake-layers add-layer ../meta-openembedded/meta-oe
bitbake-layers add-layer ../meta-openembedded/meta-python
bitbake-layers add-layer ../meta-openembedded/meta-networking
bitbake-layers add-layer ../meta-openembedded/meta-filesystems
```

and check the layers again:

```bash
bitbake-layers show-layers       

NOTE: Starting bitbake server...
layer                 path                                      priority
==========================================================================
meta                  /home/son/Projects/yocto-raspi4/poky/meta  5
meta-poky             /home/son/Projects/yocto-raspi4/poky/meta-poky  5
meta-yocto-bsp        /home/son/Projects/yocto-raspi4/poky/meta-yocto-bsp  5
meta-raspberrypi      /home/son/Projects/yocto-raspi4/meta-raspberrypi  9
meta-oe               /home/son/Projects/yocto-raspi4/meta-openembedded/meta-oe  5
meta-python           /home/son/Projects/yocto-raspi4/meta-openembedded/meta-python  5
meta-networking       /home/son/Projects/yocto-raspi4/meta-openembedded/meta-networking  5
meta-filesystems      /home/son/Projects/yocto-raspi4/meta-openembedded/meta-filesystems  5


```

## Configure the build

Edit the `conf/local.conf` file to set the machine type to `raspberrypi4`:

```bash
vi conf/local.conf
```

```
PACKAGE_CLASSES ?= "package_ipk"
MACHINE ?= "raspberrypi4"
```

And add the following lines to the end of the file:

```bash

ENABLE_UART = "1"

EXTRA_IMAGE_FEATURES = "ssh-server-dropbear allow-empty-password empty-root-password allow-root-login"

IMAGE_INSTALL:append = " mosquitto mosquitto-clients"
IMAGE_INSTALL:append = " python3 python3-pip"

```

## Build the image

Now you can build the image using the following command:

```bash
bitbake core-image-full-cmdline
```