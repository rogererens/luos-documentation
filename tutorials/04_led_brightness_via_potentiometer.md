## Reading analog values

The aim of this first tutorial is to introduce ADC functionalities. ADC stands for [analog to digital converter](https://en.wikipedia.org/wiki/Analog-to-digital_converter), it allows to capture the voltage value applied to a pin in a digital form.

The example will be to change the brightness of the blue led of the STM32F072B-DISCO board depending on the position of a potentiometer.

### Step 1: A look at the code

The program is called [led_potmeter.rs](https://github.com/pollen-robotics/luos/blob/master/examples/led_potmeter.rs) and can be found in the [examples](https://github.com/pollen-robotics/luos/blob/master/examples) folder at the root of the [Luos repository](https://github.com/pollen-robotics/luos) repository.

```rust
// This example demonstrates how to read an anlog input.
// The example change the brightness of an led depending of the position of a potentiometer
//
// Board: STM32F072B-DISCO
// Tested on: 16/01/2018

#![no_std]

// luos::hal::adc contains the function to read from an analog pin
extern crate luos;
use luos::hal::{adc, pwm, rcc};

// define pin for the LED
// PC7 is a led pin and we can set it up as PWM output
const PIN_LED: pwm::Pin = pwm::Pin::PC7;

// define pin for the analog read
// we will use the pin PA0 on the board
const PIN_ANALOG: adc::Pin = adc::Pin::PA0;
// the adc are 12 bits so values range from 0 to 4095 (4096 values)
const MAX_U12: u16 = 4095;

// main() is the start of our program
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

    // Setup the analog pin for the potentiometer
    let potentiometer = adc::Analog::setup(PIN_ANALOG);

    // the set_duty function of pwm takes an argument in percent
    // whereas the analog value are between 0 and 4095
    // thus, we create a function to convert analog values to percents
    // analog read return u16, and set_duty require f32
    fn potentiometer_in_percent(potentiometer_value: u16) -> f32 {
        // we first normalize analog value between 0 and 1, in f32
        let normalized_potentiometer = potentiometer_value as f32 / MAX_U12 as f32;
        // we return the value between 0 and 100
        normalized_potentiometer * 100.0
    }

    // in embedded your program should never end, so we loop forever
    loop {
        // we read the potentiometer
        // transform it into percent
        // and apply it to the led pwm
        led.set_duty(potentiometer_in_percent(potentiometer.read()));
    }
}
```

You can see that we introduced the ```adc``` module, which contains a ```Analog```  trait which have a ```read()``` function to get the value applied to a pin. The adc is 12 bits, hence the returned value of ```read()``` can spam from 0 to 4095.

Let's compile this program first, then we will see how to connect the potentiometer to the board.

> Note: To compile and upload the program to the board, we will follow the same steps as introduced in the hello world tutorial. For more details please refer to [01_your_first_program.md](./01_your_first_program.md).

### Step 2: Compiling the code

To compile [led_potmeter.rs](https://github.com/pollen-robotics/luos/blob/master/examples/led_potmeter.rs) for the STM32F072B-DISCO board run:
```bash
xargo build --target thumbv6m-none-eabi --example led_potmeter
```

> Tips: if you get problem compiling, you might have run into a Cargo.lock problem. Try running the following command:
```bash
rm -f Cargo.lock && cargo clean && touch Xargo.toml
```
This should clean all build history and compile everything from scratch again. We will explain more about this issue at a later time.

### Step 3: Connecting the potentiometer

To test this program, we need to apply a voltage between 0 and 3.3V to the ```PA0``` pin of the STM32F072B-DISCO board.

A simple method to to this is to use a [potentiometer](https://en.wikipedia.org/wiki/Potentiometer) to create a [voltage divider circuit](https://en.wikipedia.org/wiki/Voltage_divider) as shown in the schematic below:


<img src="https://cdn.sparkfun.com/assets/6/3/e/5/e/511ac8f5ce395f5846000000.png" width="200px">

For our setup, we need:
- Vin is the 3.3V pin and should be connected to one end of the potentiometer
- Ground is the GND pin and should be connected to one end of the potentiometer
- Vout is the PA0 pin and should be connected to the wiper terminal of the potentiometer

> Tips: To find which terminal is which on a potentiometer, use a [Ohmmeter](https://en.wikipedia.org/wiki/Ohmmeter) and measure the resistance between all pairs of terminals. The pair with the highest resistance is made of both ends of the potentiometer and should be connected to 0 and 3.3V in any order. The remaining one is the wiper terminal and should be connected to the PA0 pin.


<img src="https://www.pollen-robotics.com/uploads/tutorials/04/stmdisco_potentiometer.jpg">

We used a 10kOhms potentiometer in our setup, but you can choose any potentiometer you might have.

### Step 4: Uploading and running the code

On a first terminal, run:
```bash
openocd -f $OPENCD_ROOT/interface/stlink-v2.cfg -f $OPENCD_ROOT/target/stm32f0x.cfg
```

On a second terminal, run:
```bash
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/led_potmeter
```
Where ```target/thumbv6m-none-eabi/debug/examples/led_potmeter``` is the folder containing the compiled code to be uploaded to the board.

> Note: This command as written assumes you are currently at luos root folder. Change the path argument accordingly if it's not the case.

To start executing the code on the board, simply type ```continue``` and press enter.

And voila, the brightness of the led will change according to the position of the potentiometer!


<img src="https://www.pollen-robotics.com/uploads/tutorials/04/video_led_potmeter.gif">

You can now have fun playing with your new fine sensing capability.

---

### Troubleshooting

If you encountered any issues following these steps, please let us know.
