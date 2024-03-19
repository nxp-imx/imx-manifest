i.MX Repo Manifest README
=========================

This repo is used to download manifests for i.MX BSP releases.

Specific instructions reside in READMEs in each branch.

The branch name is based on the release type, Linux or Android, and the Yocto Project release name, with manifests in each branch tied to the base BSP release.

For example, for i.MX Linux BSP releases based on Yocto Project `Nanbield`, the branch is `imx-linux-nanbield`.

Install the `repo` utility:
---------------------------

To use this manifest repo, the `repo` tool must be installed first.

```
$: mkdir ~/bin
$: curl http://commondatastorage.googleapis.com/git-repo-downloads/repo  > ~/bin/repo
$: chmod a+x ~/bin/repo
$: PATH=${PATH}:~/bin
```

Download the Yocto Project BSP
------------------------------

```
$: mkdir <release>
$: cd <release>
$: repo init -u https://github.com/nxp-imx/imx-manifest -b <branch name> [ -m <release manifest>]
$: repo sync
```

Each branch has detailed READMEs describing exact syntax.

Examples
--------

To download the 6.6.3-1.0.0 release
```
$: repo init -u https://github.com/nxp-imx/imx-manifest -b imx-linux-nanbield -m imx-6.6.3-1.0.0.xml
```

Setup the build folder for a BSP release:
-----------------------------------------

Note: The remaining instructions are for setting up a BSP release only. For setting
up a demo, please see `imx-manifest/README-<demo>` for further instructions.

```
$: [MACHINE=<machine>] [DISTRO=fsl-imx-<backend>] source ./imx-setup-release.sh -b bld-<backend>

<machine>   defaults to `imx6qsabresd`
<backend>   Graphics backend type
    xwayland    Wayland with X11 support - default distro
    wayland     Wayland
    fb          Framebuffer (not supported for mx8)
```

Note: If the poky community distro is used, then build breaks will happen with some
components using our `meta-imx` layer.

Examples:
- Setup for XWayland.
```
$: MACHINE=imx8mnevk DISTRO=fsl-imx-xwayland source ./imx-setup-release.sh -b bld-xwayland
```

Build an image:
---------------

```
$: bitbake <image recipe>
```

Some image recipes:

Image Name           | Description
---------------------|---------------------------------------------------
imx-image-core       | core image with basic graphics and no multimedia
imx-image-multimedia | image with multimedia and graphics
imx-image-full       | image with multimedia and machine learning and Qt
