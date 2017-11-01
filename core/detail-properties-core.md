## Core Properties {#prop-core}

### PROP 0: PROP_LAST_STATUS {#prop-last-status}

* Type: Single-Value, Read-Only
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Value Type: PUI
* Units: Enumeration
* Post-Reset Value: Reset Reason Code

This property describes the status code of the last NCP operation. For
many commands, failure is indicated by emitting this property with the
TID matching the failing command. It is generally not necessary to
ever fetch the value of this property explicitly, as it is often
emitted directly as an error response. It is also occasionally emitted
as a success response with a value of `STATUS_OK`.

Upon NCP reset, this property **MUST** be emitted with a status code
indicating the reset reason.

Upon receiving an update to PROP*LAST*STATUS with a status code that
indicates a reset, the host SHALL assume that the NCP has been reset
and that all properties have reverted to their defined after-reset
values.

See (#status-codes) for the complete list of status codes.

### PROP 1: PROP_PROTOCOL_VERSION {#prop-protocol-version}

* Type: Single-Value, Constant
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NCP
* Value Type: UINT8, UINT8
* Post-Reset Value: 4, 3

Describes the spinel protocol version information. This property
contains two fields:

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| MAJOR_VERSION | MINOR_VERSION |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: PROP_PROTOCOL_VERSION Value Format

`MAJOR_VERSION`
: The major version number is used to identify backward incompatible
  differences between protocol versions. The AP **MUST** enter a
  FAULT(TODO: Define this) state if the given major version number is
  unsupportable.

`MINOR_VERSION`
: The minor version number is used to identify backward-compatible
  differences between protocol versions. A mismatch between the
  advertised minor version number and the minor version that is
  supported by the AP **SHOULD NOT** be fatal to the operation of the
  AP.

This document describes major version 4, minor version 3 of this
protocol. (TODO: UPDATE THIS)

If a NCP supports multiple NLIs, the value of this property **MUST**
be identical across all implemented NLIs.

### PROP 2: PROP_NCP_VERSION {#prop-ncp-version}

* Type: Single-Value, Constant
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NCP
* Value Type: STRING
* Post-Reset Value: Implementation-Specific


Contains a zero-terminated ASCII string which describes the firmware
currently running on the NCP.

The value of this string **MUST** be different for every firmware
release.

The format of the string is not strictly defined, but it is intended
to present similarly to the "User-Agent" string from HTTP. The
following format is **RECOMMENDED**:

  `STACK-NAME/STACK-VERSION[BUILD-INFO][; OTHER-INFO][; BUILD-DATE]`

Where:

STACK-NAME
: The name of the software running on the NCP.

STACK-VERSION
: The version number of the software running on the NCP, like `1.0` or
  `3.2.6`

BUILD-INFO
: Optional information indicating a specific build, like `b125` or
  `d26-25-gb684c7f`.

OTHER-INFO
: Other optional information about the build.

BUILD-DATE
: **OPTIONAL** build date.

Examples:

 *  `OpenThread/1.0d26-25-gb684c7f; DEBUG; May 9 2016 18:22:04`
 *  `ConnectIP/2.0b125 s1 ALPHA; Sept 24 2015 20:49:19`

If a NCP supports multiple NLIs, the value of this property **MUST** be
identical across all implemented NLIs.

### PROP 3: PROP_INTERFACE_TYPE {#prop-interface-type}

* Type: Single-Value, Constant
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NLI
* Value Type: PUI
* Units: Enumeration
* Post-Reset Value: Implementation-Specific

This unsigned packed integer identifies the network protocol
implemented by this NCP. A registry of network interface type codes is
maintained by IANA with a reservation policy of Specification
Required. The initial content of the registry is shown in the table
below:

Code    | Network protocol
:-------|:----------------
0       | Bootloader
2       | ZigBee IP(TM)
3       | Thread(R)

The AP **MUST** enter a FAULT state if it does not recognize the
network protocol given by the NCP.

### PROP 4: PROP_INTERFACE_VENDOR_ID {#prop-interface-vendor-id}

* Type: Single-Value, Constant
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NCP
* Value Type: PUI
* Units: Enumeration
* Post-Reset Value: Implementation-Specific

<!-- JW -- I have no idea how to write the IANA registry creation text for this. -- -->
<!-- RQ -- Maybe we should switch this over to be a string instead? -- -->

### PROP 5: PROP_CAPS {#prop-caps}

* Type: Multiple-Value, Constant
* Has Item Length Prefix: No
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NLI
* Item Type: PUI
* Units: Enumeration
* Post-Reset Value: Implementation-Specific

Describes the supported capabilities of this NCP. Encoded as a list of
packed unsigned integers. See (#capabilities) for a list of values.

### PROP 6: PROP_INTERFACE_COUNT {#prop-interface-count}

* Type: Single-Value, Constant
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NCP
* Item Type: UINT8
* Units: Count
* Post-Reset Value: 1-4

Describes the number of concurrent interfaces supported by this NCP.
Since the concurrent interface mechanism is still TBD, this value
**MUST** always be one.

If a NCP supports multiple NLIs, the value of this property **MUST** be
identical across all implemented NLIs.

### PROP 7: PROP_POWER_STATE {#prop-power-state}

* Type: Single-Value, Read/Write
* Asynchronous Updates: Yes
* Required: **RECOMENDED**
* Scope: NLI
* Item Type: UINT8
* Units: Enumeration
* Post-Reset Value: `POWER_STATE_ONLINE`
* Required Capability: `CAP_POWER_SAVE`
* See Also: (#prop-mac-data-poll-period)

A single byte coded that indicates the current power state of the
NCP. Setting this property allows controls of the current NCP power
state. The following table enumerates the standard codes and their
significance.

Code    | Name
:-------|:-------------------------
    0   | `POWER_STATE_OFFLINE`
    1   | `POWER_STATE_DEEP_SLEEP`
    2   | `POWER_STATE_STANDBY`
    3   | `POWER_STATE_LOW_POWER`
    4   | `POWER_STATE_ONLINE`

<!-- JW
  -- TODO: We should consider reversing the numbering here so
     that 0 is `POWER_STATE_ONLINE`. We may also want to include
	 some extra values between the defined values for future
	 expansion, so that we can preserve the ordered relationship.
  -- -->

`POWER_STATE_OFFLINE`
: NCP is physically powered off.

`POWER_STATE_DEEP_SLEEP`
: NCP is not powered to detect any events on physical network media.

`POWER_STATE_STANDBY`
: NCP is powered only to detect certain events on physical network media that signal to wake.

`POWER_STATE_LOW_POWER`
: NCP is powered only for limited responsiveness for power conservation purposes.

`POWER_STATE_ONLINE`
: NCP is powered for full responsiveness.

### PROP 8: PROP_HWADDR {#prop-hwaddr}

* Type: Single-Value, Read-Only
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NLI
* Value Type: EUI64_BE
* Post-Reset Value: Determined by Factory

The EUI-64 (TODO:CITE) format of the link-layer address of the device.
**MAY** be different across multiple NLIs.

### PROP 9: PROP_LOCK {#prop-lock}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NLI
* Required Capability: `CAP_LOCK`
* Value Type: BOOL
* Post-Reset Value: 0 (false)

Property transaction lock. Used for grouping transactional changes to
several properties for simultaneous commit, or to temporarily prevent
the automatic updating of property values. When this property is set,
all non-Spinel-related operations of the NCP are effectively frozen
until it is cleared. There is no support for transaction rollback.

This property is only supported if the `CAP_LOCK` capability is
present.

Unlike most other properties, setting this property to true when the
value of the property is already true **SHALL** fail with a last status
of `STATUS_ALREADY`.

### PROP 10: PROP_HOST_POWER_STATE {#prop-host-power-state}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **RECOMMENDED**
* Scope: NCP
* Required Capability: `CAP_POWER_SAVE`
* Value Type: UINT8
* Units: Enumeration
* Post-Reset Value: `HOST_POWER_STATE_ONLINE`

Describes the current power state of the *AP*. This property is used
by the AP to inform the NCP when it has changed power states. The NCP
can then use this state to determine which properties need
asynchronous updates. Enumeration is encoded as a single unsigned
byte.

The following table enumerates the standard codes and their significance.

Code| Name
:---|:-----------------------------
 0  | `HOST_POWER_STATE_OFFLINE`
 1  | `HOST_POWER_STATE_DEEP_SLEEP`
 3  | `HOST_POWER_STATE_LOW_POWER`
 4  | `HOST_POWER_STATE_ONLINE`

<!-- JW
  -- EDITOR: We should consider reversing the numbering here so that 0
     is `POWER_STATE_ONLINE`. We may also want to include some additional
     reserved values between the defined values for future expansion, so
     that we can preserve the ordered relationship. See the similar
     editorial comment at (#prop-power-state).
  -- -->

`HOST_POWER_STATE_OFFLINE`
: AP is physically powered off and cannot be awakened by the NCP.

`HOST_POWER_STATE_DEEP_SLEEP`
: AP is in a deep low power state and will require a long time to
  wake. In this state, the NCP **MUST NOT** send any commands, including
  any commands that contain network packets, prior to signaling the host
  explicitly to awaken and receiving a signal to update the state to
  `HOST_POWER_STATE_ONLINE`.

`HOST_POWER_STATE_LOW_POWER`
: AP is in a low power state and can be awakened quickly.

`HOST_POWER_STATE_ONLINE`
: AP is powered for full responsiveness.

After the AP sends `CMD_PROP_VALUE_SET` for this property with a value
other than `HOST_POWER_STATE_ONLINE`, it **SHOULD** wait for the NCP
to acknowledge the property update (with a `CMD_VALUE_IS` command)
before entering the specified power state.

Upon the NCP receiving any command when the state is not
`HOST_POWER_STATE_ONLINE`, it **MUST** silently update the state to
the `HOST_POWER_STATE_ONLINE` value.

When the state is not `HOST_POWER_STATE_ONLINE`, the NCP **SHOULD
NOT** send any commands except important notifications that warrant
awakening the AP host, and the NCP **MUST NOT** send any informative
messages on `PROP_DEBUG_STREAM`.

The AP **MUST NOT** send a value of `HOST_POWER_STATE` other than one
of the standard codes defined here. If the NCP receives a value other
than a standard code, then it **SHOULD** set the state to
`HOST_POWER_STATE_LOW_POWER`.

If the NCP has the `CAP_UNSOL_UPDATE_FILTER` capability, any
unsolicited property updates masked by `PROP_UNSOL_UPDATE_FILTER`
should be honored while the AP indicates it is in a low-power state.
After resuming to the `HOST_POWER_STATE_ONLINE` state, the value of
`PROP_UNSOL_UPDATE_FILTER` **MUST** be unchanged from the value
assigned prior to the AP indicating it was entering a low-power state.

The AP **MUST** use NLI 0 with commands using this property. The NCP
**SHOULD** explicitly fail to process commands setting this property
if NLI is not zero. The operational semantics of this property when
NLI is not zero are not specified.

### PROP 4104: PROP_UNSOL_UPDATE_FILTER {#prop-unsol-update-filter}

* Type: Multi-Value, Read-Write
* Has Item Length Prefix: No
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NLI
* Required Capability: `CAP_UNSOL_UPDATE_FILTER`
* Item Type: PUI ((#packed-unsigned-integer))
* Units: Enumeration (Property Keys)
* Post-Reset Value: Empty

Contains a list of property IDs which are *excluded* from generating
unsolicited value updates. This property **MUST** be empty after NCP
reset.

In other words, the AP may opt-out of unsolicited property updates for
a specific property by adding that property id to this list.

The AP **SHOULD NOT** add properties to this list which are not
present in `PROP_UNSOL_UPDATE_LIST`. If such properties are added, the
NCP **MUST** ignore the unsupported properties.

<!-- RQ
  -- The justification for the above behavior is to attempt to avoid possible
     future interop problems by explicitly making sure that unknown
     properties are ignored. Since unknown properties will obviously not be
     generating unsolicited updates, it seems fairly harmless. An
     implementation may print out a warning to the debug stream.

     Note that the error is still detectable: If you VALUE\_SET unsupported
     properties, the resulting VALUE\_IS would contain only the supported
     properties of that set(since the unsupported properties would be
     ignored). If an implementation cares that much about getting this
     right then it needs to make sure that it checks
     PROP\_UNSOL\_UPDATE\_LIST first.
  -- -->

### PROP 4105: PROP_UNSOL_UPDATE_LIST {#prop-unsol-update-list}

* Type: Multi-Value, Constant
* Has Item Length Prefix: No
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NLI
* Required Capability: `CAP_UNSOL_UPDATE_FILTER`
* Item Type: PUI ((#packed-unsigned-integer))
* Units: Enumeration (Property Keys)
* Post-Reset Value: Implementation Specific

Contains a list of properties which are capable of generating
unsolicited value updates. This list can be used when populating
`PROP_UNSOL_UPDATE_FILTER` to disable all unsolicited property
updates.

The NCP **MUST NOT** change the value of this property after sending a
`CMD_VALUE_IS` for `PROP_LAST_STATUS` with any of the
`STATUS_RESET_xxxxx` status codes.

Note: not all properties that support unsolicited updates need to be
listed here. Some properties, network media scan results for example,
are only generated due to direct action on the part of the AP, so
those properties **SHOULD NOT** not be included in this list.

## Stream Properties {#prop-stream}

### PROP 112: PROP_STREAM_DEBUG {#prop-stream-debug}

* Type: Byte-Stream, Output-Only
* Required: **OPTIONAL**
* Scope: NCP
* Value Type: UTF8 Fragment

This stream provides human-readable debugging output which may be
displayed in the AP logs. It is intended to be treated as a one-way
virtual serial stream. The AP **MUST NOT** assume that each emission
of this property is self-contained, instead the AP must use newline
characters for that purpose.

The location of newline characters **MUST NOT** not assumed by the
AP: it is the NCP's responsibility to insert newline characters
where needed, just like with any other text stream.

The emitted data is UTF8-encoded without any zero termination. Note
that the last UTF8 character **MAY** possibly be truncated. If the
last UTF8 character is truncated, the truncated bytes **MUST** be the
first bytes of the next emission from this stream.

### PROP 113: PROP_STREAM_RAW {#prop-stream-raw}

* Type: Packet-Stream, Input/Output
* Required: **OPTIONAL**
* Scope: NLI
* Related Capabilities:
	* `CAP_WRITABLE_RAW_STREAM`: Can be written to if present.
* Value Type: Structure

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-|-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   FRAME_LEN (Little endian)   | FRAME_DATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   FRAME_METADATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

This stream provides the capability of sending and receiving raw
packets to and from the network. The exact format of the frame
metadata and data is dependent on the MAC and PHY being used.

Implementations **MAY** support the ability to transmit arbitrary raw
packets. Support for this feature is indicated by the presence of the
`CAP_WRITABLE_RAW_STREAM` capability.

If the capability `CAP_WRITABLE_RAW_STREAM` is set, then packets
written to this stream with `CMD_PROP_VALUE_SET` will be sent out over
the radio. This allows the caller to use the network directly, with
the full network layer stack being implemented on the AP instead of
the NCP.

#### Frame Metadata Format {#frame-metadata-format}

Any data past the end of `FRAME_DATA` is considered metadata and
is **OPTIONAL**. Frame metadata **MAY** be empty or partially
specified. The operational semantics of using frame metadata is not
specified in the core protocol.

### PROP 114: PROP_STREAM_NET {#prop-stream-net}

* Type: Packet-Stream, Input/Output
* Required: **REQUIRED**
* Scope: NLI
* Value Type: Structure
* See Also: (#prop-stream-net-insecure)

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-|-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   FRAME_LEN (Little endian)   | FRAME_DATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   FRAME_METADATA ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

This stream provides the capability of sending and receiving data
packets to and from the currently attached network.

Any data past the end of `FRAME_LEN` is considered metadata, the
format of which is described in (#frame-metadata-format).

### PROP 115: PROP_STREAM_NET_INSECURE {#prop-stream-net-insecure}

* Type: Packet-Stream, Input/Output
* Required: **REQUIRED**
* Scope: NLI
* Value Type: Structure
* See Also: (#prop-stream-net)

The structure of the value of this property is identical to that of
PROP_STREAM_NET ((#prop-stream-net)).

This stream provides the capability of sending and receiving plaintext
non-authenticated data packets to and from the currently attached
network.

