# Customizing and advanced configuration

After you have the basics down, you can further configure components in the IBM&reg; WebSphere&reg; Connect Voice Gateway for Watson&trade; architecture to provide additional capabilities.

**New to Voice Gateway for Watson?** See the [Getting started](gettingstarted.md) section to quickly get up and running with a self-service agent or agent assistant.

### For self-service agents

* **[Set up a SIP trunk](twilio.md)** - If you tested your setup with a simple SIP client such as Linphone, set up a SIP trunk as part of your final architecture.
* **[Define your configuration](config.md)** - Using Docker environment variables, you can enable additional capabilities and configure the voice gateway's settings.
* **[Customize interactions using the API](api.md)** - The Watson API uses state variables to exchange information between the Watson services and your voice gateway. With the API, you can define when to hang up a call or set the hold music to your organization's favorite smooth jazz track.
* **[Configure real-time transcription](rttconfig.md)** - Automatically keep a written record of each conversation by setting up real-time transcription. Use these logs to monitor conversations or to analyze them to fine-tune your Watson services.
* **[Integrate with a contact center](contactctr.md)** - To cover all your bases, you can give customers a chance to transfer to a live agent by integrating with an existing contact center. Using the API and configuration variables, for example, you can trigger a call transfer if a Watson service is down.

### For agent assistants

Agent assistants take a little more work to initially set up, so less configuration is needed later.

* **[Define your configuration](config.md)** - Using the Docker environment variables, you can whitelist certain numbers, define logging levels, and more.
* **[Configure real-time transcription](rttconfig.md)** - As with self-service agents, you can automatically transcribe conversations in real time to keep a written record for monitoring or analysis.
