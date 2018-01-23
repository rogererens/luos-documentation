# Installation guide

Rust for embedded is still in its infancy.

## What we need

Before explaining you how to install them all, let us introduce briefly the tools and libraries we need. You might want to read briefly about them before starting.

* [rustup](https://www.rustup.rs/) is an installer for Rust. More info at [https://github.com/rust-lang-nursery/rustup.rs](https://github.com/rust-lang-nursery/rustup.rs)
* [Cargo](https://doc.rust-lang.org/1.5.0/book/hello-cargo.html) is the project and package manager for Rust. In practice it downloads your Rust project’s dependencies \(called crates\) and compiles your project. More info at: [https://github.com/rust-lang/cargo](https://github.com/rust-lang/cargo). [https://crates.io/](https://crates.io/) register all crates.
* [Xargo](https://github.com/japaric/xargo) is "Cargo for embedded". It helps cross compile Rust crates for non standard targets.
* [gcc-arm-embedded toolchain](https://developer.arm.com/open-source/gnu-toolchain/gnu-rm) are the tools needed to cross-compile our code for arm targets: "The GNU Arm Embedded toolchains are integrated and validated packages featuring the Arm Embedded GCC compiler, libraries and other GNU tools necessary for bare-metal software development on devices based on the Arm Cortex-M and Cortex-R processors."
* [openocd](http://openocd.org/) helps you upload and debug a program on a chip. _openocd_ means "Open On-Chip Debugger". You do not need to know much about it, but it is needed for now to interact with the development board.

## How to install them

We provide guidelines to install all those tools on your system. If you are already a Rust user most of them will feel very familiar. So feel free to adapt all those steps to your needs.

* [MacOS](/installation_guide/macOS.md)
* [Linux](/installation_guide/linux.md)
* [Windows](/installation_guide/windows.md)



