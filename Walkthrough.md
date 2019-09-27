Automation IIB walkthrough #2
=

UWSBridgeService - What about the attachments?
These have to be uploaded to another API (EZFLow)
UWS API - to create teh submission
EZFlow - for the documents attached to submissions


Most common error from UWS API
Mandatory fields missing this also leads to
EZFlow sync issue (When anything prevents the creation)


FROM UwsBridgeService (Up to this point everything is in sync), three scenarios are to be considered:
mandatory fields missing
ezflow sync error
success

UWSBridge will throw an exception exception
will go to **NotificationTokenIntakeErrorHandler**

CommonExceptionHandler is what calls

SubmissionIntakeErrorHandler - this will move the email to the manually process folder

In the fields missing scenario, what IIB will do is from the exception point we will cascade to the submissionintakehandler which will move the mail from inbox to manual processing folder

processed folder
manual processing

in ezflow sync error, this is a partial failure so in this scenario we don't throw an exception to notification, we just directly invoke the submissionintakeerrorhandler and it will invoke a function app. Drop an email to technical assistant (every underwriter has one)

Upon ez flow sync error we talk to azure logic api to move

UWS api gives you the technical assistant email id

**SubmissionIntakeErrorHandler** sends to logic api (communicated by UWSBridgeService)


Success scenario also divided
A. 1 submission
B. multiple submission -- need to check which documents go to which submission 


submissionGuid for each submissionNbr 



**SetFileStatusService** Using the file status service, it will go to share drive, read the json.txt ( this has the mappings from Watson), it takes the lobCode and if there is a mapping present for this lobCode

Setting LOB is optional (Only done if we find matching map), but the file status has to be set prior to uploading the document

**EZFlowBridgeService**

This is what sets the documents as successes / moved them to processed folder when they are.



Moved to processed folder

Common Framework library includes the Audit subflow
What it does is create a json message and then drops it to the logginservice

what **LoggingService** does is call a rest api (built on top of API) and insert the data there


---
ExceptionHandling to be discussed in detail
UploadToWatsonService to be discussed later
Uploadto

After a certain period of time due to limitations there is a script to move folders that are older than X (x currently is 15) it is moved to history.

Can Upload all watson responses to the CosmosDB using Azure apis

Upload to CosmosDB uploads all the images
