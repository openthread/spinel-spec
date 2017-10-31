## PHY Properties {#prop-phy}

### PROP 32: PROP_PHY_ENABLED {#prop-phy-enabled}

* Type: Single-Value, Read/Write
* Asynchronous Updates: No
* Required:
    * `CMD_PROP_VALUE_GET`: **REQUIRED**
    * `CMD_PROP_VALUE_SET`: **RECOMENDED**
* Scope: NLI
* Related Capabilities:
  * `CAP_MAC_RAW` required to use `CMD_PROP_VALUE_SET`
* Value Type: BOOL
* Post-Reset Value: 0 (false)

Set to 1 if the PHY is enabled, set to 0 otherwise.
May be directly enabled to bypass higher-level packet processing
in order to implement things like packet sniffers. This property
can only be written if the `SPINEL_CAP_MAC_RAW` capability is present.

### PROP 33: PROP_PHY_CHAN {#prop-phy-chan}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **REQUIRED**
* Value Type: UINT8
* Units: Channel Index
* Post-Reset Value: Unspecified
* See Also: ((#prop-phy-chan-supported))

Value is the current channel index. Must be set to one of the
values contained in `PROP_PHY_CHAN_SUPPORTED`.
m
### PROP 34: PROP_PHY_CHAN_SUPPORTED {#prop-phy-chan-supported}

* Type: Multiple-Value, Constant
* Has Item Length Prefix: No
* Asynchronous Updates: No
* Required: **REQUIRED**
* Item Type: UINT8
* Units: Channel Index
* See Also: ((#prop-phy-chan))

Value is a list of channel indexes that are supported by the
NCP.

### PROP 35: PROP_PHY_FREQ {#prop-phy-freq}

* Type: Single-Value, Read-Only
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NLI
* Value Type: UINT32_LE
* Units: kHz
* Post-Reset Value: Unspecified
* See Also: ((#prop-phy-chan))

Value is the radio frequency (in kilohertz) of the
current channel.

### PROP 36: PROP_PHY_CCA_THRESHOLD {#prop-phy-cca-threshold}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Value Type: INT8
* Unit: dB (RF Power)
* Post-Reset Value: Implementation Specified

Value is the CCA (clear-channel assessment) threshold. Set to
-128 to disable.

The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.

When setting, the value **SHOULD** be rounded down to a value
that is supported by the underlying radio hardware.

### PROP 37: PROP_PHY_TX_POWER {#prop-phy-tx-power}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **REQUIRED**
* Value Type: INT8
* Unit: dB (RF Power)
* Post-Reset Value: 0dB **RECOMMENDED**

Value is the maximum transmit power the network stack is allowed
to set the radio to, measured in decibels.

The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.

When setting, the value **SHOULD** be rounded down to a value
that is supported by the underlying radio hardware.

### PROP 38: PROP_PHY_RSSI {#prop-phy-rssi}

* Type: Single-Value, Read-Only
* Asynchronous Updates: No
* Required: **REQUIRED**
* Value Type: INT8
* Unit: dB (RF Power)
* Post-Reset Value: Unspecified

Value is the current RSSI (Received Signal Strength Indication)
from the radio. This value can be used in energy scans and for
determining the ambient noise floor for the operating environment.

The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.

### PROP 39: PROP_PHY_RX_SENSITIVITY {#prop-phy-rx-sensitivity}

* Type: Single-Value, Constant
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Value Type: INT8
* Unit: dB

Bytes:  |    1
-------:|-------------
Format: | INT8
Fields: | `RX_SENSITIVITY`

Value is the radio receive sensitivity. This value can be used as
lower bound noise floor for link metrics computation.

The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.
