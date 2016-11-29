# Getting started with Voice Gateway for Watson
IBM&reg; WebSphere&reg;  Connect Voice Gateway for Watson&trade; is deployed as part of an overall architecture that depends on the following factors:
 * **Type of assistance:** Are you implementing a self-service agent or an agent assistant? Learn more about these options and their architectures in [About Voice Gateway for Watson](about.md).
 * **Deployment location:** Where are you deploying the voice gateway? For each implementation, you can either deploy the voice gateway to the IBM&reg; Containers for Bluemix&reg;, or you can deploy the voice gateway in your own managed Docker Engine.

The following guides will help you quickly get started with a basic configuration for your chosen implementation and deployment location.

### Setting up a self-service agent

To set up a self-service agent, just follow these steps.

1. Deploy Voice Gateway for Watson either on Bluemix or in a Docker Engine.
  * [Deploy the voice gateway on IBM Containers for Bluemix](self-service-bmix.md)
  * [Deploy the voice gateway on Docker Engine](selfservice-docker.md)

1. Test your self-service agent. There are two ways you can test the self-service agent. Either using a SIP phone or via a voice call.

 1. A SIP phone such as Linphone can be used to call into the voice gateway. To test with a SIP phone, install and configure a SIP client such as [Linphone](http://www.linphone.org/).  If you will be running the SIP client on the same machine as the voice gateway, be sure to configure the SIP client to use a port other than 5060 (e.g. 5062) to avoid conflicts.

    You can now call the voice gateway from the SIP client that you configured. You'll need to call this SIP URI:

    `sip:watson@<IP address of the Voice Gateway docker engine>:5060`.  

  1. A SIP trunk can forward calls to the Voice Gateway. It's fairly simple to setup a SIP Trunk to connect to the voice gateway. You just need to idenify the IP address of the Docker engine that is hosting the Voice Gateway and configure your SIP trunk to associate that SIP address with a provisioned telephone number.

    * [Set up a Twilio SIP trunk](twilio.md), which is a popular choice for voice gateway deployments on IBM Containers. It can also be used to forward SIP calls to your own Docker engine deployment.

### Setting up an agent assistant

The voice gateway provides the ability to transcribe audio from an active phone call in real-time using the SIPREC protocol. This capability requires a Session Border Controller (SBC) that supports the ability to fork media out to the voice gateway, which is acting as a SIPREC Session Recording Server (SRS).

1. Set up the MQTT Broker for transcription message publishing.

 The voice gateway takes text output from the Watson Speech to Text service and publishes it through MQTT to an external MQTT message broker. Other services can then access the transcription messages in real-time by subscribing on the related topics. A subscriber could for instance run real-time analytics on the transcription or simply archive the transcription.

1. Deploy Voice Gateway for Watson either on Bluemix or on your own Docker Engine.
  * [Deploy the voice gateway on IBM Containers for Bluemix](self-service-bmix.md)
  * [Deploy the voice gateway on Docker Engine](selfservice-docker.md)

   Only the Watson Speech to Text service is needed for agent assistants. In the voice gateway configuration, specify your Watson service credentials.

  **Important:** To accurately transcribe phone calls, you'll need to train your service with a custom language model for the specific domain, such as healthcare or insurance.

1. Set up the session border controller (SBC).

   Configure your session border controller to open a SIPREC session with the voice gateway for all calls that need to be transcribed in real time. You can use any SBC that supports the SIPREC protocol; the voice gateway has been tested with a Sonus session border controller.

   Configuring the SIPREC protocol is specific to each SBC manufacturer, so see the documentation for your SBC for further information.

1. Test the agent-assistant.

 You can use an open source [MQTT client](http://mqtt-helper.mybluemix.net/) to subscribe on the MQTT topic that is being published on by the Voice Gateway.  You will need to dial the number that is setup at the SBC to fork media. You will also need to have a callee to answer the call
