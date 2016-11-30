# Setting up a Twilio SIP trunk for self-service agents

For self-service agents, SIP trunks provide one option for connecting a caller to IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade;. Another option is to configure a session border controller (SBC) to forward calls to the voice gateway using a SIP INVITE. Integrating directly with a SIP trunk makes sense when you want to setup the voice gateway outside of an enterprise network.

This section will explain how to configure a Twilio SIP trunk to use a voice gateway running in cloud docker service like IBM&reg; Containers for Bluemix&reg;. If you wish to deploy the voice gateway on premise in an enterprise network, there will typically be a Session Border Controller between the voice gateway and the SIP trunk. In this case the SBC will need to be configured to route calls to the voice gateway.

##### Twilio Elastic SIP Trunking

One very common setup is to deploy the voice gateway to a cloud based container service like IBM&reg; Containers for Bluemix&reg; and then use a Twilio SIP trunk to access the voice gateway. Twilio is a cloud-based communications company that provides a simple way to provision a telephone number that can be connected to a SIP-based service, such as Voice Gateway for Watson.

**Note:** Twilio SIP Trunks do not support SIP REFER messages, which are required to transfer calls. To set up call transfer, your architecture must have an intermediate call anchor point that can handle SIP REFER messages such as a Session Border Controller.

For more information about setting up Twilio Elastic SIP Trunking, see [the Twilio documentation](https://www.twilio.com/docs/api/sip-trunking).

##### Not using Twilio?

Voice Gateway for Watson can be used with any SIP trunk provider such as Tata Communications.

#### Setting up a Twilio SIP trunk to work with the voice gateway

1. [Create a Twilio account](https://www.twilio.com/try-twilio).
  **Note:** Creating an account requires a credit card, which will be periodically charged based on your usage of the SIP trunk that you configure.

1. Create a SIP trunk by going to the **SIP** dashboard and clicking on the **+** icon. In the resulting dialog, enter a name for your SIP trunk, and click **Create**.

1. Configure the Origination address, which is the public SIP URI of the voice gateway.

  If the voice gateway is running IBM Containers for Bluemix, you can run the following command to determine the IP address of the SIP Orchestrator:

   ```
   cf ic ps
   ```
  In the Twilio dashboard, enter the origination address as follows:

   ```
   sip:<address:port>;transport=tcp
   ```
  Note that you do not _have_ to set the transport protocol to TCP. However, SIP invites that are sent over UDP will not reach your voice gateway if they exceed the 1500-byte maximum transmission unit (MTU) of the IBM Containers service. Exceeding the 1500-byte MTU is not typically a problem with Twilio SIP trunks, but it is possible.

1. Create a phone number and assign it to the SIP trunk.

  1. Go to the **Number** dashboard, and click on the **+** icon. A panel will display where you can provision a new phone number in your region.

  1. Assign the number to the SIP trunk you created by navigating back to the SIP trunk and clicking on the **Number** icon.


After the number is associated with your SIP trunk, you can make calls to the self-service agent hosted by your voice gateway.

##### Monitoring your calls

The main SIP trunking dashboard has a call log that lists calls coming into your trunk. You can also see if any failures occur.
