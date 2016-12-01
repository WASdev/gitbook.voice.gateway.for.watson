# Integrating the voice gateway with a contact center for self-service agents

When using the voice gateway to host a self-service agent, in some cases a customer might want to be transferred to a live contact center agent. The voice gateway can integrate with existing contact centers through an enterprise session border controller (SBC). The SBC handles the following call routing scenarios:

* Inbound calls to the voice gateway from a caller connecting through a SIP trunk
* Calls redirected to the voice gateway from an enterprise Interactive Voice Response (IVR) system
* Calls redirected from the voice gateway to a contact center or IVR system

#### Session border controller as pivot point

The voice gateway relies on standard SIP transfer procedures to redirect a call to a contact center using an in-dialog SIP REFER message. The call path must include some entity that acts as a pivot-point for the call to catch the SIP REFER and handle the redirect.

This pivot point is typically the session border controller, which interfaces with an external SIP trunk and forwards calls to the voice gateway. Enterprise SBCs can typically handle SIP REFER messages received over an existing SIP dialog and use that message to redirect an existing call to the contact center in the enterprise network.

#### Call flow from customer to live agent
<div>
<div style="float: right; padding-left: 1em; padding-bottom: 1em">
<h5> Call flow through SBC with transfer to contact center</h5>
 <img src="images/call-transfer.png" alt="Call flows through SBC with transfer to contact center"/></div>

This diagram shows the typical flow of messages related to both setting up a new call through an enterprise SBC with a transfer out to a contact center:

1. The call arrives at the SBC through SIP trunk.
1. The call is routed to the voice gateway, and the SBC stays in the call-signaling path.
1. A new Watson conversation is established, and the caller initiates turns with the Conversation through voice.
1. The Watson Conversation service initiates a transfer on last turn.
1. The voice gateway initiates a transfer back to the SBC by sending an in-dialog SIP REFER message.
1. The SBC re-routes call to the contact center automatic call distributor (ACD).
1. The call is eventually routed to the agent.

#### Configuring call transferring

Call transfer behavior is defined though [API state variables](api.md) and the [voice gateway configuration](config.md) as described in the following sections.

##### Initiating call transfers

Initiation of the call transfer is completely controlled by the Watson Conversation service through the following state variables:

| State variable name | Expected value | Default value | Description |
| -------------- | ------ | ----------- | ---------- |
| cgwTransfer | Yes / No | - | Informs the voice gateway to initiate a transfer after the included text response is played back to the caller.|
| cgwTransferTarget | SIP or telephone URI | - |  Identifies the transfer to endpoint (e.g. tel:+18883334444). |

##### Transferring upon failure

The voice gateway can also be configured to automatically transfer to a static endpoint if a failure occurs. Failures can occur, for example, if the voice gateway loses connectivity to one of the configured Watson services.

The following state variables can be set on the first turn of the conversation and will be used if a failure occurs at any point during the call:

| State variable name | Expected value | Default value | Description |
| -------------- | ------ | ----------- | ---------- |
| cgwTransferFailedMessage | Text string | - | Message streamed to the caller if the call transfer fails. Should be set by the Conversation service on the first turn. |
| cgwConversationFailedMessage | Text string |  - | Message streamed to the caller if the Conversation service fails. Should be set by the Conversation service on the first turn. |

If the Watson Conversation service cannot be reached, the follow configuration environment variables can be used to force a transfer to a static phone number:

| Environment variables | Default value | Description |
| -------------- | -------------------- | --------------------------------------------------------------- |
| CONVERSATION_FAILED_REPLY_MESSAGE | Call being transferred to an agent due to a technical problem. Good bye. | Message streamed to the caller if the Conversation service fails |
| TRANSFER_DEFAULT_TARGET | none | Identifies the target transfer to endpoint. Must be valid SIP or tel URI (e.g. sip:10.10.10.10). This is a default transfer target. Used only when a failure occurs and the call transfer target can't be obtained from the Conversation API |
| TRANSFER_FAILED_REPLY_MESSAGE | Call transfer to an agent failed. Please try again later. Good bye. | Message streamed to the caller if the call transfer fails |

##### Tracking calls by passing GUIDs

Typically, a SIP call coming into an enterprise SBC has a globally unique ID (GUID) added to the SIP signalling as a custom header. The GUID is used to track a call throughout its lifetime within the enterprise network. The voice gateway can be configured to extract the GUID from the initial SIP invite and pass the same GUID as a header in a subsequent REFER message. Passing the GUID ensures a call can be properly tracked as it propagates to a contact center agent.

The following configuration item identifies the custom header that is pulled out of the initial SIP INVITE message that establishes the call:

| Environment variables | Default value | Description |
| -------------- | -------------------- | --------------------------------------------------------------- |
| CUSTOM_SIP_INVITE_HEADER | N/A | When set, the specified SIP header will be passed to the Conversation in the cgwSIPCustomInviteHeader state variable. |

The following state variables can be used by the Watson Conversation service to inform the voice gateway of the custom header to set in the outgoing REFER message:

| State variable name | Expected value  | Default value | Description |
| -------------- | ------ | ----------- | ---------- |
| cgwTransferHeader | User defined | - |Defines a custom header in an outgoing SIP REFER message during a transfer. The custom header value is defined by the cgwSIPTransferHeaderVal state variables. |
| cgwTransferHeaderVal | User defined | - | Defines the value of a custom header in an outgoing SIP REFER message during a transfer. The custom header is defined by the cgwTransferHeader state variables.
