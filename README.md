Robotics Edge Platform Manifest README
=====================================

This repo is used to download manifests for Robotics Edge Platform releases.

Install the `repo` utility
--------------------------
To get Robotics Edge Platform ROS2 release you need to have `repo` installed.

```
$ mkdir ~/bin
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
$ PATH=${PATH}:~/bin
```

Install essential host packages
------------------------------
Your Build Host must install required packages for the Yocto build.
Reference to the section "Build Host Packages" in the document "Yocto Project Quick build".
- https://docs.yoctoproject.org/5.2.4/brief-yoctoprojectqs/index.html#build-host-packages

Download the Yocto Project BSP
------------------------------
```
$ mkdir robotics-edge
$ cd robotics-edge
$ repo init -u https://github.com/nxp-imx/imx-manifest -b robotics-edge-walnascar -m robotics-edge-1.0.0.xml

$ repo sync
```


Setup build project
-------------------------

```
$ [MACHINE=<MACHINE>] [DISTRO=<DISTRO>] source robotics-edge-setup.sh -b build[-<DISTRO>] -r [ROS2_DISTRO]
```

MACHINE defaults to `imx95evk`.
- This corresponds to the machine configuration file at `sources/meta-imx/meta-bsp/conf/machine/imx95evk.conf`.
- Other machine config files are in the same folder.

DISTRO defaults to `robotics-edge`.

Note that the tmp folder is distro-specific but not machine-specific. All machines can be built in the same tmp folder as long as the distro is the same. It is common to use a different build folder for each distro required. It is possible to switch the distro of a build folder, but the tmp folder must be deleted before rebuilding. Be warned that this will delete the output of the previous build, which is contained in tmp/deploy.

ROS2_DISTRO defaults to `jazzy`


Examples
-------------------------
```
$ DISTRO=robotics-edge MACHINE=imx95evk source robotics-edge-setup.sh -b build -r jazzy
```


Use an existing build folder
----------------------------
If you want to build in an existing build folder:

```
$ cd robotics-edge
$ source setup-environment build
```


Build an image
--------------
```
$ bitbake <IMAGE-RECIPE>
```

Some image recipes:

Image Name                      | Description
--------------------------------|---------------------------------------------------
robotics-edge-image-core        | image with i\.MX core and ROS core
robotics-edge-image-multimedia  | image with i\.MX multimedia and ROS base
robotics-edge-image-full        | image with i\.MX multimedia, machine learning, Qt and ROS base


Examples
--------------
```
$ bitbake robotics-edge-image-full
```
