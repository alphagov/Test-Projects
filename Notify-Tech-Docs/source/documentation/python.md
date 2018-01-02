# Set up the API client

## Download Python and install the client

To install the Python client library, you must first install [Python](https://www.python.org/downloads/). The client supports both Python 3.x and 2.7.

Once Python is installed, run the following code in the command line to install the client library:

```shell
pip install notifications-python-client
```

## Generate an API key

[Register](https://www.notifications.service.gov.uk/register) for a GOV.UK Notify account and log in. If you have any issues logging in, report them at the [GOV.UK Notify support page](https://www.notifications.service.gov.uk/support) or on the GOV.UK Notify [slack channel](https://govuk.slack.com/messages/C0AC2LX7E).

1. Log into your GOV.UK Notify account.
1. Go to API integration and click _API keys_.
1. Click _Create an API key_.
1. Type a name for the key.
1. Select either "Test - pretends to send messages" or "Team or whitelist" for _Type of key_ and click _Continue_.

Your API key will be generated. Make note of this key as you won’t be able to see it again once you leave this page.

Information on API keys can be found [here](/#api-keys).

## Get a template ID

Every notification follows a template. Templates are created by logging into your GOV.UK Notify account, clicking _Templates_ and then clicking the _Add new template_ button.

The API Notification client requires a template ID. Once the notification template has been created, you must copy the template ID from that template:

1. Log into your GOV.UK Notify account.
1. Go to _Templates_ and click on the template you need.
1. Copy the template ID.

## Set up API Notification client

You will now create a new instance of API Notification client. This client will be used for your application.

1. Add the following code to your application:

    ```python
    from notifications_python_client.notifications import NotificationsAPIClient

    notifications_client = NotificationsAPIClient(api_key)
    ```

1. Copy the API key you created into the `api_key` argument.

Once this has been installed you are ready to send a test message.

# Send a message

## Send a text message

### Method

1. Add the following method code to your application code:

    ```python
    response = notifications_client.send_sms_notification(
        phone_number='PHONE NUMBER',
        template_id='TEMPLATE ID',
        personalisation='PERSONALISATION ARGUMENTS',
        reference='REFERENCE',
        sms_sender_id='SMS SENDER ID'
    )
    ```

1. Complete the required `phone_number` and `template_id` arguments.

1. Complete the optional  `personalisation`, `reference` and `sms_sender_id` arguments if required.

1. You are now ready to send a text message notification. Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive the following `dict` response:

```python
{
  "id": "NOTIFICATION ID",
  "reference": "REFERENCE",
  "content": {
    "body": "MESSAGE TEXT",
    "from_number": "GOVUK"
  },
  "uri": "https://api.notifications.service.gov.uk/v2/notifications/NOTIFICATION-ID",
  "template": {
    "id": "TEMPLATE ID",
    "version": 1,
    "uri": "https://api.notifications.service.gov.uk/v2/template/TEMPLATE-ID"
  }
}
```

If you are using the [test API key](/#test), all your messages will come back as delivered.

All successfully delivered messages will appear on your dashboard.

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM/TEST/LIVE of 3000 requests per 60 seconds"`<br>`}]`||
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (LIMIT NUMBER) for today"`<br>`}]`|Refer to [service limits](/#service-limits) for the limit number|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient using a team-only API key"`<br>`]}`||
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`||

#### Arguments

##### Required

`phone_number`

The phone number of the recipient of the text message.

`template_id`

The ID of the template. You can find this by logging into GOV.UK Notify and going to the Templates page.

##### Optional

`reference`

An unique identifier for the notification. This reference can identify a single unique notification or a batch of multiple notifications.

`personalisation`

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
    'first_name': 'Amala',
    'reference_number': '300241',
}
```

`sms_sender_id`

An unique identifier of the sender of the text message notification. To set this up:

1. Log into your GOV.UK Notify account.
1. Go to _Settings_.
1. Check that you are in the correct service; if you are not, click _Switch service_ in the top right corner of the screen and select the correct one.
1. Go to the Text Messages section and click _Manage_ on the "Text Message sender" row.
1. You can either:
  - copy the ID of the sender you want to use and paste it into the method code
  - click _Change_ to change the default sender that the service will use, and click _Save_

If you omit this argument from your method, the default `sms_sender_id` will be set for the notification.

## Send an email

### Method

1. Add the following method code to your application:

    ```python
    response = notifications_client.send_email_notification(
        email_address='TEST EMAIL ADDRESS',
        template_id='TEMPLATE ID'
        personalisation=None,
        reference=None,
        email_reply_to_id=None
    )
    ```

1. Complete the required `email_address` and `template_id` arguments.

1. Complete the optional  `personalisation`, `reference` and `email_reply_to_id` arguments if required.

You are now ready to send an email notification. Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive the following `dict` response:

```python
{
  "id": "NOTIFICATION ID",
  "reference": "REFERENCE",
  "content": {
    "subject": "SUBJECT TEXT",
    "body": "MESSAGE TEXT",
    "from_email": "FROM EMAIL ADDRESS"
  },
  "uri": "https://api.notifications.service.gov.uk/v2/notifications/NOTIFICATION_ID",
  "template": {
    "id": "TEMPLATE_ID",
    "version": 1,
    "uri": "https://api.notifications.service.gov.uk/v2/template/TEMPLATE_ID"
  }
}
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM/TEST/LIVE of 3000 requests per 60 seconds"`<br>`}]`||
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (LIMIT) for today"`<br>`}]`|Refer to [service limits](/#service-limits) for the limit number|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient using a team-only API key"`<br>`]}`||
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`||

