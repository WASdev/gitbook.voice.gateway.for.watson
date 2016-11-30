# API for self-service agents

Voice Gateway for Watson is controlled through state variables that are exchanged with the configured Watson Conversation service.
* **To the gateway:** The Conversation service sends state variables to initiate various actions in the voice gateway.
* **From the gateway:** Based on Docker environment variables in the configuration, the voice gateway sends various state variables to the Conversation service to pass various headers extracted from the call signaling.

The voice gateway assumes that the Conversation service is stateless and that all state is maintained at the voice gateway between exchanges with the Conversation service. This means that for each Conversation "turn" within a single phone call, the state is passed to the conversation and received back from the Conversation in the context section of the REST messages.

The following tables outline all the state variables that are supported by the voice gateway.
* [Variables sent from the Conversation service to the voice gateway](#variables-sent-from-the-conversation-service-to-the-voice-gateway)
* [Variables sent from the voice gateway to the Conversation service](#variables-sent-from-the-voice gateway-to-the-conversation-service)


### Variables sent from the Conversation service to the voice gateway

| State variable name | Expected value | Description | Default |
| -------------- | ------ | ----------- | ---------- |
| cgwHangUp | Yes / No| Informs the voice gateway to hangup the call after the included text response is played back to the caller.| - |
| cgwTransfer | Yes / No | Informs the voice gateway to initiate a transfer after the included text response is played back to the caller.| - |
| cgwTransferTarget | SIP or telephone URI | Identifies the transfer to endpoint (e.g. tel:+18883334444). | - |
| cgwTransferHeader | User defined | Defines a custom header in an outgoing SIP REFER message during a transfer. The custom header value is defined by the cgwSIPTransferHeaderVal state variables. | - |
| cgwTransferHeaderVal | User defined | Defines the value of a custom header in an outgoing SIP REFER message during a transfer. The custom header is defined by the cgwTransferHeader state variables. | - |
| cgwPreResponseTimeoutCount | Time in ms | Forces a wait for a new utterance before playing back the response. Can be used to collect DTMF digits or a multi-utterance response. | - |
| cgwPostResponseTimeoutCount | Time in ms | Time to wait for a new utterance after the response is played back to the caller. If timeout occurs the conversation will receive a text update with the word "cgwPostResponseTimeout" to indicate a timeout occurred.| - |
| cgwPauseSTT | Yes / No | Informs the voice gateway to pause STT processing. When applied, the same value is used in all subsequent transactions, unless a new request arrives that overrides the existing value. | No |
| cgwAllowBargeIn | Yes / No | Informs the voice gateway whether or not barge-in is allowed. When applied, the same value is used in all subsequent transactions, unless a new request arrives that overrides the existing value | Yes |
| cgwAllowDTMF | Yes / No | Informs the voice gateway whether or not DTMF is allowed. When applied, the same value is used in all subsequent transactions, unless a new request arrives that overrides the existing value. When set to 'No', DTMFs are ignored| Yes |
| cgwForceNoInputTurn | Yes / No | Force a new turn with no input from the CMR. This is needed for very long turns when the conversation wishes to turn on MusicOnHold before initiating the long transaction.| No |
| cgwMusicOnHoldURL | URL | A Music On Hold URL that should be started as soon as the included text is played back. | - |
| cgwTransferFailedMessage | Text string | Message streamed to the caller if the call transfer fails. Should be set by the Conversation on the first turn. | - |
| cgwConversationFailedMessage | Text string | Message streamed to the caller if the Conversation service fails. Should be set by the Conversation on the first turn. | - |
| cgwSessionInactivityTimeout| Time in min | Inactivity timeout interval. The timeout interval value specifies in minutes how long the voice gateway allows a session to be inactive. When the timeout expires, the voice gateway terminates a session . | 2 min |


### Variables sent from the voice gateway to the Conversation service

Each of these state variables are associated with Docker environment variables in the [voice gateway configuration](config.md).

**Important:** Be sure to set the specified environment variable in the configuration so that the voice gateway passes the state variable to the Conversation service.

**??? What about those that don't have a related environment variable?**

| State variable name | Expected value | Description | Related Docker env variable |
| -------------- | ----- | ----------- |
| cgwSessionID   | User defined | Custom session ID header pulled from SIP invite. Represents the global session ID used in all voice gateway audit logs related to the session. | CUSTOM_SIP_SESSION_HEADER |
| cgwSIPCallID | SIP Call-ID | The SIP call ID associated with the Conversation. | SEND_SIP_CALL_ID_TO_CONVERSATION |
| cgwSIPRequestURI | SIP Request URI | The SIP request URI that started the Conversation. | SEND_SIP_REQUEST_URI_TO_CONVERSATION |
| cgwSIPToURI | SIP To URI | The SIP To URI associated with the Conversation.| SEND_SIP_TO_URI_TO_CONVERSATION |
| cgwSIPFromURI | SIP From URI | The SIP From URI associated with the Conversation.| SEND_SIP_FROM_URI_TO_CONVERSATION |
| cgwBargeInOccurred | Yes / No | Indicates whether or not barge-in occurred |-|
| cgwHangUp | Yes / No | Sent to conversation when a hangup is initiated by the caller or due to an error. The text in the ask will also include cgwHangUp. | -|
| cgwSIPCustomInviteHeader | User defined | This is a user defined SIP header that will be pulled from the initial SIP INVITE and passed to the Conversation. | CUSTOM_SIP_INVITE_HEADER |
| cgwTextAlternatives | JSON array (CONV-V1) or XML array (DIALOG_V1) | The confidence level for the top hypothesis and any alternatives. <p>The format matches exactly the format received from Watson STT when CONV-V1 is used:</p><p>[{"transcript":"hello there my name is Jose matter eco","confidence":0.758},{"transcript":"hello there my name is Jose matter Pico"},{"transcript":"hello there my name is Jose matter ego"},{"transcript":"hello there my name is Jose matter peco"}].</p><p> An XML array is sent when DIALOG-V1 is used:</p><p> [\<transcript\>one two three \</transcript\>\<confidence\>0.87\</confidence\>][\<transcript\>one to three \</transcript\>][\<transcript\>one two thirty \</transcript\>][\<transcript\>one to thirty \</transcript\>]</p> |-|
