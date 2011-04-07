# Sim Toolkit Application (STK) #

[TOC]

This document offers a high-level overview of the SIM Toolkit Application for Android 1.0 and is primarily of interest for implementors of the Radio Interface Layer (RIL). The STK is  R96 compatible (3GPP TS 11.14 v5.9.0) and complies partially with R99 (3GPP TS 101.267 v8.17.0). See the [STK Feature List](#androidSTKFeatureList) for the complete feature list. 

The Android STK implementation includes three layers:

- STK RIL: Low-level layer provided by the vendor plus `libril`. 

- STK Telephony: Protocol translation layer that converts raw messages provided by the STK RIL to application level messages.

- STK Application: Provides the user interface interactions needed by the STK.
 
<img src="/images/stk.gif" alt="STK schema" width="566" height="516" />

The Sim Toolkit communication flow is bi-directional and commands can originate from the `STK RIL` packaged in `RIL_UNSOL_STK_PROACTIVE_COMMAND`, `RIL_UNSOL_STK_EVENT_NOTIFY`, `RIL_UNSOL_REFRESH` and `RIL_UNSOL_STK_SESSION_END` messages.
 
Commands originating from the STK App or STK Telephony layers are packaged in `RIL_REQUEST_STK_SEND_TERMINAL_REPSONSE`, `RIL_REQUEST_SEND_ENVELOPE_COMMAND` or `RIL_REQUEST_HANDLE_CALL_SETUP_REQUESTED_FROM_SIM`.

For commands sent or recieved by the STK RIL, it's not necessary to remove unused sub-commands because the upper layers will gracefully ignore unused information contained in the message. The content of each message is encoded in the BER-TLV format except for two:

- `RIL_UNSOL_STK_SESSION_END`: has no data  

- `RIL_REQUEST_HANDLE_CALL_SETUP_REQUESTED_FROM_SIM`: has a single byte of data that indicates accept/reject.

See the [Android Platform Development Kit](http://pdk-docs.prom.corp.google.com/docs/telephony.html) for details. 
 
## Communication from the RIL up ##

Communication to the upper layers is done using the `RIL_UNSOL_xxx` family of commands defined in `/hardware/ril/include/ril.h`. The payload for each command is encoded using BER-TLV. As stated above, it is not necessary to remove extraneous fields. This allows the SIM to pass a complete command to the upper layers and simplifies STK RIL code. Below is the mapping from RIL_UNSOL_xxx commands to the SIM commands.

### RIL_UNSOL_STK_PROACTIVE_COMMAND ###
 
The Sim Toolkit communication flow is bi-directional and commands can originate from the `STK RIL` packaged in `RIL_UNSOL_STK_PROACTIVE_COMMAND`, `RIL_UNSOL_STK_EVENT_NOTIFY`, `RIL_UNSOL_REFRESH` and `RIL_UNSOL_STK_SESSION_END` messages.
 
#### DISPLAY TEXT (6.4.1) ####
 
This command is handled by `StkDialogActivity`. If the immediate response flag is set, the terminal response is sent with an "OK" `(0x00)` result code when the message is first received. Otherwise the terminal response is sent when the dialog is dismissed either from user action or it times out.

The user sees a dialog box with a message and / or icon, an OK button, and a Cancel button.

Terminal responses include: 
- "OK" (0x00)
- "Session terminated by user" (0x10)
- "Backward move by user" (0x11)
- "No response from user" (0x12)

<img src="/images/stk_display_text.gif" border="1"> 
  
#### GET IN KEY (6.4.2) ####
 
This command is processed by `StkInputActivity`. It displays a dialog box with a prompt and or an icon and yes and no buttons.
 
Terminal responses include:
 
- "OK" (0x00) with "Yes" or "No" as the response text 
- "Session terminated by user" (0x10) << Currently not supported 
- "Backward move by user" (0x11) 
- "No response from user" (0x12) 
 
#### GET INPUT (6.4.3) ###
 
This command is handled by `StkInputActivity`.
 
The user sees a dialog box with a prompt and/or an icon, a text box with optional default text, and an OK button. 'digits only,' 'min,' 'max,' and 'hidden' modes are all supported.

Terminal responses include:
	 
- "OK" (0x00) if the input criteria is met	 
- "Session terminated by user" (0x10) << Currently not supported	 
- "Backward move by user" (0x11)         
- "No response from user" (0x12)  

#### PLAY TONE (6.4.5) ####
 
This command is processed by `ToneDialog`.
 
The user sees a dialog box with a prompt for the duration of the tone or until the user presses the back key.
 
Terminal responses include:
	 
- "OK" (0x00) if the tone is played to completion	 
- "Session terminated by user" (0x10)  if back button is pressed  
 
#### SET UP MENU (6.4.8) ####
 
This command is processed by `StkAppService`. It installs the STK ICON on the home page and prepares the Main menu for use when the ICON is selected. Typically this is the first RIL unsolicited command and should not be sent until after the `RIL_RadioFunctions.getVersion` routine is called.
 
Terminal reponses:
 
- "OK" (0x00)  
 
At a later time, the STK ICON will be selected and `StkMenuActivity` will launch and display the Main menu to the user. If an item is selected, it will be returned to the RIL in `RIL_REQUEST_STK_SEND_ENVELOPE_COMMAND` with the item id. If no item is selected, the `StkMenuActivity` will receive a timeout message and return to the Main menu and no message will be sent to the RIL.
 
Depending upon what item is selected, the SIM may perform another action, such as sending another set of menu items via a SELECT ITEM command or performing an action like send an SMS or text display. 
 
#### SELECT ITEM (6.4.9) ####
 
This command is processed by `StkMenuActivity`. Instead of displaying the Main menu, the list of menu items provided in this command are displayed.
 
Terminal responses:
 
- "OK" (0x00)     
 
The command then proceeds as in the SETUP MENU. 
 
#### SET UP IDLE MODE TEXT (6.4.22) ####
 
`StkAppService` displays the message and or icon as an Android notification.
 
Terminal responses include:
 
- "OK" (0x00) 

#### LAUNCH BROWSER (6.4.26) ####
 
This command is initially handled by `StkDialogActivity` and presents the user with a confirmation dialog, a URL, and OK, and cancel buttons. If the user presses OK, the browser is launched using the URL.
 
Terminal responses include:
 
- "OK" (0x00) the browser has been launched	     
- "Session terminated by user" (0x10)  
- "Backward move by user" (0x11) 
- "No response from user" (0x12) 
 
### RIL_UNSOL_STK_EVENT_NOTIFY ###
 
The commands in this section are proactive in nature. They are handled by the STK RIL and the upper layers and delivered using `RIL_UNSOL_STK_EVENT_NOTIFY` message. This distinction is an implementation detail of Android and is not defined in the 3GPP sepcifications.
 
The upper layers handle the UI and the STK RIL handles all other aspects of each command, which means that the STK RIL sends the terminal response (it is never sent by the STK App). Each command must be a properlery-formed proactive command. It is not necessary to remove unused fields. The behavior of any other command <em>within this context </em>sent by `RIL_UNSOL_STK_EVENT_NOTIFY` is undefined. See Event Notify Command Details. 
 
- SEND SMS (6.4.10) – no response 
- SEND SS (6.4.11) – no response 
- SEND USSD (6.4.12) – no response 
- SET UP CALL (6.4.13) – responds with `RIL_REQUEST_STK_HANDLE_CALL_SETUP_REQUESTED_FROM_SIM` 
- SEND DTMF (6.4.24) – no response 
 
<img src="/images/stk_send_SMS.gif"> 
 
### RIL_UNSOL_SIM_REFRESH ###
 
Used to send the `REFRESH` command. No response.
 
### REFRESH (6.4.7) ###
 
`RIL_UNSOL_STK_SESSION_END`
 
Sent by the SIM to signal the end of a session. No content and no response.
 
<img src="/images/stk_refresh_init.gif">

<img src="/images/stk_refresh_update.gif"> 

<img src="/images/stk_refresh_reset.gif"> 
 
### RIL_UNSOL_STK_SESSION_END ###
 
Sent by SIM to signal end of session. No content and no response.
 
## Communication from the application down ##
 
Communication from the upper layers to the RIL uses the commands below (defined in `ril.h`)
 
### RIL_REQUEST_STK_SEND_TERMINAL_REPSONSE ###
 
Used to send a terminal response for commands sent via `RIL_UNSOL_STK_PROACTIVE_COMMAND`. 

Contents include TERMINAL RESPONSE (6.8).
 
### RIL_REQUEST_STK_SEND_ENVELOPE_COMMAND ###
 
Used to send information from to SIM encoded as BER-TLV tags (see R96  section 13). Two tags are supported:
 
- BER_MENU_SELECTION_TAG (0xd3); Contents: MENU SELECTION (8) 
- BER_EVENT_DOWNLOAD_TAG (0xd6); Contents: EVENT DOWNLOAD Language selection (11.8) 
 
Content for these tags include:
 
- MENU SELECTION (8) 
- EVENT DOWNLOAD Language selection (11.8) 

### RIL_REQUEST_HANDLE_CALL_SETUP_REQUESTED_FROM_SIM ###
 
Returns a single-byte parameter:
 
- 1: accepted 
- 0: not accepted 
 
This should cause the terminal response to be sent to the SIM.
 
## Telephony ##
 
STK Telephony is a protocol translation layer used to convert messages from BERL-TLV format to Application messages and back. 
 
On the STK RIL side, STK Telphony receives raw buffer messages from RIL I/F and decodes them into a command parameters container before passing them on to the application. On the Application side, this layer receives application responses for commands and encodes them into raw buffer messages, which then get sent over the RIL I/F back to the SIM card.
 
## Feature List ##
 
Feature     | Support | Implemented by
------------|---------|---------------
PROFILE DOWNLOAD - before radio turn-on | YES | Baseband
SET UP MENU | YES    | ME
SELECT ITEM | YES    | ME
GET INPUT   | YES    | ME
GET INKEY   | YES    | ME
DISPLAY TEXT| YES    | ME
SET UP IDLE MODE TEXT | YES | ME
SEND SHORT MESSAGE | YES | Baseband – ME
SEND        | YES    | Baseband – ME
SEND USSD   | YES    | Baseband – ME
SEND DTMF   | YES    | Baseband – ME
LAUNCH BROWSER | YES | ME
SET UP CALL | YES    | Baseband – ME
PLAY TONE   | YES    | ME
POLL INTERVAL | YES  | Baseband
POLLING OFF | YES    | Baseband
TIMER MANAGEMENT | YES | Baseband
MORE TIME   | YES    | Baseband
PROVIDE LOCAL INFORMATION (MCC, MNC, LAC, Cell ID & IMEI) | YES | Baseband
PROVIDE LOCAL INFORMATION (NMR) | YES | Baseband
PROVIDE LOCAL INFORMATION (Timing Advance) | YES | Baseband
PROVIDE LOCAL INFORMATION (battery state) | YES | Baseband
PROVIDE LOCAL INFORMATION (IMEISV) | YES | Baseband
PROVIDE LOCAL INFORMATION (NMR(UTRAN)) | YES | Baseband
PROVIDE LOCAL INFORMATION (Search Mode change) | YES | Baseband
REFRESH     | YES    | ME
SET UP EVENT LIST | YES | Baseband – ME
Event: MT call | YES | Baseband
Event: Call connected (all modes) | YES | Baseband
Event: Call disconnected (all modes) | YES | Baseband
Event: Idle screen available | NO 
Event: Browser termination | NO
Event: Location status | YES | Baseband
Event: Data available | NO 
Event: Channel status | NO
Event: Access Technology changed | YES | Baseband
Event: Local Connection | NO 
Event: Network Search Mode Change | YES | Baseband
GET READER STATUS | NO
POWER ON CARD | NO 
POWER OFF CARD | NO
PERFORM CARD ADPU | NO
RUN AT COMMAND | NO
OPEN CHANNEL | NO
CLOSE CHANNEL | NO
RECEIVE DATA | NO
SEND DATA | NO
GET CHANNEL STATUS | NO
CALL CONTROL BY SIM | YES | Baseband
SMS-PP data download | YES | Baseband
SMS-CB data download | YES | Baseband
BIP over GPRS | NO
BIP over USB | NO
Text Attributes | NO
Color icons | YES | ME

