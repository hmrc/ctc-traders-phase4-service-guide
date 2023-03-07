---
title: CTC Traders API phase 4 service guide
weight: 1
description: Software developers, designers, product owners or business analysts. Send departure and arrival movement notifications to the NCTS.
---

# CTC Traders API phase 4 service guide

Learn how to use [CTC Traders API v1.0](/api-documentation/docs/api/service/common-transit-convention-traders/1.0) with your software.

## API overview

The CTC Traders API is based on REST principles with endpoints that return data in JSON format and it uses standard HTTP error response codes.

Use the CTC Traders API to:

- send departure and arrival movement notifications to the New Computerised Transit System (NCTS)
- retrieve messages sent from customs offices of departure and destination

The API endpoints relate only to Great Britain and Northern Ireland. You can also use the HMRC sandbox environment to run tests for Great Britain and Northern Ireland transit movements.

## API status

This version of the CTC Traders API supports only NCTS phase 4. [CTC Traders API v2.0](/api-documentation/docs/api/service/common-transit-convention-traders/2.0) supports only NCTS phase 5.

## Legacy CTC enrolments

NCTS phase 5 is due to go live during November 2023 and **a legacy Common Transit Convention (CTC) enrolment will not be accepted on phase 5**, so obtaining a new CTC enrolment is a mandatory requirement. A legacy CTC enrolment is an enrolment for phase 3 or earlier of the NCTS.

**Deadline:** Traders must migrate from legacy CTC enrolments before 31 March 2023.

**There is no upgrade path from phase 3 or earlier to phase 5.** Instead, if traders have not already done so, they must first upgrade to phase 4.

To get a new CTC enrolment for phases 4 and 5, traders must complete the following steps:

