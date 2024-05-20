# UART (Serial Communication)

The **UART** module is a serial communication module that allows the microcontroller to communicate with other devices using the Universal Asynchronous Receiver/Transmitter (UART) protocol. It is capable of sending up to 20 bits per second using vanilla Redstone wire.

This module is designed similarly to the **ACIA 6551** chip, which is a serial communication chip used in the Commodore 64 computer.

## Terms
* **Baud Rate**: The number of bits per second that the module can transmit or receive.
* **Data Frame**: A data frame is a sequence of bits that contains the data to be transmitted or received. It consists of a start bit, data bits, an optional parity bit, and 1 or 2 stop bits.
* **Start Bit**: The start bit is used to indicate the beginning of a data frame. The start bit is always `1`.
* **Data Bits**: The data bits are the bits that contain the data to be transmitted or received. The number of data bits can be set to `1` or `8` bits.
* **Parity Bit**: The parity bit is used to check the integrity of the data. The parity bit can be set to `EVEN` or `ODD` parity.
* **Stop Bit**: The stop bit is used to indicate the end of a data frame. The stop bit is always `0`.

## Working Principle
This module has two data registers, **UARTTXD** and **UARTRXD**, which are used to transmit and receive data respectively. The module also has **UARTCTL** and **UARTCMD** registers to configure serial communication.

Upon writing data to the **UARTTXD** register, the module will start transmitting the data to the connected device. If it is recieving data, the module will store the data in the **UARTRXD** register.

If two microcontrollers are connected to each other, they must have the same configuration for the serial communication to work properly. The configuration includes the baud rate, data bits, parity, and stop bits.

## Status Register (UARTSTS)
The **UARTSTS** register is used to check the status of the UART module. Here are the list of status flags (_N/U_ means Not Used):

| Bit | Flag |
|-------|-------|
|7|**IRQ**|
|6|_N/U_|
|5|_N/U_|
|4|**TX_BUFFER_EMPTY**|
|3|**RX_BUFFER_FULL**|
|2|**OVERRUN_ERROR**|
|1|**FRAME_ERROR**|
|0|**PARITY_ERROR**|


Reading this register will return the status of the UART module and clear the `IRQ`, `OVERRUN_ERROR`, `FRAME_ERROR`, and `PARITY_ERROR` flags.

Writing to this register will trigger a software reset, which will clear all flags and reset the module's configuration to default.
### Status Flags
* `IRQ` - **Interrupt Request**: This flag is set when the UART module has finished transmitting or receiving data if their respective interrupt enable bits are set in the **UARTCMD** register. If this flag is set, the `IRQ` line of the processor will be held `LOW` until the flag is cleared.

* `TX_BUFFER_EMPTY` - **TX Buffer Empty/Ready**:  This flag is set when the **UARTTXD** register is empty and the module is ready to transmit data. Writing data to the **UARTTXD** register while it is not empty will cause and **overrun error**.

* `RX_BUFFER_FULL` - **RX Buffer Full**: This flag is set when the **UARTRXD** register is full and it contains received data.

* `OVERRUN_ERROR` - **Overrun Error**: This flag is set when the **UARTRXD** register is full and new data is received or when the **UARTTXD** register is written to while it is not empty.

* `FRAME_ERROR` - **Frame Error**: This flag is set when the received data does not match the configured frame format. This is triggered specifically when the recieved stop bit is not a `0`.

* `PARITY_ERROR` - **Parity Error**: This flag is set when the received data does not match the configured parity bit.

## Control Register (UARTCTL)
The **UARTCTL** register is used to configure the module's baud rate, word length, and stop bit.

| Bit 7| Bits 6-4 | Bits 3-0 |
|-------|-------|-------|
|**STOP_BITS** (1 or 2 bits)|**WRDLEN** (1 or 8 bits)|**BAUD_RATE**|

By default, it is set to `1` **stop bit**, `8` **data bits**, and a **baud rate** of `10`. This is set every hard/software reset.

### Stop Bits
Following the protocol, the stop bit is used to indicate the end of a data frame. The stop bit is always `0`.

* `0` - 1 stop bit
* `1` - 2 stop bits

### Word Length
The word length is the number of bits in a data frame. The word length can be set to `1` or `8` bits. **WRDLEN** is a 3-bit field, so the resulting number of data bits follows this formula: `WRDLEN + 1`.

### Baud Rate
The baud rate is the number of bits per second that the module can transmit or receive. **BAUD_RATE** is a 4-bit field, so the resulting baud rate follows this table:

