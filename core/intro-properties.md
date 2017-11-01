# Property Overview #

Spinel is largely a property-based protocol. The NCP exposes various
properties that the host can manipulate to control its behavior. The
theory of operation is similar to representational state transfer
(REST)(TODO:CITE), with a property defined for every attribute of the operational
state of a network interface for which an IPv6 node may need the
typical operators, i.e. Create, Read, Update, Delete and Alert.

The inspiration of the approach used in Spinel was the concept of
hardware registers for peripherals. The goal was to avoid, as much as
possible, the use of large complicated structures and/or method
argument lists. The reason for avoiding these is because they have a
tendency to change in incompatible ways, especially early in
development. For example, adding or removing an argument from a
command will render the entire protocol incompatible. By using
properties and conforming to a well-designed information model,
extending the protocol is usually as simple as an additional property.

Almost all features and capabilities are implemented using properties.
Most new features that are initially proposed as explicit commands can
be satisfactorily adapted to be property-based instead. In cases where
doing so is particularly awkward, explicit commands are used instead
(See (#commands)).

Properties are identified by a unique unsigned integer value between 0
and 2,097,151 (See (#packed-unsigned-integer)) called a "property
key". Property keys are recorded in the Spinel property key registry
(EDITOR: proposal is to create an IANA registry) with some ranges
already reserved for future expansion of the core and other ranges
available for profile specialization.

What follows is a overview of how properties work.

## Property Operators {#property-operators}

Conceptually, the following operations are used by the AP to query or change a property's value:

*   `GET`
*   `SET`
*   `INSERT`
*   `REMOVE`

In addition, the following operations can be used by the NCP to indicate changes to the value of a property:

*   `IS`
*   `INSERTED`
*   `REMOVED`

Not all properties support all of the above operations. Which operations are supported is generally determined by two things: what type of property it is and that property's readability/writability.

## Property Types ##

Conceptually, there are three different types of properties:

*   Single-value properties
*   Multiple-value (Array) properties
*   Stream properties

These are described in further detail in the following sections.

### Single-Value Properties ###

Single-value properties are properties that have a simple representation of a single value. Examples would be:

*   Current radio channel (Represented as a unsigned 8-bit integer)
*   Network name (Represented as a UTF-8 encoded string)
*   802\.15.4 PAN ID (Represented as a unsigned 16-bit integer)

The valid operators on these sorts of properties are `GET` and `SET`.

### Multiple-Value Properties ###

Multiple-Value Properties have more than one value associated with them. Examples would be:

*   List of channels supported by the radio hardware.
*   List of IPv6 addresses assigned to the interface.
*   List of capabilities supported by the NCP.

The valid operators on these sorts of properties are `GET`, `SET`, `INSERT`, and `REMOVE`.

When the value is fetched using `GET`, an individual property will return the entire list of items as either of the following:

1. The concatenation of all of the individual values. This is used in cases where the length of an individual item is fixed.
2. The concatenation of all of the individual values, each prefixed by a 16-bit big-endian integer describing the length of the individual item. This would be used in cases where the length of an individual item is not constant.

The order of the returned items, unless explicitly defined for that specific property, is undefined.

`SET` provides a way to completely replace all previous values, with the item format matching what would be used for `GET`. Calling `SET` with an empty value effectively instructs the NCP to empty that property.

`INSERT` and `REMOVE` provide mechanisms for the insertion or removal of individual items *by value*. The payload for these operators is a single item.

### Stream Properties ###

Stream properties represent dynamic streams of data rather than a specific value. Examples would be:

*   Network packet stream ((#prop-stream-net))
*   Raw packet stream ((#prop-stream-raw))
*   Debug message stream ((#prop-stream-debug))

All such properties emit changes asynchronously using the `IS` operator, sent from the NCP to the AP. For example, as IPv6 traffic is received by the NCP, the IPv6 packets are sent to the AP by way of asynchronous `IS` operations for the network packet stream property.

Some of these properties also support the AP sending data back to the NCP using the `SET` operation. For example, this is how the AP sends IPv6 traffic to the NCP.

The behavior and meaning of the `GET`, `INSERT`, `REMOVE`, `INSERTED`, and `REMOVED` operations for stream properties is undefined and SHOULD NOT be used.
