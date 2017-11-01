# Properties

This section defines the syntax and specific operational details
of the Spinel properties common to the core protocol.

## Command Semantics

Individual properties MAY support one or more of the following
commands:

*   `CMD_PROP_VALUE_GET` ((#cmd-prop-value-get))
*   `CMD_PROP_VALUE_SET` ((#cmd-prop-value-set))
*   `CMD_PROP_VALUE_INSERT` ((#cmd-prop-value-insert))
*   `CMD_PROP_VALUE_REMOVE` ((#cmd-prop-value-remove))

Upon an error condition, the NCP **SHALL** respond in the typical
fasion of indicating the error code via a `CMD_PROP_VALUE_IS` command
with the appropriate status code(See @detail-status).

Unless otherwise specified, an NCP **MUST NOT** emit a
`PROP_LAST_STATUS` of `STATUS_OK` in response to the successful
execution of any of the above commands.

Upon successful execution of one of the property manipulation commands, the
NCP SHALL send one of the following commands (with the same property key that
was used for the sending command) to indicate success:

* `CMD_PROP_VALUE_IS` ((#cmd-prop-value-is))
* `CMD_PROP_VALUE_INSERTED` ((#cmd-prop-value-inserted))
* `CMD_PROP_VALUE_REMOVED` ((#cmd-prop-value-removed))

When an NCP has successfully handled a `CMD_PROP_VALUE_INSERT` or
`CMD_PROP_VALUE_REMOVE` command on a multi-value property, the NCP
**SHOULD** respond with a `CMD_PROP_VALUE_INSERTED` or
`CMD_PROP_VALUE_REMOVED`, respectfully. However, if doing so is
prohibitively complex, the NCP **MAY** instead respond with a
`CMD_PROP_VALUE_IS` containing the entire list of items.

The reason for the above behavior is that the NCP is the ultimate
authority on the value of all properties. By always indicating success
by responding with the value of the property, we can make sure that
the software running on the host is in sync with the behavior of the NCP.

## Asynchronous Updates

Some properties support indicating changes in their state asynchronously
by sending the following commands with a TID value of 0:

* `CMD_PROP_VALUE_IS` ((#cmd-prop-value-is))
* `CMD_PROP_VALUE_INSERTED` ((#cmd-prop-value-inserted))
* `CMD_PROP_VALUE_REMOVED` ((#cmd-prop-value-removed))

Asynchronous updates for specific properties can be enabled or disabled
using `PROP_UNSOL_UPDATE_FILTER`, documented in (#prop-unsol-update-filter).

## Property Identifiers

IANA maintains a registry of Spinel `PROP_ID` property identifier
numbers, with varying registration policies assigned for different
ranges according to the following table:

Property ID Range     | Description
:---------------------|:-----------------
0 - 127               | Standards Action
128 - 4,095           | Unassigned
4,096 - 6,143         | Standards Action
6,144 - 15,359        | Unassigned
15,360 - 16,383       | Private Use
16,384 - 17,407       | Standards Action
17,408 - 1,999,999    | Unassigned
2,000,000 - 2,097,151 | Experimental Use

## Property Identifier Sections

Standard property identifier numbers are assigned in a hierarchy
according to their purpose, as shown in the table below:

Name   | Primary    | Extended          | Documentation
:------|:-----------|:------------------|:-------------
Core   | 0 - 31     | 4,096 - 4,607     | (#prop-core)
PHY    | 32 - 47    | 4,608 - 4,863     | Physical (PHY) layer specific
MAC    | 48 - 63    | 4,864 - 5,119     | Media access (MAC) layer specific
NET    | 64 - 79    | 5,120 - 5,375     | (#prop-net)
Tech   | 80 - 95    | 5,376 - 5,631     | (#prop-interface-type)
IPv6   | 96 - 111   | 5,632 - 5,887     | (#prop-ipv6)
Stream | 112 - 127  | 5,888 - 6,143     | (#prop-core)
Debug  | no primary | 16,384 - 17,407   | (#prop-debug)

Note: most of the property identifier sections have two reserved
ranges: a "primary" range (which is encoded as a single byte) and an
"extended" range (which is encoded as two bytes). Properties used
very frequently are generally allocated from the "primary" range.

EDITOR: the IANA registration template for Spinel standard properties identifiers requires the "Section Name" and "Range Identifier" parameters to facilitate the assignment of a suitable number from the appropriate range.

{{detail-properties-core.md}}

{{detail-properties-phy.md}}

{{detail-properties-mac.md}}

{{detail-properties-net.md}}

{{detail-properties-ipv6.md}}

{{detail-properties-debug.md}}
