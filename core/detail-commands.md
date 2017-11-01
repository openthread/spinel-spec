# Commands {#commands}

This section defines the standard commands used in all Spinel protocol interactions. Each command is defined for one of the following two contexts:

* AP -> NCP: commands sent by the Application Processor (AP) to the Network Co-Processor (NCP).
* NCP -> AP: commands sent by the Network Co-Processor (NCP) to the Application Processor (AP).

A conforming implementation MAY transmit any command defined for its operating context, and MUST process any command received for its operating context, according to the operational semantics defined in this section.

IANA maintains a registry of Spinel `CMD` command numbers, with varying registration policies assigned for different ranges according to the following table:

CMD Number            | Reservation policy
----------------------|-------------------
0 - 63                | Standards Action
64 - 15,359           | Unassigned
15,360 - 16,383       | Private Use
16,384 - 1,999,999    | Unassigned
2,000,000 - 2,097,151 | Experimental Use

## CMD 0: (AP -> NCP) CMD_NOOP {#cmd-noop}

~~~
 0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |    CMD_NOOP   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
^     HEADER    ^    COMMAND    ^
~~~
Figure: Structure of CMD_NOOP

No-Operation. Commands the NCP to reply with a `STATUS_OK` code. This is primarily used for liveliness checks.

The command payload for this command SHOULD be empty. The receiver MUST ignore any non-empty command payload.

There is no error condition for this command.

## CMD 1: (AP -> NCP) CMD_RESET {#cmd-reset}

     0                   1
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |1 0|NLI|  TID  |   CMD_RESET   |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
Figure: Structure of CMD_RESET

Reset NCP. Commands the NCP to perform a software reset. Due to the nature of this command, the TID is ignored. The AP should instead wait for a `CMD_PROP_VALUE_IS` command from the NCP indicating `PROP_LAST_STATUS` has been set to `STATUS_RESET_SOFTWARE` (see (#status-codes)).

The command payload SHOULD be empty, and it SHOULD NOT be processed.

If an error occurs, the value of the emitted `PROP_LAST_STATUS` will be set accordingly to the status code for the error.

## CMD 2: (AP -> NCP) CMD_PROP_VALUE_GET {#cmd-prop-value-get}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_GET

Get property value. Commands the NCP to emit a `CMD_PROP_VALUE_IS` command for the given property identifier.

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer).

If an error occurs, the value of the emitted `PROP_LAST_STATUS` will be set accordingly to the status code for the error.

## CMD 3: (AP -> NCP) CMD_PROP_VALUE_SET {#cmd-prop-value-set}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  NEW PROPERTY VALUE ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_SET

Set property value. Commands the NCP to set the given property to the specific given value, replacing any previous value, and to emit a `CMD_PROP_VALUE_IS` command for the `PROP_LAST_STATUS` command indicating `STATUS_OK` if successful.

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer), followed by the property value. The exact format of the property value is defined by the property.

If an error occurs, the value of the emitted `PROP_LAST_STATUS` will be set accordingly to the status code for the error.

## CMD 4: (AP -> NCP) CMD_PROP_VALUE_INSERT {#cmd-prop-value-insert}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  ITEM TO INSERT ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_INSERT

Insert value into list property. Commands the NCP to insert the given value into a list-oriented property, without removing other items in the list. The resulting order of items in the list is defined by the individual property being operated on.

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer), followed by the value to be inserted. The exact format of the value is defined by the property.

If the type signature of the property specified by `PROP_ID` consists of a single structure enclosed by an array (`A(t(...))`), then the contents of `VALUE` MUST contain the contents of the structure (`...`) rather than the serialization of the whole item (`t(...)`).  Specifically, the length of the structure MUST NOT be prepended to `VALUE`. This helps to eliminate redundant data.

If an error occurs, the value of the emitted `PROP_LAST_STATUS` will be set accordingly to the status code for the error.

## CMD 5: (AP -> NCP) CMD_PROP_VALUE_REMOVE {#cmd-prop-value-remove}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  ITEM TO REMOVE ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_REMOVE

Remove value from list property. Commands the NCP to remove the given value from a list-oriented property, without affecting other items in the list. The resulting order of items in the list is defined by the individual property being operated on.

Note that this command operates *by value*, not by index!

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer), followed by the value to be removed. The exact format of the value is defined by the property.

If the type signature of the property specified by `PROP_ID` consists of a single structure enclosed by an array (`A(t(...))`), then the contents of `VALUE` MUST contain the contents of the structure (`...`) rather than the serialization of the whole item (`t(...)`).  Specifically, the length of the structure MUST NOT be prepended to `VALUE`. This helps to eliminate redundant data.

