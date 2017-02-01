# Resources

Want to learn more? We've gathered some helpful links, tools, and other resources related to IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade;.

## Voice Gateway for Watson downloads
* **[Media Relay on Docker Hub](https://hub.docker.com/r/ibmcom/voice-gateway-mr)** - Download the Docker image for the Media Relay, which together with the SIP Orchestrator makes up the voice gateway.
* **[SIP Orchestrator on Docker Hub](https://hub.docker.com/r/ibmcom/voice-gateway-so)** - Download the Docker image for the SIP Orchestrator, which you use with the Media Relay.
* **[GitHub repository with samples](https://github.com/WASdev/sample.voice.gateway.for.watson)** - Download sample deploy scripts and configuration files to quickly get started setting up the voice gateway.

## IBM Watson services on Bluemix
* **[Watson Speech to Text service](https://www.ibm.com/watson/developercloud/doc/speech-to-text/)** - Learn about setting up the Speech to Text service, which transcribes the caller's speech into text for processing in self-service agents and agent assistants.
* **[Watson Conversation service](https://www.ibm.com/watson/developercloud/doc/conversation/)** - Learn about the Conversation service, which takes the natural-language input from the Speech to Text service and uses machine learning to provide a conversation-like response to the caller. This service is used only for self-service agents.
* **[Watson Text to Speech service](https://www.ibm.com/watson/developercloud/doc/text-to-speech/)** - Learn about setting up the Text to Speech service, which takes the provided output from the Conversation service and transforms it into natural-sounding speech. This service is used only for self-service agents.

## Docker resources
* **[Docker](https://docs.docker.com/)** - Before you can [deploy the voice gateway on Docker Engine](deploydocker.md), you'll need to install Docker Engine.

## Logging and tracing
* **[Bunyan](https://github.com/trentm/node-bunyan)** - Use the Bunyan command-line tool to easily view [Media Relay log files](troubleshooting.md#finding-and-viewing-log-files).

## MQTT resources
* **[Mosca MQTT Broker](https://github.com/mcollina/mosca)** - An open source MQTT broker that you can configure to capture MQTT messages for [real-time transcription](rttconfig.md).

## Security resources
* **[OpenSSL](https://www.openssl.org/)** - An open source toolkit for Secure Sockets Layer (SSL) and Transport Layer Security (TLS) protocols that you can use to [configure SSL/TLS encryption](security.md).

## SIP resources
* **[Linphone](https://www.linphone.org/)** - An open source SIP client that you can use to test call your self-service agent or agent assistant configuration.
* **[Twilio](https://www.twilio.com/)** - A cloud-based option for setting up a SIP trunk for self-service agents. For example, you might deploy the voice gateway in IBM Containers for Bluemix and then [set up Twilio elastic SIP trunking](https://www.twilio.com/docs/api/sip-trunking) as part of your architecture.
