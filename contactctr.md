# Integrating the voice gateway with a contact center

The voice gateway can integrate with existing contact centers through an enterprise Session Border Controller (SBC). The SBC handles the following call routing scenarios:

* Inbound calls to the voice gateway from a caller connecting through a SIP Trunk.
* Calls redirected to the voice gateway from an enterprise Interactive Voice Response (IVR) system.
* Calls redirected from the voice gateway to a contact center or IVR system.

When using the voice gateway to host a self-service agent, there will be cases when a caller will wish to "opt-out" of the call and be transferred to a live contact center agent. The voice gateway relies on standard SIP transfer procedures to redirect a call to a contact center using an "in-dialog" SIP REFER message.

Its important to understand that there must be some entity in the call path that acts as a "pivot-point" or "anchor-point" for the call which can catch the SIP REFER and handle the redirect. This call anchor point is typically the Session Border Controller that interfaces with an external SIP trunk and forwards calls to the voice gateway. Enterprise SBCs can typically handle SIP REFER messages received over an existing SIP dialog and use that message to redirect an existing call to the contact center in the enterprise network.

The diagram below describes in more detail the typical flow of messages related to both setting up a new call through an Enterprise SBC with a self-service agent hosted by a voice gateway as well as a transfer out to a contact center:

![](images/call-transfer.png)

Here is a description of each step in this flow:

1. Call arrives at the SBC via SIP Trunk
1. Call routed to the Voice Gateway, SBC stays in the call-signaling path.
1. New Conversation established and caller initiates turns with the Conversation through voice.
1. Transfer initiated from Conversation on last turn.
1. The Voice Gateway initiates a transfer back to the SBC by sending an in-dialog SIP REFER.
1. SBC re-routes call to Contact Center ACD.
1. Eventually the call is routed to the Agent.

Initiation of the call transfer is completely controlled by the Conversation through the following state variables:

| state var name | value | description | default |
| -------------- | ------ | ----------- | ---------- |
| cgwTransfer | Yes | Informs the CGW to initiate a transfer after the included text response is played back to the caller| - |
| cgwTransferTarget | sip or tel URI | Identifies the transfer to endpoint (e.g. tel:+18883334444) | - |

The voice gateway can also be configured to automatically transfer to a static endpoint if a failure occurs. This can happen for instance if the voice gateway loses connectivity to one of the configured Watson services. First, this state variables can be set on the first turn of the conversation and will be used if there is a failure of some kind at any point during the call:

| state var name | value | description | default |
| -------------- | ------ | ----------- | ---------- |
| cgwTransferFailedMessage | text string | Message streamed to the caller if the call transfer fails. Should be set by the Conversation on the first turn. | - |
| cgwConversationFailedMessage | text string | Message streamed to the caller if the Conversation service fails. Should be set by the Conversation on the first turn. | - |

If for some reason, the Watson Conversation service can not be reached, the follow configuration environment variables can be used to force a transfer to a static phone number:

| env var | default              | details |
| -------------- | -------------------- | --------------------------------------------------------------- |
| CONVERSATION_FAILED_REPLY_MESSAGE | Call being transferred to an agent due to a technical problem. Good bye. | Message streamed to the caller if the Conversation service fails |
| TRANSFER_DEFAULT_TARGET | none | Identifies the target transfer to endpoint. Must be valid SIP or tel URI (e.g. sip:10.10.10.10). This is a default transfer target. Used only when a failure occurs and the call transfer target can't be obtained from the Conversation API |
| TRANSFER_FAILED_REPLY_MESSAGE | Call transfer to an agent failed. Please try again later. Good bye. | Message streamed to the caller if the call transfer fails |

Typically, a SIP call coming into an enterprise SBC will have a globally unique ID (GUID) added to the SIP signalling as a custom header which is used to track a call throughout its lifetime within the enterprise network. The voice gateway can be configured to extract the GUID from the initial SIP invite and pass the same GUID as a header in a subsequent REFER message to insure a call can be properly tracked as it propagates to a contact center agent.

This configuration item identifies the custom header that will be pulled out of the initial SIP INVITE message that establishes the call:

| env var | default              | details |
| -------------- | -------------------- | --------------------------------------------------------------- |
| CUSTOM_SIP_INVITE_HEADER | n/a | When set, the specified SIP header will be passed to the Conversation in this state variable: cgwSIPCustomInviteHeader |

And these state variables can be used by the Watson Conversation to inform the voice gateway of the custom header to set in the outgoing REFER message:

| state var name | value | description | default |
| -------------- | ------ | ----------- | ---------- |
| cgwTransferHeader | user defined | Defines a custom header in an outgoing SIP REFER message during a transfer. The custom header value is defined by the cgwSIPTransferHeaderVal state variables | - |
| cgwTransferHeaderVal | user defined | Defines the value of a custom header in an outgoing SIP REFER message during a transfer. The custom header is defined by the cgwTransferHeader state variables | - |
