# GPIO

The **GPIO** module controls and reads the state of the pins of a microcontroller via the processor's data bus. This module can control up to 64 pins, and a single pin can be configured to be an `INPUT` or an `OUTPUT`.

Additionally, when a pin is an `INPUT`, it is capable of triggering interrupts to execute code when a certain event is detected.

### Terms
* **Pin Value**: A register containing an input or output Redstone signal strength level.
* **Code**: Refering to the processor (the 6502) that is executing machine code.
* **Byte Pin**: A byte containing a pin value.
* **Pin Driver**: A device that samples or sends a Redstone signal from a physical pin.
* **Zero Based**: Arrays that begins indexing elements from 0.

## Working With Digital and Analog Signals

Each pin is capable to recieve/send analog and digital signals. This is possible due to the fact that Redstone signals have 16 levels from `0` to `15`, so the code of the microcontroller can interperet them as either.
#### Analog
When a pin is designated to read or write analog signals, pin values can be interperted as such (`0x0` -> `0xF`).
#### Digital
When a pin is designated to read or write digital signals, there can only be two possible states, `0` or `1`. So in this case, when a pin value is `0`, then the digital value is `0`. Otherwise, any pin value greater than `0` is a digital `1` such as `0xC`.

Generally when writing a digital `1`, it is recommended to write `0xF` (`15`) to the pin value so it can output the strongest possible signal to represent it. 

## Working With Interrupts

This module is capable of triggering **interrupts** to the processor; this applies to all pins. This is a great alternative to polling the pin's state because the processor will not have to spend clock cycles continously fetching **updates** from this module.

Furthermore, interrupts are used in **timing** critical situations since the processor will be notified of a certain event immediatley, and polling, on the other hand, may not be able to tend to an event at all (especially if the poll rate is slow).

For this module, the `IRQ` line of the processor is held `LOW` until all interrupt flags in the [GPIOIFL](#interrupt-flags-buffer-gpioifl) are cleared.

## Pin Values Buffer (GPIOPV)

The module has a buffer of `64` bytes to store the state of every pin (not all channels may be used). Each byte corresponds to a single pin.

Since Redstone signals has a range of `0` through `15`, only the first four bits of a byte pin are used by the pin drivers. In essence, pin values should not increment over `15` as it will cause an undesired effect of the pin value to overflow to `0` even though the pin value is not `0`. To further clarify, when pin values are represented as hexidecimal values, the second digit is ignored (ex. `0xC8` will just be `0x8`).

This buffer can be **read** from the processor, but a byte pin can only be **written** to if the corresponding pin is an `OUTPUT`. If the pin is an `INPUT`, only the pin driver will write to the byte pin.

## Pin Direction Buffer (GPIODIR)

The module has a buffer of `8` bytes to store the **direction** of every pin (`INPUT` or `OUTPUT`) which has `64` directional flags, one for each pin.

When accessing this buffer, it is to be treated as an **array** of bits, so, for example, the 35th bit is on the 4th byte, 3rd bit. You can use the following formulas to get the indexes for a bit (array are zero-based):
* Byte index: `floor( n / 8 )`
* Bit index: `n % 8`

When the nth bit is a `0`, the nth pin is an `INPUT`. When the nth bit is a `1`, the nth pin is an `OUTPUT`. Note that when a pin is an `INPUT`, the corresponding byte pin cannot be written to by the processor, and it can only be written to by the pin driver.

This buffer can be **read** from and **written** to by the processor.

## Interrupt Type Buffer (GPIOINT)

The module has a buffer of `32` bytes to store the **interrupt type** of every pin.

Every pin has an interrupt flag the size of 4 bits (a nibble). When accessing this buffer, it is to be treated as an **array** of nibbles, so, for example, the 17th nibble is on the 8th byte, 1st nibble. You can use the following formulas to get the indexes for a nibble (array are zero-based):
* Byte index: `floor( n / 2 )`
* Nibble index: `n % 2`

The interrupt type of a pin can be one of the following:
* `0x0`: `NO_INTERRUPT` - Pin does not trigger an interrupt
* `0x1`: `LOW` - Pin triggers an interrupt while it is `LOW`
* `0x2`: `HIGH` - Pin triggers an interrupt while it is `HIGH`
* `0x3`: `RISING` - Pin triggers an interrupt when it transitions from `LOW` to `HIGH`
* `0x4`: `FALLING` - Pin triggers an interrupt when it transitions from `HIGH` to `LOW`
* `0x5`: `CHANGE` - Pin triggers an interrupt when it transitions from `LOW` to `HIGH` or `HIGH` to `LOW`
* `0x6`: `ANALOG_CHANGE` - Pin triggers an interrupt when the pin value changes
* `0x7`: `ANALOG_RISING` - Pin triggers an interrupt when the pin value increases
* `0x8`: `ANALOG_FALLING` - Pin triggers an interrupt when the pin value decreases
* `0x9`: `NO_CHANGE` - Pin triggers an interrupt when the pin value does not change

By default, all pins have `NO_INTERRUPT` set, but any illegal flags may cause undefined behavior.

This buffer can be **read** from and **written** to by the processor.

## Interrupt Flags Buffer (GPIOIFL)

The module has a buffer of `8` bytes to store the **interrupt flags** of every pin (`NOT_TRIGGERED` or `TRIGGERED`).

When accessing this buffer, it is to be treated as an **array** of bits, so this is similar to the [GPIODIR](#pin-direction-buffer-gpiodir) buffer. You can use those formulas to get the indexes for a bit.

When the nth bit is a `0`, the nth pin has not triggered an interrupt. When the nth bit is a `1`, the nth pin has triggered an interrupt. Note that when a pin has triggered an interrupt, the code must clear the flag to `0` to allow the pin to trigger another interrupt. The processor can clear the flag by writing a `1` to the nth bit.

Note that the processor can only **clear** the flag, and the pin driver can only **set** the flag.

#### Potential Pitfalls

If any of the following interrupt types are set, the processor will not be able to clear the flag while the interrupt is still active:

* `LOW`
* `HIGH`

For example, if the interrupt type is set to `LOW`, the flag cannot be cleared until the pin is not `LOW`.

---

For interrupts to work, a pin must be an `INPUT`, and the interrupt type must be set to a value other than `NO_INTERRUPT`. However, if the pin is an `OUTPUT`, then no matter what interrupt type is set, the flag will be locked to `0` and will never trigger an interrupt.

## Registers

Register addresses depends on every microcontroller, so you have to refer to their respective summaries to know where the GPIO registers are located. Find the register summaries [here](../registers/index.md).