## Debug Properties {#prop-debug}

### PROP 16384: PROP_DEBUG_TEST_ASSERT {#prop-debug-test-assert}

* Type: Single-Value, Read-Only
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NCP
* Post-Reset Value: Unspecified

Reading this property will cause an assert on the NCP. This is intended for testing the assert functionality of underlying platform/NCP. Assert should ideally cause the NCP to reset, but if `assert` is not supported or disabled boolean value of `false` is returned in response.

### PROP 16385: PROP_DEBUG_NCP_LOG_LEVEL {#prop-debug-ncp-log-level}

* Type: Single-Value, Read-Write
* Asynchronous Updates: No
* Required: **OPTIONAL**
* Scope: NCP
* Value Type: UINT8
* Post-Reset Value: Implementation Specific

Provides access to the NCP log level. Standard log level codes, which are derived from [@?RFC5424], are shown below:

 *  0: Emergency (emerg).
 *  1: Alert (alert).
 *  2: Critical (crit).
 *  3: Error (err).
 *  4: Warning (warn).
 *  5: Notice (notice).
 *  6: Information (info).
 *  7: Debug (debug).

If the NCP supports dynamic log level control, setting this property changes the log level accordingly. Getting the value returns the current log level.  If the dynamic log level control is not supported, setting this property returns a `PROP_LAST_STATUS` with `STATUS_INVALID_COMMAND_FOR_PROP`.
