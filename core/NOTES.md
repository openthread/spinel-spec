# Spinel Core Notes

This file contains various notes associated with the Spinel
Core protocol.

## Format for Properties

There are a lot of properties in Spinel. To keep things readable
and consistent, all properties should be documented in the following
manner:

    ## PROP XXXX: PROP_YYYY {#prop-yyyy}
     * Attribute One: Blah
     * Attribute Two: Bleh

    Detailed explanation of the protocol.

    Value Format Information, including enumeration details.

Note that no constants have the prefix `SPINEL_`. Such a prefix would be
redundant.

### Attributes

The attributes section provides a quick, well-defined way to get a feel for the
behavior of the property. Here is the list of attributes, in order:

#### "Type"

This attribute contains two fields, separated by a comma. The first value
can be one of the following:

* **Single-Value**: The value represents a single variable or structure.
* **Multiple-Value**: The value represents multiple items.
* **Packet-Stream**: The value represents individual packets. (Similar to a datagram socket)
* **Byte-Stream**: The value represents bytes in a stream. (Similar to a stream socket)

The second field can be one of the following:

* **Read-Only**: (Non-stream Only)
* **Read/Write**: (Non-stream Only)
* **Constant**: (Non-stream Only)
* **Output-Only**: (Stream-only)
* **Input/Output**: (Stream-only)

This attribute is REQUIRED for all properties.

#### "Has Item Length Prefix"

This attribute determines if individual items in the value are prefixed with the length
of the item. It's value may be one of the following:

* **Yes**
* **No**

This attribute is **REQUIRED** for all multiple-value properties.

#### "Asynchronous Updates"

This attribute determines whether the AP should expect to receive asynchronous
updates for this property and can be either `Yes` or `No`.

This attribute is omitted for stream properties.

#### "Required"

This attribute determines to what level the implementation of the property by
the NCP is required. It may be one of the following:

* **OPTIONAL**
* **RECOMMENDED**
* **REQUIRED**

In some cases the implementation of specific commands may or may not
be required. In those cases, a sublist gives individual values like so:

     * Required:
         * `CMD_PROP_VALUE_GET`: **REQUIRED**
         * `CMD_PROP_VALUE_SET`: **OPTIONAL**

This attribute is **REQUIRED** for all properties.

#### "Scope"

This attribute defines the scope of the value of the property. It may have
one of the following values:

* **NLI**: The value of this property is scoped to the NLI. If this attribute
is not specified, this is assumed.
* **NCP**: The value of this property is a specific value that is defined to
be the same across all NLIs.

#### "Related Capabilities"

This attribute lists any related (but not required) capabilities, along with a short
explanation. For example:

    * Related Capabilities:
        * `CAP_POWER_STATE`: **REQUIRED** when writable

If there are no related capabilities, this attribute is skipped.

#### "Required Capability"

This attribute identifies a capability which is required to be present for this
property to be useable. For example:

    * Required Capability: `CAP_LOCK`

#### "Value Type" or "Item Type"

For properties that don't contain structs with several fields, this attribute
allows you to quickly describe the format of the value (or item, as appropriate).

The possible values are:

* `PUI ((#packed-unsigned-integer))`: Packed Unsigned Integer
* `BOOL`: 8-bit boolean (0 and 1 are only valid values)
* `UINT8`: Unsigned 8-bit integer
* `INT8`: Unsigned 8-bit integer
* `UINT16_LE`: Unsigned 16-bit little-endian integer
* `INT16_LE`: Signed 16-bit little-endian integer
* `UINT32_LE`: Unsigned 32-bit little-endian integer
* `INT32_LE`: Signed 32-bit little-endian integer
* `EUI64_BE`: EUI64 in big endian format
* `IPV6_BE`: IPv6 address in big-endian format
* `STRING`: Zero-terminated UTF-8 string (May be ASCII if specified)
* `DATA`: A blob of data with an unspecified type

If omitted, it is assumed that the value/item type will be explained in detail
after the attributes section.

Use "Value Type" for properties of the "Single-Value" type. Use "Item Type"
for properties of the "Multiple-Value" type. Do not use this attribute for
stream properties.

#### "Units"

For properties which the value or item type is an integer, a units attribute
SHOULD be specified to identify the units. For example:

    * Units: dB
    * Units: Enumeration
    * Units: Channel

If the value is unitless, omit this attribute.

#### "Post-Reset Value"

This attribute defines what the effective value of this property **MUST**
be after a reset has occured.

If the property doesn't have a specificly defined post-reset value, this
attribute MUST describe the value as "Unspecified". If the post-reset
value is to be defined by the implementation, then this attribute MUST
describve the value as "Implementation Specific"

For example:

    * Post-Reset Value: Unspecified
    * Post-Reset Value: 0
    * Post-Reset Value: HOST_POWER_STATE_ONLINE

This attribute is REQUIRED for all non-stream properties properties that
are not type marked as "Constant".


#### "See Also"

Provides links to other related sections.

    * See Also: (#prop-mac-data-poll-period)

### Template


#### Template: Single-Value

~~~
* Type: Single-Value, Read/Write
* Asynchronous Updates: No
* Required: **REQUIRED**
* Scope: NCP
* Related Capabilities:
  * `CAP_BLAH_BLAH`
* Required Capability: `CAP_BLEH_BLEH`
* Value Type: PUI ((#packed-unsigned-integer))
* Units: Enumeration
* Post-Reset Value: 0
* See Also: (#prop-foo-bar)
~~~

#### Template: Multiple-Value

~~~
* Type: Multi-Value, Read-Write
* Has Item Length Prefix: No
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NLI
* Related Capabilities:
  * `CAP_BLAH_BLAH`
* Required Capability: `CAP_BLEH_BLEH`
* Item Type: PUI ((#packed-unsigned-integer))
* Units: Enumeration
* Post-Reset Value: Empty
* See Also: (#prop-foo-bar)
~~~

#### Template: Stream

~~~
* Type: Packet-Stream, Input/Output
* Required: **REQUIRED**
* Scope: NLI
* Related Capabilities:
  * `CAP_BLAH_BLAH`
* Required Capability: `CAP_BLEH_BLEH`
* Value Type: Structure
* See Also: (#prop-foo-bar)
~~~
