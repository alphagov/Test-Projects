# Set up the client

## Install the client

### Maven

The notifications-java-client is deployed to [Bintray](https://bintray.com/gov-uk-notify/maven/notifications-java-client). Add this snippet to your Maven `settings.xml` file.

_QP: Where is this file located?_
_QP: What are the steps? Maven followed by something else? Any criteria on what steps to use?_
_QP: Should we put anything in about the version number?_

```xml
<?xml version='1.0' encoding='UTF-8'?>
<settings xsi:schemaLocation='http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd' xmlns='http://maven.apache.org/SETTINGS/1.0.0' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
<profiles>
	<profile>
		<repositories>
			<repository>
				<snapshots>
					<enabled>false</enabled>
				</snapshots>
				<id>bintray-gov-uk-notify-maven</id>
				<name>bintray</name>
				<url>http://dl.bintray.com/gov-uk-notify/maven</url>
			</repository>
		</repositories>
		<pluginRepositories>
			<pluginRepository>
				<snapshots>
					<enabled>false</enabled>
				</snapshots>
				<id>bintray-gov-uk-notify-maven</id>
				<name>bintray-plugins</name>
				<url>http://dl.bintray.com/gov-uk-notify/maven</url>
			</pluginRepository>
		</pluginRepositories>
		<id>bintray</id>
	</profile>
</profiles>
<activeProfiles>
	<activeProfile>bintray</activeProfile>
</activeProfiles>
</settings>
```
Then add the Maven dependency to your project.
```xml
    <dependency>
        <groupId>uk.gov.service.notify</groupId>
        <artifactId>notifications-java-client</artifactId>
        <version>3.8.0-RELEASE</version>
    </dependency>

```

### Gradle

_QP: Add this to the application code? If so, in any particular order? Is this in addition to Maven, or instead of?_

```
repositories {
    jcenter()
}

dependencies {
    compile('uk.gov.service.notify:notifications-java-client:3.8.0-RELEASE')
}
```

### Artifactory or Nexus

Click 'set me up!' on https://bintray.com/gov-uk-notify/maven/notifications-java-client for instructions.

_QP: This link takes you to some code that looks the same as the code in the Maven section. How does this all fit together? What exactly are you supposed to do and in what order?_


Refer to the [client change log](https://github.com/alphagov/notifications-java-client/blob/master/CHANGELOG.md) for the version number and the latest updates.

## Create a new instance of the client

Add this code to your application:

```java
import uk.gov.service.notify.NotificationClient;
import uk.gov.service.notify.Notification;
import uk.gov.service.notify.NotificationList;
import uk.gov.service.notify.SendEmailResponse;
import uk.gov.service.notify.SendSmsResponse;

NotificationClient client = new NotificationClient(apiKey);
```

To get an API key, [log in to GOV.UK Notify](https://www.notifications.service.gov.uk/) and go to the _API integration_ page. More information can be found in the [API keys](/#api-keys) section.

# Send a message

You can use GOV.UK Notify to send text messages, emails and letters.

## Send a text message

### Method

```java
  Map<String, String> personalisation = new HashMap<>();
  personalisation.put("name", "Jo");
  personalisation.put("reference_number", "13566");
  SendSmsResponse response = client.sendSms(mobileNumber, templateId, personalisation, "yourReferenceString", emailReplyToId);
```
  _QP: Assume that emailReplyToId is incorrect in text message section?_
  _QP: Should we have name and reference_number in this example or are they optional?_
  _QP: How does reference_number relate to yourReferenceString?_
  _QP: Is smsSenderId anywhere?_

### Arguments

#### mobileNumber (required)

The phone number of the recipient of the text message. This number can be UK or international.

#### templateId (required)

The ID of the template. You can find this by logging into [GOV.UK Notify](https://www.notifications.service.gov.uk/) and going to the _Templates_ page.

#### personalisation (optional)

If a template has placeholder fields for personalised information such as name or reference number, you need to __provide their values in a dictionary with key value pairs__. For example:

```java
Map<String, String> personalisation = new HashMap<>();
personalisation.put("name", "Jo");
personalisation.put("reference_number", "13566");
```
_QP: Is this a dict or is it something else?_

#### "yourReferenceString" (optional)

A unique identifier. This reference can identify a single unique notification or a batch of multiple notifications.

_Example?_

#### smsSenderId (optional)

A unique identifier of the sender of the text message notification. To set this up:

1. Log into your GOV.UK Notify account.
1. Go to _Settings_.
1. Check that you are in the correct service. If you are not, click _Switch service_ in the top right corner of the screen and select the correct one.
1. Go to the _Text Messages_ section and click _Manage_ on the "Text Message sender" row.
1. You can do one of the following:
  - copy the ID of the sender you want to use and paste it into the method
  - click _Change_ to change the default sender that the service will use, and click _Save_

`example?`

If you omit this argument from your method, the default `sms_sender_id` will be set for the notification.

### Response

If the request to the client is successful, you will receive the following `dict` response:

```java
   UUID notificationId;
   Optional<String> reference;
   UUID templateId;
   int templateVersion;
   String templateUri;
   String body;
   Optional<String> fromNumber;
```

If you are using the [test API key](/#test), all your messages will come back as delivered.

All successfully delivered messages will appear on your dashboard.

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

_UPDATE ERROR CODES_

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can't send to this recipient using a team-only API key"`<br>`]}`|Use the correct type of API key, refer to [API keys](/#api-keys) for more information|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can't send to this recipient when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`|Refer to [trial mode](https://www.notifications.service.gov.uk/features/using-notify#trial-mode) for more information|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM/TEST/LIVE of 3000 requests per 60 seconds"`<br>`}]`|Refer to [API rate limits](/#api-rate-limits) for more information|
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (LIMIT NUMBER) for today"`<br>`}]`|Refer to [service limits](/#service-limits) for the limit number|
|`500`|`[{`<br>`"error": "Exception",`<br>`"message": "Internal server error"`<br>`}]`|Notify was unable to process the request, resend your notification.|

## Send an email

### Method

```java
  HashMap<String, String> personalisation = new HashMap<>();
  personalisation.put("name", "Jo");
  personalisation.put("reference_number", "13566");
  SendEmailResponse response = client.sendEmail(templateId, emailAddress, personalisation, reference, emailReplyToId);
```

### Arguments

#### emailAddress (required)

The email address of the recipient, only required for email notifications.

#### templateId (required)

The ID of the template. You can find this by logging into GOV.UK Notify and going to the _Templates_ page.

#### personalisation (optional)

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values in a _dictionary with key value pairs_. For example:

```java
Map<String, String> personalisation = new HashMap<>();
personalisation.put("name", "Jo");
personalisation.put("reference_number", "13566");
```

#### reference (optional)

A unique identifier. This reference can identify a single unique notification or a batch of multiple notifications.

_QP: example?_

#### emailReplyToId (optional)

This is an email reply-to address specified by you to receive replies from your users. Your service cannot go live until at least one email address has been set up for this. To set up:

1. Log into your GOV.UK Notify account.
1. Go to _Settings_.
1. Check that you are in the correct service. If you are not, click _Switch service_ in the top right corner of the screen and select the correct one.
1. Go to the Email section and click _Manage_ on the "Email reply to addresses" row.
1. Click _Change_ to specify the email address to receive replies, and click _Save_.

_example?_

If you omit this argument, your default email reply-to address will be set for the notification.

### Response

If the request to the client is successful, you will receive the following `dict` response:

```java
	UUID notificationId;
	Optional<String> reference;
	UUID templateId;
	int templateVersion;
	String templateUri;
	String body;
	String subject;
	Optional<String> fromEmail;
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can't send to this recipient using a team-only API key"`<br>`]}`|Use the correct type of API key, refer to [API keys](/#api-keys) for more information|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Can't send to this recipient when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`|Refer to [trial mode](https://www.notifications.service.gov.uk/features/using-notify#trial-mode) for more information|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM/TEST/LIVE of 3000 requests per 60 seconds"`<br>`}]`|Refer to [API rate limits](/#api-rate-limits) for more information|
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (LIMIT NUMBER) for today"`<br>`}]`|Refer to [service limits](/#service-limits) for the limit number|
|`500`|`[{`<br>`"error": "Exception",`<br>`"message": "Internal server error"`<br>`}]`|Notify was unable to process the request, resend your notification.|

## Send a letter

When your service first signs up to GOV.UK Notify, you’ll start in trial mode. You can only send letters in live mode.

### Method

```java
	HashMap<String, String> personalisation = new HashMap<>();
	personalisation.put("address_line_1", "The Occupier"); // mandatory address field
	personalisation.put("address_line_2", "Flat 2"); // mandatory address field
	personalisation.put("postcode", "SW14 6BH"); // mandatory address field
	SendLetterResponse response = client.sendLetter(templateId, personalisation, "yourReferenceString");
```




### Arguments

#### templateId (required)

The ID of the template. You can find this by logging into GOV.UK Notify and going to the _Templates_ page.

#### personalisation (required)

The personalisation argument always contains the following required parameters for the letter recipient's address:

- `address_line_1`
- `address_line_2`
- `postcode`

Any other placeholder fields included in the letter template also count as required parameters. You need to provide their values _in a dictionary with key value pairs_. For example:

_QP: How do you provide the information?_

_example?_

#### "yourReferenceString" (optional)

A unique identifier. This reference can identify a single unique notification or a batch of multiple notifications.

_example?_

#### personalisation (optional)

The following parameters in the letter recipient's address are optional:

```java
	personalisation.put("address_line_3", "123 High Street"); // optional address field
	personalisation.put("address_line_4", "Richmond upon Thames"); // optional address field
	personalisation.put("address_line_5", "London"); // optional address field
	personalisation.put("address_line_6", "Middlesex"); // optional address field
	personalisation.put("application_id", "1234"); // field from template
	personalisation.put("application_date", "2017-01-01"); // field from template
```

_QP: How do you provide the information?_

_example?_

### Response

If the request to the client is successful, you will receive the following `dict` response:

```java
	UUID notificationId;
	Optional<String> reference;
	UUID templateId;
	int templateVersion;
	String templateUri;
	String body;
	String subject;
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Cannot send letters with a team api key"`<br>`]}`|Use the correct type of API key, refer to [API keys](/#api-keys) for more information|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Cannot send letters when service is in trial mode - see https://www.notifications.service.gov.uk/trial-mode"`<br>`}]`|Refer to [trial mode](https://www.notifications.service.gov.uk/features/using-notify#trial-mode) for more information|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "personalisation address_line_1 is a required property"`<br>`}]`|Ensure that your template has a field for the first line of the address, check [personlisation](/#send-a-letter-required-arguments-personalisation) for more information.|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|
|`429`|`[{`<br>`"error": "RateLimitError",`<br>`"message": "Exceeded rate limit for key type TEAM/TEST/LIVE of 3000 requests per 60 seconds"`<br>`}]`|Refer to [API rate limits](/#api-rate-limits) for more information|
|`429`|`[{`<br>`"error": "TooManyRequestsError",`<br>`"message": "Exceeded send limits (LIMIT NUMBER) for today"`<br>`}]`|Refer to [service limits](/#service-limits) for the limit number|
|`500`|`[{`<br>`"error": "Exception",`<br>`"message": "Internal server error"`<br>`}]`|Notify was unable to process the request, resend your notification.|


# Get message status

The possible status of a message depends on the message type.

## Status - text and email

_QP: Should I add created and sent for text and email and text respectively?_

### Sending

The message has been passed on to our providers to send to the recipient, and we are waiting for delivery information.

### Created

The message is queued to be sent to our providers. The notification typically only remains in this state for a few seconds.

### Sent

The message was delivered internationally. We may not receive additional status updates depending on the recipient's country and telecoms provider.

### Delivered

The message was successfully delivered.

### Failed

This covers all failure statuses:

- permanent-failure - "The provider was unable to deliver message, email or phone number does not exist; remove this recipient from your list"
- temporary-failure - "The provider was unable to deliver message, email inbox was full or phone was turned off; you can try to send the message again"
- technical-failure - "Notify had a technical failure; you can try to send the message again"

## Status - letter

### Failed

The only failure status that applies to letters is __technical-failure__ - Notify had an unexpected error while sending to our printing provider.

### Accepted

Notify is printing and posting the letter.

## Get the status of one message

### Method

```java
Notification notification = client.getNotificationById(notificationId);
```

### Arguments

#### notificationId (required)

The ID of the notification.

### Response

If the request to the client is successful, you will receive the following `notification` response:

```
UUID id;
Optional<String> reference;
Optional<String> emailAddress;
Optional<String> phoneNumber;
Optional<String> line1;
Optional<String> line2;
Optional<String> line3;
Optional<String> line4;
Optional<String> line5;
Optional<String> line6;
Optional<String> postcode;
String notificationType;
String status;
UUID templateId;
int templateVersion;
String templateUri;
String body;
Optional<String subject;
DateTime createdAt;
Optional<DateTime> sentAt;
Optional<DateTime> completedAt;
Optional<DateTime> estimatedDelivery;
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "id is not a valid UUID"`<br>`}]`|Check the notification ID|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|
|`404`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No result found"`<br>`}]`|Check the notification ID|


## Get the status of all messages

This API call returns the status of all messages. You can either get the status of all messages in one call, or one page of up to 250 messages.

### Method

#### All messages

This will return all your messages with statuses; they will be displayed in pages of up to 250 messages each.

```java
NotificationList notification = client.getNotifications(status, notificationType, reference, olderThanId);
```

You can filter the returned messages by including the following optional arguments in the method:

- [`notificationType`](/#template-type)
- [`status`](/#status)
- [`reference`](/#get-the-status-of-all-messages-optional-arguments-reference)
- [`olderThanId`](/#older-than)


#### One page of up to 250 messages

_QP: Does this apply?_

This will return one page of up to 250 messages and statuses. You can get either the most recent messages, or get older messages by specifying a particular notification ID in the [`older_than`](/#older-than) argument.

##### Most recent messages

```python
response = get_all_notifications_iterator(status="sending")
```

You must set the [`status`](/#status) argument to "sending".

##### Older messages

To get older messages:

1. Get the ID of an older notification.
1. Add the following code to your application, with the older notification ID in the [`older_than`](/#older-than) argument.

```python
response = get_all_notifications_iterator(status="sending",older_than="NOTIFICATION ID")
```

You must set the [`status`](/#status) argument to "sending".

This method will return the next oldest messages from the specified notification ID.

### Arguments

You can omit any of these arguments to ignore these filters.

#### notificationType (optional)

You can filter by:

* `email`
* `sms`
* `letter`

#### status (optional)

| status | description | text | email | letter |
|:--- |:--- |:--- |:--- |:--- |
|`sending` |The message is queued to be sent by the provider|Yes|Yes||
|`delivered`|The message was successfully delivered|Yes|Yes||
|`failed`|This will return all failure statuses:<br>- `permanent-failure`<br>- `temporary-failure`<br>- `technical-failure`|Yes|Yes||
|`permanent-failure`|The provider was unable to deliver message, email or phone number does not exist; remove this recipient from your list|Yes|Yes||
|`temporary-failure`|The provider was unable to deliver message, email inbox was full or phone was turned off; you can try to send the message again|Yes|Yes||
|`technical-failure`|Email / Text: Notify had a technical failure; you can try to send the message again. <br><br>Letter: Notify had an unexpected error while sending to our printing provider. <br><br>You can omit this argument to ignore this filter.|Yes|Yes||
|`accepted`|Notify is printing and posting the letter|||Yes|

#### reference (optional)

A unique identifier. This reference can identify a single unique notification or a batch of multiple notifications.

_example?_

#### olderThanId (optional)

Input the ID of a notification into this argument. If you use this argument, the next 250 received notifications older than the given ID are returned.

_example?_

If this argument is omitted, the most recent 250 notifications are returned.

### Response

If the request to the client is successful, you will receive a `dict` response.

#### All messages

```java
List<Notification> notifications;
String currentPageLink;
Optional<String> nextPageLink;
```

#### One page of up to 250 messages

_QP: Does this apply?_

```python
<generator object NotificationsAPIClient.get_all_notifications_iterator at 0x1026c7410>
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`||
|:---|:---|:---|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "bad status is not one of [created, sending, sent, delivered, pending, failed, technical-failure, temporary-failure, permanent-failure, accepted, received]"`<br>`}]`|
|`400`|`[{`<br>`"error": "ValidationError",`<br>`"message": "Apple is not one of [sms, email, letter]"`<br>`}]`|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|


# Get a template

## Get a template by ID

### Method

This will return the latest version of the template.

```java
Template template = client.getTemplateById(templateId);
```

### Arguments

#### templateId (required)

The ID of the template. You can find this by logging into GOV.UK Notify and going to the _Templates_ page.

### Response

If the request to the client is successful, you will receive a `dict` response.

```java
UUID id;
String templateType;
DateTime createdAt;
Optional<DateTime> updatedAt;
String createdBy;
int version;
String body;
Optional<String> subject;
Optional<Map<String, Object>> personalisation;
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|
|`404`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No Result Found"`<br>`}]`|Check your [template ID](/#arguments-template-id)|


## Get a template by ID and version

### Method

This will return the latest version of the template.

```java
Template template = client.getTemplateVersion(templateId, version);
```

### Arguments

#### templateId (required)

The ID of the template. You can find this by logging into GOV.UK Notify and going to the _Templates_ page.

#### version (required)

The version number of the template.

### Response

If the request to the client is successful, you will receive a `dict` response.

```Java
UUID id;
String templateType;
DateTime createdAt;
Optional<DateTime> updatedAt;
String createdBy;
int version;
String body;
Optional<String> subject;
Optional<Map<String, Object>> personalisation;
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|
|`404`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No Result Found"`<br>`}]`|Check your [template ID](/#get-a-template-by-id-and-version-required-arguments-template-id) and [version](/#version)|


## Get all templates

### Method

This will return the latest version of all templates.

```java
TemplateList templates = client.getAllTemplates(templateType);
```

### Arguments

#### templateType (optional)

If omitted all templates are returned. Otherwise you can filter by:

- `email`
- `sms`
- `letter`

### Response

If the request to the client is successful, you will receive a `dict` response.

```java
List<Template> templates;
```

If no templates exist for a template type or there no templates for a service, the templates list will be empty:

_Example?_

## Generate a preview template

### Method

This will generate a preview version of a template.

```java
TemplatePreview templatePreview = client.getTemplatePreview(templateId, personalisation)
```

The parameters in the personalisation argument must match the placeholder fields in the actual template. The API notification client will ignore any extra fields in the method.

### Arguments

#### templateId (required)

The ID of the template. You can find this by logging into GOV.UK Notify and going to the _Templates_ page.

#### personalisation (required)

If a template has placeholder fields for personalised information such as name or reference number, you need to provide their values, for example:

```java
Map<String, String> personalisation = new HashMap<>();
personalisation.put("name", "Jo");
personalisation.put("reference_number", "13566");
```

### Response

If the request to the client is successful, you will receive a `dict` response.

```java
UUID id;
String templateType;
int version;
String body;
Optional<String> subject;
```

### Error codes

If the request is not successful, the client will raise an `HTTPError`:

|`error.status_code`|`error.message`|Notes|
|:---|:---|:---|
|`400`|`[{`<br>`"error": "BadRequestError",`<br>`"message": "Missing personalisation: [PERSONALISATION FIELD]"`<br>`}]`|Check that the personalisation arguments in the method match the placeholder fields in the template|
|`400`|`[{`<br>`"error": "NoResultFound",`<br>`"message": "No result found"`<br>`}]`|Check the [template ID](/#generate-a-preview-template-required-arguments-template-id)|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Error: Your system clock must be accurate to within 30 seconds"`<br>`}]`|Check your system clock|
|`403`|`[{`<br>`"error": "AuthError",`<br>`"message": "Invalid token: signature, api token not found"`<br>`}]`|Check your API key, refer to [API keys](/#api-keys) for more information|


# Get received text messages

This API call returns received text messages. Depending on which method you use, you can either get all received text messages, or a page of up to 250 text messages.

## Get all received text messages

This method will return a `ReceivedTextMessageList` with all received text messages.

### Method

```java
ReceivedTextMessageList response = client.getReceivedTextMessages(olderThanId);
```

### Arguments

#### olderThanId (optional)

Input the ID of a received text message into this argument. If you use this argument, the next 250 received text messages older than the given ID are returned.

_EXAMPLE_

If this argument is omitted, the most recent 250 text messages are returned.

### Response

If the request to the client is successful, you will receive a `ReceivedTextMessageList` response that will return all received texts.

```java
private List<ReceivedTextMessage> receivedTextMessages;
private String currentPageLink;
private String nextPageLink;
```
The `ReceivedTextMessageList` will have the following properties:

```java
private UUID id;
private String notifyNumber;
private String userNumber;
private UUID serviceId;
private String content;
private DateTime createdAt;
```

## Get one page of received text messages

_DOES THIS APPLY?_

This will return one page of up to 250 text messages.  

### Method

```python
response = client.get_received_texts(older_than)
```

You can specify which texts to receive by inputting the ID of a received text message into the [`older_than`](/#get-one-page-of-received-text-messages-optional-arguments-older-than) argument.

### Arguments

#### olderThanId (optional)

Input the ID of a received text message into this argument. If you use this argument, the next 250 received text messages older than the given ID are returned.

```python
older_than=’740e5834-3a29-46b4-9a6f-16142fde533a’ # optional string - notification ID
```

If this argument is omitted, the most recent 250 text messages are returned.

### Response

If the request to the client is successful, you will receive a `dict` response.


```python
{
  "received_text_messages":
  [
    {
      "id": "STRING", # required string - ID of received text message
      "user_number": "STRING", # required string
      "notify_number": "STRING", # required string - receiving number
      "created_at": "STRING", # required string - date and time template created
      "service_id": "STRING", # required string - service ID
      "content": "STRING" # required string - text content
    },
    …
  ],
  "links": {
    "current": "/received-text-messages",
    "next": "/received-text-messages?other_than=last_id_in_list"
  }
}
```