If an error occurs, the value of the emitted `PROP_LAST_STATUS` will be set accordingly to the status code for the error.

## CMD 6: (NCP -> AP) CMD_PROP_VALUE_IS {#cmd-prop-value-is}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  CURRENT PROPERTY VALUE ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_SET

Property value notification. This command can be sent by the NCP in response to a previous command from the AP, or it can be sent by the NCP in an unsolicited fashion to notify the AP of various state changes asynchronously.

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer), followed by the current value of the given property.

## CMD 7: (NCP -> AP) CMD_PROP_VALUE_INSERTED {#cmd-prop-value-inserted}

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| ITEM TO BE INSERTED ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_INSERTED

Property value insertion notification. This command can be sent by the NCP in response to the `CMD_PROP_VALUE_INSERT` command, or it can be sent by the NCP in an unsolicited fashion to notify the AP of various state changes asynchronously.

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer), followed by the value that was inserted into the given property.

If the type signature of the property specified by `PROP_ID` consists of a single structure enclosed by an array (`A(t(...))`), then the contents of `VALUE` MUST contain the contents of the structure (`...`) rather than the serialization of the whole item (`t(...)`).  Specifically, the length of the structure MUST NOT be prepended to `VALUE`. This helps to eliminate redundant data.

The resulting order of items in the list is defined by the given property.

## CMD 8: (NCP -> AP) CMD_PROP_VALUE_REMOVED {#cmd-prop-value-removed}

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| ITEM TO BE REMOVED ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PROP_VALUE_REMOVED

Property value removal notification command. This command can be sent by the NCP in response to the `CMD_PROP_VALUE_REMOVE` command, or it can be sent by the NCP in an unsolicited fashion to notify the AP of various state changes asynchronously.

Note that this command operates *by value*, not by index!

