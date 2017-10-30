## MAC Properties {#prop-mac}

### PROP 48: PROP_MAC_SCAN_STATE {#prop-mac-scan-state}
* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Unit: Enumeration
* Post-Reset Value: 0 (false)

Bytes:  |    1
-------:|-------------
Format: | UINT8
Fields: | `SCAN_STATE`

TODO(RQ): This needs to be re-thought out. I don't like the whole discovery
thing being a separate scan state. Maybe we should add another property
for scanning only compatible networks.

Possible Values:

* 0: `SCAN_STATE_IDLE`
* 1: `SCAN_STATE_BEACON`
* 2: `SCAN_STATE_ENERGY`
* 3: `SCAN_STATE_DISCOVER` DEPRECATED

Set to `SCAN_STATE_BEACON` to start an active scan.
Beacons will be emitted from `PROP_MAC_SCAN_BEACON`.

Set to `SCAN_STATE_ENERGY` to start an energy scan.
Channel energy result will be reported by emissions
of `PROP_MAC_ENERGY_SCAN_RESULT` (per channel).

DEPRECATED: Set to `SCAN_STATE_DISOVER` to start a Thread MLE discovery
scan operation. Discovery scan result will be emitted from
`PROP_MAC_SCAN_BEACON`.

Value switches to `SCAN_STATE_IDLE` when scan is complete.

### PROP 49: PROP_MAC_SCAN_MASK {#prop-mac-scan-mask}

* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Required: **REQUIRED**
* Unit: Channel Index
* Post-Reset Value: Same as PROP_PHY_CHAN_SUPPORTED

Bytes:  |   1 |  1  | ...
-------:|-------|-------|-----
Format: |  UINT8  |  UINT8  | ...
Fields: | `CHANNEL` | `CHANNEL` | ...

This property contains the list of channels that will be scanned.

### PROP 50: PROP_MAC_SCAN_PERIOD {#prop-mac-scan-period}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Unit: milliseconds per channel
* Post-Reset Value: Implementation-specific-default

Bytes:  |    1
-------:|-------------
Format: | UINT16_LE
Fields: | `MSEC`

### PROP 51: PROP_MAC_SCAN_BEACON {#prop-mac-scan-beacon}

* Type: Packet Stream, Output-Only
* Required: **REQUIRED**

* Packed-Encoding: `Ccdd` (or `Cct(ESSc)t(iCUdd)`)

Bytes: | 1  |   1  |    2    |   *n*    |    2    |   *n*
--------|----|------|---------|----------|---------|----------
Format: | UINT8 | INT8 | UINT16_LE | DATA | UINT16_LE | DATA
Fields: | CH | RSSI | MAC_LEN | MAC_DATA | NET_LEN | NET_DATA

Scan beacons have two embedded structures which contain
information about the MAC layer and the NET layer. Their
format depends on the MAC and NET layer currently in use.

For example, in an 802.15.4 PHY, MAC_DATA would be formatted as follows:

Bytes: | 8  |   2  |    2    |  1
--------|----|------|---------|----------
Format: | EUI-64 | UINT16_LE | UINT16_LE | UINT8
Fields: | LONG_ADDR | SHORT_ADDR | PAN-ID | LQI

For the standard network layer, the NET_DATA formatted as follows:

Bytes: | 1-3  |   1  |    *n*                             |  1   |  2 |  *n*    |  2| *n*
--------|----|------|----------------------------|------|---|------|----------
Format: | PUI | UINT8 | UTF8                      |  ZT  |  UINT16_LE  | DATA   |UINT16_LE   | DATA
Fields: | PROTO | FLAGS | NETWORK_NAME | 0x00 | XPANID_LEN| XPANID | STEERING_LEN | STEERING

Extra parameters may be added to each of the structures
in the future, so care should be taken to read the length
that prepends each structure.

The RSSI is measured in dB.
The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.

### PROP 52: PROP_MAC_15_4_LADDR {#prop-mac-15-4-laddr}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **ONLY** used with 802.15.4 MAC
* Post-Reset Value: Tehnology-dependent. **MAY**  be `PROP_HWADDR` or be randomly generated.

