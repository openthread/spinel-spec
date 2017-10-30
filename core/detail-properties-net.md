## NET Properties {#prop-net}

### PROP 65: PROP_NET_IF_UP  {#prop-net-if-up}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Post-Reset Value: 0 (false)

Bytes:  |    1
-------:|-------------
Format: | BOOL
Fields: | `NET_IF_UP`

Network interface up/down status. Non-zero (set to 1) indicates up, zero indicates down.

Setting this to false implies also setting `PROP_NET_STACK_UP` to false.

#### Relationship with PROP_NET_STACK_UP

EDITOR: the examples show that the order of operations to bring up a network interface is first set PROP_NET_IF_UP=TRUE, then PROP_NET_STACK_UP=TRUE. What does it mean when PROP_NET_IF_UP=TRUE and PROP_NET_STACK_UP=FALSE? Does the NLI matter in operations with the PROP_NET_STACK_UP property?

RQ: PROP_NET_IF_UP=TRUE, PROP_NET_STACK_UP=FALSE is used for in-band commissioning.
It bring up link-local IPv6 capabilities on PROP_STREAM_NET_INSECURE.

### PROP 66: PROP_NET_STACK_UP  {#prop-net-stack-up}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Post-Reset Value: 0 (false)

Bytes:  |    1
-------:|-------------
Format: | BOOL
Fields: | `NET_STACK_UP`

Network protocol stack operational status. Non-zero (set to 1) indicates up, zero indicates down.

Setting this to true implies also setting `PROP_NET_IF_UP` to true.


### PROP 67: PROP_NET_ROLE {#prop-net-role}

* Type: Single-Value, Read-Only\*
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Unit: Enumeration
* Post-Reset Value: 0 (`NET_ROLE_DETACHED`)

Bytes:  |    1
-------:|-------------
Format: | UINT8
Fields: | `NET_ROLE`

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
* Required: **REQUIRED** for standard network layer
* Post-Reset Value: Empty

Bytes:  |    *n*        | 1
-------:|---------------|------
Format: | UTF8         | CONST
Fields: | `NETWORK_NAME` | 0x00

Contains a zero-terminated UTF8 string which describes the name of
the current network. This is analogous to the SSID in 802.11. The maximum
length of the string is technology specific. Setting this property to a value
that is too large will cause `STATUS_PROP_VALUE_TOO_BIG` to be emitted.

When this value is being retrieved, all trailing zero bytes in the network name
**MUST** be stripped. When this value is being set, all trailing zero bytes
**SHOULD** be stripped.

See (#security-network-name) for security considerations regarding UTF8
normalization.

### PROP 69: PROP_NET_XPANID   {#prop-net-xpanid}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED** for standard network layer
* Post-Reset Value: All zeros

Bytes:  |    8
-------:|---------------------
Format: | XPANID
Fields: | `NET_XPANID`

Contains the extended personal-area network identifier for the current
network. It allows for different networks with identical names to not
conflict with each other. It is defined to be eight bytes long.

For network technologies which don't have the concept of an XPANID,
this property is hard-coded to always be all zeros.

### PROP 70: PROP_NET_MASTER_KEY   {#prop-net-master-key}

* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED** for standard network layer
* Post-Reset Value: Unspecified

Bytes:  |    *n*
-------:|---------------------
Format: | DATA
Fields: | `NET_MASTER_KEY`

Master key material. The exact length of the value for this property is defined by
the technology, but is generally 16-bytes (128-bit).


### PROP 71: PROP_NET_KEY_SEQUENCE_COUNTER   {#prop-net-key-sequence-counter}
* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **REQUIRED** for standard network layer
* Post-Reset Value: 0

Bytes:  |    4
-------:|---------------------
Format: | UINT32
Fields: | `NET_KEY_SEQUENCE_COUNTER`

### PROP 72: PROP_NET_PARTITION_ID   {#prop-net-partition-id}
* Type: Single-Value, Read-Only
* Asynchronous Updates: Yes
* Required: **OPTIONAL**
* Post-Reset Value: Unspecified

Bytes:  |    4
-------:|---------------------
Format: | UINT32
Fields: | `NET_PARTITION_ID`

The partition ID of the partition that this node is a member of.

For network technologies which don't have the concept of a partition id,
this property is hard-coded to always be zero.

### PROP 73: PROP_NET_REQUIRE_JOIN_EXISTING   {#prop-net-require-join-existing}
* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required:
    * Read: **OPTIONAL**
    * Write: **REQUIRED**
* Post-Reset Value: 0 (false)

Bytes:  |    1
-------:|---------------------
Format: | BOOL
Fields: | `NET_REQUIRE_JOIN_EXISTING`

<!-- RQ -- TODO: Elaborate on PROP_NET_JOIN_EXISTING -- -->

### PROP 74: PROP_NET_KEY_SWITCH_GUARDTIME   {#prop-net-key-swtich-guardtime}
* Type: Read-Write
* Packed-Encoding: `L`

<!-- RQ -- TODO: This likely doesn't belong in core, at least not in the small-number section.  -- -->

### PROP 75: PROP_NET_PSKC   {#prop-net-pskc}
* Type: Read-Write
* Packed-Encoding: `D`

<!-- RQ -- TODO: This likely doesn't belong in core, at least not in the small-number section.  -- -->
