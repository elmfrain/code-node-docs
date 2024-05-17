# EL (Event Listener)

The **EL** module provides a simple way to listen for Minecraft events and execute code in response. You may be able to poll for events in a loop, or you may enable interrupts for certain events.

**Note**: Currently, the only use for this module is to listen for game ticks, but in the future, it will be expanded to include other events.

## Events
This module supports up to 64 different events. The following events are currently implemented:

| Index | Event Name | Description |
|----|------------|-------------|
| 0  | Game Tick  | Fires every game tick. |

## Working with Interrupts
To enable an interrupt for an event, you must set the corresponding bit in the **Interrupt Control Buffer** (ELICL).

This module loops through each event and checks if interrupts are enabled for it. If it is **enabled** and the corresponding flag is set in the **Interrupt Flags Buffer**, the interrupt is triggered. If there are **unhandled** interrupts, the `IRQ` line of the processor will remain held `LOW` until all interrupts are **handled** (flags are cleared).

For example, if the `Game Tick` event is enabled and the flag is set, the module will trigger an interrupt.

## Interrupt Control Buffer (ELICL)
This module has a buffer of `8` bytes to store interrupt control data. Each bit in this buffer corresponds to an event (not all are used). If the bit is set, the interrupt for that event is enabled. If the bit is cleared, the interrupt for that event is disabled.

When accessing this buffer, it is to be treated as an **array** of bits. The first byte of the buffer corresponds to the first 8 events, the second byte corresponds to the next 8 events, and so on. You can use the following formulas to get the indecies for a bit (array is zero-based):
- Byte index: `floor( n / 8 )`
- Bit index: `n % 8`

This buffer can be **read** from and **written** to by the processor.

## Interrupt Flags Buffer (ELIFL)
This module has a buffer of `8` bytes to store interrupt flags. Each bit in this buffer corresponds to an **event** (not all are used). If the bit is set, the interrupt for that event has **occurred**. If the bit is cleared, the interrupt for that event has not occurred.

Simliar to the ELICL buffer, this buffer is to be treated as an **array** of bits. You can use the same formulas to get the indecies for a bit.

This buffer can be **read** from and **written** to by the processor. Writing a `1` to a bit will clear the corresponding flag.

**Note**: These flags are set regardless of whether the interrupt is enabled or not. This allows code to poll for events if desired.

## Registers
Register addresses depends on every microcontroller, so you have to refer to their respective summaries to know where the EL registers are located. Find the register summaries [here](../registers/index.md).