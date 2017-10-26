# GOV.UK Notify Node.js client

This documentation is for developers interested in using this Node.js client to integrate their government service with GOV.UK Notify.

## Installation

```shell
npm install --save notifications-node-client
```

## Getting started

```javascript
var NotifyClient = require('notifications-node-client').NotifyClient,
	notifyClient = new NotifyClient(apiKey);
```

Generate an API key by logging in to [GOV.UK Notify](https://www.notifications.service.gov.uk) and going to the _API integration_ page.

### Connect through a proxy (optional)

```
notifyClient.setProxy(proxyUrl);
```

## Send messages

### Text message

#### Method 

<details>
<summary>
Click here to expand for more information.
</summary>

```javascript
notifyClient
	.sendSms(templateId, phoneNumber, personalisation, reference)
	.then(response => console.log(response))
	.catch(err => console.error(err))
;
```

</details>

#### Response

If the request is successful, `response` will be an `object`.
<details>
<summary>
Click here to expand for more information.
</summary>

```javascript
{
    "id": "bfb50d92-100d-4b8b-b559-14fa3b091cda",
    "reference": null,
    "content": {
        "body": "Some words",
        "from_number": "40604"
    },
    "uri": "https://api.notifications.service.gov.uk/v2/notifications/ceb50d92-100d-4b8b-b559-14fa3b091cd",
    "template": {
        "id": "ceb50d92-100d-4b8b-b559-14fa3b091cda",
       "version": 1,
       "uri": "https://api.notifications.service.gov.uk/v2/templates/bfb50d92-100d-4b8b-b559-14fa3b091cda"
    }
}
```

Otherwise the client will return an error `err`:

|`err.error.status_code`|`err.error.errors`|
|:---|:---|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM of 10 requests per 10 seconds"`<br>`}]`|
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (50) for today"`<br>`}]`|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient using a team-only API key"`<br>`]}`|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`|

</details>

#### Arguments

<details>
<summary>
Click here to expand for more information.
</summary>

##### `phoneNumber`

The phone number of the recipient, only required for sms notifications.

##### `templateId`

Find by clicking **API info** for the template you want to send.

##### `reference`

An optional identifier you generate. The `reference` can be used as a unique reference for the notification. Because Notify does not require this reference to be unique you could also use this reference to identify a batch or group of notifications.

You can omit this argument if you do not require a reference for the notification.

##### `personalisation`

If a template has placeholders, you need to provide their values, for example:

```javascript
personalisation={
    'first_name': 'Amala',
    'reference_number': '300241',
}
```

Otherwise the parameter can be omitted or `undefined` can be passed in its place.

</details>


### Email

#### Method

<details>
<summary>
Click here to expand for more information.
</summary>

```javascript
notifyClient
	.sendEmail(templateId, emailAddress, personalisation, reference, emailReplyToId)
    .then(response => console.log(response))
    .catch(err => console.error(err))
;
```

</details>


#### Response

If the request is successful, `response` will be an `object`.

<details>
<summary>
Click here to expand for more information.
</summary>

```javascript
{
    "id": "bfb50d92-100d-4b8b-b559-14fa3b091cda",
    "reference": null,
    "content": {
        "subject": "Licence renewal",
        "body": "Dear Bill, your licence is due for renewal on 3 January 2016.",
        "from_email": "the_service@gov.uk"
    },
    "uri": "https://api.notifications.service.gov.uk/v2/notifications/ceb50d92-100d-4b8b-b559-14fa3b091cd",
    "template": {
        "id": "ceb50d92-100d-4b8b-b559-14fa3b091cda",
        "version": 1,
        "uri": "https://api.notificaitons.service.gov.uk/service/your_service_id/templates/bfb50d92-100d-4b8b-b559-14fa3b091cda"
    }
}
```
Otherwise the client will return an error `err`:

|`err.error.status_code`|`err.error.errors`|
|:---|:---|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM of 10 requests per 10 seconds"`<br>`}]`|
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (50) for today"`<br>`}]`|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient using a team-only API key"`<br>`]}`|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can"t send to this recipient when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`|

</details>


#### Arguments

???
<details>
<summary>
Click here to expand for more information.
</summary>

???
</details>


### Letter

#### Method

<details>
<summary>
Click here to expand for more information.
</summary>

```javascript
notifyClient
    .sendLetter(templateId, personalisation, reference)
    .then(response => console.log(response))
    .catch(err => console.error(err))
;
```

where `personalisation` is