#### Arguments

##### Required

`email_address`

The email address of the recipient, only required for email notifications.

`template_id`

The ID of the template. You can find this by logging into GOV.UK Notify and going to the Templates page.

##### Optional

`reference`

An unique identifier for the notification. This reference can identify a single unique notification or a batch of multiple notifications.

`personalisation`

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
    'first_name': 'Amala',
    'reference_number': '300241',
}
```

`email_reply_to_id`

This is an email reply-to address specified by you to receive replies from your users. Your service cannot go live until at least one email address has been set up for this. To set up:

1. Log into your GOV.UK Notify account.
1. Go to _Settings_.
1. Check that you are in the correct service; if you are not, click _Switch service_ in the top right corner of the screen and select the correct one.
1. Go to the Email section and click _Manage_ on the "Email reply to addresses" row.
1. Click _Change_ to specify the email address to receive replies, and click _Save_.

If you omit this argument, your default email reply-to address will be set for the notification.

## Send a letter

When your service first signs up to GOV.UK Notify, you’ll start in trial mode. You can only send letters in live mode.

### Method

1. Add the following method code to your application:

    ```python
    response = notifications_client.send_letter_notification(
        template_id='TEMPLATE_ID',
        personalisation={
          'address_line_1': 'ADDRESS LINE 1 for example The Occupuier',
          'address_line_2': 'ADDRESS LINE 2 for example 123 High Street',
          'postcode': 'POSTCODE for example SW14 6BH',
          ...
        },
        reference=None
    )
    ```

1. Complete the required `template_id` argument.
1. Complete the required `personalisation` arguments (the code example above only includes the required parameters).
1. Complete the optional `personalisation` arguments if required.

You are now ready to send a letter notification. Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive the following `dict` response:

```python
{
  "id": "NOTIFICATION_ID",
  "reference": 'REFERENCE',
  "content": {
    "subject": "SUBJECT TEXT",
    "body": "LETTER TEXT",
  },
  "uri": "https://api.notifications.service.gov.uk/v2/notifications/NOTIFICATION_ID",
  "template": {
    "id": "TEMPLATE_ID",
    "version": 1,
    "uri": "https://api.notifications.service.gov.uk/v2/template/TEMPLATE_ID"
  }
  "scheduled_for": None
}
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM/TEST/LIVE of 3000 requests per 60 seconds"`<br>`}]`||
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (LIMIT) for today"`<br>`}]`|Refer to [service limits](/#service-limits) for the limit number|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Cannot send letters with a team api key"`<br>`]}`||
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Cannot send letters when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`||
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "personalisation address_line_1 is a required property"`<br>`}]`||

#### Arguments

##### Required

`template_id`

The ID of the template. You can find this by logging into GOV.UK Notify and going to the Templates page.

`personalisation`

The personalisation argument always contains the following required parameters for the letter recipient's address:

- `address_line_1`
- `address_line_2`
- `postcode`

The personalisation argument also contains all specified letter template placeholders; these count as required parameters. You need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
  'address_line_1': '123 High Street', # first line of address
  'address_line_2': 'London', # second line of address
  'postcode': 'SW14 6BF', # postcode
  'name': 'John Smith', # name
  'application_id': '4134325', # application identifier
}
```

