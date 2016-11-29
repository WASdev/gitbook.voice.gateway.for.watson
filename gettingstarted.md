# Getting started with Voice Gateway for Watson
IBM&reg; WebSphere&reg;  Connect Voice Gateway for Watson&trade; is deployed as part of an overall architecture that depends on the following factors:
 * **Type of assistance:** Are you implementing a self-service agent or an agent assistant? Learn more about these options and their architectures in [About Voice Gateway for Watson](about.md).
 * **Deployment location:** Where are you deploying the voice gateway? For each implementation, you can either deploy the voice gateway on the cloud using IBM&reg; Containers for Bluemix&reg;, or you can deploy the voice gateway on-premises or in a Docker Engine.

The following guides will help you quickly get started with a basic configuration for your chosen implementation and deployment location.

### Setting up a self-service agent

To set up a self-service agent, just follow these steps.

1. Deploy Voice Gateway for Watson either on Bluemix or in Docker Engine.
  * [Deploy the voice gateway on IBM Containers for Bluemix](self-service-bmix.md)
  * [Deploy the voice gateway on Docker Engine](selfservice-docker.md)
1. Set up a SIP trunk or session border controller.

  SIP trunks and session border controllers (SBCs) act as intermediaries that forward calls from the customer to the Voice Gateway.

  * [Set up a Twilio SIP trunk](twilio.md), which is a popular choice for voice gateway deployments on IBM Containers. **??? What about for Docker Engine?**
  * **??? Info on SBC for self-service?**
1. Use the API to pass information between the voice gateway and the Watson services.

  For instance, you can receive information from the Watson Conversation service that triggers the voice gateway to hang up or transfer the call. For a full list of actions, see [API for self-service agents](api.md).

### Setting up an agent assistant

The voice gateway provides the ability to transcribe audio from an active phone call in real-time using the SIPREC protocol. This capability requires a Session Border Controller (SBC) that supports the ability to fork media out to the voice gateway, which is acting as a SIPREC Session Recording Server (SRS).

1. Deploy Voice Gateway for Watson either on Bluemix or in Docker Engine.
  * [Deploy the voice gateway on IBM Containers for Bluemix](self-service-bmix.md)
  * [Deploy the voice gateway on Docker Engine](selfservice-docker.md)

  Only the Watson Speech to Text service is needed for agent assistants. In the voice gateway configuration, specify your Watson service credentials.

  **Important:** To accurately transcribe phone calls, you'll need to train your service with a custom language model for the specific domain, such as healthcare or insurance.

1. Set up the real-time transcription.
 The voice gateway takes text output from the Watson Speech to Text service and publishes it through the included MQTT plug-in to an external MQTT message broker. **??? And then what happens to it?**

1. Set up the session border controller (SBC).

   Configure your session border controller to open a SIPREC session with the voice gateway for all calls that need to be transcribed in real time. You can use any SBC that supports the SIPREC protocol; the voice gateway has been tested with a Sonus session border controller.

   Configuring the SIPREC protocol is specific to each SBC manufacturer, so see the documentation for your SBC for further information.
