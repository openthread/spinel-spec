## MAC Properties {#prop-mac}

### PROP 48: PROP_MAC_SCAN_STATE {#prop-mac-scan-state}
* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Value Type: UINT8
* Units: Enumeration
* Post-Reset Value: `SCAN_STATE_IDLE`

Id | Name
---|-----------
0| `SCAN_STATE_IDLE`
1| `SCAN_STATE_BEACON`
2| `SCAN_STATE_ENERGY`
Table: Enumeration of scan states

`SCAN_STATE_IDLE`
: Indicates that the scanning subsystem is not currently scanning.
You can set the scan state to this value to cancel a scan in progress.

`SCAN_STATE_BEACON`
: Indicates that the scanning subsystem is performing an active scan
for nearby networks. You can start an active scan by setting the scan
state to this value.

`SCAN_STATE_ENERGY`
: Indicates that the scanning subsystem is performing a passive
scan to determine RF energy levels. You can start a passive scan
by setting the scan state to this value.

The scan is complete when the NCP has indicated that the value of
has changed to `SCAN_STATE_IDLE`.

If an attempt to set this property to a value of anything other than
`SCAN_STATE_IDLE` while the current value isn't `SCAN_STATE_IDLE`,
the NCP **MUST** fail and respond with a last status of `STATUS_ALREADY`.
However, setting this property to `SCAN_STATE_IDLE` while it is already
`SCAN_STATE_IDLE` **MUST NOT** be considered an error.

### PROP 49: PROP_MAC_SCAN_MASK {#prop-mac-scan-mask}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: No
* Asynchronous Updates: No
* Required: **REQUIRED**
* Item Type: UINT8
* Units: Channel Index
* Post-Reset Value: Same as `PROP_PHY_CHAN_SUPPORTED`

This property contains the list of channels that will be scanned.

Setting this property to be empty **MUST** reinitialize it with the contents
of `PROP_PHY_CHAN_SUPPORTED`.

### PROP 50: PROP_MAC_SCAN_PERIOD {#prop-mac-scan-period}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **RECOMMENDED**
* Value Type: UINT16_LE
* Unit: milliseconds per channel
* Post-Reset Value: 100ms **RECOMMENDED**

Number of milliseconds that the scan should wait per channel for
beacons or for collecting energy samples.

### PROP 51: PROP_MAC_SCAN_BEACON {#prop-mac-scan-beacon}

* Type: Packet-Stream, Output-Only
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Value Type: Structure

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    CHANNEL    |      RSSI     | MAC_LEN(Bytes, Little endian) |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   MAC_DATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| NET_LEN(Bytes, Little endian) | NET_DATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

Scan beacons have two embedded structures which contain
information about the MAC layer and the NET layer. Their
format depends on the MAC and NET layer currently in use.

For example, in an 802.15.4 PHY, `MAC_DATA` would be formatted as follows:

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      EUI-64 (Upper word) ...                  :
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
:                  ... EUI-64 (Lower word)                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  SHORT_ADDR (Little Endian)   |     PAN_ID (Little Endian)    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      LQI      |
+-+-+-+-+-+-+-+-+
~~~

For the standard network layer, the `NET_DATA` formatted as follows:

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|        PROTOCOL (PUI, three-bytes shown)      |     FLAGS     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   NETWORK_NAME (UTF8) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|0 0 0 0 0 0 0 0|   XPANID_LEN (Little endian)  | XPANID ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-|-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  STEERING_LEN(Little endian)  | STEERING_DATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

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
* Required: **ONLY** if used with 802.15.4 MAC
* Value Type: EUI64_BE
* Post-Reset Value: Tehnology-dependent. **MAY**  be `PROP_HWADDR` or randomly generated.

<!-- RQ -- Break PROP_MAC_15_4_LADDR out into an 802.15.4-specific section -- -->

The 802.15.4 long (extended) address of this node.

This property is only present on NCPs which implement 802.15.4

### PROP 53: PROP_MAC_15_4_SADDR {#prop-mac-15-4-saddr}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required:
    * `CMD_PROP_VALUE_GET`: **REQUIRED** when used with 802.15.4 MAC
    * `CMD_PROP_VALUE_SET`: **RECOMMENDED** when used with 802.15.4 MAC
* Value Type: UINT16_LE
* Post-Reset Value: 65535 (0xFFFF)

<!-- RQ -- Break PROP_MAC_15_4_SADDR out into an 802.15.4-specific section -- -->

The 802.15.4 short address of this node.

This property is only present on NCPs which implement 802.15.4

### PROP 54: PROP_MAC_15_4_PANID {#prop-mac-15-4-panid}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required:
    * `CMD_PROP_VALUE_GET`: **REQUIRED** when used with 802.15.4 MAC
    * `CMD_PROP_VALUE_SET`: **RECOMMENDED** when used with 802.15.4 MAC
* Value Type: UINT16_LE
* Post-Reset Value: 65535 (0xFFFF)

<!-- RQ -- Break PROP_MAC_15_4_PANID out into an 802.15.4-specific section -- -->