Bytes: | 8
--------|----
Format: | EUI-64
Fields: | LADDR

TODO(RQ): Break this out into an 802.15.4-specific section

The 802.15.4 long address of this node.

This property is only present on NCPs which implement 802.15.4

### PROP 53: PROP_MAC_15_4_SADDR {#prop-mac-15-4-saddr}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **ONLY** when used with 802.15.4 MAC
* Post-Reset Value: 0xFFFF

Bytes: | 2
--------|----
Format: | UINT16_LE
Fields: | SADDR

TODO(RQ): Break this out into an 802.15.4-specific section

The 802.15.4 short address of this node.

This property is only present on NCPs which implement 802.15.4

### PROP 54: PROP_MAC_15_4_PANID {#prop-mac-15-4-panid}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **ONLY** when used with 802.15.4 MAC
* Post-Reset Value: 0xFFFF ?

Bytes: | 2
--------|----
Format: | UINT16_LE
Fields: | PANID

TODO(RQ): Break this out into an 802.15.4-specific section

The 802.15.4 PANID this node is associated with.

This property is only present on NCPs which implement 802.15.4

### PROP 55: PROP_MAC_RAW_STREAM_ENABLED {#prop-mac-raw-stream-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **RECOMMENDED**
* Post-Reset Value: 0 (false)

Bytes: | 1
--------|----
Format: | BOOL
Fields: | RAW_STREAM_ENABLED

