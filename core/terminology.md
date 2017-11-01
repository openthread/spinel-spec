## Terminology ##

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in [@!RFC2119].

The layout diagrams in this document that show encodings of data in
byte sequences use the convention described in Guide for Internet
Standards Writers [@?RFC2360] section 3.1, Packet Diagrams.
Specifically, bits in bytes are indexed from most significant to
least significant, and the least significant bit in a field
representing an integer value more than one bit in length is the least
significant bit of a the signed or unsigned number, as the case
requires.

The term "byte" is used in its now-customary sense as a synonym for
"byte".

Most multi-byte values are encoded in little-endian order
(that is, most significant byte first). This is discussed in more detail in
(#data-representation).

This specification makes use of the following terminology:

<!-- RQ -- Alphabetize terminology before finalization. -->

AP
: Application Processor, the host processor to which the NCP is a peripheral.

FCS
: Final Checksum. Bytes added to the end of a packet to help determine if the packet was received without corruption.

NCP
: Network Control Processor.

NLI
: Network Link Identifier. May be a value between zero and three. See (#nli-network-link-identifier) for more information.

PHY
: Physical layer. Refers to characteristics and parameters related to the physical implementation and operation of a networking medium.

PUI
: Packed Unsigned Integer. A way to serialize an unsigned integer using one, two, or three bytes. Used throughout the Spinel protocol. See (#packed-unsigned-integer) for more information.

TID
: Transaction Identifier. May be a value between zero and fifteen. See (#tid-transaction-identifier) for more information.
