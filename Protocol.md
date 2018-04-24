Protocol
====

The protocol is very simple. Inspired by Altera's USB-Blaster.

Currently, we just defined data transfer protocol, the target report protocol and frequency control protocol still remains undefined.

Data Transfer and Interface
-----

We have two endpoints, one in, one out. So we have two FIFOs to handle data. Just like serial port.

I prefer to realize it as a CDC-ACM interface, or a custom bulk-in/bulk-out interface. But, the most important thing is, I want driverless solution, AND I want extendable solution, for example, it can composed with an HID or a CDC interface.

But it depends on Windows driver, I doesn't want to write Windows kernel-mode driver and get signature from M$.

Control byte
-----

|7|6|5|4|3|2|1|0|When|
|:-|:-|:-|:-|:-|:-|:-|:-|:-|
|Shift|Readback|Length|L|L|L|L|L|Shift = 1|
|Shift|Reserved|TCK1|TDI1|TMS1|TCK0|TDI0|TMS0|Shift = 0|

Shift mode
----

When nRB is set, the adapter will read back TDO data simultaneously, and send it to the master using TxFIFO.

When nRB is not set, the adapter will drop TDO data, because it is be considered don't care.

Real transfer length (bits) = 8 * (length + 1), length 63(64) are reserved, maybe for other usage (like adding two length bytes to shift more data, reduce overhead).

Bitbang mode
----

Sample TDO0, transfer TDI0, TMS0, TCK0 to the line, wait a bit, and simple TDO1, transfer TDI1, TMS1, TCK1 to the line, and wait a bit.

Data return to host: REV[7:2] TDO1 TDO0.

Continuous all signal '1' for more than 8 times (16 transfers) is reserved for device firmware update operation.

License
--------

I know what all of you would think now, so do what you here to do!

WTFPL

Authors
-------

Zhiyuan Wan <h@iloli.bid>
