## IPv6 Properties {#prop-ipv6}

### PROP 96: PROP_IPV6_LL_ADDR {#prop-ipv6-ll-addr}
* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Value Type: IPv6 Address
* Post-Reset Value: Unspecified

### PROP 99: PROP_IPV6_ADDR_TABLE {#prop-ipv6-address-table}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: Structure
* Post-Reset Value: Empty

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |              VALID LIFETIME...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  ...           |            PREFERRED LIFETIME...
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  ...           |     FLAGS     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: PROP_IPV6_ADDRESS_TABLE structure

This property provides the table of all of the unicast IPv6 addresses associated
with this NLI.

TODO: This needs a lot of work.

EDITOR: this conflates the IPv6 interface address list with the IPv6 on-link prefix used in IPv6 Neighbor Discovery and other address reservation and resolution protocols with similar function, e.g. Thread(R). It probably makes sense to create an additional set of properties that represent neighbor discovery and router discovery parameters.

EDITOR: the operational semantics of the Flags field is not well-specified.

### PROP 101: PROP_IPv6_ICMP_PING_OFFLOAD
* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NLI
* Value Type: BOOL
* Post-Reset Value: 0 (false)

Allow the NCP to directly respond to ICMP ping requests. If this is turned on, ICMP echo request packets will not be passed to the AP.

### PROP 102: PROP_IPV6_MULTICAST_ADDR_TABLE {#prop-ipv6-multicast-address-table}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: IPv6
* Post-Reset Value: Implemenation specific




















## RETHINK IPv6 Properties {#prop-ipv6-rethink}

This section is discussing a possible renumbering/reorganization of the IPv6
section.

### PROP XXXX: PROP_IPV6_UCAST_ADDRS {#prop-ipv6-ucast-addrs}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: Structure
* Post-Reset Value: Empty

This property contains all unicast IPv6 addresses currently assigned to this
NLI. Addresses can be added automatically by the NCP or added manually
by the AP.

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH | Rsrvd | SCOPE |V|S|N|F|O|D|T|    Reserved     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_UCAST_ADDRS

ADDRESS
: IPv6 Address.

PREFIX LENGTH
: The prefix length, in bits. See [@RFC4191].

Rsv/Reserved
: Reserved bits. All bits in reserved fields **SHOULD** be set to zero by the AP and NCP and
**MUST** be ignored by the AP and NCP.