```javascript
personalisation={
    'address_line_1': 'The Occupier',  # required
    'address_line_2': '123 High Street', # required
    'address_line_3': 'London',
    'postcode': 'SW14 6BH',  # required

    ... # any other optional address lines, or personalisation fields found in your template
},
```
</details>


#### Response

If the request is successful, `response` will be an `object`:
<details>
<summary>
Click here to expand for more information.
</summary>

```javascript
{
  "id": "740e5834-3a29-46b4-9a6f-16142fde533a",
  "reference": null,
  "content": {
    "subject": "Licence renewal",
    "body": "Dear Bill, your licence is due for renewal on 3 January 2016.",
  },
  "uri": "https://api.notifications.service.gov.uk/v2/notifications/740e5834-3a29-46b4-9a6f-16142fde533a",
  "template": {
    "id": "f33517ff-2a88-4f6e-b855-c550268ce08a",
    "version": 1,
    "uri": "https://api.notifications.service.gov.uk/v2/template/f33517ff-2a88-4f6e-b855-c550268ce08a"
  }
  "scheduled_for": null
}
```

Otherwise the client will raise a `HTTPError`:

|`error.status_code`|`error.message`|
|:---|:---|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type live of 10 requests per 20 seconds"`<br>`}]`|
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (50) for today"`<br>`}]`|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Cannot send letters with a team api key"`<br>`]}`|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Cannot send letters when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "personalisation address_line_1 is a required property"`<br>`}]`|

</details>


#### Arguments

<details>
<summary>
Click here to expand for more information.
</summary>

##### `template_id`

Find by clicking **API info** for the template you want to send.

##### `reference`

An optional identifier you generate. The `reference` can be used as a unique reference for the notification. Because Notify does not require this reference to be unique you could also use this reference to identify a batch or group of notifications.

You can omit this argument if you do not require a reference for the notification.

##### `personalisation`

The letter must contain:

- mandatory address fields
- optional address fields if applicable
- fields from template

```javascript
personalisation={
    'address_line_1': 'The Occupier', 		# mandatory address field
    'address_line_2': 'Flat 2', 		# mandatory address field
    'address_line_3': '123 High Street', 	# optional address field
    'address_line_4': 'Richmond upon Thames', 	# optional address field
    'address_line_5': 'London', 		# optional address field
    'address_line_6': 'Middlesex', 		# optional address field
    'postcode': 'SW14 6BH', 			# mandatory address field
    'application_id': '1234', 			# field from template
    'application_date': '2017-01-01', 		# field from template
}
```

##### `emailAddress`
The email address of the recipient, only required for email notifications.

##### `templateId`

Find by clicking **API info** for the template you want to send.

##### `reference`

An optional identifier you generate. The `reference` can be used as a unique reference for the notification. Because Notify does not require this reference to be unique you could also use this reference to identify a batch or group of notifications.

You can omit this argument if you do not require a reference for the notification.

##### `emailReplyToId`

Optional. Specifies the identifier of the email reply-to address to set for the notification. The identifiers are found in your service Settings, when you 'Manage' your 'Email reply to addresses'. 

If you omit this argument your default email reply-to address will be set for the notification.

If other optional arguments before `emailReplyToId` are not in use they need to be set to `undefined`.

Example usage with optional reference -

```
sendEmail('123', 'test@gov.uk', undefined, 'your ref', '465')
```

Example usage with optional personalisation -

```
sendEmail('123', 'test@gov.uk', '{"name": "test"}', undefined, '465')
```

Example usage with only optional `emailReplyToId` set -

```
sendEmail('123', 'test@gov.uk', undefined, undefined, '465')
```

##### `personalisation`

If a template has placeholders, you need to provide their values, for example:

```javascript
personalisation={
    'first_name': 'Amala',
    'application_number': '300241',
}
```

Otherwise the parameter can be omitted or `undefined` can be passed in its place.

</details>


## Get the status of one message

#### Method

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Response

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>

#### Arguments

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>

## Get the status of all messages (with pagination)

#### Method

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Response

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>



#### Arguments

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


## Get the status of all messages (without pagination)

#### Method

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>

#### Response

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Arguments

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


## Get a template by ID

#### Method 

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Response

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Arguments

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


## Get all templates

#### Method

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Response

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Arguments

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


## Generate a preview template

#### Method

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Response

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


#### Arguments

XYZ
<details>
<summary>
Click here to expand for more information.
</summary>

XYZ
</details>


