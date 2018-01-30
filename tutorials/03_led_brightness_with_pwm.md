## Taking a deep breath, using PWM to change led brightness

The aim of this first tutorial is to introduce [PWM](https://en.wikipedia.org/wiki/Pulse-width_modulation) functionalities. More specifically, we will experiment with creating and changing the [duty cycle](https://en.wikipedia.org/wiki/Duty_cycle) of a PWM signal.

The example will be to change periodically and smoothly the brightness of the blue led of the STM32F072B-DISCO board at a desired frequency.

### Step 1: A look at the code

The program is called [led_breathe.rs](https://github.com/pollen-robotics/luos/blob/master/examples/led_breathe.rs) and can be found in the [examples](https://github.com/pollen-robotics/luos/blob/master/examples) folder at the root of the [Luos repository](https://github.com/pollen-robotics/luos) repository.

```rust
// This example demonstrates how to use the pwm functionalities and use it to change the brightness of the led
//
// Board: STM32F072B-DISCO
// Tested on: 30/01/2018

#![no_std]

// luos::hal::pwm contains function relative to pwm
// we also need luos::hal::rcc that handle and setup the clocks in the microcontroller
extern crate luos;
use luos::hal::{pwm, rcc};

// initialize constants for the pin we want to use
// pwm::Pin contains enums for each pin available on the microcontroller PWM
// on the STM32F072B-DISCO board, PC7 is a led pin and we can set it up as PWM output
const PIN_LED: pwm::Pin = pwm::Pin::PC7;
// compared to the led_toggle example, the pin is the same PC7 but we access it via the pwm module
// this will be changed in the furture for a more consistent API

fn main() {
    // initialize rcc
    // it set some register in the microcontroller regarding frequency of timers
    rcc::init();

    // declare `led` as an output PWM
    let led = pwm::Pwm::init(PIN_LED);
    // We need to set the frequency of the pwm
    // Here we arbitrarily choose to generate a pwm at 500hz
    led.set_frequency(500);
    // Set the duty cycle to 0%
    led.set_duty(0.0);
    // enable the pwm on the pin
    led.enable();

    // in embedded your program should never end, so we loop forever
    loop {
        // we want to have a breathing led
        // to do so we want to repeatidly increase and decrease the brightness of the led
        // this is done by changing the duty cycle of the PWM from 0 to 100 % at fixed time intervals

        // first increase brightness from 0 to 100 percent
        for percent in 0..101 {
            led.set_duty(percent as f32); // set_duty requires a f32
            rcc::ms_delay(10); //pause for 10 ms
        }
        // then decrease brightness from 100 to 0 percent
        // .rev() on the range
        for percent in (0..101).rev() {
            led.set_duty(percent as f32); // set_duty requires a f32
            rcc::ms_delay(10); //pause for 10 ms
        }
        // repeat
    }
}
```

You can see that we introduced the ```pwm``` module, which contains a ```Pwm```  trait which have a ```set_duty``` function to change the duty cycle of the pwm.

Let's run this program, the led will smoothly change its brightness, from fully off to fully on, with a full cycle spamming 2 seconds.

> Note: To compile and upload the program to the board, we will follow the same steps as introduced in the hello world tutorial. For more details please refer to [01_your_first_program.md](./01_your_first_program.md).

### Step 2: Compiling the code

To compile [led_breathe.rs](https://github.com/pollen-robotics/luos/blob/master/examples/led_breathe.rs) for the STM32F072B-DISCO board run:
```bash
xargo build --target thumbv6m-none-eabi --example led_breathe
```

> Tips: if you get problem compiling, you might have run into a Cargo.lock problem. Try running the following command:
```bash
rm -f Cargo.lock && cargo clean && touch Xargo.toml
```
This should clean all build history and compile everything from scratch again. We will explain more about this issue at a later time.

### Step 3: Uploading and running the code

On a first terminal, run:
```bash
openocd -f $OPENCD_ROOT/interface/stlink-v2.cfg -f $OPENCD_ROOT/target/stm32f0x.cfg
```

On a second terminal, run:
```bash
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/led_breathe
```
Where ```target/thumbv6m-none-eabi/debug/examples/led_breathe``` is the folder containing the compiled code to be uploaded to the board.

> Note: This command as written assumes you are currently at luos root folder. Change the path argument accordingly if it's not the case.

To start executing the code on the board, simply type ```continue``` and press enter.

And voila, your board is breathing!

<img src="https://www.pollen-robotics.com/uploads/tutorials/03/video_led_breathe.gif">

You can now have fun playing with the delays and loop, or even adding different behavior depending on the user button state.

---

### Troubleshooting

If you encountered any issues following these steps, please let us know.
