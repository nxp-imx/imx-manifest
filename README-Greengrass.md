# AWS IoT Greengrass demo on i.MX Linux Yocto Project BSP

This readme describes how to build a demo image with AWS IoT Greengrass
support for an i.MX device, how to qualify the device using the AWS IoT
Device Tester (IDT) [1], and how to run the tutorial Getting Started
with AWS IoT Greengrass [2].

[1] https://aws.amazon.com/greengrass/device-tester/  
[2] https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html

## Build the image

To get the demo `repo` must be installed:

```
mkdir ~/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
PATH=${PATH}:~/bin
```

Download the demo source:

```
mkdir imx-greengrass
cd imx-greengrass
repo init -u https://source.codeaurora.org/external/imx/imx-manifest -b imx-linux-zeus -m imx-VERSION_demo_greengrass.xml
repo sync
```

Create the build folder and setup the shell for building:

```
[MACHINE=MACHINE] [DISTRO=DISTRO] . setup-greengrass BUILD-DIR
```

The setup will default to `MACHINE=imx6qpsabresd` and
`DISTRO=fsl-imx-xwayland`. Note that the same build folder can (and
should) be shared with multiple MACHINEs, but a different build folder
is required for each DISTRO.

If the build folder already exists, the setup-greengrass command
must be run again to setup a new shell.

Create and configure an AWS account:

https://docs.aws.amazon.com/greengrass/latest/developerguide/dev-tst-prereqs.html

Download a root CA certificate as discussed in the Greengrass tutorial:

https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-device-start.html

Download the security resources for the Greengrass core from the AWS
IoT console:

https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html#gg-core-download

Copy the root CA certificate and the security resources into the BSP.
Note that the security resources archive `HASH-setup.tar.gz` must be
renamed to `my-core-setup.tar.gz`:

```
cp root.ca.pem imx-greengrass
cp HASH-setup.tar.gz imx-greengrass/my-core-setup.tar.gz
```

Build the image:

```
bitbake imx-image-multimedia
```

Program the SD card for the device:

```
cd tmp/deploy/images/MACHINE
bunzip2 imx-image-multimedia-MACHINE.wic.bz2
sudo dd if=imx-image-multimedia-MACHINE.wic of=/dev/sdDRIVE bs=1M
sync
```

## Qualify the device

This section shows how to verify that the device is qualified to run
Greengrass. The section will approximately follow the IDT User Guide.

https://docs.aws.amazon.com/greengrass/latest/developerguide/device-tester-for-greengrass-ug.html

### Run the Greengrass dependency checker

Download and run the Greengrass dependency checker on the device:

```
wget https://github.com/aws-samples/aws-greengrass-samples/raw/master/greengrass-dependency-checker-GGCv1.10.x.zip
unzip greengrass-dependency-checker-GGCv1.10.x.zip
cd greengrass-dependency-checker-GGCv1.10.x
sudo modprobe configs
sudo ./check_ggc_dependencies
```

### Run the IoT Device Tester

Download IDT for AWS IoT Greengrass:

https://docs.aws.amazon.com/greengrass/latest/developerguide/dev-tst-prereqs.html

Configure the device. Follow the instructions to a) configure IDT to
Use an Existing Installation of Greengrass (Option 2), and b) configure
the host computer:

https://docs.aws.amazon.com/greengrass/latest/developerguide/device-config-setup.html

Set IDT to use the AWS account credentials created earlier:

https://docs.aws.amazon.com/greengrass/latest/developerguide/set-config.html

Run the test:

https://docs.aws.amazon.com/greengrass/latest/developerguide/run-tests.html

## Run the tutorial

Instructions for running the Greengrass tutorial on the device are
here:

https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-gs.html

Modules 1 and 2 can be skipped if the device image was created with
this README.

The Greengrass core credentials and configuration are overwritten by
IDT. Please restore them before running the tutorial.
