# Requirements

The network save/recall feature is an **OPTIONAL** NCP capability that, when present, allows the host to save and recall network credentials and state to and from nonvolatile storage.

The presence of the save/recall feature can be detected by checking for the presence of the `CAP_NET_SAVE` capability in `PROP_CAPS`.

Network clear feature allows host to erase all network credentials and state from non-volatile memory.
