# Nano

Here is a summary of the registers available in the **Nano** microcontroller.

## Modules
* [GPIO](#gpio)

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