##### Optional

`reference`

An unique identifier for the notification. This reference can identify a single unique notification or a batch of multiple notifications.

`personalisation`

The personalisation argument contains the following optional parameters for the letter recipient's address:

```python
personalisation={
    'address_line_3': '123 High Street', 	# optional address field
    'address_line_4': 'Richmond upon Thames', 	# optional address field
    'address_line_5': 'London', 		# optional address field
    'address_line_6': 'Middlesex', 		# optional address field
}
```

# Get message status

## Get the status of one message

### Method

1. Add the following method code to your application:

    ```python
    response = notifications_client.get_notification_by_id(notification_id)
    ```

1. Complete the required `notification_id` argument with the ID of the notification that you want the status of.

You are now ready to get the status of the notification. Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive the following `dict` response:

```python
{
  "id": "NOTIFICATION ID", # required
  "reference": "CLIENT REFERENCE", # optional
  "email_address": "EMAIL ADDRESS",  # required for emails
  "phone_number": "PHONE NUMBER",  # required for sms
  "line_1": "Full name of a person or company", # required for letter
  "line_2": "123 The Street", # required for letter
  "line_3": "Some Area", # optional
  "line_4": "Some Town", # optional
  "line_5": "Some county", # optional
  "line_6": "Something else", # optional
  "postcode": "postcode", # required for letter
  "type": "sms|letter|email", # required
  "status": "current status", # required
  "template": {
    "version": 1 # template version num # required
    "id": 1 # template id # required
    "uri": "/v2/template/{id}/{version}", # required
  },
  "body": "Body of the notification",
  "subject": "Subject of an email notification or None if an sms message"
	"created_at": "created at", # required
	"sent_at": " sent to provider at", # optional
	"completed_at:" "date the notification is delivered or failed" # optional
}
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|
|:---|:---|
|`404`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No result found"`<br>`}]`|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "id is not a valid UUID"`<br>`}]`|

</details>

#### Arguments

##### Required

`notification_id`

The ID of the notification.

## Get the status of all messages

This API call returns the status of all messages. You can either get the status of all messages in one call, or one page of up to 250 messages.

### Method

#### All messages

This will return all your messages with statuses; they will be displayed in pages of up to 250 messages each.

Add the following method code to your application:

```python
response = notifications_client.get_all_notifications(template_type, status, reference, older_than)
```

You can filter the returned messages by including the optional arguments in the method code.

Run your application to send a request to API Notification client.

#### One page of up to 250 messages

This will return one page of up to 250 messages and statuses. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the `older_than` argument.

##### Most recent messages

Add the following method code to your application:

```python
response = get_all_notifications_iterator(status="sending")
```

Run your application to send a request to API Notification client.

##### Older messages

To get older messages:

1. Get the ID of an older notification.
1. Add the following code to your application, with the older notification ID in the `older_than` argument.

    ```python
    response = get_all_notifications_iterator(status="sending",older_than="NOTIFICATION ID")
    ```

Run your application to send a request to API Notification client. It will return the next oldest messages from the specified notification ID.

### Response

If the request to the client is successful, you will receive a `dict` response.

#### All messages

```python
{"notifications":
  [
    {
      "id": "notify_id", # required
      "reference": "client reference", # optional
      "email_address": "email address",  # required for emails
      "phone_number": "phone number",  # required for sms
      "line_1": "full name of a person or company", # required for letter
      "line_2": "123 The Street", # required for letter
      "line_3": "Some Area", # optional
      "line_4": "Some Town", # optional
      "line_5": "Some county", # optional
      "line_6": "Something else", # optional
      "postcode": "postcode", # required for letter
      "type": "sms | letter | email", # required
      "status": sending | delivered | permanent-failure | temporary-failure | technical-failure # required
      "template": {
        "version": 1 # template version num # required
        "id": 1 # template id # required
        "uri": "/v2/template/{id}/{version}", # required
      },
      "body": "Body of the notification",
      "subject": "Subject of an email notification or None if an sms message"
      "created_at": "created at", # required
      "sent_at": " sent to provider at", # optional
      "completed_at:" "date the notification is delivered or failed" # optional
    },
    …
  ],
  "links": {
    "current": "/notifications?template_type=sms&status=delivered",
    "next": "/notifications?other_than=last_id_in_list&template_type=sms&status=delivered"
  }
}
```

#### One page of up to 250 messages

```python
<generator object NotificationsAPIClient.get_all_notifications_iterator at 0x1026c7410>
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|
|:---|:---|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "bad status is not one of [created, sending, delivered, pending, failed, technical-failure, temporary-failure, permanent-failure]"`<br>`}]`|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "Apple is not one of [sms, email, letter]"`<br>`}]`|

