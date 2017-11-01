## IPv6 Properties {#prop-ipv6}

### PROP 96: PROP_IPV6_LL_ADDR {#prop-ipv6-ll-addr}
* Type: Single-Value, Read-Write
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Value Type: IPv6 Address
* Post-Reset Value: Unspecified

### PROP 99: PROP_IPV6_ADDRESS_TABLE {#prop-ipv6-address-table}

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

### PROP 102: SPINEL_PROP_IPV6_MULTICAST_ADDRESS_TABLE {#prop-ipv6-multicast-address-table}

* Type: Multiple-Value, Read-Write
* Has Item Length Prefix: Yes
* Asynchronous Updates: Yes
* Required: **REQUIRED**
* Scope: NLI
* Item Type: IPv6
* Post-Reset Value: Implemenation specific


