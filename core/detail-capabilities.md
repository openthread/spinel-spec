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

Code    | Name                      | Description
:-------|:--------------------------|:----------------------------------
   1    | `CAP_LOCK`                | See (#prop-lock)
   2    | `CAP_NET_SAVE`            | draft-rquattle-spinel-network-save
   3    | `CAP_HBO`                 | draft-rquattle-spinel-hbo
   4    | `CAP_POWER_SAVE`          | EDITOR: to be provided.
   5    | `CAP_COUNTERS`            | EDITOR: to be provided.
   7    | `CAP_PEEK_POKE`           | Peek/poke debugging commands.
   8    | `CAP_WRITABLE_RAW_STREAM` | `PROP_STREAM_RAW` is writable.
   9    | `CAP_GPIO`                | draft-rquattle-spinel-gpio
  10    | `CAP_TRNG`                | draft-rquattle-spinel-trng
  11    | `CAP_CMD_MULTI`           | See (#cmd-prop-value-multi-get), (#cmd-prop-value-multi-set), (#cmd-prop-values-are)
  12    | `CAP_UNSOL_UPDATE_FILTER` | See (#prop-unsol-update-filter), (#prop-unsol-update-list)
  48    | `CAP_ROLE_ROUTER`         | EDITOR: to be provided.
  49    | `CAP_ROLE_SLEEPY`         | EDITOR: to be provided.
 512    | `CAP_MAC_WHITELIST`       | EDITOR: to be provided.
 513    | `CAP_MAC_RAW`             | EDITOR: to be provided.
 514    | `CAP_OOB_STEERING_DATA`   | EDITOR: to be provided.