#### Arguments

##### Optional

You can omit any of these arguments to ignore these filters.

`template_type`

You can filter by:

* email
* sms
* letter

`status`

| status | description | text | email | letter |
|:--- |:--- |:--- |:--- |:--- |
|sending |The message is queued to be sent by the provider|Yes|Yes||
|delivered|The message was successfully delivered|Yes|Yes||
|failed|This will return all failure statuses:<br>- `permanent-failure`<br>- `temporary-failure`<br>- `technical-failure`|Yes|Yes||
|permanent-failure|The provider was unable to deliver message, email or phone number does not exist; remove this recipient from your list|Yes|Yes||
|temporary-failure|The provider was unable to deliver message, email inbox was full or phone was turned off; you can try to send the message again|Yes|Yes||
|technical-failure|Email / Text: Notify had a technical failure; you can try to send the message again. <br><br>Letter: Notify had an unexpected error while sending to our printing provider. <br><br>You can omit this argument to ignore this filter.|Yes|Yes||
|accepted|Notify is printing and posting the letter|||Yes|

`reference`

An unique identifier for the notification. This reference can identify a single unique notification or a batch of multiple notifications.

`older_than`

Input the ID of a notification into this argument. If you use this argument, the next 250 received notifications older than the given ID are returned.

If this argument is omitted, the most recent 250 notifications are returned.

# Get a template

## Get a template by ID

### Method

This will return the latest version of the template.

Add the following method code to your application:


```python
response = notifications_client.get_template(
  'template_id'
)
```

Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive a `dict` response.

```python
{
    "id": "template_id", # required
    "type": "sms" | "email" | "letter", # required
    "created_at": "created at", # required
    "updated_at": "updated at", # required
    "version": "version", # integer required
    "created_by": "someone@example.com", # email required
    "body": "Body of the notification", # required
    "subject": "Subject of an email or letter notification or None if an sms message"
}
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|
|:---|:---|
|`404`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No Result Found"`<br>`}]`|


#### Arguments

`template_id`

The ID of the template. You can find this by logging into GOV.UK Notify and going to the Templates page.

## Get a template by ID and version

### Method

This will return the latest version of the template.

Add the following method code to your application:

```python
response = notifications_client.get_template_version(
    'template_id',
    `version` # integer required for version number
)
```

Run your application to send a request to API Notification client.


### Response

If the request to the client is successful, you will receive a `dict` response.

