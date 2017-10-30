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
