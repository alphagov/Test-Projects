# Callbacks

Received text messages can be forwarded to a URL that you specify, using the callback feature. Messages are forwarded as they are received.

## Set up callbacks

To set up this functionality, you must:

- enable "receive text messages" for your service
- provide a bearer token
- specify the URL

### Enable "Receive text messages" for your service

Contact the GOV.UK Notify team on the [support page](https://www.notifications.service.gov.uk/support) or through the [slack channel](https://govuk.slack.com/messages/C0AC2LX7E) to enable "Receive text messages" for your service.

### Provide a bearer token and a URL

You must provide a bearer token and a URL to Notify:

- the token allows the API notifications client access to your service, and goes in the authorisation header of the callback requests
- the URL specifies where Notify will post the callback to

To provide this information:

1. Log into your GOV.UK Notify account.
1. Go to _API integration_ and click _Callbacks_.
1. Under _Callbacks for received text messages_, click _change_.
1. Complete the _URL_ and _Bearer token_ fields and click _Save_.

You have now set up your service to use Callbacks. There has two functions:

- Text message forwarding
- Message delivery receipt delivery

## Text message forwarding

If your service receives text messages in Notify, Notify can forward them to your callback URL as soon as they arrive.

The callback message is formatted in JSON. The key, description and format of the callback message arguments are specified below:

|Key|Description|Format|
|:---|:---|:---|
|id|Notify’s id for the received message|UUID|
|source_number|The phone number the message was sent from|447700912345|
|destination_number|The number the message was sent to (your number)|07700987654|
|message|The received message|Hello Notify!|
|date_received|The UTC datetime that the message was received by Notify|2017-05-14T12:15:30.000000Z|

_QP: Should we have an example callback message?_

## Message delivery receipts

When you send an email or text message through Notify, Notify will send a receipt to your callback URL with the status of the message. This is an automated method to get the status of messages.  

This functionality works with test API keys but does not work with smoke testing phone numbers or email addresses.
