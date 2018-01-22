## Glossary

We know how frustating it is sometimes to join a new project or a new community. Most of the time is spent trying to understand what people say, it all sounds very technical. Most of the time it is really nothing difficult, you just need someone to explain it to you once in simple terms.

This page is our attempt to do just that. It hosts a list of library/crate names and their function as well as other jargon we use often.

Let us know if you would like anything added to this list or if an explantion is not clear enough. Feel free to contribute to this list via a pull request.

### Rust vanilla

- Rust

- [rustup](https://www.rustup.rs/) is an installer for Rust. More info at [https://github.com/rust-lang-nursery/rustup.rs](https://github.com/rust-lang-nursery/rustup.rs)

- [Cargo](https://doc.rust-lang.org/1.5.0/book/hello-cargo.html) is a project and package manager for Rust. In practice it downloads your Rust project’s dependencies \(called crates\) and compiles your project. More info at: [https://github.com/rust-lang/cargo](https://github.com/rust-lang/cargo). [https://crates.io/](https://crates.io/) register all crates.

- rust-fmt

### Rust for embedded

- [Xargo](https://github.com/japaric/xargo) is "Cargo for embedded". It helps cross compile Rust crates for targets that don't have binary releases of the standard crates.

- rust-src

- cortex-m

- cortex-m-rt

- [gcc-arm-embedded toolchain](https://developer.arm.com/open-source/gnu-toolchain/gnu-rm) are the tools needed to cross-compile our code for arm targets: "The GNU Arm Embedded toolchains are integrated and validated packages featuring the Arm Embedded GCC compiler, libraries and other GNU tools necessary for bare-metal software development on devices based on the Arm Cortex-M and Cortex-R processors."

- [openocd](http://openocd.org/) helps you upload and debug a program on a chip. _openocd_ means "Open On-Chip Debugger". You do not need to know much about it, but it is needed for now to interact with the development board.

- target

- cross-compilation
