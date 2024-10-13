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
* `sm3_generic`: A generic implementation of the SM3 cryptographic hash algorithm. This module provides functionality for generating secure hash values, commonly used in data integrity checks and digital signatures. Need for `rk_crypto2`.
* `rockchip-rng`: A hardware random number generator module for Rockchip SoCs. It provides high-quality random numbers, crucial for cryptographic applications and ensuring system security.