1. [Get a new Electronic Customs Clearance (ECC) enrolment](https://www.gov.uk/government/publications/new-computerised-transit-system-ncts-how-to-register-and-enrol).
2. [Get an EORI number](https://www.gov.uk/eori) (if needed).
3. Start using only your new CTC enrolment for making all of your customs declarations.

[Contact the NCTS Helpdesk](https://www.gov.uk/government/organisations/hm-revenue-customs/contact/new-computerised-transit-system-enquiries) if you need any help or advice when using the NCTS.

## Quick start
Learn how to get started with the CTC Traders API.

If you are new to the NCTS, you should review all of this document before reviewing other documents for phase 4. If you are migrating from an earlier NCTS phase to phase 4, you should review this section at least before reviewing other documents for phase 4.

### Before you start

Before you start using the CTC Traders API, you should:

- ensure that you have an HMRC [developer account](/developer/login) - if you don’t have one, you must [register for an account](/developer/registration), activate it by email, and sign in

- add your subscription to this API to your application

- learn about the user-restricted [authentication](/api-documentation/docs/authorisation/user-restricted-endpoints) used by the API  

- [create an application](/developer/applications/) in our sandbox environment

- use the [Create Test User API](/api-documentation/docs/api/service/api-platform-test-user/1.0) to create one or more test users for your sandbox application

- download [NCTS-P4 reference data](https://ec.europa.eu/taxation_customs/dds2/rd/rd_download_home.jsp?Lang=en) that can be used for testing

- read the [testing guide](/guides/ctc-traders-phase4-testing-guide/) to check that your software is compatible with this version of the API and to learn how to test your application in the sandbox environment

- download these files:

   - [XSD zip file](/figures/ctc-traders-api-xsds.zip) (file unzips and downloads)

   - [Postman collections of example API calls and cURL commands](https://github.com/hmrc/common-transit-convention-traders-postman) - if you want to use the XML files directly, you can extract these from the Postman collections link


### Production environment requirements

Before you can use the production environment for the CTC Traders API, you must:

- complete the [CTC Traders API Application for Production Credentials Checklist](https://developer.service.hmrc.gov.uk/guides/ctc-traders-phase4-testing-guide/figures/CTC_Traders_API_Application_for_Productions_Credentials_v0.1_Aug22.docx) (Microsoft Word document)
- use your [developer account](/developer/login) to apply for production credentials

### Get your customers ready

If you work for as software house, each trader you serve must use the [Government Gateway](https://www.access.service.gov.uk/login/signin/creds) to [sign up to the CTC Traders API](https://www.tax.service.gov.uk/customs-enrolment-services/ctc/subscribe?_gl=1*itulmt*_ga*MjA2MDk0MTQyMi4xNjY3Mzk2ODM5*_ga_Y4LWMWY6WS*MTY3NDgyMzU5OC41MS4xLjE2NzQ4NDE2NzcuMC4wLjA.&_ga=2.207635798.536493967.1674469117-2060941422.1667396839) and provide you with the following:

- GB Economic Operators Registration and Identification (EORI) number
- VAT details (optional)
- Standard Industrial Classification (SIC) code
- company or organisation details:
  - unique tax reference (UTR) number
  - registered company name (this must be an exact match)
  - registered company address
  - date of company establishment
- email address
- contact details

## User journeys

These journeys show examples of use:

- [developer setup](documentation/developer-setup.html)
- [submit a transit declaration](documentation/submit-transit-declaration.html)
- [query declarations sent](documentation/query-declarations-sent.html)
- [submit message related to a departure](documentation/submit-message-related-to-departure.html)
- [submit arrival notification](documentation/submit-arrival-notification.html)
- [resubmit arrival notification](documentation/resubmit-arrival-notification.html)
- [query arrival notifications sent](documentation/query-arrival-notifications-sent.html)
- [submit message related to an arrival](documentation/submit-message-related-to-arrival.html)
- [get notifications](documentation/get-notifications.html) 

## Process flows

Review the expected order of messages that can be sent and received.

### Departures

The following diagram shows the messages that an office of departure receives. 

<img src="figures/Departures_Diagram.svg" alt="Departures workflow flow. Flow is described in this section." />

<a href="figures/Departures_Diagram.svg" target="_blank">Open the Departures diagram in a new tab.</a>

1. An IE015 message containing declaration data is sent by the user to the NCTS.
2. NCTS responds with one of the following messages: 
   - an IE016 message if the declaration is rejected - the transit movement ends
   - an IE028 message if an MRN is allocated (go to step 3)
   - an IE928 message for a positive acknowledgment followed by an IE016 or an IE028 (see previous options in this step)
3. If the user sends an IE014 declaration cancellation request, NCTS sends an IE009 cancellation decision. This also happens following a cancellation request by the office of departure. A cancellation decision has one of the following outcomes: 
   - the cancellation request is accepted and the transit movement ends
   - the cancellation request is rejected and NCTS sends an IE029 release for transit (go to step 5)
4. If the user does not send an IE014 declaration cancellation request following the allocation of an MRN (see step 2), one of the following actions takes place:
   - the NCTS sends an IE060 control of decision notification, then sends either an IE029 release for transit (go to step 5) or an IE051 no release for transit message
   - the NCTS sends an IE051 no release for transit message - at this point, the transit movement ends
   - the NCTS sends an IE055 guarantee not valid message - if there is an intervention, the NCTS then sends an IE029 release for transit, but if there is no intervention, the NCTS then sends an IE051 no release for transit message 
   - the NCTS sends an IE029 release for transit message (go to step 5)
5. When the NCTS issues an IE029 release for transit (following an IE028, an IE060, or an IE009), one of the following actions takes place:
   - the user chooses to send an IE014 declaration cancellation request
   - a cancellation is requested by the office of departure, which results in the NCTS sending an IE009 cancellation decision message (go to step 3)
   - upon completion of the arrival, an IE045 write-off notification movement is issued by the NCTS

### Arrivals

The following diagram shows the messages that the office of destination receives.

<img src="figures/Arrivals_Diagram.svg" alt="Arrivals workflow flow. Flow is described in this section." />

<a href="figures/Arrivals_Diagram.svg" target="_blank">Open the Arrivals diagram in a new tab.</a>

1. An IE007 arrival notification message is sent by the user to the NCTS.
2. One of the following actions takes place:
   - the NCTS sends the user an IE008 arrival notification rejection (go to step 1)
   - the NCTS sends an IE043 unloading permission message (go to step 3)
   - the NCTS sends an IE025 good release notification message (go to step 6)
3. An IE043 unloading permission message results in one of the following actions:
   - an IE044 unloading remarks message is sent by the user to the NCTS (go to step 4)
   - if manual helpdesk intervention takes place, the NCTS sends an IE025 goods release notification message (go to step 6)
4. After the NCTS receives an IE044 unloading remarks message, one of the following actions takes place:
   - NCTS sends an IE058 unloading remarks rejection message (go to step 5)
   - NCTS sends an IE025 goods release notification message (go to step 6)
   - if manual helpdesk intervention takes place, the NCTS sends an IE043 unloading permission message (go to step 3)
5. After the NCTS sends an IE058 unloading remarks rejection message, one of the following actions takes place:
   - an IE044 unloading remarks message is sent by the user to the NCTS (go to step 4)
   - if manual Border Force intervention takes place, the NCTS sends an IE043 unloading permission message (go to step 3)
6. After the NCTS sends an IE025 goods release notification message, it sends an IE045 to the office of departure for the transit movement.

## Terms of use

Your application must comply with [our terms of use](/api-documentation/docs/terms-of-use). You must accept the terms of use before we issue your application’s production credentials.

## Related documentation

- [CTC Traders API roadmap](/roadmaps/common-transit-convention-traders-roadmap/)
- [CTC Traders API v1.0 reference](/api-documentation/docs/api/service/common-transit-convention-traders/1.0/oas/page)
- [CTC Traders API phase 4 testing guide](https://developer.service.hmrc.gov.uk/guides/ctc-traders-phase4-testing-guide/)
- [CTC Guarantee Balance phase 4 service guide](https://developer.service.hmrc.gov.uk/guides/ctc-guarantee-balance-service-guide/)
- [NCTS phase 4 technical interface specification](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/1025727/NCTSPhase4TIS-D1-V3-9-5.odt)  (OpenDocument Text document) - a full list of key information including messages, message content and sequence diagrams, plus instructions on how to use test message transfer and content
- [NCTS phase 4 TIS - Appendix B Technical Message Structures](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/641489/NCTSPhase4TISAppv3_9_3.pdf) (PDF) - defines which data fields are optional and mandatory - these fields cover the NCTS mandatory business rules and logic needed when building software to connect with the NCTS
- [CTC XML API - EDIFACT to XML mapping table](/guides/ctc-traders-phase4-testing-guide/figures/xml-2-edifact-mapping-updated12112020.pdf) (PDF)
- [Transit Manual Supplement](https://www.gov.uk/government/publications/transit-manual-supplement) - UK transit procedures (OpenDocument Text document)

**Note:** The NCTS documents are technical specifications for both legacy channels and CTC Traders API, so they include some EDIFACT information in certain places for legacy channel users. CTC Traders API v1.0 uses XML and RESTful standards, so it does not use EDIFACT wrapper or SOAP, which means that you can disregard guidance about EDIFACT. Therefore, the phase 4 TIS is tailored more towards the legacy systems and not the CTC Traders API, but you must still understand its contents.

## Getting help and support

Before contacting us, find out if there is planned API downtime or a technical issue by checking [HMRC API Platform Status](https://api-platform-status.production.tax.service.gov.uk/?_ga=2.139406967.536493967.1674469117-2060941422.1667396839) and [New Computerised Transit System service availability](https://www.gov.uk/guidance/new-computerised-transit-system-service-availability?_ga=2.174532070.536493967.1674469117-2060941422.1667396839).

If you have specific questions about the CTC Traders API, contact our Software Developer Support (SDS) Team. You’ll get an initial response within 2 working days.

You can also email questions to [SDSTeam@hmrc.gov.uk](mailto:SDSTeam@hmrc.gov.uk). We might ask for more detailed information when we respond.
