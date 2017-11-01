# Status Codes {#status-codes}

The Spinel core protocol defines some standard status codes. Status
codes are used to indicate either the success of a transaction or the
specific reason that a specific transaction failed. Their names,
assigned numbers and a short description of their operational
semantics are shown in the following table:

Id          | Name
:-----------|:----------------------------------
  0         | `STATUS_OK`
  1         | `STATUS_FAILURE`
  2         | `STATUS_UNIMPLEMENTED`
  3         | `STATUS_INVALID_ARGUMENT`
  4         | `STATUS_INVALID_STATE`
  5         | `STATUS_INVALID_COMMAND`
  6         | `STATUS_INVALID_INTERFACE`
  7         | `STATUS_INTERNAL_ERROR`
  8         | `STATUS_SECURITY_ERROR`
  9         | `STATUS_PARSE_ERROR`
 10         | `STATUS_IN_PROGRESS`
 11         | `STATUS_NOMEM`
 12         | `STATUS_BUSY`
 13         | `STATUS_PROP_NOT_FOUND`
 14         | `STATUS_PACKET_DROPPED`
 15         | `STATUS_EMPTY`
 16         | `STATUS_CMD_TOO_BIG`
 17         | `STATUS_NO_ACK`
 18         | `STATUS_CCA_FAILURE`
 19         | `STATUS_ALREADY`
 20         | `STATUS_ITEM_NOT_FOUND`
 21         | `STATUS_INVALID_COMMAND_FOR_PROP`
*TBD*       | `STATUS_PROP_VALUE_TOO_BIG`
Table: Enumeration of status codes

`STATUS_OK`
: Indicates that the operation has completed successfully.

`STATUS_FAILURE`
: Indicates that the operation has failed for an unspecified reason. The
  use of this status code **SHOULD** be avoided. If a more specific status
  code exists that better explains the failure, then that status code **MUST**
  be used instead.

`STATUS_UNIMPLEMENTED`
: Indicates that the given operation has not been implemented.

`STATUS_INVALID_ARGUMENT`
: Indicates that an argument to the given operation is invalid. The value may
  be out of range or improperly formatted. This status code is also returned
  when setting an invalid value to a property.

`STATUS_INVALID_STATE`
: Indicates that the given operation is invalid for the current state of
  the device.

`STATUS_INVALID_COMMAND`
: The given command id is not recognized.

`STATUS_INVALID_INTERFACE`
: The given NLI is not supported.

`STATUS_INTERNAL_ERROR`
: An internal runtime error has occurred.
  TODO(RQ): What sort of error would cause this to be emitted?

`STATUS_SECURITY_ERROR`
: A security or authentication error has occurred.
  TODO(RQ): Elaborate on when this would be returned.

`STATUS_PARSE_ERROR`
: An error has occurred while parsing the command.

`STATUS_IN_PROGRESS`
: An error has occurred while parsing the command.
  TODO(RQ): Are we still using this? Elaborate on when this would be returned.

`STATUS_NOMEM`
: The operation has been prevented due to memory pressure.

`STATUS_BUSY`
: The device is currently performing a mutually exclusive operation. For
  example, you will get this status code if you attempt to change the value
  of `PROP_NET_STACK_UP` while performing an energy scan. This status differs
  from `STATUS_INVALID_STATE` in that it will resolve spontaneously.

`STATUS_PROP_NOT_FOUND`
: The given property key is not recognized.

`STATUS_PACKET_DROPPED`
: The packet was dropped. This status code is only emitted when sending
  data to a packet stream with a TID other than zero.

`STATUS_EMPTY`
: The result of the operation is empty.
  TODO(RQ): Elaborate on when this would be returned.

`STATUS_CMD_TOO_BIG`
: The command was too large to fit in the internal buffer. This is generally
  indicates some sort of misconfiguration.

`STATUS_NO_ACK`
: The packet was not acknowledged.
  This status code is only emitted when sending data to a packet stream with
  a TID other than zero.

`STATUS_CCA_FAILURE`
: The packet was not sent due to a CCA failure.
  This status code is only emitted when sending data to a packet stream with
  a TID other than zero.

