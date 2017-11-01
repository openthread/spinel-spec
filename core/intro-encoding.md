# Data Representation {#data-representation}

Spinel, being a low-level protocol between two devices which are
likely to have a little-endian architecture, uses little-endian representations
exclusively for all integers smaller than four bytes. For implementation
convenience, values larger than four bytes (EUI64, IPv6 addresses, etc)
are stored as they are traditionally represented (typically big-endian).

## Packed Unsigned Integers {#packed-unsigned-integer}

Certain types of integers, such as command or property identifiers,
usually have a value on the wire that is less than 127. However, in
order to not preclude the use of values larger than 255, we would need
to add an extra byte. Doing this would add an extra byte to all
packets, which can add up in terms of bandwidth. To address this,
Spinel uses Packed Unsigned Integers, or PUIs.

The PUI format used in Spinel is based on the [unsigned integer format
in EXI][EXI], except that we limit the maximum value to the largest
value that can be encoded to three bytes. The maximum value that
can be encoded is 2,097,151.

[EXI]: https://www.w3.org/TR/exi/#encodingUnsignedInteger

For all values less than 127, the packed form of the number is simply
a single byte which directly represents the number. For values larger
than 127, the following process is used to encode the value:

1.  The unsigned integer is broken up into *n* 7-bit chunks and placed
    into *n* bytess, leaving the most significant bit of each byte
    unused.
2.  Order the bytess from least-significant to most-significant.
    (Little-endian)
3.  Clear the most significant bit of the most significant byte. Set
    the least significant bit on all other bytes.

Where *n* is the smallest number of 7-bit chunks you can use to
represent the given value.

Take the value 1337, for example:

    1337 => 0x0539
         => [39 0A]
         => [B9 0A]

To decode the value, you collect the 7-bit chunks until you find an
byte with the most significant bit clear.

