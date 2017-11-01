## NET Properties {#prop-net}

### PROP 65: PROP_NET_IF_UP  {#prop-net-if-up}

* Type: Single-Value, Read/Write
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED**
* Value Type: BOOL
* Post-Reset Value: 0 (false)

Network interface up/down status. Non-zero (set to 1) indicates up, zero indicates down.

Setting this to false implies also setting `PROP_NET_STACK_UP` to false.

#### Relationship with PROP_NET_STACK_UP

EDITOR: the examples show that the order of operations to bring up a network interface is first set PROP_NET_IF_UP=TRUE, then PROP_NET_STACK_UP=TRUE. What does it mean when PROP_NET_IF_UP=TRUE and PROP_NET_STACK_UP=FALSE? Does the NLI matter in operations with the PROP_NET_STACK_UP property?

RQ: PROP_NET_IF_UP=TRUE, PROP_NET_STACK_UP=FALSE is used for in-band commissioning.
It bring up link-local IPv6 capabilities on PROP_STREAM_NET_INSECURE.

### PROP 66: PROP_NET_STACK_UP  {#prop-net-stack-up}

* Type: Single-Value, Read/Write
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED**
* Value Type: BOOL
* Post-Reset Value: 0 (false)

Network protocol stack operational status. Non-zero (set to 1) indicates up, zero indicates down.

Setting this to true implies also setting `PROP_NET_IF_UP` to true.


### PROP 67: PROP_NET_ROLE {#prop-net-role}

* Type: Single-Value, Read-Only
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED**
* Value Type: UINT8
* Unit: Enumeration
* Post-Reset Value: 0 (`NET_ROLE_DETACHED`)

Indicates what role the current device is playing on the network. This property is
read-only, with the exception that the AP can indicate that it wants the NCP to
detach and then attempt to reattach by setting this property to `NET_ROLE_DETACHED`
when it was previously set to another value. Attempting to set this property
to any other value will result in `STATUS_INVALID_ARGUMENT` being emitted.

Code | Name
-----|---------------------
0    | `NET_ROLE_DETACHED`
1    | `NET_ROLE_CHILD`
2    | `NET_ROLE_ROUTER`
3    | `NET_ROLE_LEADER`
4    | `NET_ROLE_PEER`

NET_ROLE_DETACHED
: The NCP is not currently participating on a network, or is in the process of attaching
  to a network.

NET_ROLE_CHILD
: The NCP is currently attached to the mesh network as a child to a router node. Communication
  with nodes more than one hop away is possible. The NCP does not route any mesh traffic on the
  behalf of other nodes.

NET_ROLE_ROUTER
: The NCP is currently a router node on the network, and will transparently route mesh
  traffic on behalf of other nodes.

NET_ROLE_LEADER
: This role is identical ot `NET_ROLE_ROUTER` except that there is only node designated as the
  leader per partition. This role is Thread-specific.

NET_ROLE_PEER
: In this role, the NCP may communicate with nodes which are no more than one hop away. This
  role is used while obtaining the network provision during in-band commissioning.

### PROP 68: PROP_NET_NETWORK_NAME  {#prop-net-network-name}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED** for `CAP_NET_STANDARD`
* Value Type: Zero-terminated UTF8 String
* Post-Reset Value: Empty

Contains a *zero-terminated* UTF8 string which describes the name of
the current network. This is analogous to the SSID in 802.11. The maximum
length of the string is technology specific. Setting this property to a value
that is too large **MUST** cause `STATUS_PROP_VALUE_TOO_BIG` to be emitted.

When this value is being retrieved, all trailing zero bytes in the network name
**MUST** be stripped by the NCP. When this value is being set, all trailing zero bytes
**SHOULD** be stripped by the AP.