SCOPE
: 4-bit integer representing scope of prefix address. See (#ipv6-scope).

V
: 1-bit "Address Valid" flag. Support for this flag is **REQUIRED**.

S
: 1-bit "Static Address" flag. Indicates that this address was manually added and will not
be automatically removed or invalidated.  Support for this flag is **REQUIRED**.

N
: 1-bit "No-DAD" flag. Indicates that duplicate address detection should not be attempted
on this address. Support for this flag is **OPTIONAL**.

F
: 1-bit "DAD Failed" flag. Indicates that another node on the mesh network is using this
address.  The AP **MUST** support this flag.

O
: 1-bit "Optimistic" flag.  Support for this flag is **OPTIONAL**.

D
: 1-bit "Deprecated" flag.  Support for this flag is **OPTIONAL**.

T
: 1-bit "Tentative" flag.  Support for this flag is **OPTIONAL**.

The AP and NCP **SHOULD** ignore any set flags it does not support, and **MUST**
clear flags it does not support.

TODO: Consider adding a property that is a bitmask of supported flags?

#### IPv6 Scope {#ipv6-scope}
An enumeration describing the scope of the address, value between 0 and 15.

Value | Names
----|--------
1    | IPV6_SCOPE_IFACE
2    | IPV6_SCOPE_LINK
3    | IPV6_SCOPE_REALM
4    | IPV6_SCOPE_ADMIN
5    | IPV6_SCOPE_SITE
8    | IPV6_SCOPE_ORG
14    | IPV6_SCOPE_GLOBAL
Figure: Interpretation of SCOPE field

All unspecified values are RESERVED.


### PROP XXXX: PROP_IPV6_MCAST_ADDRS {#prop-ipv6-mcast-addrs}

* Type: Multiple-Value, Read-Only
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: IPv6
* Post-Reset Value: Implemenation specific

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_MCAST_ADDRS

This property contains all IPv6 multicast groups that this NLI is currently a
member of. The AP cannot directly manipulate this property, but it can add
the NLI to new groups via the `PROP_IPV6_HOST_MCAST_ADDRS` property.

### PROP XXXX: PROP_IPV6_HOST_MCAST_ADDRS {#prop-ipv6-mcast-addrs}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NLI
* Item Type: IPv6
* Post-Reset Value: Empty

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_HOST_MCAST_ADDRS

This property contains only IPv6 multicast addresses that have been added
by the AP. The AP can manipulate this list by setting it outright, inserting addresses,
or removing addresses. When attached to a network, the addresses in this list are a
subset of the addresses in `PROP_IPV6_MCAST_ADDRS`.

Note that it is up to the AP to perform any necessary reference counting on
joined groups.

Some network technologies may limit which nodes have permission to add items
to this list. In such a case, the following behaviors are acceptable (in order of
behavior preference):

1. The command fails with `STATUS_SECURITY_ERROR`.
2. The command initially appears to succeed, but a few moments later
the item is asynchronously removed via either `CMD_PROP_VALUE_IS` or
`CMD_PROP_VALUE_REMOVED`.

### PROP XXXX: PROP_IPV6_PREFIXES {#prop-ipv6-prefixes}

* Type: Multiple-Value, Read-Only
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: Structure
* Post-Reset Value: Empty

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 PREFIX                          -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |Rsv|PRF| SCOPE |H|M|O|L|A|D|     Reserved      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        VALID LIFETIME (Little Endian)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      PREFERRED LIFETIME (Little Endian)       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                      ROUTER ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_PREFIXES

This property contains all IPv6 prefixes that are being
advertised by routers on this NLI's network, with the exception of prefixes listed
in `PROP_IPV6_HOST_PREFIXES` with the

The AP cannot directly manipulate this property. but it can add new on-link or on-mesh
prefixes via the `PROP_IPV6_HOST_PREFIXES` property, which uses the
same structure.

PREFIX
: IPv6 Address Prefix.

PREFIX LENGTH
: The prefix length, in bits. See [@RFC4191]. If this is zero, then the entry represents a default
route. Some network technologies (such as Thread(R)) only support values of 0 and 64.

Rsv/Reserved
: Reserved bits. All bits in reserved fields **SHOULD** be set to zero by the AP and NCP and
**MUST** be ignored by the AP and NCP.

PRF
: Route preference. [@RFC4191]

SCOPE
: 4-bit integer representing scope of prefix address. See (#ipv6-scope).

H
: Host-added flag. If set, this prefix was added via `PROP_IPV6_HOST_PREFIXES` and is
currently being advertised to other nodes.

M
: 1-bit "Managed Address" flag.  When set indicates that ROUTER ADDRESS runs
a DHCPv6 server for assigning and configuring addresses for this prefix.

O
: 1-bit "Other Configuration" flag. When set, it indicates that other configuration information is
available via DHCPv6.  Examples of such information are DNS-related information or
information on other  servers within the network.

L
: 1-bit On-Link flag. If set, this prefix is considered to be on-link/on-mesh. If clear, this
router is advertising that they can

A
: 1-bit autonomous address-configuration flag.  When set indicates that nodes
**SHOULD** automatically configure addresses for this prefix using SLAAC. [TODO:Cite]

D
: 1-bit "default route" flag. When set, it indicates that this NLI is a default route for packets
with a source address in PREFIX.

VALID LIFETIME
: 32-bit unsigned integer.  The remaining amount of time (in seconds) that the prefix is
entry is considered valid. A value of all one bits (0xffffffff) represents infinity.

PREFERRED LIFETIME
: 32-bit unsigned integer.  The remaining amount of time (in seconds) that that addresses
generated from the prefix via stateless address autoconfiguration remain preferred.
A value of all one bits (0xffffffff) represents infinity. This is ignored unless the `A` flag is set.

ROUTER ADDRESS
: This is the IPv6 address of the router advertising this prefix.

### PROP XXXX: PROP_IPV6_HOST_PREFIXES {#prop-ipv6-host-prefixes}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **OPTIONAL**
* Scope: NLI
* Item Type: Structure
* Post-Reset Value: Empty

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 PREFIX                          -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |Rsv|PRF| SCOPE |H|M|O|L|A|D|     Reserved      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        VALID LIFETIME (Little Endian)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      PREFERRED LIFETIME (Little Endian)       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_HOST_PREFIXES

This property contains all prefixes that *this host* is
advertising on this NLI's network. The structure is identical to that of
`PROP_IPV6_PREFIXES`, witht he following exceptions:

* The `H` flag is always set when read, and ignored when written.
* There is no `ROUTER ADDRESS` field, since it is implied.

When removing prefixes from the list, only the `IPv6 PREFIX` and `PREFIX LENGTH`
are required to be specified.

Some network technologies **MAY** limit which nodes have permission to add items
to this list. In such a case, the following behaviors are acceptable (in order of
behavior preference):

1. The command fails with `STATUS_SECURITY_ERROR`.
2. The command initially appears to succeed, but a few moments later
the prefix is asynchronously removed via either `CMD_PROP_VALUE_IS` or
`CMD_PROP_VALUE_REMOVED`.



### PROP XXXX: PROP_IPV6_EXT_ROUTES {#prop-ipv6-ext-routes}

* Type: Multiple-Value, Read-Only
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: Structure
* Post-Reset Value: Empty

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 PREFIX                          -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |Rsv|PRF| SCOPE |H|           Reserved          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        ROUTE LIFETIME (Little Endian)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                      ROUTER ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_EXT_ROUTES

This property contains all external routes that are being
advertised by routers on this NLI's network, with the exception of routes listed
in `PROP_IPV6_HOST_EXT_ROUTES`.

The AP cannot directly manipulate this property. but it can add new on-link or on-mesh
prefixes via the `PROP_IPV6_HOST_EXT_ROUTES` property, which uses the
same structure.

PREFIX
: TODO: Write me

PREFIX LENGTH
: The prefix length, in bits. See [@RFC4191]. If this is zero, then the entry represents a default
route. Some network technologies (such as Thread(R)) only support values of 0 and 64.

Rsv/Reserved
: Reserved bits. All bits in reserved fields **SHOULD** be set to zero by the AP and NCP and
**MUST** be ignored by the AP and NCP.

PRF
: Route preference. [@RFC4191]

SCOPE
: 4-bit integer representing scope of prefix address. See (#ipv6-scope).

H
: Host-added flag. If set, this prefix was added via `PROP_IPV6_HOST_EXT_ROUTES` and is
currently being advertised to other nodes.

ROUTE LIFETIME
: 32-bit unsigned integer.  The remaining amount of time (in seconds) that this external
route is considered valid. A value of all one bits (0xffffffff) represents infinity.

ROUTER ADDRESS
: This is the IPv6 address of the router advertising this external route.

### PROP XXXX: PROP_IPV6_HOST_EXT_ROUTES {#prop-ipv6-host-ext-routes}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **OPTIONAL**
* Scope: NLI
* Item Type: Structure
* Post-Reset Value: Empty

~~~
  0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+-                                                             -+
|                                                               |
+-                        IPv6 PREFIX                          -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |Rsv|PRF| SCOPE |H|           Reserved          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        ROUTE LIFETIME (Little Endian)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_HOST_EXT_ROUTES

This property contains all external routes that *this host* is
advertising on this NLI's network. The structure is identical to that of
`PROP_IPV6_EXT_ROUTES`, witht he following exceptions:

* The `H` flag is always set when read, and ignored when written.
* There is no `ROUTER ADDRESS` field, since it is implied.

When removing prefixes from the list, only the `IPv6 PREFIX` and `PREFIX LENGTH`
are required to be specified.

Some network technologies **MAY** limit which nodes have permission to add items
to this list. In such a case, the following behaviors are acceptable (in order of
behavior preference):

1. The command fails with `STATUS_SECURITY_ERROR`.
2. The command initially appears to succeed, but a few moments later
the prefix is asynchronously removed via either `CMD_PROP_VALUE_IS` or
`CMD_PROP_VALUE_REMOVED`.
