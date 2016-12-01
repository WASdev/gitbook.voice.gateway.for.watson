# Getting started with Voice Gateway for Watson
IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade; is deployed as part of an overall architecture that depends on the following factors:
 * **Type of assistance:** Are you implementing a self-service agent or an agent assistant? Learn more about these options and their architectures in [About Voice Gateway for Watson](about.md).
 * **Deployment location:** Are you deploying the gateway in the cloud or on-premises? For each implementation, you can either deploy the voice gateway to the IBM&reg; Containers for Bluemix&reg;, or you can deploy the voice gateway in your own managed Docker Engine.

The following guides will help you quickly get started with a basic configuration for your chosen implementation and deployment location.

### Set up a self-service agent

To set up a self-service agent, just follow these steps.

1. Deploy Voice Gateway for Watson either on Docker Engine or on Bluemix.

  * [Deploy the voice gateway on Docker Engine](deploydocker.md)
  * [Deploy the voice gateway on IBM Containers for Bluemix](deploybmix.md)

1. Test your self-service agent, either by using a SIP phone or via a voice call.

 * **Test with a SIP phone:** You can call the voice gateway using a SIP phone such as [Linphone](http://www.linphone.org/). If you run the SIP client on the same machine as the voice gateway, be sure to configure the SIP client to use a port other than 5060 (e.g. 5062) to avoid port conflicts.

    To test the voice gateway, call the following SIP URI from your SIP phone:

    `sip:watson@<IP address of the voice gateway Docker Engine>:5060`.  

  * **Test with a voice call:** To test with a voice call, set up a simple SIP trunk, which can forward calls to the voice gateway. When you configure your SIP trunk, you just need to identify the IP address of the Docker Engine that is hosting the voice gateway, and then configure your SIP trunk to associate that SIP address with a provisioned telephone number.

    For example, you can [set up a Twilio SIP trunk](twilio.md), which is a popular choice for voice gateway deployments on IBM Containers. It can also be used to forward SIP calls to your own Docker Engine deployment.

### Set up an agent assistant

The voice gateway provides the ability to transcribe audio from an active phone call in real-time using the SIPREC protocol. This capability requires a session border controller (SBC) that supports the ability to fork media out to the voice gateway, which is acting as a SIPREC Session Recording Server (SRS).

1. Set up the MQTT broker for transcription message publishing.

 The voice gateway takes text output from the Watson Speech to Text service and publishes it through MQTT to an external MQTT message broker. Other services can then access the transcription messages in real time by subscribing on the related topics. For example, a subscriber could run real-time analytics on the transcription or simply archive the transcription.

 You can use any broker that supports the MQTT 3.1 protocol, such as the open source [Mosca MQTT Broker](https://github.com/mcollina/mosca).

1. Deploy Voice Gateway for Watson either on Docker Engine or on Bluemix.

  * [Deploy the voice gateway on Docker Engine](deploydocker.md)
  * [Deploy the voice gateway on IBM Containers for Bluemix](deploybmix.md)

  Only the [Watson Speech to Text](https://console.ng.bluemix.net/catalog/services/speech-to-text) service is needed for agent assistants. In the voice gateway configuration, specify your Watson service credentials.

  **Important:** To accurately transcribe phone calls, you'll need to train your Speech to Text service with a custom language model for the specific domain, such as healthcare or insurance.

1. Set up the session border controller (SBC).

   Configure your session border controller to open a SIPREC session with the voice gateway for all calls that need to be transcribed in real time. You can use any SBC that supports the SIPREC protocol; the voice gateway has been tested with a Sonus session border controller.

   Configuring the SIPREC protocol is specific to each SBC manufacturer, so you'll have to see the documentation for your SBC for configuration information.

1. Test the agent assistant.

 You can use an open source [MQTT client](http://mqtt-helper.mybluemix.net/) to subscribe on the MQTT topic that the voice gateway publishes on.  To test the agent assistant, dial the number that is set up at the session border controller to fork media. A callee must be available to answer the call.
