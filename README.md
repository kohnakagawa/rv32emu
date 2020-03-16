# NOTE
To run Zephyr applications using rv32emu, edit `zephyr/boards/riscv/qemu_riscv32/qemu_riscv32_defconfig` as follows.

```
diff --git a/boards/riscv/qemu_riscv32/qemu_riscv32_defconfig b/boards/riscv/qemu_riscv32/qemu_riscv32_defconfig
index f26eeb53f9..30e10b415d 100644
--- a/boards/riscv/qemu_riscv32/qemu_riscv32_defconfig
+++ b/boards/riscv/qemu_riscv32/qemu_riscv32_defconfig
@@ -16,3 +16,6 @@ CONFIG_RISCV_MACHINE_TIMER=y
 CONFIG_GPIO=y
 CONFIG_GPIO_SIFIVE=y
 CONFIG_SYS_CLOCK_HW_CYCLES_PER_SEC=10000000
+CONFIG_XIP=y
+CONFIG_RISCV_SOC_INTERRUPT_INIT=y
+CONFIG_MULTITHREADING=n
```

Then, build Zephyr applications as follows:

```
$ west build -b qemu_riscv32 samples/hello_world/
```

# RISC-V RV32I[MA] emulator with ELF support

This is a RISC-V emulator for the RV32I architecture, based on [TinyEMU](https://bellard.org/tinyemu/)
and stripped down for RV32I only.

Requires libelf-dev:
```shell
$ sudo apt-get install libelf-dev
```

How to compile it:
```shell
$ gcc -O3 -Wall -lelf emu-rv32i.c -o emu-rv32i
```

Run RV32I compliance tests.
Assume `emu-rv32i` in `$PATH` environment variable.
```shell
$ git clone https://github.com/riscv/riscv-compliance
$ cd riscv-compliance
$ make RISCV_PREFIX=riscv-none-embed- RISCV_DEVICE=rv32i TARGET_SIM=emu-rv32i variant
```

Compiling and running simple code:
```shell
$ riscv32-unknown-elf-gcc -O3 -nostdlib test1.c -o test1
```

or
```shell
$ riscv64-unknown-elf-gcc -march=rv32i -mabi=ilp32 -O3 -nostdlib test1.c -o test1
```

then
```shell
$ ./emu-rv32i test1
Hello RISC-V!
```

RV32M and RV32A instructions may be enabled by commenting `#define STRICT_RV32I`.

## How to build RISC-V toolchain from scratch

https://github.com/riscv/riscv-gnu-toolchain

64-bit universal version (riscv64-unknown-elf-* that can build 32-bit code too):
```shell
$ ./configure --prefix=/opt/riscv
$ make
```

32-bit version (riscv32-unknown-elf-*):
```shell
$ ./configure --prefix=/opt/riscv32 --with-arch=rv32i --with-abi=ilp32
$ make
```
