# Troubleshooting

When troubleshooting, it's important to understand that the Voice Gateway for Watson is made up of two Docker containers, the SIP Orchestrator and the Media Relay.
* The SIP Orchestrator handles all the SIP call signaling and the interactions with the Watson Conversation or Dialog services. It also communicates with the Media Relay through a web socket over which it sets up the media session and send and receives text utterances.
* The Media Relay is responsible for processing the audio media streams and communicates directly with the Watson Speech to Text and Watson Text To Speech services. The Media Relay also sources and sinks the RTP audio streams to and from the caller.

Consider how each of these components work together as you attempt to isolate the problem. For more detail, see [Architecture](about.md#architecture).

## Logging and tracing

Like other configuration for the voice gateway, logging and tracing levels are configured as Docker environment variables, as described in [Configuration environment variables for the voice gateway](config.md).

#### Logging environment variables

The following Docker environment variables are used to control the log level in the SIP Orchestrator and the Media Relay container:

| Environment variable | Default | Details |
| --- |--- | ---|
| LOG_LEVEL | audit | This is the log level for the SIP Orchestrator. Valid values are off, fatal, severe, warning, fine, finest, and all.  |
|RTP_RELAY_LOGLEVEL| INFO | This is the log level for the Media Relay. Valid values are INFO, DEBUG, or TRACE.|

#### Tracing environment variables

These Docker environment variables for the SIP Orchestrator are used to control various aspects of the tracing:

| Environment variable | Default | Details |
| --- |--- | ---|
| ENABLE_AUDIT_MESSAGES | true | Set to false to disable audit messages. |
| ENABLE_TRANSCRIPTION_AUDIT_MESSAGES | false | Set to true to enable audit transcription messages. |
| LATENCY_REPORTING_THRESHOLD | 20 | Threshold in milliseconds for reporting round-trip conversation latency. |
| RELAY_LATENCY_REPORTING_THRESHOLD | 1000 | Threshold in milliseconds for reporting Media Relay latencies. |

#### Finding and viewing log files

The log files for the SIP Orchestrator container are in the **logs** directory. This directory contains the **messages.log** file. If the LOG_LEVEL is set to at least `fine`, the directory also contains the **trace.log** file, which contains additional details. To copy the log files off of the SIP Orchestrator container, run the following commands:

```
cf ic cp cgw.sip.orchestrator:/logs/messages.log .
cf ic cp cgw.sip.orchestrator:/logs/trace.log .
```

Similarly, the main log file for the Media Relay, **trace.log**, is in the **logs** directory on its container. The **trace.log** file is best viewed by using [Bunyan](https://github.com/trentm/node-bunyan), especially when the log level is set to `DEBUG`. To copy the **trace.log** file off of the Media Relay container, run the following command:

```
cf ic cp cgw.sip.orchestrator:/logs/trace.log .
```

If Bunyan is installed, you can use the following command to view your log file:

```
bunyan trace.log
```

## Common problems that you might encounter

* **Calls are failing:** Calls can fail for many reasons, including misconfiguration of one of your Watson services, IP routing issues, firewall issues, etc.
 1. First, check that both the SIP Orchestrator and the Media Relay containers are running and that the Watson services are properly configured.
 1. Next, look at the SIP Orchestrator logs to see if the SIP INVITE message that initiates the call is reaching the container. If it does, follow the flow of the call in the trace to the point where the error occurs, which is where the SIP error response is sent back to the caller.
 1. If the SIP INVITE is not reaching the voice gateway (that is, it does not appear in the logs), your firewall might be blocking the SIP port (5060), or the SIP trunk might not be properly configured to route to the correct IP address of the SIP Orchestrator container.
* **I hear Watson, but Watson doesn't hear me:** This problem is almost always a firewall issue or a problem with the SIP trunk or SIP client you are using. Because audio media can flow over such a wide range of ports, it's possible that your firewall is not configured to receive media over the configured port stream. Check the configuration of the Media Relay to see what the media port range is, and make sure your firewall is configured to accommodate that port range. If you are trying to use a SIP client to connect with the gateway, also check for port conflicts.
* **There is a lot of latency between caller questions and Watson answers:** This problem is most likely coming from latency caused by one of the Watson services. You can look at the audit logs to determine which service is misbehaving.
* **Call transfers are failing:** Call transfers are supported by the voice gateway, but they require an entity that understands SIP REFER messages to stay in the call path and anchor the call for the life of the call with Watson, such as a session border controller (SBC). For example, Twilio SIP trunks do not support SIP REFER messages, so if you want to support call transfer, you'll need to use another SIP trunking vendor.
* **Help, I need help!!** If you need help, the fastest way to get answers on questions about the voice gateway is to post your questions in the [WASdev Forum](https://developer.ibm.com/answers/smartspace/wasdev/). We'll get back to you ASAP.
