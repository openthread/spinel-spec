# Requirements {#thread-requirements}

Thread(R) NCPs which adhere to this specification **MUST** also satisfy the following requirements:

* The property `PROP_INTERFACE_TYPE` must be 3.
* The non-optional properties in the following sections **MUST** be
  implemented: CORE, PHY, MAC, NET, and IPV6.

All serious implementations of an NCP **SHOULD** also support the network
save feature (See draft-rquattle-spinel-network-save).
