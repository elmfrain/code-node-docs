# Nano

Here is a summary of the registers available in the **Nano** microcontroller.

## Modules
* [GPIO](#gpio)
* [EL](#el-event-listener)
* [UART](#uart)

## GPIO
In depth information about the GPIO module can be found [here](../modules/gpio.md).

### GPIOPV (Pin Value Registers)
There are 64 GPIOPV registers, each representing the value of a pin. Only the first 4 are used.

* **Read**: Returns the redstone signal strength of the pin.
* **Write**: Sets the redstone signal strength of the pin (if it is an output).

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7000` | GPIOPV0 | Pin 0 / Front pin | R/W |
| `0x7001` | GPIOPV1 | Pin 1 / Right pin | R/W |
| `0x7002` | GPIOPV2 | Pin 2 / Back pin | R/W |
| `0x7003` | GPIOPV3 | Pin 3 / Left pin | R/W |

### GPIODIR (Pin Direction Registers)
There are 8 GPIODIR registers, each bit representing the direction of a pin. Only 1 register is used.

* **Read**: Returns the value of the register.
* **Write**: Sets the value of the register.

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7040` | GPIODIR0 | Pins (0 - 7) direction | R/W |

#### GPIODIR0 (N/U - Not Used)
| Bit 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| N/U | N/U | N/U | N/U | Pin 3 dir | Pin 2 dir | Pin 1 dir | Pin 0  dir |

### GPIOINT (Pin Interrupt Type Registers)
There are 32 GPIOINT registers, each nibble representing the interrupt type of a pin. Only the first 4 are used.

* **Read**: Returns the value of the register.
* **Write**: Sets the value of the register.

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7048` | GPIOINT0 | Pins (0 - 1) interrupt types | R/W |
| `0x7049` | GPIOINT1 | Pins (2 - 3) interrupt types | R/W |

#### Interrupt types
| Value | Name | Description |
| --- | --- | --- |
| `0x0` | NO_INTERRUPT | Disable interrupts |
| `0x1` | LOW | Trigger while low |
| `0x2` | HIGH | Trigger while high |
| `0x3` | RISING | On rising edge |
| `0x4` | FALLING | On falling edge |
| `0x5` | CHANGE | On digital rise or fall edge |
| `0x6` | ANALOG_CHANGE | On pin value change |
| `0x7` | ANALOG_RISING | On pin value increasing |
| `0x8` | ANALOG_FALLING | On pin value decreasing |
| `0x9` | NO_CHANGE | Trigger while no change |

#### GPIOINT0 (N/U - Not Used)
| Bits 7 - 4 | Bits 3 - 0 |
| --- | --- |
| Pin 1 interrupt type | Pin 0 interrupt type |

#### GPIOINT1 (N/U - Not Used)
| Bits 7 - 4 | Bits 3 - 0 |
| --- | --- |
| Pin 3 interrupt type | Pin 2 interrupt type |

### GPIOIFL (Pin Interrupt Flag Registers)
There are 8 GPIOIFL registers, each bit representing the interrupt flag of a pin. Only 1 register is used.
If any flag is set, the `IRQ` line is held `LOW`. The flag must be cleared after the interrupt is serviced.

* **Read**: Returns the value of the register.
* **Write**: Clears bits of the register.

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7068` | GPIOIFL0 | Pins (0 - 7) interrupt flags | R/W |

#### GPIOIFL0 (N/U - Not Used)
| Bit 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| N/U | N/U | N/U | N/U | Pin 3 flag | Pin 2 flag | Pin 1 flag | Pin 0 flag |

## EL (Event Listener)
In depth information about the EL module can be found [here](../modules/el.md).

### ELICL (Interrupt Control Buffer)
This module has a buffer of `8` bytes to store interrupt control data. Each bit in this buffer corresponds to an event (not all are used). If the bit is set, the interrupt for that event is enabled. If the bit is cleared, the interrupt for that event is disabled.

* **Read**: Returns the value of the register.
* **Write**: Sets the value of the register.

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7100` | ELICL0 | Events (0 - 7) interrupt control | R/W |

#### ELICL0 (N/U - Not Used)
| Bit 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| N/U | N/U | N/U | N/U | N/U | N/U | N/U | Game Tick |

### ELIFL (Interrupt Flags Buffer)
This module has a buffer of `8` bytes to store interrupt flags. Each bit in this buffer corresponds to an **event** (not all are used). If the bit is set, the interrupt for that event has **occurred**. If the bit is cleared, the interrupt for that event has not occurred.

* **Read**: Returns the value of the register.
* **Write**: Clears bits of the register.

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7108` | ELIFL0 | Events (0 - 7) interrupt flags | R/W |

#### ELIFL0 (N/U - Not Used)
| Bit 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| N/U | N/U | N/U | N/U | N/U | N/U | N/U | Game Tick |

## UART
In depth information about the UART module can be found [here](../modules/uart.md).

There are 7 registers in the UART module.

#### Register Table
| Address | Name | Description | Read/Write |
| --- | --- | --- | --- |
| `0x7200` | UARTSTS | Status | R |
| `0x7201` | UARTCTL | Control | R/W |
| `0x7202` | UARTCMD | Command | R/W |
| `0x7203` | UARTTXD | Transmit Data | R/W |
| `0x7204` | UARTRXD | Receive Data | R |
| `0x7205` | UARTTXP | Transmit Pin | R/W |
| `0x7206` | UARTRXP | Receive Pin | R/W |
| `0x7207` | UART_ | Internal | R |


### UARTSTS (Status Register)
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

### UARTCTL (Control Register)
The **UARTCTL** register is used to configure the module's baud rate, word length, and stop bit.

| Bit 7| Bits 6-4 | Bits 3-0 |
|-------|-------|-------|
|**STOP_BITS** (1 or 2 bits)|**WRDLEN** (1 or 8 bits)|**BAUD_RATE**|

#### Stop Bits
|Value|Bits|
|---|---|
|`0`|1 stop bit|
|`1`|2 stop bits|

#### Word Length
The word length is the number of bits in a data frame. The word length can be set to `1` or `8` bits. **WRDLEN** is a 3-bit field, so the resulting number of data bits follows this formula: `WRDLEN + 1`.

#### Baud Rate
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


By default, it is set to `1` **stop bit**, `8` **data bits**, and a **baud rate** of `10`. This is set every hard/software reset.

### UARTCMD (Command Register)
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

### UARTTXD (Transmit Data Register)
The **UARTTXD** register is used to write data to the UART module. Writing to this register will transmit the data.

### UARTRXD (Receive Data Register)
The **UARTRXD** register is used to read data from the UART module. Reading this register will return the received data.

### UARTTXP (Transmit Pin Register)
The **UARTTXP** register is used to set the transmit pin. Writing to this register will set the transmit pin.

### UARTRXP (Receive Pin Register)
The **UARTRXP** register is used to set the receive pin. Writing to this register will set the receive pin.

### UART_ (Internal Register)
The **UART_** register is an internal register that is not meant to be used by the user. Reading from this register will return undisclosed data.