`STATUS_ALREADY`
: The operation is already in progress, or the property already has the value.
  This is emitted by `PROP_LOCK` ((#prop-lock)) and `PROP_MAC_SCAN_STATE`
  ((#prop-mac-scan-state)).

`STATUS_ITEM_NOT_FOUND`
: The given item could not be found in the property. This status code **MAY**
  be used to indicate a failure to remove a specific value from a
  multiple-value property.
  TODO(RQ): Are we still using this?

`STATUS_INVALID_COMMAND_FOR_PROP`
: The given command cannot be performed on this property. For example, if
  a property was read-only, this status would be emitted if you attempted
  to perform a `CMD_PROP_VALUE_SET` on it.

`STATUS_PROP_VALUE_TOO_BIG`
: The value for this property is too large.

## Reset Codes {#reset-codes}

All status codes which fall into the inclusive range of 112-127
are considered *reset codes*. These codes are emitted asynchronously
after an NCP reset and provide a way to differentiate different
causes of resets. If the first command the AP sends to the NCP
after a reset is to fetch `PROP_LAST_STATUS`, then the reset code
**MUST** be returned.

Id          | Name
:-----------|:----------------------------------
112         | `STATUS_RESET_POWER_ON`
113         | `STATUS_RESET_EXTERNAL`
114         | `STATUS_RESET_SOFTWARE`
115         | `STATUS_RESET_FAULT`
116         | `STATUS_RESET_CRASH`
117         | `STATUS_RESET_ASSERT`
118         | `STATUS_RESET_OTHER`
119         | `STATUS_RESET_UNKNOWN`
120         | `STATUS_RESET_WATCHDOG`
Table: Enumeration of reset codes

Of these defined reset codes, only `STATUS_RESET_POWER_ON`,
`STATUS_RESET_EXTERNAL`, and `STATUS_RESET_SOFTWARE` are emitted
during normal operation. All other reset codes generally indicate
some sort of software bug or hardware failure.

Unexpected/unrequested resets are always an indication of a problem,
no matter what the code value is.

`STATUS_RESET_POWER_ON`
: Cold power-on start.

`STATUS_RESET_EXTERNAL`
: External device reset. This is generally caused by RESET pin on the NCP
  being asserted.

`STATUS_RESET_SOFTWARE`
: Software-requested orderly reset. This is generally caused by the AP sending
  the NCP `CMD_RESET`.

`STATUS_RESET_FAULT`
: Internal software abortive reset.
  TODO: Elaborate on what this means. Consider repurposing this code.

`STATUS_RESET_CRASH`
: Unrecoverable software execution failure.

`STATUS_RESET_ASSERT`
: Software invariant property not respected.

`STATUS_RESET_OTHER`
: Unspecified cause.

`STATUS_RESET_UNKNOWN`
: Failure while recovering cause of reset.

`STATUS_RESET_WATCHDOG`
: Watchdog timer expired, forcing a reset.

## Status Code Ranges

IANA maintains a registry of Spinel `STATUS_CODE` numbers, with
varying registration policies assigned for different ranges according
to the following table:

Status Code Range     | Reservation Policy
:---------------------|:-----------------
0 - 127               | Standards Action
128 - 15,359          | Unassigned
15,360 - 16,383       | Private Use
16,384 - 1,999,999    | Unassigned
2,000,000 - 2,097,151 | Experimental Use

## How Status is Indicated

Status codes are sent from the NCP to the AP via `PROP_LAST_STATUS`
using the `CMD_VALUE_IS` command to indicate the return status of a
previous command. As with any response, the TID field of the FLAG byte
is used to correlate the response with the request.

Note that most successfully executed commands do not indicate a last
status of `STATUS_OK`. The usual way the NCP indicates a successful
command is to mirror the property change back to the AP. For example,
if you do a `CMD_VALUE_SET` on `PROP_PHY_ENABLED`, the NCP would
indicate success by responding with a `CMD_VALUE_IS` for
`PROP_PHY_ENABLED`. If the command failed, `PROP_LAST_STATUS` would be
emitted instead.

See (#prop-last-status) for more information on `PROP_LAST_STATUS`.

