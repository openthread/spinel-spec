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
+-                        IPv6 ADDRESS                         -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     SCOPE     |             FLAGS             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_UCAST_ADDRS

This property contains all unicast IPv6 addresses currently assigned to this
NLI. This includes both automatically assigned addresses and addresses manually
added by the AP via `PROP_IPV6_HOST_UCAST_ADDRS`.

The AP cannot directly manipulate this property. but it can add new unicast
IPv6 addresses via the `PROP_IPV6_HOST_ON_LINK_PREFIXES` property.

SCOPE
: An enumeration describing the scope of the address. Can be link-local,
mesh-local, site-local, or global. (TODO: Enumeration TBD)

FLAGS
: Flags describing various properties of the address. Flags are dynamic/static,
temporary, deprecated, secured [@RFC7217], etc. (TODO: Enumeration TBD)

### PROP XXXX: PROP_IPV6_HOST_UCAST_ADDRS {#prop-ipv6-host-ucast-addrs}

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
~~~
Figure: Structure of PROP_IPV6_HOST_UCAST_ADDRS

Addresses added to this list will be added to PROP_IPV6_UCAST_ADDRS as
static addresses.

### PROP XXXX: PROP_IPV6_ON_LINK_PREFIXES {#prop-ipv6-on-link-prefixes}

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
| PREFIX LENGTH |     SCOPE     |     FLAGS (Little Endian)     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        VALID LIFETIME (Little Endian)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      PREFERRED LIFETIME (Little Endian)       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_ON_LINK_PREFIXES

This property contains all on-link or on-mesh IPv6 prefixes that are being
advertised by routers on this NLI's network, including all prefixes listed in
`PROP_IPV6_HOST_ON_LINK_PREFIXES`.

The AP cannot directly manipulate this property. but it can add new on-link or on-mesh
prefixes via the `PROP_IPV6_HOST_ON_LINK_PREFIXES` property.

### PROP XXXX: PROP_IPV6_HOST_ON_LINK_PREFIXES {#prop-ipv6-host-on-link-prefixes}

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
+-                        IPv6 PREFIX                          -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |     SCOPE     |     FLAGS (Little Endian)     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        VALID LIFETIME (Little Endian)         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      PREFERRED LIFETIME (Little Endian)       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_HOST_ON_LINK_PREFIXES

This property contains all on-link or on-mesh IPv6 prefixes that *this host* is
advertising on this NLI's network.

The AP cannot directly manipulate this property. but it can add new on-link or on-mesh
prefixes via the `PROP_IPV6_HOST_ON_LINK_PREFIXES` property.

When removing prefixes from the list, only the `IPv6 PREFIX` and `PREFIX LENGTH`
are required.

Some network technologies may limit which nodes have permission to add items
to this list. In such a case, the following behaviors are acceptable (in order of
behavior preference):

1. The command fails with `STATUS_SECURITY_ERROR`.
2. The command initially appears to succeed, but a few moments later
the prefix is asynchronously removed via either `CMD_PROP_VALUE_IS` or
`CMD_PROP_VALUE_REMOVED`.


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
* Required: **REQUIRED**
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

### PROP XXXX: PROP_IPV6_ROUTES {#prop-ipv6-routes}

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
| PREFIX LENGTH |    (TODO: Likely needs more stuff here...)
+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_ROUTES

This property contains *all* prefixes that can be reached via this NLI, **both off-link and
on-link**. This list **MUST NOT** include any prefixes listed in `PROP_IPV6_HOST_ROUTES`
**UNLESS** that route is being advertised by some other node.

### PROP XXXX: PROP_IPV6_HOST_ROUTES {#prop-ipv6-host-routes}

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
+-                        IPv6 PREFIX                          -+
|                                                               |
+-                                                             -+
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| PREFIX LENGTH |    (TODO: Likely needs more stuff here...)
+-+-+-+-+-+-+-+-+
~~~
Figure: Structure of PROP_IPV6_HOST_ROUTES

This property contains all prefixes that the AP is willing to route traffic to on behalf
of other nodes the current network. This property **MUST NOT** be populated
automatically by the NCP. Routes added to this property will be advertised to
other nodes on the network.

Some network technologies may limit which nodes have permission to add items
to this list. In such a case, the following behaviors are acceptable (in order of
behavior preference):

1. The command fails with `STATUS_SECURITY_ERROR`.
2. The command initially appears to succeed, but a few moments later
the item is asynchronously removed via either `CMD_PROP_VALUE_IS` or
`CMD_PROP_VALUE_REMOVED`.
