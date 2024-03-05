# GPIO

The **GPIO** module controls and reads the state of the pins of a microcontroller via the processor's data bus. This module can control up to 64 pins, and a single pin can be configured to be an `INPUT` or an `OUTPUT`.

Additionally, when a pin is an `OUTPUT`, it is capable of triggering interrupts to execute code when a certain event is detected.

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

## Pin Values Buffer (GPIOPV)

The module has a buffer of `64` bytes to store the state of every pin (not all channels may be used). Each byte corresponds to a single pin.

Since Redstone signals has a range of `0` through `15`, only the first four bits of a byte pin are used by the pin drivers. In essence, pin values should not increment over `15` as it will cause an undesired effect of the pin value to overflow to `0` even though the pin value is not `0`. To further clarify, when pin values are represented as hexidecimal values, the second digit is ignored (ex. `0xC8` will just be `0x8`).
#### Double Buffering
One concept to note about the GPIOPV buffer is that it is **double buffered**, meaning that it has a **back-buffer** and a **front-buffer**. Since the processor can have a clock cycle faster than Minecraft's tick rate, any changes to the buffer is saved to its back-buffer. Upon the next game tick, the buffers are swapped and the front-buffer is sent to the pin drivers to  carry out IO operations to or from the physical world.

As far for the processor is concerned, the GPIOPV is a **single** buffer. When the processor writes to GPIOPV, it writes the back-buffer, and when it reads from GPIOPV, it reads the front-buffer. Thus, any writes to the buffer may not be reflected right away until the next game tick happens. This guarantees that all values read from GPIOPV are accuratlely representing the physical state of the pins.

## Pin Direction Buffer (GPIODIR)

The module has a buffer of `8` bytes to store the **direction** of every pin (`INPUT` or `OUTPUT`) which has `64` directional flags for each pin.

When accessing this buffer, it is to be treated as an **array** of bits, so, for example, the 35th bit is on the 4th byte, 3rd bit. You can use the following formulas to get the indexes for a bit (array are zero-based):
* Byte index: `floor( n / 8 )`
* Bit index: `n % 8`

When the nth bit is a `0`, the nth pin is an `INPUT`. When the nth bit is a `1`, the nth pin is an `OUTPUT`. Note that when a pin is an `INPUT`, the corresponding byte pin cannot be written to by the processor, and it can only be written to by the pin driver.

## Interrupt Type Buffer (GPIOINT)

The module has a buffer of `16`