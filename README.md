# Friendy fork

This is a friendly fork, only here as the Turing RK1 has a different kernel, therefor it needs different extensions for kernel module drivers.
Also please use the *rk3588* extension and make sure to add `rockchip-cpufreq` to the `machine.kernel.modules` in your machine config.
See: [rk3588](sbcs/rk3588) extension

# Talos Linux System Extensions

This repo serves as a central place for publishing supported extensions to Talos Linux.
Extensions allow for additional functionality on top of the default Talos Linux capabilities.
Things like gVisor, GPU support, etc. are good candidates for extensions.

## Using Extensions

Extensions in this repo are published as container images.
These images can be added to the the Talos Linux [boot asset](https://www.talos.dev/latest/talos-guides/install/boot-assets/) to produce a final boot asset containing a base Talos `initramfs` and
a set of [system extensions](https://www.talos.dev/latest/talos-guides/configuration/system-extensions/) appended to it.

The extension image is composed of a `manifest.yaml` file that provides information and compatibility information, as well as a `rootfs` that contains things like compiled binaries that are bind mounted into the system.

## Installing Extensions

In order to find a container reference for a system extension compatible with your Talos Linux version, you can use the following command:

```bash
crane export ghcr.io/nberlee/extensions:v<talos-version> | tar x -O image-digests | grep <extension-name>
```

For example, to find a compatible version of the `gasket-driver` extension for Talos v1.5.3, you can run:

```bash
$ crane export ghcr.io/nberlee/extensions:v1.5.3 | tar x -O image-digests | grep gasket-driver
ghcr.io/nberlee/gasket-driver:97aeba58-v1.5.3@sha256:c786edb356edae3b451cb82d5322f94e54ea0710195181b93ae37ccc8e7ba908
```

Please always use the pinned digest when referencing an extension image.

## Extension Catalog

### Container Runtimes

| Name                                | Image                                                                                       | Description                                     | Version Format                     |
| ----------------------------------- | ------------------------------------------------------------------------------------------- | ----------------------------------------------- | ---------------------------------- |

### Firmware

| Name                                               | Image                                                                                                               | Description                 | Version Format           |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------- | ------------------------ |

### Drivers

| Name                                 | Image                                                                                                       | Description                          | Version Format                                        |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------- | ------------------------------------ | ----------------------------------------------------- |
| [usb-modem](drivers/usb-modem/)      | [ghcr.io/nberlee/usb-modem-drivers](https://github.com/nberlee/extensions/pkgs/container/usb-modem-drivers) | USB Modem drivers                    | `talos version`                                       |

### Miscellaneous

| Name                            | Image                                                                                             | Description                        | Version Format     |
| ------------------------------- | ------------------------------------------------------------------------------------------------- | ---------------------------------- | ------------------ |
| [binfmt-misc](misc/binfmt-misc) | [ghcr.io/nberlee/binfmt-misc](https://github.com/nberlee/extensions/pkgs/container/binfmt-misc) | Miscellaneous Binary Format | `talos version`                                       |

### Network

| Name                            | Image                                                                                             | Description                        | Version Format     |
| ------------------------------- | ------------------------------------------------------------------------------------------------- | ---------------------------------- | ------------------ |

### SBCs
| Name                  | Image                                                          | Description                       | Version Format     |
| --------------------- | -------------------------------------------------------------- | --------------------------------- | ------------------ |
| [rk3588](sbcs/rk3588) | [ghcr.io/nberlee/rk3588](https://github.com/nberlee/extensions | Support modules for RK3588 boards | `talos version`    |


### Storage

| Name                                | Image                                                                                                 | Description         | Version Format                     |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------- | ---------------------------------- |

### Power

| Name                            | Image                                                                                                     | Description                                                    | Version Format                     |
| ------------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------------------------------- |

### Guest Agents

| Name                                                   | Image                                                                                                                     | Description                                                            | Version Format     |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- | ------------------ |

### NVIDIA GPU

| Name                                                             | Description                                                                                                                        | Version Format                     |
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- |

#### Tools

| Name                                  | Description                        | Version Format  |
| ------------------------------------- | ---------------------------------- | --------------- |

## Building Extensions

In the current form, building extensions requires the use of our [bldr](https://github.com/siderolabs/bldr) tool.
It is highly recommended to take a look at an existing extensions as a template for building your own.
The rough flow should look like the following:

- Create a `manifest.yaml` file that contains information about your system extension. See instructions below for this file.
- Create a `pkg.yaml` file that details the full flow of downloading, building, installing your application.
- Once you have these, add your extension to the `TARGETS` list in the `Makefile`.
- You can now build your extension using make like `make <extension-name> PLATFORM=linux/amd64`
- If you wish to output the contents of the image and validate your install, you can issue `make local-<extension-name> PLATFORM=linux/amd64 DEST=_out`. The contents will then be present in the `_out` directory.

### Creating `manifest.yaml`

The `manifest.yaml` file should match the following format:

```yaml
version: v1alpha1
metadata:
  name: <extension name>
  version: <version of the package the extension installs>-<version of the extensions repo (tracks with talos version)>
  author: Andrew Rynhard
  description: |
    <detailed description of the extension/package>
  ## The compatibility section is "optional" but highly recommended to specify a Talos version that
  ## has been tested and known working for this extension.
  compatibility:
    talos:
      version: ">= v1.0.0"
```

### Creating `pkg.yaml`

Creating a `pkg.yaml` file is the normal process from bldr.
See instructions [here](https://github.com/siderolabs/bldr#pkgyaml) for details and examples on this format.
Using other existing extensions in this repo for tips is also highly recommended.
One important note is that the final directory tree of the generated package should look like this example from the `gvisor` package:

```bash
├── manifest.yaml
└── rootfs
    ├── etc
    │   └── cri
    │       └── conf.d
    │           └── gvisor.part
    └── usr
        └── local
            └── bin
                ├── containerd-shim-runsc-v1
                └── runsc

```

Note that the `manifest.yaml` file lives at the root, while all installed files live under `/rootfs` with the full tree of where they should live on the eventual Talos Linux install.

### `rootfs` Restrictions

The following restrictions are applied to the contents of the `rootfs` of the system extension:

- no special files (FIFOs, devices, etc.)
- no world-writeable files or directories

Any paths in the `rootfs` should be contained within the following hierarchies:

- `/etc/cri/conf.d/`
- `/lib/firmware/`
- `/lib/modules/`
- `/lib64/ld-linux-x86-64.so.2`
- `/usr/etc/udev/rules.d/`
- `/usr/local/`
- `/usr/share/glvnd/`
- `/usr/share/egl/`
- `/etc/vulkan/`

## Dependency Diagram

![Dependency Diagram](/deps.png)