The payload for this command is the property identifier encoded in the packed unsigned integer format described in (#packed-unsigned-integer), followed by the value that was removed from the given property.

If the type signature of the property specified by `PROP_ID` consists of a single structure enclosed by an array (`A(t(...))`), then the contents of `VALUE` MUST contain the contents of the structure (`...`) rather than the serialization of the whole item (`t(...)`).  Specifically, the length of the structure MUST NOT be prepended to `VALUE`. This helps to eliminate redundant data.

The resulting order of items in the list is defined by the given property.

## CMD 18: (AP -> NCP) CMD_PEEK {#cmd-peek}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |   ADDRESS (LITTLE ENDIAN) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   ... ADDRESS (LITTLE ENDIAN)  |     COUNT (LITTLE ENDIAN)     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PEEK


This command allows the NCP to fetch values from the RAM of the NCP for debugging purposes. Upon success, `CMD_PEEK_RET` is sent from the NCP to the AP. Upon failure, `PROP_LAST_STATUS` is emitted with the appropriate error indication.

Due to the low-level nature of this command, certain error conditions may induce the NCP to reset.

The NCP MAY prevent certain regions of memory from being accessed.

The implementation of this command has security implications. See (#security-considerations) for more information.

This command requires the capability `CAP_PEEK_POKE` to be present.

## CMD 19: (NCP -> AP) CMD_PEEK_RET {#cmd-peek-ret}


~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |   ADDRESS (LITTLE ENDIAN) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   ... ADDRESS (LITTLE ENDIAN)  |     COUNT (LITTLE ENDIAN)     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  VALUE OF MEMORY ADDRESS ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_PEEK_RET


This command contains the contents of memory that was requested by a previous call to `CMD_PEEK`.

This command requires the capability `CAP_PEEK_POKE` to be present.

## CMD 20: (AP -> NCP) CMD_POKE {#cmd-poke}

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |   ADDRESS (LITTLE ENDIAN) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   ... ADDRESS (LITTLE ENDIAN)  |     COUNT (LITTLE ENDIAN)     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  VALUE TO WRITE TO MEMORY ADDRESS ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of CMD_POKE

This command writes the bytes to the specified memory address for debugging purposes.

Due to the low-level nature of this command, certain error conditions may induce the NCP to reset.

The implementation of this command has security implications. See (#security-considerations) for more information.

This command requires the capability `CAP_PEEK_POKE` to be present.

## CMD 21: (AP -> NCP) CMD_PROP_VALUE_MULTI_GET {#cmd-prop-value-multi-get}

*   Required Capability: `CAP_CMD_MULTI`

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP_KEY 1 (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP_KEY 2 (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP_KEY 3 (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
:            etc ...            :
~~~
Figure: Structure of CMD_PROP_VALUE_MULTI_GET

Fetch the value of multiple properties in one command. Arguments are a list of property IDs. If all properties are fetched successfully, a `CMD_PROP_VALUES_ARE` command is sent back to the AP containing the property identifier and value of each fetched property. The order of the results in `CMD_PROP_VALUES_ARE` match the order of properties given in `CMD_PROP_VALUE_GET`.

Errors fetching individual properties are reflected as indicating a change to `PROP_LAST_STATUS` for that property's place.

Not all properties can be fetched using this method. As a general rule of thumb, any property that blocks when getting will fail for that individual property with `STATUS_INVALID_COMMAND_FOR_PROP`.

## CMD 22: (AP -> NCP) CMD_PROP_VALUE_MULTI_SET {#cmd-prop-value-multi-set}

*   Required Capability: `CAP_CMD_MULTI`

This command sets the value of several properties at once in the given order.
The setting of properties stops at the first error, ignoring any later properties.

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP/VALUE PAIR 1 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP/VALUE PAIR 2 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP/VALUE PAIR 3 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
:            etc ...            :
~~~
Figure: Structure of CMD_PROP_VALUE_MULTI_SET

With each property/value pair above being:

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    LENGTH (LITTLE ENDIAN)     | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  NEW PROPERTY VALUE ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

The LENGTH field is the sum of the length of the property key and the new
property value.

The result of this command is generally `CMD_PROP_VALUES_ARE` unless
(for example) a parsing error has occured (in which case
`CMD_PROP_VALUE_IS` for `PROP_LAST_STATUS` would be the result). The
order of the results in `CMD_PROP_VALUES_ARE` match the order of
properties given in `CMD_PROP_VALUE_MULTI_SET`.

Since the processing of properties to set stops at the first error,
the resulting `CMD_PROP_VALUES_ARE` can contain fewer items than the
requested number of properties to set.

Not all properties can be set using this method. As a general rule of
thumb, any property that blocks when setting will fail for that
individual property with `STATUS_INVALID_COMMAND_FOR_PROP`.

## CMD 23: (NCP -> AP) CMD_PROP_VALUES_ARE {#cmd-prop-values-are}

*   Required Capability: `CAP_CMD_MULTI`

~~~
  0                   1
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|1 0|NLI|  TID  |      CMD      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP/VALUE PAIR 1 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP/VALUE PAIR 2 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PROP/VALUE PAIR 3 ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
:            etc ...            :
~~~
Figure: Structure of CMD_PROP_VALUE_MULTI_ARE

With each property/value pair above being:

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    LENGTH (LITTLE ENDIAN)     | PROP_KEY (PUI, 1-3 bytes) ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  CURRENT PROPERTY VALUE ...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~

This command is emitted by the NCP as the response to both the
`CMD_PROP_VALUE_MULTI_GET` and `CMD_PROP_VALUE_MULTI_SET` commands. It
is roughly analogous to `CMD_PROP_VALUE_IS`, except that it contains
more than one property.

This command must **ONLY** be emitted by the NCP in response to either
`CMD_PROP_VALUE_MULTI_GET` or `CMD_PROP_VALUE_MULTI_SET`. This command
**MUST NOT** be emitted in response to any other command, or emitted
asynchronously.

The arguments are a list of structures containing the emitted property
and the associated value. These are presented in the same order as
given in the associated initiating command. In cases where getting or
setting a specific property resulted in an error, the associated slot
in this command will describe `PROP_LAST_STATUS`.

## CMD 24: (AP -> NCP) CMD_RESET_NLI {#cmd-reset-nli}

     0                   1
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |1 0|NLI|  TID  |     CMD 24    |
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
Figure: Structure of `CMD_RESET_NLI`

Reset a specific NLI. Commands the NLI to return all properties and state to their defined
"at reset" values. The intent of this command is to be analogous to `CMD_RESET`,
except that it applies only to a single NLI.

If the value of `PROP_INTERFACE_COUNT` is equal to one (1), then the NCP **MAY**
treat this command as a synonym for `CMD_RESET` ((#cmd-reset)). Thus, the AP
**MUST NOT** treat an NCP software reset as an unexpected reset unless
`PROP_INTERFACE_COUNT` was previously detremined to be greater than one (1).

Otherwise, upon success this command emits `STATUS_OK` from `PROP_LAST_STATUS`.
The resetting of all of the NLI's state **MUST NOT** cause any asynchronous property
updates to be emitted, with the exception of `PROP_STREAM_DEBUG`
((#prop-stream-debug)).

