## Taking a short break or how to pause your program for a bit

The aim of this first tutorial is to introduce simple time base functionalities. More specifically, we will experiment with pausing a program for a specific amount of time.

The example will be to toggle on and off the blue led of the STM32F072B-DISCO board at a desired frequency.

### Step 1: A look at the code

The program is called [led_toggle.rs](**TODO**) and can be found in the [examples](**TODO**) folder at the root of the Luos repository.

```rust
// This example demonstrates how to blink an led on/off at a defined frequency
// It demonstrates how to access low level hardware from luos and use basic time functionalities
//
// Board: STM32F072B-DISCO
// Tested on: 16/01/2018

#![no_std]
/// #![no_std] is needed when compiling for embedded
// We need to specify to the compiler that we don't want to compile the whole standard library
// because it's way too big and would not work on our tiny microcontroller.

// import the luos crate
// luos contains a hal module (hardware abastraction layer)
// luos::hal contains the functions to access peripherals
// luos::hal::gpio contains specific functions and constants to access, read and write on pins
// luos::hal::rcc contains function relative to clocks
extern crate luos;
use luos::hal::{gpio, rcc};

// intialize constants for the pin we want to use
// gpio::Pin contains enums for each pin available on the microcontroller
// on the STM32F072B-DISCO board:
// - gpio::Pin::PC7 is a led pin
const PIN_LED: gpio::Pin = gpio::Pin::PC7;

// main() is the start of our program
fn main() {
    // initialize rcc
    // it set some register in the microcontroller regarding frequency of timers
    rcc::init();

    // declare `led` as an output pin on PIN_LED
    // `led` is mutable as setting the pin to high or low requires to modify it.
    let mut led = gpio::Output::setup(PIN_LED);

    // in embedded your program should never end, so we loop forever
    loop {
        // turn led on -> set pin to high
        led.high();
        // pause for 1 sec -> 1000 ms
        rcc::ms_delay(1000);
        // turn led off -> set pin to low
        led.low();
        // pause for 1 sec -> 1000 ms
        rcc::ms_delay(1000);
    }
}
```

You can see that we introduced the ```rcc``` module, which contains a ```ms_delay()``` function that pause the program for a specified number of millisecond.

Let's run this program, the led will toggle on and off every second.

> Note: To compile and upload the program to the board, we will follow the same steps as introduced in the hello world tutorial. For more details please refer to [01_your_first_program.md](./01_your_first_program.md).

### Step 2: Compiling the code

To compile [led_toggle.rs](https://github.com/pollen-robotics/luos/blob/tutorial-led-button/examples/led_toggle.rs) for the STM32F072B-DISCO board run:
```bash
xargo build --target thumbv6m-none-eabi --example led_toggle
```

### Step 3: Uploading and running the code

On a first terminal, run:
```bash
openocd -f $OPENCD_ROOT/interface/stlink-v2.cfg -f $OPENCD_ROOT/target/stm32f0x.cfg
```

On a second terminal, run:
```bash
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/led_toggle
```
Where ```target/thumbv6m-none-eabi/debug/examples/led_toggle``` is the folder containing the compiled code to be uploaded to the board.

> Note: This command as written assumes you are currently at luos root folder. Change the path argument accordingly if it's not the case.

To start executing the code on the board, simply type ```continue``` and press enter.

And voila! You have a functioning Rust program loaded in your board and making use of basic time functionalities. The led blinks on its own at a defined frequency.

<img src="https://www.pollen-robotics.com/uploads/tutorials/02/video_led_toggle.gif">

You can now have fun playing with the delays. You could send ''luos'' using [morse code](https://en.wikipedia.org/wiki/Morse_code) for example ^^

---

### Troubleshooting

If you encountered any issues following these steps, please let us know.