| Value | Baud Rate |
|-------|-------|
|`0x0`|1|
|`0x1`|2|
|`0x2`|5|
|`0x3`|10|
|`0x4`|20|
|`0x5`|50|
|`0x6`|150|
|`0x7`|300|
|`0x8`|600|
|`0x9`|1200|
|`0xA`|1800|
|`0xB`|2400|
|`0xC`|3600|
|`0xD`|4800|
|`0xE`|7200|
|`0xF`|9600|

Obviously, since Minecraft only runs 20 ticks per second, the maximum baud rate is `20`. Higher bit rates are planned to be supported using custom Redstone wire from the mod, and they may be added in the future.

> **Tip:**
>
> It is recommended to set the baud rate to `10` for signal lines with repeaters and `20` for direct connections with Redstone wire.

## Command Register (UARTCMD)
The **UARTCMD** register is used to configure how the module deals with received and transmitted data. Here are the list of command flags (_N/U_ means Not Used):

| Bit | Flag |
|-------|-------|
|7|_N/U_|
|6|**TX_PIN_ENABLE**|
|5|**RX_PIN_ENABLE**|
|4|**PARITY_TYPE**|
|3|**PARITY_ENABLE**|
|2|**ECHO_ENABLE**|
|1|**TX_INTERRUPT_ENABLE**|
|0|**RX_INTERRUPT_ENABLE**|

### Command Flags
* `TX_PIN_ENABLE` - **TX Pin Enable**: This flag is used to enable the pin selected in register **UARTTXP** to transmit data. When enabled, the module will override the pin's output and transmit data. However, the pin must be an `OUTPUT` by setting the pin's direction using the **GPIO** module.

* `RX_PIN_ENABLE` - **RX Pin Enable**: This flag is used to enable the pin selected in register **UARTRXP** to receive data. When enabled, the module will read the pin's input to assemble incoming serial data. The pin must be an `INPUT` by setting the pin's direction using the **GPIO** module.

* `PARITY_TYPE` - **Parity Type**: This flag is used to select the type of parity bit to use. If it is set, the module will use **EVEN** parity, and If it is cleared, it will use **ODD** parity.

* `PARITY_ENABLE` - **Parity Enable**: This flag is used to enable the parity bit in the data frame. If this flag is set, the module will add a parity bit to the data frame. When receiving data, the module will check the parity bit to see if the data is correct (keep in mind that parity configuration must be the same on both devices).

* `ECHO_ENABLE` - **Echo Enable**: This flag is used to enable the echo feature. When enabled, the module will transmit the received data back through `TX`.

* `TX_INTERRUPT_ENABLE` - **TX Interrupt Enable**: This flag is used to enable the interrupt request when the **UARTTXD** register is empty and the module is ready to transmit data.

* `RX_INTERRUPT_ENABLE` - **RX Interrupt Enable**: This flag is used to enable the interrupt request when the **UARTRXD** register is full and contains received data.

## Transmit Data Register (UARTTXD)
The **UARTTXD** register is used to transmit data to the connected device. When data is written to this register, the module will start transmitting the data to the connected device.

If the `TX_BUFFER_EMPTY` flag is cleared while this register is written to, the module may transmit garbled data and set the `OVERRUN_ERROR` flag.

## Receive Data Register (UARTRXD)
The **UARTRXD** register is used to store received data from the connected device. The instant the entire data frame is read, the `RX_BUFFER_FULL` status is set. 

Reading this register will clear the `RX_BUFFER_FULL`, `OVERRUN_ERROR`, `FRAME_ERROR`, and `PARITY_ERROR` flag. If data is not read from this register before new data is received, the module will set the `OVERRUN_ERROR` flag.

Reading this register while it the `RX_BUFFER_FULL` flag is cleared may return garbled data.

## Transmit Pin Register (UARTTXP)
The **UARTTXP** register is used to select the pin that will be used to transmit data. The selected pin must be an `OUTPUT` by setting the pin's direction using the **GPIO** module.

By default, the **UARTTXP** register is set to `0`, which corresponds to the first pin in the **GPIO** module.

## Receive Pin Register (UARTRXP)
The **UARTRXP** register is used to select the pin that will be used to receive data. The selected pin must be an `INPUT` by setting the pin's direction using the **GPIO** module.

By default, the **UARTRXP** register is set to `1`, which corresponds to the second pin in the **GPIO** module.

## Register Locations
Register addresses depends on every microcontroller, so you have to refer to their respective summaries to know where the EL registers are located. Find the register summaries [here](../registers/index.md).