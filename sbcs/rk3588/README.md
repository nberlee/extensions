# RK3588 extension

## Installation

See [Installing Extensions](https://github.com/siderolabs/extensions#installing-extensions).

## Usage

The following modules to to loaded manually, so add this to the talos config:
```yaml
machine:
  kernel:
    modules:
      - name: rockchip-cpufreq
```

The rest is automatically detected and loaded based on the device-tree.

Provides:

* `rockchip-cpufreq`: his module is responsible for CPU frequency scaling on Rockchip processors. It manages the CPU's operating frequency and voltage to balance performance and power consumption.
* `rk_crypto2`: A cryptographic driver module for Rockchip SoCs, providing hardware acceleration for cryptographic operations like encryption and decryption, enhancing performance in cryptographic tasks. Currently only hashes are supported.
* `phy-rockchip-naneng-combphy`: Enable this to support the Rockchip PCIe/USB3.0/SATA/QSGMII combo PHY with NaNeng IP block.
* `phy-rockchip-snps-pcie3`: A PHY driver module for Rockchip's Synopsys PCIe3, it manages the physical layer interface for PCI Express 3.0, ensuring reliable and efficient data transmission over PCIe connections.
* `rk808-regulator`:  This module controls voltage regulators in the RK808 chip. It's used for managing power supply to various components of the system, ensuring stable operation and power efficiency.
* `rk8xx-core`: A core driver for the RK8xx series of Multi-Function Devices (MFD). This module provides support and interfaces for various integrated functions like power management and others within RK8xx chips.
* `rk8xx-spi`: This module is specifically for SPI (Serial Peripheral Interface) support in RK8xx series chips. It facilitates communication with devices connected via SPI, a common interface used in embedded systems.
* `sm3_generic`: A generic implementation of the SM3 cryptographic hash algorithm. This module provides functionality for generating secure hash values, commonly used in data integrity checks and digital signatures. Need for `rk_crypto2`.
* `rockchip-rng`: A hardware random number generator module for Rockchip SoCs. It provides high-quality random numbers, crucial for cryptographic applications and ensuring system security.
