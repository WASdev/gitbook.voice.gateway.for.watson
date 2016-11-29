# Setting up a Twilio SIP trunk for self-service agents

For self-service agents, SIP trunks provide one option for forwarding communications from the customer to IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade;. The other option is to set up a session border controller (SBC). **??? When do you use each of these? What can we link to for more info?**

##### Twilio Elastic SIP Trunking

One very common setup is to deploy the voice gateway to IBM&reg; Containers for Bluemix&reg; and then use a Twilio SIP trunk to access the voice gateway. Twilio is a cloud-based communications company that provides a simple way to provision a telephone number that can be connected to a SIP-based service, such as Voice Gateway for Watson.

**??? Do you have to use IBM Containers on Bluemix? Doesn't seem like it - how can we clarify?**

**Note:** Twilio SIP Trunks do not support SIP REFER messages, which are required to transfer calls. To set up call transfer, your architecture must have an intermediate call anchor point that can handle SIP REFER messages. **??? What's an example of this?**

For more information about setting up Twilio Elastic SIP Trunking, see [the Twilio documentation](https://www.twilio.com/docs/api/sip-trunking).

##### Not using Twilio?

Voice Gateway for Watson can be used with any SIP trunk. **??? Any alternatives?**

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