See (#security-network-name) for security considerations regarding UTF8
normalization.

This property **SHOULD** only be changed by the AP when `PROP_NET_STACK_UP` is
false. The behavior of changing this property when `PROP_NET_STACK_UP` is true is
unspecified.

### PROP 69: PROP_NET_XPANID   {#prop-net-xpanid}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED** for `CAP_NET_STANDARD`
* Value Type: Eight (8) bytes of opaque data
* Post-Reset Value: All zeros

Contains the extended personal-area network identifier for the current
network. It allows for different networks with identical names to not
conflict with each other. It is defined to be eight bytes long.

This property is not used by all network technologies. In cases where it is not used,
both `CMD_PROP_VALUE_GET` and `CMD_PROP_VALUE_SET` calls should return with an
`CMD_PROP_VALUE_IS` for this property with a value of all zeros.

This property **SHOULD** only be changed by the AP when `PROP_NET_STACK_UP` is
false. The behavior of changing this property when `PROP_NET_STACK_UP` is true is
unspecified.

### PROP 70: PROP_NET_MASTER_KEY   {#prop-net-master-key}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED** for `CAP_NET_STANDARD`
* Value Type: Cryptographic key (usually 16 bytes)
* Post-Reset Value: Unspecified value that is the length required by the underlying
network technology.

Master key material. The exact length of the value for this property is defined by
the technology, but is generally 16-bytes (128-bit).

**This is not a password**, it is the raw cryptographic key used to authenticate
this node to other nodes on the network and (possibly) to secure network traffic.
It must be generated from either a cryptographically-secure random number generator
or the output of a suitable password-based key derivation funciton (the specification of
which would be network technology dependent).

If the AP attempts to set a master key of a length that is not a supported by the
underlying technology, the NCP **MUST** fail with `STATUS_INVALID_ARGUMENT`.

Network technologies which support unsecured/unauthenticated operation
**MAY** allow this property to be cleared to be empty.

This property **SHOULD** only be changed by the AP when `PROP_NET_STACK_UP` is
false. The behavior of changing this property when `PROP_NET_STACK_UP` is true is
unspecified.

### PROP 71: PROP_NET_KEY_SEQUENCE_COUNTER   {#prop-net-key-sequence-counter}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Scope: NLI
* Required: **REQUIRED** for `CAP_NET_STANDARD`
* Value Type: UINT32_LE
* Post-Reset Value: 0

This property is not used by all network technologies. In cases where it is not used,
both `CMD_PROP_VALUE_GET` and `CMD_PROP_VALUE_SET` calls should return with an
`CMD_PROP_VALUE_IS` for this property with a value of zero (0).

This property **SHOULD** only be changed by the AP when `PROP_NET_STACK_UP` is
false. The behavior of changing this property when `PROP_NET_STACK_UP` is true is
unspecified.

### PROP 72: PROP_NET_PARTITION_ID   {#prop-net-partition-id}

* Type: Single-Value, Read-Only
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED** for `CAP_NET_STANDARD`
* Value Type: UINT32_LE
* Post-Reset Value: 0

The partition ID of the partition that this node is a member of.

This property is not used by all network technologies. In cases where it is not used,
 `CMD_PROP_VALUE_GET` calls should return with an `CMD_PROP_VALUE_IS` for this
 property with a value of zero (0).

### PROP 73: PROP_NET_REQUIRE_JOIN_EXISTING   {#prop-net-require-join-existing}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Scope: NLI
* Required: **REQUIRED** for `CAP_NET_STANDARD`
* Value Type: BOOL
* Post-Reset Value: 0 (false)

This property is used to force the NLI to fail hard (as opposed to creating a new partition)
if there are no nodes in radio range on the same network. This is useful when
joining a network for the first time to ensure that there was no errors or mistakes in
the provision.

To use this property, the AP follows these steps:

1. Provisions the NLI for the network by setting all of the required properties (technology-specific).
2. Sets `PROP_NET_REQUIRE_JOIN_EXISTING` to true.
3. Sets `PROP_NET_STACK_UP` to true.

Upon successful association with another nearby node, the NLI sends the AP an
asynchronous update setting `PROP_NET_REQUIRE_JOIN_EXISTING` to false.

If there were no nearby nodes, the NLI sends the AP several asynchronous updates
in the following order with a TID of zero:

* `PROP_NET_STACK_UP` IS false
* `PROP_NET_IF_UP` IS false
* `PROP_NET_LAST_STATUS` IS ...
    * `STATUS_JOIN_FAIL_SECURITY`
    * `STATUS_JOIN_FAIL_LONELY`
    * `STATUS_JOIN_FAIL_OTHER`
