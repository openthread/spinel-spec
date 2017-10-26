# Comparison with other protocols {#appendix-comparison}

The problem of needing to manage a connectivity peripheral attached to
a general-purpose computer is not new. The Hayes Command Set
(TODO:CITE), for example, has been in use since 1981 and quickly
became a ubiquitous de-facto standard for configuring dial-up modems
and other similar peripherals. In more recent times, the standardized
Bluetooth HCI protocol (TODO:CITE) helps to ensure plug-and-play
interoperability between the operating system and the Bluetooth
peripheral.

IEEE 802.15.4 (TODO:CITE) based low-power wireless networks are
quickly becoming ubiquitous, and with the introduction of 6LoWPAN
([@RFC4944]), IPv6 connectivity is now possible on these networks.
6LoWPAN-based network technologies such as Thread(R) and ZigBee(R) IP
additionally provide additional features such as network security,
mesh-networking, and ultra-low-power operation. To fully realize
low-power operation, a great deal of the IPv6 network and routing
stack needs to be implemented directly on the connectivity peripheral.
With that, we again find ourselves with a question of how best to
communicate with and manage this new class of peripheral.
There have been a wide range of solutions to this problem, each with
significant drawbacks:

*   Using SLIP ([@RFC1055])
    *   UART-only
    *   Management must be handled out-of-band
    *   Widely supported
*   Using slipmux ([@I-D.bormann-t2trg-slipmux#01])
    *   UART-only
    *   Management in-band, but is managed using CoAP. Exact details
        of management protocol are not specified.
    *   Backward compatible with existing SLIP implementations.
*   USB CDC-ECM for data, using some other USB protocol for management
    (TODO:CITE)
    *   USB-only
    *   Somewhat Plug-and-play (appears as an ethernet device)
    *   Mismatched network layer: CDC-ECM is layer-2, whereas IPv6 is
        layer-3. This leads to the requirement of clever work-arounds
        that can't satisfactorily handle all cases.
    *   Bespoke, text-based management protocol
*   Proprietary protocols
    *   Often covered by NDA
    *   Not plug-and-play, can't easily swap parts with a different
        vendor
    *   Can change often and without warning

Standardized interfaces to specific classes of connectivity
peripherals significantly reduces the implementation and deployment
burdens. Bluetooth, for example, has standardized on the Bluetooth
HCI, which allows plug-and-play interoperability between Bluetooth
peripherals. Similar to Bluetooth HCI, Spinel is frame-based and
doesn't mandate the exact mechanism used to exchange frames between
the peripheral and the host. It does, however, make recommendations,
and we do likewise in (#appendix-framing).

Note that, unlike PPP [-@RFC1661] or SLIP [-@RFC1055] (where the cable
is the network medium and the serial port of the network interface),
Spinel is not a link-layer network protocol between machines: it is an
extension of the IPv6 stack into an external network co-processor.
Spinel isn't used for directly communicating with another independent
host: it is instead used for communicating with an intermediary (the
NCP) who acts on the behalf of the application processor. The network
interface doesn't end at the serial port, rather it extends into the
NCP---which might not have it's own independent IPv6 address.
