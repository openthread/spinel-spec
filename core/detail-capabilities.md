# Capabilities {#capabilities}

Capabilities are how an NCP can advertise support for specific behaviors and functionalities. They can be fetched via the `PROP_CAPS` property.

See (#prop-caps) for more information on `PROP_CAPS`.

A registry of capability codes is maintained by IANA with various reservation policies in effect for different ranges of values as shown in the table below:

Capability Range        | Description
:-----------------------|:-----------------
0 - 1027                | Standards Action
1027 - 15,359           | Unassigned
15,360 - 16,383         | Private Use
16,384 - 1,999,999      | Unassigned
2,000,000 - 2,097,151   | Experimental Use

The initial content of the registry is shown in the table below.

Code    | Name                      | Reference
:-------|:--------------------------|:----------------------------------
   1    | `CAP_LOCK`                | (#prop-lock)
   2    | Reserved                  |
   3    | Reserved                  |
   4    | `CAP_POWER_SAVE`          | (#prop-power-state), (#prop-host-power-state)
   5    | Reserved                  |
   7    | `CAP_PEEK_POKE`           | (#cmd-peek)-(#cmd-poke)
   8    | `CAP_WRITABLE_RAW_STREAM` | (#prop-stream-raw)
   9    | Reserved                  |
  10    | Reserved                  |
  11    | `CAP_CMD_MULTI`           | (#cmd-prop-value-multi-get)
  12    | `CAP_UNSOL_UPDATE_FILTER` | (#prop-unsol-update-filter)
 512    | `CAP_MAC_WHITELIST`       | (#prop-mac-whitelist)
 513    | `CAP_MAC_RAW`             | EDITOR: to be provided.
 TBD    | `CAP_MAC_802_15_4`        | TBD
 TBD    | `CAP_NET_STANDARD`        | TBD
