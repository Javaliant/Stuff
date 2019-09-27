### High Level Automation Flow
```flow

st=>start: User sends email
lapp=>subroutine: Logic App
apic=>inputoutput: API Connect
iib=>subroutine: IIB
e=>end: Submission is created

st->lapp(right)->apic(right)->iib(right)->e
```

###### Notes
- Logic App is cloud service which pools an Office 365 mailbox

### Logic App
```flow

st=>start: Email arrives
co=>operation: Clean Email Data
so=>operation: Save Email Data to CosmosDB
e=>end: POST call to IIB with unique email entry id

st->co->so->e
```
###### Logic App Notes
- **Email Monitoring Logic App** polls the mailbox every 30 seconds (configurable)
- **CosmosDB** NoSQL database used through azure via APIs. What generates and returns the email entry id 

### IIB
```flow

st=>start: POST call from Logic App with unique email entry id
dds=>operation: DocDownloadService
dcs=>inputoutput: DocConversionService
iss=>operation: IdentifySubmissionService
kes=>operation: KVPExtractionService
ubs=>operation: UWSBridgeService
e=>end: POST call to UWS Submission API

st->dds(right)->dcs->iss->kes->ubs(right)->e
```


###### IIB Notes
- **DocDownloadService** Downloads everything about the Submission Email into a shared folder
- **DocConversionService** Converts files from one format to another based on configured rules. E.g. May check filename for keyword that indicates conversion from pdf to image. Leverages Tessaract (Optical Character Recognition - OCR -  engine which facilitates conversion from image to text), and Doc Ultimate API (Azure function that converts to text)
- **IdentifySubmissionService** What puts all files together in Al.txt and others.txt
- **KVPExtractionService** Sends text files to IBM Watson. Watson uses Machine Learning to produce json with relevant key-value pairs describing the file -- this data is used for the Submission Post  call
- **UWSBridgeService** Creates the submission. Post call returns a unique SubmissionNbr and IIBParentId which is identical to email entry id. There is 1 submission created for each type (AL, GL, etc)
- **OCR** - Optical Character Recognition
