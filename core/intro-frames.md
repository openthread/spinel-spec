# Spinel Frames

Spinel is a frame-based (rather than stream-based) protocol. Frames
can be of any size between two bytes and MTU of the channel (which is
**RECOMMENDED** to be at least 1300 bytes). Spinel frames **SHALL** be
delivered reliably and in-order. It is the responsibility of the lower layer
to make those guarantees.

The mechanism responsible for transporting frames between the AP and
the NCP (while also ensuring both reliable and in-order delivery) is
called the *framing mechanism*. While Spinel does not mandate any
specific framing mechanism be used, the **RECOMMENDED** mechanisms are
outlined in (#appendix-framing). This layered approach privides a
great deal of implementation flexibility.

## Frame Format ##

A Spinel frame is the concatenation of the following elements:

*   A header comprising a single byte. (See (#header-format)).
*   A command identifier. (See (#commands))
*   A command-defined payload, which may be empty.

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     HEADER    |  COMMAND ID   | PAYLOAD ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of a typical Spinel frame

Since the size of the frame is a part of the framing mechanism, it is
omitted from the frame.

## Header {#header-format}

The header byte is composed as follows:

~~~
  0   1   2   3   4   5   6   7
+---+---+---+---+---+---+---+---+
|  FLG  |  NLI  |      TID      |
+---+---+---+---+---+---+---+---+
~~~
Figure: Header Format

### FLG: Flag ###

The Flag (FLG) field in the two most significant bits of the header
byte (`FLG`) is always set to the value two (or `10` in binary). Any
frame received with these bits set to any other value SHALL NOT be
considered a Spinel frame.

This convention allows Spinel to be line compatible with BTLE HCI. By
defining the first two bit in this way we can disambiguate between
Spinel frames and HCI frames (which always start with either `0x01` or
`0x04`) without any additional framing overhead. (TODO: Verify)

### NLI: Network Link Identifier {#nli-network-link-identifier}

The Network Link Identifier (NLI) field is used to distinguish between
independent "virtual" network links. It is stored in the third and
fourth most significant bits as an integer between 0 and 3.

The NLI field allows for the control and management of an NCP that can
be connected to more than one network simultaneously, as if multiple
independent NCPs were present. The exact details regarding the usage
of such a feature are not yet specified and considered outside the scope
of this document.

All NCPs conforming to this protocol **SHALL** implement NLI 0 and **MAY**
provide additional network interfaces via NLI values 1, 2, and 3.

Any commands sent to an unimplemented NLI MUST fail with:

* Header byte identical to the offending command.
* `CMD_PROP_VALUE_IS` for the command id.
* `PROP_LAST_STATUS` as the property key.
* `STATUS_INVALID_INTERFACE` as the value.

### TID: Transaction Identifier ###

The Transaction Identifier (TID) field in the four least significant
bits of the header is used for correlating responses to the commands
which generated them.

When a command is sent from the AP, any reply to that command sent by
the NCP will use the same value for the TID. When the AP receives a
frame that matches the TID of the command it sent, it can easily
recognize that frame as the actual response to that command.

The zero value of TID is used for commands to which a correlated
response is not expected or needed, such as for unsolicited update
commands sent to the AP from the NCP.

Note that while the frame format is symmetric between the
frames being sent to the NCP versus frames being sent from
the NCP, the behaviors are not. The NCP **MUST NOT** send
a frame with a non-zero TID that is not a response to a frame
it had recently received with that same TID. All unsolicited or
asynchronous commands originating from the NCP **MUST**
use TID zero (0).

## Command ID ##

The command identifier is a 21-bit unsigned integer encoded in up to
three bytes using the packed unsigned integer format described in
(#packed-unsigned-integer). This encoding allows for up to 2,097,152
individual commands, with the first 127 commands represented as a
single byte. Command identifiers larger than 2,097,151 are explicitly
forbidden.

To date, no command id has been longer than one byte long, so all
figures in this document will represent the command ID as only taking
a single byte.

## Payload ##

The command payload follows the command identifier in a Spinel frame,
containing the serialization of any arguments that the indicated
command may require. The exact composition of a command payload is
determined by the specific command identifier being used and **MUST** be
empty if the command has no arguments.

