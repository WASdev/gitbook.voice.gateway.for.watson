# Configuring real-time transcription for the voice gateway

Voice Gateway for Watson provides the ability to access the transcription text from the call in real time, which can be valuable in many scenarios, including:
* Monitoring conversations as they occur
* Logging conversations for later analysis, such as to fine-tune your Watson services for a better customer experience

You can configure real-time transcription for both a self-service agent and an agent assistant; that is, for both the SIP and SIPREC protocols. Transcription messages are published through the MQTT plug-in included in voice gateway to an external MQTT message broker.

### Prerequisites
Set up an MQTT message broker. You can use any broker that supports the MQTT 3.1 protocol, such as the open source [Mosca MQTT Broker](https://github.com/mcollina/mosca). The broker can run in any environment that is accessible to the voice gateway; for example, if the voice gateway is running in IBM&reg; Containers&reg; for Bluemix&trade;, the port that the broker is listening on must be publicly accessible from Bluemix.

### Configuring the voice gateway MQTT plug-in

To publish transcription messages from the voice gateway, configure the MQTT plug-in by adding the following Docker environment variables to your SIP Orchestrator configuration. To learn more about configuring the voice gateway, see [Configuration environment variables](config.md).

1. Enable the voice gateway's MQTT plug-in.
```
MQTT_PLUGIN_ENABLE=true
```
1. Configure the MQTT broker address.
```
MQTT_BROKER_ADDRESS=<IP Address> (e.g MQTT_BROKER_ADDRESS=169.44.122.120)
```

1. Lastly, you can also configure the root topic that is used when publishing all the transcript utterance messages.
```
MQTT_TOPIC_PATH=<topic path>
```

  Note that the default topic path is set to `/voice-gateway/`.

When the MQTT plug-in is enabled, transcription messages are published regardless of whether the voice gateway is set up as a self-service agent or an agent assistant.

### Accessing real-time transcripts through MQTT

Transcription messages are published from the voice gateway to the MQTT message broker every time a new utterance is detected. You can choose to receive all transcription messages or only messages for specific callers depending on the MQTT topic being subscribed on.

The transcription messages are published to subtopics off this root topic according to following topic path pattern:

```
/<configured MQTT topic path>/<tenant number>/<caller number>
```

To listen for transcription messages at a certain level, subscribe in your MQTT message broker on the topic path at level of the tenant or caller whose messages you want to receive.

* **Listen for all calls:** `<configured MQTT topic path>/#`

 For example:

   ```
   /voice-gateway/#
   ```
* **Listen for all calls to a specific tenant:** `<configured MQTT topic path>/<tenant number>/#`

 For example:

 ```
 /voice-gateway/18444523778/#
 ```

* **Listen for a specific caller:** `<configured MQTT topic path>/<tenant number>/<caller number>/#`

 **Note:** The caller number must include the country code.

 For example, to listen for the 123-456-7890 caller from the USA (country code: 1):
 ```
 /voice-gateway/18444523778/11234567890
 ```
