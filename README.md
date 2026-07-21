![Punktum dk Logo](https://punktum.dk/sites/default/files/logo/dk_logo_symbol_1.png)

# Punktum dk Domain Availability Service Specification

![Markdownlint Action](https://github.com/DK-Hostmaster/das-service-specification/workflows/Markdownlint%20Action/badge.svg)
![Spellcheck Action](https://github.com/DK-Hostmaster/das-service-specification/workflows/Spellcheck%20Action/badge.svg)

2026-07-21
Revision: 2.2

## Table of Contents

<!-- MarkdownTOC bracket=round levels="1,2,3,4" indent="  " autolink="true" autoanchor="true" -->

- [Introduction](#introduction)
- [About this Document](#about-this-document)
  - [License](#license)
  - [Document History](#document-history)
- [The .dk Registry in Brief](#the-dk-registry-in-brief)
- [Domain Availability Service](#domain-availability-service)
- [Available Environments](#available-environments)
  - [Production Environment](#production-environment)
  - [Sandbox Environment](#sandbox-environment)
- [Implementation Limitations](#implementation-limitations)
  - [Authentication](#authentication)
  - [Supported Media Types](#supported-media-types)
  - [Rate Limiting](#rate-limiting)
- [Domain Status](#domain-status)
- [Service /domain/is_available](#service-domainis_available)
  - [Request](#request)
  - [Response](#response)
  - [Examples for unavailable domain](#examples-for-unavailable-domain)
  - [Examples for available domain](#examples-for-available-domain)
  - [Example with bad domain parameter](#example-with-bad-domain-parameter)
- [Test Data](#test-data)
  - [Domains](#domains)
  - [Accounts / Credentials](#accounts--credentials)
- [References](#references)
- [Issue Reporting](#issue-reporting)
- [Appendices](#appendices)
  - [HTTP Status Codes](#http-status-codes)

<!-- /MarkdownTOC -->

<a id="introduction"></a>
## Introduction

This document describes and specifies the implementation offered by Punktum dk A/S for interaction with the central registry for the ccTLD dk using the Domain Availability Service (DAS). It is primarily aimed at a technical audience, and the reader is required to have prior knowledge of HTTP and possibly DNS registration.

<a id="about-this-document"></a>
## About this Document

This specification describes the Punktum dk Domain Availability Service (DAS). Future releases will be reflected in updates to this document; please refer to the Document History below for changes.

The document describes the current Punktum dk DAS implementation. The document describes the current Punktum dk DAS implementation. For additional information, please refer to the [References](#references) chapter below.

Any future extensions and possible additions and changes to the implementation are not within the scope of this document and will not be discussed or mentioned throughout this document.

This document is owned and maintained by Punktum dk A/S and must not be distributed without this information.

All examples provided in the document are fabricated/modified from real data to demonstrate commands etc. any resemblance to actual data are coincidental.

<a id="license"></a>
### License

This document is copyright by Punktum dk A/S and is licensed under the MIT License, please see the separate [LICENSE][MITLICENSE] file for details.

<a id="document-history"></a>
### Document History

- 2.2 2026-07-21
  - Verified and corrected request/response examples against the production service
  - Corrected the IDN/punycode behaviour: punycode-encoded names are rejected; submit UTF-8
  - Removed session handling, demo client, mailing list and additional information sections
  - Updated test data to reflect the current sandbox environment
  - General review and rewrite for clarity and consistency

- 2.1 2021-09-02
  - Updated support information

- 2.0 2021-09-02
  - Service updated to version 2.0.0, specification following service, no protocol changes only implementation changes

- 1.15 2019-03-28
  - Added information on IP address whitelisting to section on
  - Added information on IP address whitelisting to section on [Environments](#available-environments)

- 1.14 2018-11-30
  - Sandbox accounts and credentials are no longer supported and has been removed
  - Information on the consolidated sandbox environment has been added

- 1.13 2018-11-22
  - Minor correction to listing of enumerated values for responses

- 1.12 2018-11-21
  - Minor correction to the [Test Data](#test-data) section

- 1.11 2018-11-16
  - Corrected the examples so these are directly executable

- 1.10 2018-10-31
  - Updated the [Test Data](#test-data) section. Example data for the deprecated `blocked` removed and `enqueded` added

- 1.9 2018-10-26
  - Clarified the endpoint URLs

- 1.8 2018-08-22
  - Added information on status `enqueued`, introduced with server version 1.4.0

- 1.7 2017-12-19
  - Removed information on status `blocked`, which has been deprecated

- 1.6 2016-10-18
  - Added information on new status `available-on-waiting-list`

- 1.5 2016-09-01
  - Minor clarification on credentials

- 1.4 2016-06-09
  - Removed obsolete data sheet

- 1.3 2016-06-09
  - Added link to demo client, also available on GitHub

- 1.2 2016-04-19
  - Filled in data in the data sheet, more information will follow
  - Filled in details on blocking policy for failed login attempts based on user-id and IP-address
  - Added link to the gitprint service

- 1.1 2015-09-02
  - Migrated to markdown and hosting on GitHub, no changes to actual content just formatting

- 1.0 2013-02-25
  - Initial revision

<a id="the-dk-registry-in-brief"></a>
## The .dk Registry in Brief

Punktum dk is the registry for the Danish country-code top-level domain (.dk) and maintains the central DNS registry.

<a id="domain-availability-service"></a>
## Domain Availability Service

Punktum dk's DAS is regarded as a service offered to external parties requiring inquiry actions towards the Punktum dk registry.

DAS is an HTTP-based protocol aimed at providing a speedy interface for requesting information from the Punktum dk registry. The service is intended for machine-to-machine communication in a client-server setup. The service is not subject to any sorts of standards but adheres to best practices in the implementation of REST and use of HTTP in context of REST.

The service requires the use and possible development of client software. This is beyond the scope of this specification as the API and other assets for assisting in this are the primary object of this document.

In addition to the assets, Punktum dk aims to assist users and developers of possible client software with integration towards Punktum dk and therefore provide facilities to ease this integration. This is primarily centered around a sandbox environment and related documentation.

<a id="available-environments"></a>
## Available Environments

Punktum dk offers the following two environments:

| Environment | Endpoint | Purpose |
|-------------|----------|---------|
| Production | `https://das.dk-hostmaster.dk/` | Live production data |
| Sandbox | `https://das-sandbox.dk-hostmaster.dk/` | Client development against isolated sandbox data |

👉 Do note that accessing the service does not require IP address whitelisting with Punktum dk prior to use.

<a id="production-environment"></a>
### Production Environment

- is_available requests made to this environment will reflect live production data
- production credentials and proper authorization are needed to access the service

Production is available at: `https://das.dk-hostmaster.dk/`

<a id="sandbox-environment"></a>
### Sandbox Environment

- is_available requests made to this environment will reflect data only available in the isolated sandbox environment, please see the [sandbox environment specification](https://github.com/Punktum-dk/sandbox-environment-specification) for details.
- Please see the section on [Test Data](#test-data).

Sandbox is available at: `https://das-sandbox.dk-hostmaster.dk/`

<a id="implementation-limitations"></a>
## Implementation Limitations

In general the service is not localized and all DAS related errors and messages are provided in English.

The only localization support provided by the service is the support for IDN (Internationalized Domain Name) domains. Domain names must be submitted in UTF-8 using their native characters, for example rødtråd.dk. The service evaluates and returns the domain name in this form.

Please note that punycode-encoded domain names (for example xn--rdtrd-pra3k.dk) are not accepted by the service and will be rejected with an HTTP 400 response. Submit the UTF-8 representation instead.

<a id="authentication"></a>
### Authentication

The service is called using Basic HTTP Authentication with a dedicated DAS service user, consisting of:

- user-id (DAS user)
- password

Too many failed login attempts will block the account. The block for a user-id lasts for 24 hours and is automatically lifted.

If failed login attempts continue, or are spread across user-ids originating from the same IP address, the IP address will be blocked. The block for an IP address lasts for 24 hours and is automatically lifted.

<a id="supported-media-types"></a>
### Supported Media Types

The service supports JSON, XML and plain text, using the UTF-8 character set. Specify the desired response format in the HTTP `Accept` header, using one of the following:

- `Accept: application/json; charset=utf-8`
- `Accept: application/xml; charset=utf-8`
- `Accept: text/plain; charset=utf-8`

If a supported format is not specified in the `Accept` header, the service responds with HTTP status code 415 (see the [HTTP Status Codes][http-status-codes] section in the appendices).

<a id="rate-limiting"></a>
### Rate Limiting

We only allow a certain number of requests per minute. We reserve the right to adjust the rate limit in order to provide a high quality of service.

If the rate limit is exceeded, the HTTP status code 429 "Too many requests" is returned. The response includes a `Retry-After` header indicating how many seconds to wait before retrying.

👉 The current limit is set to 60 requests per minute.

Please note that the sandbox environment does not enforce rate limiting at this time, to allow unlimited use for developers.

<a id="domain-status"></a>
## Domain Status

The service returns a queried domain name and its status where possible. The status values have the following meanings:

| Status | Meaning |
|--------|---------|
| `available` | The domain name is available for application. |
| `unavailable` | The domain name is in use and is not available for application. |
| `available-on-waiting-list` | The domain name has been offered to the applicant at the top of the waiting list and is awaiting that applicant's acceptance or decline of the offer to register the domain name. |
| `enqueued` | An application has been enqueued with Punktum dk but not yet processed. This can last from a few seconds to a few days, for example while the application awaits completion of a required approval before registration is finalised. |

<a id="service-domainis_available"></a>
## Service `/domain/is_available`

<a id="request"></a>
### Request

Method: `GET`

URL path: `/domain/is_available/<domain>`

| Parameter | Type | Description | Mandatory | Example |
|-----------|------|-------------|-----------|---------|
| domain | string | The domain name to evaluate. It must adhere to the domain name format expected by Punktum dk, see [References][references]. | yes | `rødtråd.dk`, `example.dk` |

<a id="response"></a>
### Response

The service returns the following fields:

| Field | Type | Description |
|-------|------|-------------|
| domain | string | The queried domain name. |
| domain_status | enumerated string | The status of the domain, one of: `available`, `unavailable`, `enqueued` or `available-on-waiting-list`. |
| status | integer | The HTTP status code of the response. |
| message | string | A human-readable message, "OK" on success. |

On success the service responds with HTTP status code 200. For other HTTP status codes that the service can return, see the [HTTP Status Codes][http-status-codes] section in the appendices.

<a id="examples-for-unavailable-domain"></a>
### Examples for unavailable domain

JSON:

```Shell
% curl --header Accept:application/json \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/example.dk
```

```JSON
{"domain":"example.dk","domain_status":"unavailable","message":"OK","status":200}
```

XML:

```Shell
% curl --header Accept:application/xml \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/example.dk
```

```XML
<response xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <domain>example.dk</domain>
  <domain_status>unavailable</domain_status>
  <message>OK</message>
  <status>200</status>
</response>
```

Text:

```Shell
% curl --header Accept:text/plain \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/example.dk
```

```Text
domain_status:unavailable
status:200
message:OK
domain:example.dk
```

<a id="examples-for-available-domain"></a>
### Examples for available domain

JSON:

```Shell
% curl --header Accept:application/json \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/asdfg.dk
```

```JSON
{"domain":"asdfg.dk","domain_status":"available","message":"OK","status":200}
```

XML:

```Shell
% curl --header Accept:application/xml \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/asdfg.dk
```

```XML
<response xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <domain>asdfg.dk</domain>
  <domain_status>available</domain_status>
  <message>OK</message>
  <status>200</status>
</response>
```

Text:

```Shell
% curl --header Accept:text/plain \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/asdfg.dk
```

```Text
domain:asdfg.dk
message:OK
status:200
domain_status:available
```
<a id="example-with-bad-domain-parameter"></a>
### Example with bad domain parameter

Please note the `-v` flag to `curl` and that the response has been stripped down.

JSON:

```Shell
% curl -v --header Accept:application/json \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/asdf
```

```JSON
{"domain":"asdf","domain_status":null,"status":"400","message":"Invalid domain syntax"}
```

Text:

```Shell
% curl -v --header Accept:text/plain \
https://DAS-999:secret@das.dk-hostmaster.dk/domain/is_available/asdf
```

```Text
domain:asdf
status:400
message:Invalid domain syntax
```

⚠️ Note: for error responses, the XML format returns an empty body; only the HTTP status code `400` is provided. Use JSON or plain text to receive a machine-readable error message.

<a id="test-data"></a>
## Test Data

The sandbox uses a predefined set of test data. All domains not listed below will be reported as `available` in the sandbox environment.

<a id="domains"></a>
### Domains

| Domain name | Status | Notes |
|-------------|--------|-------|
| `punktum.dk` | `unavailable` | The domain is active |
| `waiting-list.dk` | `available-on-waiting-list` | The domain has been offered to the applicant at the top of the waiting list |
| `æøåöäüé.dk` | `unavailable` | The domain is active |
| * | * | Depending on which domains have been registered in the sandbox environment (see the statuses above); if not registered, `available` is returned. Please see the [sandbox environment specification](https://github.com/Punktum-dk/sandbox-environment-specification) for details. |


<a id="accounts--credentials"></a>
### Accounts / Credentials

To use the DAS sandbox you must use your own account, please see the [sandbox environment specification](https://github.com/Punktum-dk/sandbox-environment-specification) for details.

The DAS Service API User is created within the RP (Registrar Portal).

<a id="references"></a>
## References

Here is a list of documents and references used in this document

- [General Terms and Conditions][general_terms_and_conditions]
- [Documentation on the format of a domain name with the Punktum dk registry][DKHMNSDOM]

<a id="issue-reporting"></a>
## Issue Reporting

For issue reporting related to this specification, the DAS implementation, or the sandbox or production environments, please [contact us][contact] or write to registrar@punktum.dk.

<a id="appendices"></a>
## Appendices

<a id="http-status-codes"></a>
### HTTP Status Codes

| Status code | Message | Description |
|-------------|---------|-------------|
| 200 | OK | Service returned a valid response |
| 400 | Bad request | The request could not be fulfilled due to missing parameters or malformed syntax |
| 401 | Unauthorized | Authentication failed |
| 403 | Forbidden | Not authorized |
| 404 | Page not found | The request assumes a service (URL) not provided or unsupported at this time |
| 415 | Unsupported Media Type | The requested media type is unsupported, see section on Media Types |
| 429 | Too many attempts | Rate limiting triggered, please see section on Rate Limiting |
| 500 | Server Error | Service malfunction |
| 503 | Service Unavailable | Maintenance mode |

Please see the [Wikipedia; List of HTTP status codes][WIKIPEDIA].

[WIKIPEDIA]: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes
[MITLICENSE]: https://github.com/Punktum-dk/das-service-specification/blob/master/LICENSE
[general_terms_and_conditions]: https://punktum.dk/en/articles/terms-and-conditions-dk-domain-name
[DKHMNSDOM]: https://github.com/Punktum-dk/dkhm-name-service-specification#domain-names
[references]: #references
[http-status-codes]: #http-status-codes
[contact]: https://punktum.dk/en/contact-customer-service?lvl1=Registrars&lvl2=CSRegistrarOther
