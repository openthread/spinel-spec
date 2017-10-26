# Spinel Frames

Spinel is a frame-based (rather than stream-based) protocol. Frames
can be of any size between two bytes and MTU of the channel (which is
RECOMMENDED to be at least 1300 bytes). Spinel frames SHALL be
delivered reliably and in-order.

The mechanism responsible for transporting frames between the OS and
the NCP (while also ensuring both reliable and in-order delivery) is
called the *framing mechanism*. While Spinel does not mandate any
specific framing mechanism be used, this document does make detailed
recommendations in (#appendix-framing). This layered approach privides
a great deal of implementation flexibility.

## Frame Format ##

A Spinel frame is the concatenation of the following elements:

*   A header comprising a single octet (see (#header-format) below).
*   A command identifier (up to three octets, see
    (#packed-unsigned-integer) for format)
*   An optional command payload (Contents of which are defined by the
    specific command being sent)

Octets: |    1   | 1-3 |    *n*
--------|--------|-----|-------------
Fields: | HEADER | CMD | CMD_PAYLOAD

For example, each of the property operations described in the previous
section is defined as a specific command identifier with the property
key as the first part of the payload. Additional commands are defined
for special purposes (see (#commands)), and the command identifier
registry has values reserved for future standard expansion,
application specialization, and experimental purposes.

## Header Format ##

The header comprises the following information elements packed into a
single octet:

      0   1   2   3   4   5   6   7
    +---+---+---+---+---+---+---+---+
    |  FLG  |  NLI  |      TID      |
    +---+---+---+---+---+---+---+---+

<!-- RQ -- Eventually, when https://github.com/miekg/mmark/issues/95
is addressed, the above table should be swapped out with this:

| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|---|
|  FLG ||  NLI ||      TID   ||||
-->

### FLG: Flag ###

The Flag (FLG) field in the two most significant bits of the header
octet (`FLG`) is always set to the value two (or `10` in binary). Any
frame received with these bits set to any other value SHALL NOT be
considered a Spinel frame.

This convention allows Spinel to be line compatible with BTLE HCI. By
defining the first two bit in this way we can disambiguate between
Spinel frames and HCI frames (which always start with either `0x01` or
`0x04`) without any additional framing overhead.

### NLI: Network Link Identifier ###

The Network Link Identifier (NLI) field is used to distinguish between
independent "virtual" network links. It is stored in the third and
fourth most significant bits as a number between 0 and 3.

The NLI field allows for the control and management of an NCP that can
be connected to more than one network simultaneously, as if multiple
independent NCPs were present. The exact implementation of such
capability is outside the scope of this document.

All NCPs conforming to this protocol SHALL implement NLI 0 and MAY
provide additional network interfaces via NLI values 1, 2, and 3.

Any commands sent to an unimplemented NLI MUST fail with
`STATUS_UNIMPLEMENTED`.

### TID: Transaction Identifier ###

The Transaction Identifier (TID) field in the four least significant
bits of the header is used for correlating responses to the commands
which generated them.

When a command is sent from the OS, any reply to that command sent by
the NCP will use the same value for the TID. When the OS receives a
frame that matches the TID of the command it sent, it can easily
recognize that frame as the actual response to that command.

The zero value of TID is used for commands to which a correlated
response is not expected or needed, such as for unsolicited update
commands sent to the OS from the NCP.

### CMD: Command Identifier ###

The command identifier is a 21-bit unsigned integer encoded in up to
three octets using the packed unsigned integer format described in
(#packed-unsigned-integer). This encoding allows for up to 2,097,152
individual commands, with the first 127 commands represented as a
single octet. Command identifiers larger than 2,097,151 are explicitly
forbidden.

### CMD_PAYLOAD: Command Payload ###

The command payload follows the command identifier in a Spinel frame,
containing the serialization of any arguments that the indicated
command may require. The exact composition of a command payload is
determined by the specific command identifier being used and MUST be
empty if the command has no arguments.

