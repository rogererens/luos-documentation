## Hello world: compile and upload your first program

The aim of this first tutorial is to explain the procedure to compile, upload, and run a simple program on the development board STM32F072B-DISCO.

In embedded, hello world examples usually consider simple GPIO manipulation, typically configuring, reading and writing on a digital pin. We will do just that by turning on or off the blue LED  of the STM32F072B-DISCO board according to the state of the user button (the blue one) on the board.

### Step 1: A look at the code

The program is called [led_button.rs](https://github.com/pollen-robotics/luos/blob/tutorial-led-button/examples/led_button.rs) and can be found in the [examples](https://github.com/pollen-robotics/luos/blob/tutorial-led-button/examples/) folder at the root of the Luos repository. Using the examples folder to store example programs is a convention in Rust.

For our tutorials, we assume you have basic experience with programming so reading a code should not be problem to you. So let's have directly a look at the code, it is documented in details.


```rust
/*
This example demonstrate how to turn an led on/off depending on the state of a button.
It demonstrate how to access low level hardware from luos.

Board: STM32F072B-DISCO
Tested on: 16/01/2018
*/

// #![no_std] is needed when compiling for embedded
//we need to compile parts of the standard library for the target
#![no_std]

// import the luos crate
// luos contains a hal (hardware abastraction layer)
// luos::hal contains the functions to access peripherals
// luos::hal::gpio contains specific functions and constants to access, read and write on pins
extern crate luos;
use luos::hal::gpio;

// intialize constants for each pin we want to use
// gpio::Pin contains enums for each pin available on the microprocessor
// on the STM32F072B-DISCO board:
// - gpio::Pin::PC7 is a led pin
// - gpio::Pin::PA0 is the user button (the blue one on the board)
const PIN_LED: gpio::Pin = gpio::Pin::PC7;
const PIN_BUTTON: gpio::Pin = gpio::Pin::PA0;

// main() is the start of our program
fn main() {
    // declare `led` as an output pin on PIN_LED
    // `led` is mutable: setting the pin to high or low requires to borrow a the variable
    let mut led = gpio::Output::setup(PIN_LED);

    // declare `button` as an input pin on PIN_BUTTON
    let button = gpio::Input::setup(PIN_BUTTON);

    // in embedded your main program loops forever
    loop {
        // read state of the button
        // the read function returns a boolean: True or False
        if button.read() {
            // if button is pressed
            // turn led on -> set pin to high
            led.high();
        } else {
            // is button is released
            // turn led off -> set pin to low
            led.low();
        }
    }
}
```

> Note: For the very advanced and adventurous of you, you can follow the crate tail and to find out how the hal is implemented. Hint - look in the [src/lib.rs](https://github.com/pollen-robotics/luos/blob/master/src/lib.rs) file and the [Cargo.toml](https://github.com/pollen-robotics/luos/blob/master/Cargo.toml). They will lead you to [this crate](https://github.com/pollen-robotics/stm32f0/). This is where we go low-level and set some microprocessor registry, explaining all of this is out of the scope of this tutorial. It is Luos jobs to abstract all that complexity for you, so do not worry about it.

At this stage, you already have an idea of the kind of API we will provide to access hardware functionalities. However, the goal of Luos will be to hide these direct hal low-level access.

Luos will rather provide standard traits for each type of sensors, actuators, filters, and anything roboticists need. We will introduce these abstraction later in this tutorial series.

Let's compile this code now.

### Step 2: Compiling the code

To compile your code for an embedded target, you need to use the ```build``` of  ```xargo``` and provide a target (via --target) and the program to be compiled.

Our target for the STM32F072B-DISCO board is called ```thumbv6m-none-eabi```.

The program is in the examples folder. The examples folder has been standardized in Rust for a reason, it makes it easier for people to share and work on Rust project as they all share the same structure. So they also built in Cargo, and Xargo, a compilation option specific for programs in the examples folder, it is ```--example```

Our final command to compile our examples for the STM32F072B-DISCO board is:
```bash
xargo build --target thumbv6m-none-eabi --example led_button
```

Run it now. You should see something like that:

<img src="https://www.pollen-robotics.com/uploads/tutorials/01/first_build.png">

Congratulation you just cross-compiled a Rust code program for embedded!

Compiled code is stored in the target folder at the root of the project. You will see a thumbv6m-none-eabi subfolder with many more subfolders and files created by the compiler.

>Tips: If you need to recompile the program several times after changes, sometimes it is good to clean Cargo history and start afresh. You can do it by running:
```bash
cargo clean
```

To known more about compiling in Rust, we recommend reading more about Cargo and Xargo, as well as having a look at the [Cargo.toml](https://github.com/pollen-robotics/luos/blob/master/Cargo.toml) and [Xargo.toml](https://github.com/pollen-robotics/luos/blob/master/Xargo.toml) files.


### Step 3: Uploading and running the code

At the moment this step requires to have two terminal open at once. One will run ```openocd``` to detect and make a link with the board. The other will run ```arm-none-eabi-gdb``` to push the code on the board through that connection.

But first, you need to plug your board to the computer via USB. There is two usb port, the one to upload the program is the one in the middle of the board. A picture is better than a thousand words.

<img src="https://www.pollen-robotics.com/uploads/tutorials/01/usb_to_board.jpg">

On the first terminal, run:
```bash
openocd -f $OPENCD_ROOT/interface/stlink-v2.cfg -f $OPENCD_ROOT/target/stm32f0x.cfg
```
 Where:
 - ```-f $OPENCD_ROOT/interface/stlink-v2.cfg``` is the description of the XYZ that is on the STM32F072B-DISCO board
 - ```-f $OPENCD_ROOT/target/stm32f0x.cfg``` is the description of the microprocessor that is on the STM32F072B-DISCO board

> Tips: Alternatively we created a [openocd_stm32f0x.sh](../../.scripts/openocd_stm32f0x.sh) script in the [.script](../../.scripts) folder at the root of this repo so you do not have to remember the command by heart

The connection is now open with the board.

On the second terminal, run:
```bash
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/led_button
```
Where ```target/thumbv6m-none-eabi/debug/examples/led_button``` is the folder containing the compiled code to be uploaded to the board.

> Note: This command as written assumes you are currently at luos root folder. Change the path argument accordingly

Some things will be displayed on each terminal certifying a connection is being made with the board and the program as been sent.

The second terminal will enter debugging mode, which means the program will be initially stopped. To start executing the code on the board, simply type ```continue``` and press enter.


At this point a GIF will help I am sure to better understand this process.

<img src="https://www.pollen-robotics.com/uploads/tutorials/01/screen_upload_to_board.gif">

> Note: To quit ```openocd``` you can simply ctrl-c. To quit ```arm-none-eabi-gdb``` you can ctrl-c and the type the command ```quit```.

And voila! You have a functioning Rust program loaded in your board. When you press the blue user button a led should turn on. Let me show you:

<img src="https://www.pollen-robotics.com/uploads/tutorials/01/video_led_button.gif">

> Note: There is two buttons on the board, a blue one labelled USER and a black one labelled RESET. I am sure you know what I am going to say, use the blue one.

> Tips: The program will stay loaded on the board until you upload a new one. You do not need the terminal open, you just need to provide power to the board.

Ok, that is not revolutionizing robotics just yet ^^, but I am sure you see the potential and where this is going. So stay with us, in the next tutorials we will progressively use more complex embedded features and build real robots.

### Bonus: following the program step by step

In the current procedure, you might have noticed we are compiling and running our program in debug mode. This is for us to be able to follow it step by step.

> Note: It is possible to compile and push the code in release mode, that is fully optimized. Simply add the ```-- release``` option to the build command in step 2. And then link to the corresponding target when upload the firmware in step 3, that is using the release target instead of the debug one: ```target/thumbv6m-none-eabi/release/examples/led_button```

In practice compiling in debug mode adds a lot of additional information to the program for the debugger to follow. It also remove all optimization of code which again helps a lot to follow the code as you wrote it.

```gdb``` stands for [GNU Debugger](https://en.wikipedia.org/wiki/GNU_Debugger) and ''offers extensive facilities for tracing and altering the execution of computer programs''.

When you run the command ```
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/led_button``` you actually run the code on the board via [_gdb_](https://www.gnu.org/software/gdb/) through [_openocd_](http://openocd.org/). _gdb_ come with additional commands. When we asked you to type ```continue```, this is the command than run the program until termination or at a breakpoint.

The functions you really need to know are:

- ```c``` or ```continue```: Continue running your program (after stopping, e.g. at a breakpoint).
- ```n``` or ```next```: Execute next program line (after stopping); step over any function calls in the line.
- ```s``` or ```step```: Execute next program line (after stopping); step into any function calls in the line.
- ```b [filename:]linenum``` or ```break [filename:]linenum```: Set a breakpoint at the specified line in the specified filename.
- ```clear [filename:]linenum```: Delete any breakpoints set at or within the code of the specified line.
- ```q``` or ```quit```: Exit from _gdb_

Let's use them to follow our program:

- first start the program ```
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/led_button```
- then set a break point in the loop of [led_button.rs](../../examples_led_button.rs) - that is line 38, by running: ```b led_button.rs:38```
- then continue the program by running: ```c```. The program will stop at the breakpoint line 38
- you can now go step by step by running ```n``` a few times and notice you are goind throught the ```button.read()``` state and the ```led.low()``` or ```led.high()``` depending on if you press or release the blue button.
> Note: _gdb_ will show you the code that is about to be executed, so the state of the button will be read only once you proceed to run the code. Same thing with the led that will turn on or off only once you proceed to the next step.
- to run one full loop cycle, you can simply run ```c``` and the program will execute the all loop until it reaches the breakpoint again. A simpler way to check each loop of the led_button.rs behavior.
- to run the program continuously again, remove the breakpoint first: ```clear led_button.rs:38``` and continue the program: ```c```

> Tips: A blank line as input will repeat the previous command. Useful when you want to go step by step repeating the ```n``` command as in the example above.

I know you like gif, so here is one:

<img src="https://www.pollen-robotics.com/uploads/tutorials/01/screen_gdp_tuto.gif">

For more information about debugging, you can enjoy reading the [gbd man page](https://linux.die.net/man/1/gdb).

---

### Troubleshooting

If you encountered any issues following these steps, please let us know.
