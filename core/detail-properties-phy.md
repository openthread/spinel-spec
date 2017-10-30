## PHY Properties {#prop-phy}

### PROP 32: PROP_PHY_ENABLED {#prop-phy-enabled}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required:
    * Read: Yes
    * Write: No
* Post-Reset Value: 0 (false)
* Related Capabilities: SPINEL_CAP_MAC_RAW

Bytes:  |    1
-------:|-------------
Format: | BOOL
Fields: | `PHY_ENABLED`

Set to 1 if the PHY is enabled, set to 0 otherwise.
May be directly enabled to bypass higher-level packet processing
in order to implement things like packet sniffers. This property
can only be written if the `SPINEL_CAP_MAC_RAW` capability is present.

### PROP 33: PROP_PHY_CHAN {#prop-phy-chan}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **REQUIRED**
* Unit: Channel Index
* Post-Reset Value: Unspecified

Bytes:  |    1
-------:|-------------
Format: | UINT8
Fields: | `CHANNEL`

Value is the current channel. Must be set to one of the
values contained in `PROP_PHY_CHAN_SUPPORTED`.

### PROP 34: PROP_PHY_CHAN_SUPPORTED {#prop-phy-chan-supported}

* Type: Multiple-Value, Constant
* Asynchronous Updates: No
* Required: **REQUIRED**
* Unit: Channel Index
* Post-Reset Value: Implementation Specific

Bytes:  |   1 |  1  | ...
-------:|-------|-------|-----
Format: |  UINT8  |  UINT8  | ...
Fields: | `CHANNEL` | `CHANNEL` | ...

Value is a list of channel values that are supported by the
NCP.

### PROP 35: PROP_PHY_FREQ {#prop-phy-freq}

* Type: Single-Value, Read-Only
* Asynchronous Updates: No
* Required: **REQUIRED**
* Unit: kHz
* Post-Reset Value: Unspecified

Bytes:  |    1
-------:|-------------
Format: | UINT32_LE
Fields: | `FREQ`

Value is the radio frequency (in kilohertz) of the
current channel.

### PROP 36: PROP_PHY_CCA_THRESHOLD {#prop-phy-cca-threshold}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Unit: dB
* Post-Reset Value: Implementation-specific default

Bytes:  |    1
-------:|-------------
Format: | INT8
Fields: | `CCA_THRESHOLD`

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
* Unit: dB
* Post-Reset Value: 0dB

Bytes:  |    1
-------:|-------------
Format: | INT8
Fields: | `TX_POWER`

Value is the transmit power of the radio, measured in decibells.

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
* Unit: dB
* Post-Reset Value: Unspecified

Bytes:  |    1
-------:|-------------
Format: | INT8
Fields: | `RSSI`

Value is the current RSSI (Received Signal Strength Indication)
from the radio. This value can be used in energy scans and for
determining the ambient noise floor for the operating environment.

The specific meaning of zero dB is implementation-specific,
but **MUST** be consistant with definitions of other properties
that express power. It is **RECOMMENDED** that zero dB
represent one milliwatt of power (0dBm), as measured from the
antenna connector.

### PROP 39: PROP_PHY_RX_SENSITIVITY {#prop-phy-rx-sensitivity}

* Type: Single-Value, Read-Only
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Unit: dB
* Post-Reset Value: Unspecified

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
