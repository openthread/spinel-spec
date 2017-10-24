Spinel NCP Protocol Specification
=================================

This repository contains the raw, [mmark][]-formatted sources for the
documentation of the Spinel protocol (otherwise known just as
"Spinel").

[mmark]: https://github.com/miekg/mmark

Spinel facilitates the control and management of IPv6 network
interfaces on devices where general purpose application processors
offload network functions at their interfaces to network co-processors
(NCP) connected by simple communication links like serial data
channels. Spinel was initially designed for use with Thread(R) network
co-processors, but its core is general purpose and intended to be
easily adapted to other types of IPv6 network interfaces.

The specific specifications currently being developed here are:

*   `draft-rquattle-spinel-core`: Spinel protocol core specification
    (in the `core` subdirectory)
*   `draft-rquattle-spinel-network-save`: Network-save functionality
    (in the `network-save` subdirectory)
*   `draft-rquattle-spinel-thread`: Support for
    [Thread](http://threadgroup.org)(R) (in the `thread` subdirectory)
*   `draft-rquattle-spinel-gpio`: Support for GPIO observation and
    manipulation (in the `gpio` subdirectory)
*   `draft-rquattle-spinel-hbo`: Support for host-buffer-offload (in
    the `hbo` subdirectory)
*   `draft-rquattle-spinel-jam-detect`: Support for jamming-detection
    (in the `jam-detect` subdirectory)
*   `draft-rquattle-spinel-trng`: Support for true random number
    generation (in the `trng` subdirectory)

These specifications are all intended to eventually become IETF
standards-track, but are currently at varrying stages of completeness.

## Building ##

If you would like to build the Spinel documentation from these
sources, the easiest way to do so is to use [Docker](http://docker.io)
and the included makefiles.

After Docker is set up properly on your machine, using `make` from the
root will build all documentation targets. Using `make` from the
individual directories will only build the documentation for that
specific directory. The first time you run `make`, some extra time may
be required while Docker fetches the appropriate container.

## Temporary Registry ##

A temporary authoritative registry of all commands, properties, and
capabilities is currently located in the `registery` directory. See
the related [README](registry/README.md) for more information.