The 802.15.4 PANID this node is associated with.

This property is only present on NCPs which implement 802.15.4

### PROP 55: PROP_MAC_RAW_STREAM_ENABLED {#prop-mac-raw-stream-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **RECOMMENDED**
* Value Type: BOOL
* Post-Reset Value: 0 (false)
* See also: ((#prop-stream-raw))

Set to true to enable raw MAC frames to be emitted from `PROP_STREAM_RAW`.

### PROP 56: PROP_MAC_PROMISCUOUS_MODE {#prop-mac-promiscuous-mode}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **RECOMMENDED**
* Value Type: UINT8
* Units: Enumeration
* Post-Reset Value: `MAC_PROMISCUOUS_MODE_OFF`

Possible Values:

Id | Name                          | Description
---|-------------------------------|------------------
 0 | `MAC_PROMISCUOUS_MODE_OFF`    | Normal MAC filtering is in place.
 1 | `MAC_PROMISCUOUS_MODE_NETWORK`| All MAC packets matching network are passed up the stack.
 2 | `MAC_PROMISCUOUS_MODE_FULL`   | All decoded MAC packets are passed up the stack.

See (#prop-stream-raw).

### PROP 57: PROP_MAC_ENERGY_SCAN_RESULT {#prop-mac-escan-result}

* Type: Packet-Stream, Output-Only
* Required: **REQUIRED**
* Value Type: UINT8, INT8
* Units: Channel Index, dB (RF Power)

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    CHANNEL    |      RSSI     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

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
* Value Type: UINT32_LE
* Unit: milliseconds
* Post-Reset Value: 5000ms **RECOMMENDED**
* Required Capability: `CAP_POWER_SAVE`
* See Also: (#prop-power-state)

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
* Required Capability: `CAP_MAC_WHITELIST`
* Item Type: EUI64_BE, UINT8
* Post-Reset Value: empty
* See Also: (#prop-mac-whitelist-enabled)

TODO(RQ): Consider moving this to the debug section.

When whitelisting is enabled, this property prevents the device from being able
to communicate with devices which are not in this list. Also, if the
RSSI field is not 127, the given RSSI value will always be reported
instead of using the RSSI from the radio hardware.

This property is intended to facilitate stack testing and debugging
and SHOULD NOT be used in production.

Item Format:

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      EUI64 (Upper word) ...                   :
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
:                  ... EUI64 (Lower word)                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     RSSI      |
+-+-+-+-+-+-+-+-+
~~~

EUI64
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
* Required capability: `CAP_MAC_WHITELIST`
* Value Type: BOOL
* Post-Reset Value: 0 (false)
* See Also: (#prop-mac-whitelist)

TODO(RQ): Consider moving this to the debug section.

Setting this to true **SHALL** cause `PROP_MAC_BLACKLIST_ENABLED` to be automatically
set to false.

### PROP 4867: PROP_MAC_SRC_MATCH_ENABLED  {#prop-mac-src-match-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Required capability: `CAP_MAC_RAW`, TODO: 802.15.4 PHY CAP
* Value Type: BOOL
* Post-Reset Value: 0 (false)
* See Also: (#prop-mac-src-match-short-addresses), (#prop-mac-src-match-extended-addresses)

Set to true to enable radio source matching or false to disable it. The source match
functionality is used by radios when generating ACKs. The short and extended address
lists are used for settings the Frame Pending bit in the ACKs.

### PROP 4868: PROP_MAC_SRC_MATCH_SHORT_ADDRESSES  {#prop-mac-src-match-short-addresses}

* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Per-Item Length: No
* Required: **OPTIONAL**
* Item Type: UINT16_LE
* Post-Reset Value: empty
* Required Capability: `CAP_MAC_RAW`, TODO: 802.15.4 PHY CAP
* See Also: (#prop-mac-src-match-enabled)

Configures the list of short addresses used for source matching. These
short address are used for hardware generated ACKs.

### PROP 4869: PROP_MAC_SRC_MATCH_EXTENDED_ADDRESSES  {#prop-mac-src-match-extended-addresses}
* Type: Multi-Value, Read-Write
* Asynchronous Updates: No
* Per-Item Length: No
* Required: **OPTIONAL**
* Item Type: EUI64_BE
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
* Required Capability: `CAP_MAC_WHITELIST`
* Item Type: EUI64_BE
* Post-Reset Value: empty
* See Also: (#prop-mac-blacklist-enabled)

TODO(RQ): Consider moving this to the debug section.

When blacklisting is enabled, this property prevents the device from being able
to communicate with devices which are in this list.

### PROP 4871: PROP_MAC_BLACKLIST_ENABLED  {#prop-mac-blacklist-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Value Type: BOOL
* Post-Reset Value: 0 (false)
* Required capability: `CAP_MAC_WHITELIST`
* See Also: (#prop-mac-blacklist)

TODO(RQ): Consider moving this to the debug section.

Setting this to true **SHALL** cause `PROP_MAC_WHITELIST_ENABLED` to be automatically
set to false.

