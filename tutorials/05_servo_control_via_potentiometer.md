## Build a remote controlled elastic gun

The aim of this first tutorial is to introduce servomotors control functionalities.You can learn more about what a servomotor is and how to control it [here](https://learn.sparkfun.com/tutorials/hobby-servo-tutorial).

The example will be to control the position of a servo motor depending on the position of a potentiometer.

With this new power to act in the physical world, we will build a toy project: a remote controlled elastic gun!

### Step 1: A look at the code

The program is called [servo_potmeter.rs](https://github.com/pollen-robotics/luos/blob/master/examples/servo_potmeter.rs) and can be found in the [examples](https://github.com/pollen-robotics/luos/blob/master/examples) folder at the root of the [Luos repository](https://github.com/pollen-robotics/luos) repository.

```rust
// This example demonstrates how to read an anlog input.
// The example change the brightness of an led depending of the position of a potentiometer
//
// Board: STM32F072B-DISCO
// Tested on: 16/01/2018

#![no_std]

// luos::hal::servo contains the utils to control a servo
extern crate luos;
use luos::hal::{adc, pwm, rcc, servo};

// servo::Pin contains enums for each pin available for servo on the microcontroller
// on the STM32F072B-DISCO board:

// servo make use internally of pwm functionalities
// servo are controled using a specific pwm frequency (50)
// servo position is controleld via a constrained range of duty cycle
// hence we control a servo on a pwm Pin
// we choose the pin PB4 on the STM32F072B-DISCO
const PIN_SERVO: pwm::Pin = pwm::Pin::PB4;

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

    // declare `servo` and init it
    let servo = servo::Servo::init(PIN_SERVO);

    // Setup the analog pin for the potentiometer
    let potentiometer = adc::Analog::setup(PIN_ANALOG);

    // as for led_potmeter example we need to convert analog read values, here to degrees
    // analog read return u16,
    // servo.set_position requires f32 in degrees
    fn potentiometer_in_degree(potentiometer_value: u16) -> f32 {
        // we first normalize analog value between 0 and 1, in f32
        let normalized_potentiometer = potentiometer_value as f32 / MAX_U12 as f32;
        // we return the value between 0 and 180, in degree
        normalized_potentiometer * 180.0
    }

    // in embedded your program should never end, so we loop forever
    loop {
        // we read the potentiometer
        // transform it into degree
        // and apply it to the servo position
        servo.set_position(potentiometer_in_degree(potentiometer.read()));
    }
}
```

You can see that we introduced the ```servo``` module, which contains a ```Servo```  trait which have a ```set_position``` function to change the position of the servomotor. It takes as argument a position in degree.

Let's compile this program first, then we will see how to connect the potentiometer and the servomotor to the board.

> Note: To compile and upload the program to the board, we will follow the same steps as introduced in the hello world tutorial. For more details please refer to [01_your_first_program.md](./01_your_first_program.md).

### Step 2: Compiling the code

To compile [servo_potmeter.rs](https://github.com/pollen-robotics/luos/blob/master/examples/servo_potmeter.rs) for the STM32F072B-DISCO board run:
```bash
xargo build --target thumbv6m-none-eabi --example servo_potmeter
```

> Tips: if you get problem compiling, you might have run into a Cargo.lock problem. Try running the following command:
```bash
rm -f Cargo.lock && cargo clean && touch Xargo.toml
```
This should clean all build history and compile everything from scratch again. We will explain more about this issue at a later time.

### Step 3: Connecting the potentiometer

To test this program, we need to connect a variable voltage (between 0 and 3.3V) to the ```PA0``` pin and the control signal of a servomotor to the ```PB4``` pin.

Please refer to [the previous tutorial](04_led_brightness_via_potentiometer.md) to learn how to connect the potentiometer.

To connect the servo, you need to:
- connect the power cable to the 5V pin
- connect the ground cable to the GND pin
- connect the signal pin to the PB4 pin of the board

<img src="https://www.pollen-robotics.com/uploads/tutorials/05/stmdisco_potmeter_servo.jpg">

We used a 10kOhms potentiometer in our setup, but you can choose any potentiometer you might have. You can use any servo at this stage, although we recommend to use a small one

### Step 4: Building the elastic gun

This step is optional, if you simply want to test the servomotor control its own, please move to step 5 directly.

Our elastic gun contraption is very simple and made of two parts:
- a rotating disc that pull the elastic band and release it automatically after a quarter of a turn
- a base that holds the servomotor in place and have an arm that hold the elastic band

We recommend using a [MG 90S Micro servo](https://www.adafruit.com/product/1143). Our setup and all 3D parts have been designed around this motor. You can find the [3D design all parts on Onshape](https://cad.onshape.com/documents/377bdf2cb3cb7ca072c33e6e/w/db312e320441710cda64a325/e/21d75710359c904413084725) so you can modify them for your needs.

Below is how the final assembly look like.

<img src="https://www.pollen-robotics.com/uploads/tutorials/05/elastic_contraption_front.jpg" width=400px>

<img src="https://www.pollen-robotics.com/uploads/tutorials/05/elastic_contraption_back.jpg" width=400px>


### Step 5: Uploading and running the code

On a first terminal, run:
```bash
openocd -f $OPENCD_ROOT/interface/stlink-v2.cfg -f $OPENCD_ROOT/target/stm32f0x.cfg
```

On a second terminal, run:
```bash
arm-none-eabi-gdb target/thumbv6m-none-eabi/debug/examples/servo_potmeter
```
Where ```target/thumbv6m-none-eabi/debug/examples/servo_potmeter``` is the folder containing the compiled code to be uploaded to the board.

> Note: This command as written assumes you are currently at luos root folder. Change the path argument accordingly if it's not the case.

To start executing the code on the board, simply type ```continue``` and press enter.

And voila, you can control the servomotor position with the potentiometer!

<img src="https://www.pollen-robotics.com/uploads/tutorials/05/elastic_rubber_shooter.gif">

You can now rule your office by sending elastic bands all over the place.

---

### Troubleshooting

If you encountered any issues following these steps, please let us know.
