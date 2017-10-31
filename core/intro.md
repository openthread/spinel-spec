# Introduction #

IEEE 802.15.4 based low-power wireless networks are quickly becoming
ubiquitous, and with the introduction of 6LoWPAN[@RFC4944],
IPv6[@!RFC8200] connectivity is now possible on these networks.
6LoWPAN-based network technologies such as Thread(R) and ZigBee(R) IP
additionally provide additional features such as network security,
mesh-networking, and ultra-low-power operation. To fully realize
low-power operation, a great deal of the IPv6 network and routing
stack needs to be implemented directly on a NCP (Network
Co-Processor). With that, we again find ourselves with a question of
how best to communicate with and manage this new class of peripheral.

The problem of needing to communicate with and manage an NCP is not
new: However, the current existing and proposed approaches have severe
drawbacks (See (#appendix-comparison)).

Spinel is a host-controller protocol designed to address the drawbacks
of existing protocols and lower implementation and deployment burdens
by providing a standardized mechanism for the management and control
of IPv6-based NCPs by general purpose device operating systems (OS).

## Objectives ##

Spinel was designed to satisfy the following objectives:

1.  The protocol must allow for managing the network interface at the
    network layer.
2.  The protocol must allow for multiple data streams, including IPv6
    traffic, debug messages, and raw sniffed traffic.
3.  The protocol must be link agnostic: should work efficiently over
    UART, SPI, USB or other busses and links.
4.  The protocol must be relatively straightforward to understand and
    implement.
5.  The protocol must gracefully handle the addition of new features
    and capabilities without necessarily breaking backward
    compatibility.
6.  The protocol must support multiple conceptually independent
    network interfaces.
7.  The protocol must be reasonably compact and lightweight.

Beyond this core framework, properties and commands enable various
common features of IPv6. In related and forthcoming documents, the
Spinel protocol is extended to support NCP implementations for
specific IPv6 link types, e.g. Thread(R).
