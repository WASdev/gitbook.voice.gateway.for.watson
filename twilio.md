A very common setup for the voice gateway is to deploy it to Bluemix and then use a Twilio SIP trunk to access the voice gateway as a self-service agent. Twilio is a cloud based communications company that provides a simple way to provision a telephone number that can be connected to a SIP based service, which is the voice gateway in this case.

**Note:** Twilio SIP Trunks do not support SIP REFER messages so call transfer will not work without an intermediate call anchor point that can handle SIP REFER messages.

Here is a step-by-step guide to setting up a SIP Trunk to work with the voie gateway:

1. First you will need a Twilio account. You can do this by going to www.twilio.com and provisioning a new account. Note that you will need a credit card which will be periodically charged based on your usage of the SIP trunk you configure.

1. Create a SIP Trunk by going to the **SIP** dashboard and click on the **+** icon which will bring up a dialog where the name of the trunk can be entered. Enter the name of your trunk and hit create.

1. Configure the Origination address. This is the public SIP URI of the voice gateway. If the voice gateway is running in the Bluemix Docker container service you can use this command to determine the IP address of the SIP Orchestrator:

   ```
   cf ic ps
   ```
Enter the origination address as follows:

   ```
   sip:<address:port>;transport=tcp
   ```
Note that you don't have to set a transport of tcp but for cases where SIP invites could exceed the MTU of the Bluemix ICaaS service (which is 1500 bytes), SIP invites sent over UDP will not reach your voice gateway. This is not typically a problem with Twilio SIP trunks but it could happen.

1. Create a phone number and assign it to the SIP trunk. Start by going to the **Number** dashboard and click on the **+** icon. A panel will display allowing you to provision a new phone number in your region.

1. Assign the number to the SIP trunk you just created by navigating back to the SIP trunk and clicking on the **Number** icon.

1. Once you do this association of the number to your SIP trunk you are ready to make calls to the self-service agent hosted by your voice gateway.

Note that there is a handy call log on the main SIP trunking dashboard page that you can use to see calls coming into your trunk. It will also show you if any failures are occurring.
