## Linux installation guide

Currently this is a replicate of the [Mac OS installation guide](./macOS.md). Only step 5, the installation of the cross-compilation toolchain, differs.

For now, it all starts by opening a terminal.

### Step 1: Rust

To install Rust, we will use [rustup](https://www.rustup.rs/), the official Rust environment manager.

Simply execute the following in your terminal:

```bash
 curl https://sh.rustup.rs -sSf | sh
```

It should look like this:

<img src="https://www.pollen-robotics.com/uploads/installation_guide/macos/install_rustup_question.png" width="600px">

The script will pause to ask you for installation options. We recommend the default. Simply type 1 and press enter in your terminal.

The result is a successful installation!

<img src="https://www.pollen-robotics.com/uploads/installation_guide/macos/install_rustup_ok.png" width="600px">

Now notice _rustup_ is asking you to configure you current shell. It ask you so because your shell needs to know the path to some executable, here cargo.

Simply type the following in the terminal:

```bash
source $HOME/.cargo/env
```

> Note that _rustup_ added ```export PATH="$HOME/.cargo/bin:$PATH"``` to your ```~/.bash_profile``` file. So you would not have to worry about this anymore.


###  Step 2: Rust nightly

Because we are working with embedded, we need a more recent version of Rust that the stable or even beta releases. Currently Luos is working with the nightly version of the 25th December 2017. Christmas day that was, why not eh! Actually, more recent changes in nightly break some APIs in one of the crate we use ([cortex-m-rt](https://github.com/japaric/cortex-m-rt/issues/49)) and are thus incompatible. We are working on fixing that real soon.

This version is called _nightly-2017-12-25_. Type the following to install it and check it is successfully installed:

```bash
rustup install nightly-2017-12-25
rustup show
```

It should look like this:

<img src="https://www.pollen-robotics.com/uploads/installation_guide/macos/rustup_install_nightly.png" width="600px">

> Note that you might end up with a more recent stable version of rustc. It is ok.

Under _installed toolchains_ notice you have two versions:
- the default stable one for your system
- the nightly-2017-12-25 for your system

###  Step 3: Xargo

_rustup_ installed the offical package manager for Rust called Cargo.

Because we work on embedded, we need an additional tool called Xargo. Xargo sits on top of Cargo and can handle cross-compilation.

To install Xargo simply type:

```bash
cargo install xargo
```

###  Step 4: rust-src

Because we will work on yet unsupported platforms, we will need to recompile all the library we use locally for our specific target.

By default, _rustup_ does not download all the source of Rust, simply because there is pre-compiled version of them available for all supported platforms.

So we need to download them, simply execute the following:

```bash
rustup component add rust-src
```

###  Step 5: Cross-compilation toolchain

Again, because we want to compile our code for a microprocessor, we need the right compiler for that. You can install it via apt-get on Ubuntu or Debian (or use your favorite package manager for other distributions):

```bash
sudo apt-get install binutils-arm-none-eabi gdb-arm-none-eabi
```

And by following the step required, mainly providing your root password.

You will also need ```openocd```. As above, you can install via your package manager. Here via apt-get:

```bash
sudo apt-get install openocd
```

###  Step 6: Luos

Almost there, you just need to download Luos now!

Navigate to your desire folder and clone the Luos repository:

```bash
git clone https://github.com/pollen-robotics/luos.git
```

You should now have a new luos folder!

*Note: If you do not use git, you can also directly download the zip folder from [GitHub](https://github.com/pollen-robotics/luos) directly.*

Now, remember we installed a specific nightly version of Rust? We now need to tell Rust to use this specific version for all Luos projects. There is a special command in _rustup_ for that:


```bash
# navigate first into the luos folder
cd luos
# overide the default Rust version to our nightly version
rustup override set nightly-2017-12-25
```

If you do a ```rustup show``` now, you will see that the _active toolchain_ is the nightly one:

<img src="https://www.pollen-robotics.com/uploads/installation_guide/macos/rustup_show_luos.png" width="600px">

###  Step 7: Testing

Let's check it all worked fine by compiling one of the examples:

```bash
xargo build --target thumbv6m-none-eabi --example led_button
```

If it all went to plan, you are all set and ready to go!

You will learn more about compiling and uploading an example to a board in the next tutorials. But first lets discover the [development board we will use for this tutorial](../tutorials/00_development_boards.md).

---

### Troubleshooting

If you encountered any issues following these steps, please let us know.
