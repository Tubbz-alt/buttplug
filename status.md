# Status Messages


## Ok

**Description:** Signifies that the previous message sent by the client was received and processed successfully by the server.

**Fields:**

-   *Id* (unsigned int): The Id of the client message that this reply is in response to.

**Expected Response:**

None. Server-to-Client message only.

**Flow Diagram:**

![img](ok_diagram.svg)

**Serialization Example:**

```json
[
  {
    "Ok": {
      "Id": 1
    }
  }
]
```


## Error

**Description:** Signifies that the previous message sent by the client caused some sort of parsing or processing error on the server.

**Fields:**

-   *Id* (unsigned int): The Id of the client message that this reply is in response to, assuming the Id could be parsed. Id will be 0 if message could not be parsed (due to issues like invalid JSON).
-   *ErrorMessage* (string): Message describing the error that happened on the server.

**Expected Response:**

None. Server-to-Client message only.

**Flow Diagram:**

![img](error_diagram.svg)

![img](error_malformed_diagram.svg)

**Serialization Example:**

```json
[
  {
    "Error": {
      "Id": 0,
      "ErrorMessage": "Server received invalid JSON."
    }
  }
]
```


## Ping

**Description:** Ping acts a watchdog between the client and the server. The server will expect the client to send a ping message at a certain interval (interval will be sent to the client as part of the identification step). If the client fails to ping within the specified time, the server will disconnect and stop all currently connected devices.

This will handle cases like the client crashing without a proper disconnect. This is not a guaranteed global failsafe, since it will not guard against problems like a client UI thread locking up while a client communication thread continues to work.

**Fields:**

-   *Id* (unsigned int): Message Id

**Expected Response:**

-   Ok message with matching Id on successful logging request.
-   Error message on value or message error.

**Flow Diagram:**

![img](ping_diagram.svg)

**Serialization Example:**

```json
[
  {
    "Ping": {
      "Id": 5
    }
  }
]
```


## RequestLog

**Description:** Requests that the server send all internal log messages to the client. Useful for debugging.

**Fields:**

-   *Id* (unsigned int): Message Id
-   *LogLevel* (string): The highest level of message to receive. Sending "Off" turns off messages, while sending "Trace" denotes that all log messages should be sent to the client. Valid LogLevel values:
    -   Off
    -   Fatal
    -   Error
    -   Warn
    -   Info
    -   Debug
    -   Trace

**Expected Response:**

-   Ok message with matching Id on successful logging request. Assuming the LogLevel was not "Off", Log type messages will be received after this.
-   Error message on value or message error.

**Flow Diagram:**

![img](requestlog_diagram.svg)

**Serialization Example:**

```json
[
  {
    "RequestLog": {
      "Id": 1,
      "LogLevel": "Warn"
    }
  }
]
```


## Log

**Description:** Log message from the server. Only sent after the client has sent a RequestLog message with a level other than "Off".

**Fields:**

-   *Id* (unsigned int): Message Id
-   *LogLevel* (string): The level of the log message.
    -   Off
    -   Fatal
    -   Error
    -   Warn
    -   Info
    -   Debug
    -   Trace
-   *LogMessage* (string): Log message.

**Expected Response:**

None. Server-to-Client message only.

**Flow Diagram:**

![img](log_diagram.svg)

**Serialization Example:**

```json
[
  {
    "Log": {
      "Id": 0,
      "LogLevel": "Trace",
      "LogMessage": "This is a Log Message."
    }
  }
]
```