Set to true to enable raw MAC frames to be emitted from `PROP_STREAM_RAW`.
See (#prop-stream-raw).

### PROP 56: PROP_MAC_PROMISCUOUS_MODE {#prop-mac-promiscuous-mode}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **RECOMMENDED**
* Unit: Enumeration
* Post-Reset Value: `MAC_PROMISCUOUS_MODE_OFF`

Bytes: | 1
--------|----
Format: | UINT8
Fields: | MAC_PROMISCUOUS_MODE

Possible Values:

Id | Name                          | Description
---|-------------------------------|------------------
 0 | `MAC_PROMISCUOUS_MODE_OFF`    | Normal MAC filtering is in place.
 1 | `MAC_PROMISCUOUS_MODE_NETWORK`| All MAC packets matching network are passed up the stack.
 2 | `MAC_PROMISCUOUS_MODE_FULL`   | All decoded MAC packets are passed up the stack.

See (#prop-stream-raw).

### PROP 57: PROP_MAC_ENERGY_SCAN_RESULT {#prop-mac-escan-result}

* Type: Packet Stream, Output-Only
* Required: **REQUIRED**

Bytes: | 1 | 1
--------|----|-
Format: | UINT8 | INT8
Fields: | CHANNEL | RSSI

This property is emitted during energy scan operation (SCAN_STATE_ENERGY)
once per scanned channel.

The RSSI is measured in dB.
The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.


### PROP 58: PROP_MAC_DATA_POLL_PERIOD {#prop-mac-data-poll-period}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Unit: milliseconds
* Post-Reset Value: 5000ms
* Required Capability: `CAP_POWER_SAVE`
* See Also: (#prop-power-state)

Bytes: | 1
--------|----
Format: | UINT32_LE
Fields: | MAC_DATA_POLL_PERIOD

The (user-specified) data poll (802.15.4 MAC Data Request) period
in milliseconds. Value zero means the poll period will be calculated
by the stack to be the maximum interval acceptable by the parent. If the
maximum poll interval is longer than the maximum interval acceptable
by the parent, the later value is used instead.

The value of this property is only used if `PROP_POWER_STATE` is set to
`POWER_STATE_LOW_POWER`. In all other states, the value of this property is
ignored.

This property is only used on NCPs which support `POWER_STATE_LOW_POWER`.

### PROP 4864: PROP_MAC_WHITELIST  {#prop-mac-whitelist}

* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Per-Item Length: No
* Required: **OPTIONAL**
* Post-Reset Value: empty
* Required Capability: `CAP_MAC_WHITELIST`
* See Also: (#prop-mac-whitelist-enabled)

TODO(RQ): Consider moving this to the debug section.

When whitelisting is enabled, this property prevents the device from being able
to communicate with devices which are not in this list. Also, if the
RSSI field is not 127, the given RSSI value will always be reported
instead of using the RSSI from the radio hardware.

This property is intended to facilitate stack testing and debugging
and SHOULD NOT be used in production.

Item Format:

Bytes: | 8   | 1
--------|----|-----
Format: | EUI-64-BE | UINT8
Fields: | ADDR | RSSI

ADDR
: EUI64 address of node

RSSI
: Optional RSSI-override value. The value 127 indicates
       that the RSSI-override feature is not enabled for this
       address. If this value is omitted when setting or
       inserting, it is assumed to be 127. This parameter is
       ignored when removing and may be omitted.

### PROP 4865: PROP_MAC_WHITELIST_ENABLED  {#prop-mac-whitelist-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Post-Reset Value: 0 (false)
* Required capability: `CAP_MAC_WHITELIST`
* See Also: (#prop-mac-whitelist)

Bytes:  |    1
-------:|-------------
Format: | BOOL
Fields: | `WHITELIST_ENABLED`

TODO(RQ): Consider moving this to the debug section.

Setting this to true **SHALL** cause `PROP_MAC_BLACKLIST_ENABLED` to be automatically
set to false.

### PROP 4867: PROP_MAC_SRC_MATCH_ENABLED  {#prop-mac-src-match-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Post-Reset Value: 0 (false)
* Required capability: `CAP_MAC_RAW`, TODO: 802.15.4 PHY CAP
* See Also: (#prop-mac-src-match-short-addresses), (#prop-mac-src-match-extended-addresses)

Bytes:  |    1
-------:|-------------
Format: | BOOL
Fields: | `SRC_MATCH_ENABLED`

Set to true to enable radio source matching or false to disable it. The source match
functionality is used by radios when generating ACKs. The short and extended address
lists are used for settings the Frame Pending bit in the ACKs.

### PROP 4868: PROP_MAC_SRC_MATCH_SHORT_ADDRESSES  {#prop-mac-src-match-short-addresses}

* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Per-Item Length: No
* Required: **OPTIONAL**
* Post-Reset Value: empty
* Required Capability: `CAP_MAC_RAW`, TODO: 802.15.4 PHY CAP
* See Also: (#prop-mac-src-match-enabled)

Configures the list of short addresses used for source matching. These
short address are used for hardware generated ACKs.

Item Format:

Bytes: | 2
--------|----
Format: | UINT16_LE
Fields: | SADDR

### PROP 4869: PROP_MAC_SRC_MATCH_EXTENDED_ADDRESSES  {#prop-mac-src-match-extended-addresses}
* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Per-Item Length: No
* Required: **OPTIONAL**
* Post-Reset Value: empty
* Required Capabilities: `CAP_MAC_RAW`, TODO: 802.15.4 PHY CAP
* See Also: (#prop-mac-src-match-enabled)

Configures the list of long addresses used for source matching. These
long address are used for hardware generated ACKs.

### PROP 4870: PROP_MAC_BLACKLIST  {#prop-mac-blacklist}

* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Per-Item Length: No
* Required: **OPTIONAL**
* Post-Reset Value: empty
* Required Capability: `CAP_MAC_WHITELIST`
* See Also: (#prop-mac-blacklist-enabled)

TODO(RQ): Consider moving this to the debug section.

When blacklisting is enabled, this property prevents the device from being able
to communicate with devices which are in this list.

### PROP 4871: PROP_MAC_BLACKLIST_ENABLED  {#prop-mac-blacklist-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Post-Reset Value: 0 (false)
* Required capability: `CAP_MAC_WHITELIST`
* See Also: (#prop-mac-blacklist)

TODO(RQ): Consider moving this to the debug section.

Setting this to true **SHALL** cause `PROP_MAC_WHITELIST_ENABLED` to be automatically
set to false.

Bytes:  |    1
-------:|-------------
Format: | BOOL
Fields: | `BLACKLIST_ENABLED`