```python
{
    "id": "template_id", # required
    "type": "sms" | "email" | "letter", # required
    "created_at": "created at", # required
    "updated_at": "updated at", # required
    "version": "version", # integer required
    "created_by": "someone@example.com", # email required
    "body": "Body of the notification", # required
    "subject": "Subject of an email or letter notification, or None if an sms message"
}
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|
|:---|:---|
|`404`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No Result Found"`<br>`}]`|

#### Arguments

`template_id`

The ID of the template. You can find this by logging into GOV.UK Notify and going to the Templates page.

`version`

The version number of the template.


## Get all templates

### Method

This will return the latest version of all templates.

Add the following method code to your application:

```python
response = notifications_client.get_all_templates(
    template_type=None # optional
)
```

Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive a `dict` response.

```python
{
    "templates": [
        {
            "id": "template_id", # required
            "type": "sms" | "email" | "letter", # required
            "created_at": "created at", # required
            "updated_at": "updated at", # required
            "version": "version", # integer required
            "created_by": "someone@example.com", # email required
            "body": "Body of the notification", # required
            "subject": "Subject of an email or letter notification, or None if an sms message"
        },
        {
            ... another template
        }
    ]
}
```

If no templates exist for a template type or there no templates for a service, you will receive a `dict` response with an empty `templates` list element:

```python
{
    "templates": []
}
```

### API Reference

#### Arguments

`template_type`

If omitted all templates are returned. Otherwise you can filter by:

- email
- sms
- letter

## Generate a preview template

### Method

This will generate a preview version of a template.

Add the following method code to your application:

```Python
response = notifications_client.post_template_preview(
    'template_id',
    personalisation={'name': 'chris'}
)
```
The parameters in the personalisation argument must match the placeholder fields in the actual template. Any extra fields in the method code are ignored by the API notification client.

Run your application to send a request to API Notification client

### Response

If the request to the client is successful, you will receive a `dict` response.

```python
{
    "id": "notify_id", # required
    "type": "sms" | "email" | "letter", # required
    "version": "version", # integer required
    "body": "Body of the notification", # required
    "subject": "Subject of an email or letter notification, or None if an sms message"
}
```

### API Reference

#### Error

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|
|:---|:---|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Missing personalisation: [PERSONALISATION FIELD]"`<br>`}]`|
|`400`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No result found"`<br>`}]`|

#### Arguments

`template_id`

The ID of the template. You can find this by logging into GOV.UK Notify and going to the Templates page.

`personalisation`

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values in a dictionary with key value pairs. For example:

```python
personalisation={
    'first_name': 'Amala',
    'reference_number': '300241',
}
```

# Get received text messages

This API call returns received text messages. Depending on which method you use, you can either get all received text messages, or a page of up to 250 text messages.

## Get all received text messages

This method will return a `<generator object>` with all received text messages.

### Method

To return all received text messages, add the following method code to your application:

```python
response = get_received_texts_iterator()
```

Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive a `<generator object>` response that will return all received texts.

```python
<generator object NotificationsAPIClient.get_received_texts_iterator at 0x1026c7410>
```


## Get one page of received text messages

This will return one page of up to 250 text messages.  

### Method

Add the following method code to your application:

```python
response = client.get_received_texts(older_than)
```

You specify which texts to receive by inputting the ID of a received text message into the `older_than` argument.

Run your application to send a request to API Notification client.

### Response

If the request to the client is successful, you will receive a `dict` response.


```python
{
  "received_text_messages":
  [
    {
      "id": "ID of the received text message", # required
      "user_number": "user number", # required user number
      "notify_number": "notify number", # receiving number
      "created_at": "created at", # required
      "service_id": "service id", # required service id
      "content": "text content" # required text content
    },
    …
  ],
  "links": {
    "current": "/received-text-messages",
    "next": "/received-text-messages?other_than=last_id_in_list"
  }
}
```

### API reference

#### Arguments

##### Optional

`older_than`

Input the ID of a received text message into this argument. If you use this argument, the next 250 received text messages older than the given ID are returned.

If this argument is omitted, the most recent 250 text messages are returned.
