# DK Hostmaster Domain Availability Service Specification

2015/08/22
Revision: 1.0

# Table of Contents

<!-- MarkdownTOC -->

- Introduction
- About this Document
    - Document History
- The .dk Registry in Brief
- Domain Availability Service
- Available Environments
- Implementation Limitations
    - AAA
    - Supported Media-types
    - Rate Limiting
- Session Handling
- Service `/domain/is_available`
    - Request
    - Examples for unavailable domain
    - Examples for available domain
    - Example with bad domain parameter
    - Example with bad credentials
- Test Data
    - Domains
    - Accounts / Credentials
- References
- Resources
    - Mailing list
    - Issue Reporting
    - Additional Information
- Data Sheet
- Appendices
    - HTTP Status Codes

<!-- /MarkdownTOC -->

# Introduction

This document describes and specifies the implementation offered by DK Hostmaster A/S for interaction with the central registry for the ccTLD dk using the Domain Availability Service (DAS). It is primarily aimed at a technical audience, and the reader is required to have prior knowledge of HTTP and possibly DNS registration.

# About this Document

This specification describes version 1 (1.0.x) of the DK Hostmaster DAS Implementation. Future releases will be reflected in updates to this specification, please see the document history section below.
The document describes the current DK Hostmaster DAS implementation, for more general documentation on the used protocols and additional information please refer to the RFCs and additional resources in the References and Resources chapters below.
Any future extensions and possible additions and changes to the implementation are not within the scope of this document and will not be discussed or mentioned throughout this document.

## Document History

* 1.0 2013.02.25
  * Initial revision

# The .dk Registry in Brief

DK Hostmaster is the registry for the ccTLD for Denmark (dk). The current model used in Denmark is based on a sole registry, with DK Hostmaster maintaining the central DNS registry.
The service does is not subject to any sorts of standards, but adheres to practices in implementation of REST and use of HTTP in context of REST.

# Domain Availability Service

The DK Hostmaster’s DAS is based on a SOA architecture. The implementation is regarded as a service offered to external parties requiring inquiry actions towards the DK Hostmaster registry.

DAS is an HTTP-based protocol aimed at providing a speedy interface for requesting information from the DK Hostmaster registry. The service is intended for machine-to-machine communication in a client-server setup. Please see the References chapter for more information on specifications and references for HTTP and related.

The service requires the use of and possible development of client software. This is beyond the scope of this specification as the API and other assets for assisting in this are the primary object of this document.

In addition to the assets, DK Hostmaster aims to assist users and developers of possible client software with integration towards DK Hostmaster and therefore provide facilities to ease this integration. This is primarily centered around a sandbox environment and related documentation.

# Available Environments

DK Hostmaster offers the following environments:

| Environment | Role | Policies |
| ----------- | ---- | ----------- |
| production  | production | This environment will be the production environment for the DK Hostmaster Domain Availability Service 
* is_available requests made to this environment will reflect live production data
* production credentials and proper authorization are needed access the service |
| sandbox     | development | This environment is intended for client development towards the DK Hostmaster Domain Availability Service.
* is_available requests made to this environment will reflect dummy data. Please see the section on test data. 
* The sandbox does not implement actual rate limiting, but offers simulated rate limiting by using a specific request, please see the section on test data. |

# Implementation Limitations

In general the service is not localized and all DAS related errors and messages are provided in English. 

The only localization support provided by the service is the support for IDN domains. Please note the service require requests in UTF-8, meaning punycode encoded domain names will be interpreted as-is, meaning in ASCII context. 

The punycode encoded example of: xn--kdplg-orai3l.dk will be evaluated as xn--kdplg-orai3l.dk and not decoded to kødpålæg.dk prior to evaluation.

## AAA

This service is called using Basic HTTP Authentication supporting current login credentials.

Too many login attempts will block the account. Todo: Block details...

## Supported Media-types

The service supports JSON, XML and plain text format, using the UTF-8 character set. In order to specify what format you want to retrieve the format should be specified in the HTTP header: Accept-header.
Control the content type by setting header info, using the below examples:

`Accept: application/json; charset=utf-8`
`Accept: application/xml; charset=utf-8`
`Accept: text/plain; charset=utf-8`

If content type is not specified, response will reflect this with an HTTP status code: 415 (see: HTTP status code listing in appendices).

## Rate Limiting

We only allow a certain number of requests per minute. We reserve the right to adjust the rate limit in order to provide a high quality of service. 
If rate limit is exceeded the HTTP status code 429 “Too many requests” is returned. Further, the response will have a `Retry-After` header that tells you for how many seconds to wait before retrying. 

Current limit is set to 60 requests perl minute.

Please note the sandbox environment is not under rate limiting at this time, due to a wish for unlimited use for developers. 

# Session Handling

The service uses a basic session handling based on cookies. 

| Parameter | Value | Description |
| cookie name | dkhm-das-session | This is the name of the cookie
| cookie domain | .dk-hostmaster.dk |  
| expiration | 3600 seconds | The expiration date provided in the cookie is in the GMT timezone |

# Service `/domain/is_available`

## Request

Method: 
    `GET`

URL path: 
    `/domain/is_available/<domain>`

| Parameter | Type | Description | Mandatory | Example |
| domain    | string | The domain name to evaluate, it has to adhere to the domain name format expected by DK Hostmaster, see References. | yes | abc.dk, jordbærgrød.dk |
| status | enumerated string | "string indicating status of request, either one of:
* available
* unavailable
* blocked" | yes | |
| message | enumerated string | string providing human readable message, “ok” on success | optional |

Default HTTP header observed: 200 OK. Additional status data in Status and Message. For additional HTTP status codes, which can be exhibited by the service, please refer to the addendum.

## Examples for unavailable domain

JSON:
`% curl --header Accept:application/json \
https://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/dk-hostmaster.dk`

`{"domain":"dk-hostmaster.dk","status":"ok","domain_status":"unavailable"}`

XML:

`% curl --header Accept:application/xml \ 
https://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/dk-hostmaster.dk`

```
<?xml version='1.0' encoding='UTF-8' standalone='yes'?>
<response>
  <domain>dk-hostmaster.dk</domain>
  <domain_status>unavailable</domain_status>
</response>
```

Text:

`% curl --header Accept:text/plain \
http://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/dk-hostmaster.dk`

```
domain:dk-hostmaster.dk
status:ok
domain_status:unavailable
```

## Examples for available domain

`% curl --header Accept:application/json \
https://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/adsf.dk`

`{"domain":"adsf.dk","status":"ok","domain_status":"available"}``

XML:

`% curl --header Accept:application/xml \ 
https://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/asdf.dk`

```
<?xml version='1.0' encoding='UTF-8' standalone='yes'?>
<response>
  <domain>adsf.dk</domain>
  <domain_status>available</domain_status>
  <status>ok</status>
</response>
```

Text:

`% curl --header Accept:text/plain \
http://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/asdf.dk`

```
domain:adsf.dk
status:ok
domain_status:available
```

## Example with bad domain parameter

Please note the -v flag to curl and that the response has been stripped down.

Text:

`% curl -v --header Accept:text/plain \
http://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/asdf`

```
< HTTP/1.1 415 Unsupported Media Type
< Connection: keep-alive
< Content-Type: application/json; charset=utf-8;
< Cache-Control: max-age=1, no-cache
< Date: Wed, 23 Oct 2013 11:59:30 GMT
< Content-Length: 24
< Server: Mojolicious (Perl)
< 
"Unsupported media type"`
```

## Example with bad credentials

Please note the -v flag to `curl` and that the response has been stripped down.

Text:
`% curl -v --header Accept:text/plain \
http://REG-12345:secret@das-sandbox.dk-hostmaster.dk/domain/is_available/asdf`

```
< HTTP/1.1 401 Unauthorized
< Connection: keep-alive
< Content-Type: application/json; charset=utf-8;
< Cache-Control: max-age=1, no-cache
< Date: Wed, 23 Oct 2013 12:05:22 GMT
* Authentication problem. Ignoring this.
< WWW-Authenticate: Basic realm=DKH Domain Availability Service (DAS)
< Content-Length: 56
< Server: Mojolicious (Perl)
status:error
message:User authentication error
```

# Test Data

The sandbox uses a predefined set of test data.

## Domains

| Domain name      | Status      | Notes |
| dk-hostmaster.dk | Unavailable | The domain is active |
| asdf.dk          | Available   | Not in the registry at this time |
| blocked.dk       | Available   | The domain status is blocked |
| æøåöäüé.dk       | Unavailable | This domain is active |

## Accounts / Credentials

| Username   | Password | Status | Notes |
| REG-999999 | secret   | Active | The domain status us active
| TEST1-DK   | secret   | Active | Not authorized, the user does not have registrator status |
| REG-123456 | secret   | Active | The users password is temporary and cannot be used to access service. |

# References

Here is a list of documents and references used in this document

* General Terms and Conditions: https://www.dk-hostmaster.dk/fileadmin/filer/pdf/generelle_vilkaar/general-conditions.pdf
* RFC: 2616 Hypertext Transfer Protocol -- HTTP/1.1: https://tools.ietf.org/html/rfc2616
* RFC: 2617 HTTP Authentication: Basic and Digest Access Authentication: http://tools.ietf.org/html/rfc2617
* Documentation on the format of a domain name with the DK Hostmaster A/S registry: https://www.dk-hostmaster.dk/english/technical-administration/forms/register-domainname/

# Resources

A list of resources for DK Hostmaster DAS support is listed below.

## Mailing list

DK Hostmaster operates a mailing list for discussion and inquiries  about the DK Hostmaster DAS implementation. To subscribe to this list, write to the address below and follow the instructions. Please note that the list is for technical discussion only, any issues beyond the technical scope will not be responded to, please send these to the contact issue reporting address below and they will be passed on to the appropriate entities within DK Hostmaster A/S.

* `das-discuss+subscribe@liste.dk-hostmaster.dk`

## Issue Reporting

For issue reporting related to this specification, the DAS implementation or sandbox or production environments, please contact us.  You are of course welcome to post these to the mailing list mentioned above, otherwise use the address specified below:

 * `tech@dk-hostmaster.dk`

## Additional Information

More information and the latest revision of this specification are available at the DK Hostmaster website:

  * `https://www.dk-hostmaster.dk/english/tech-notes/das/``

# Data Sheet

| Environment | Version | URI | Notes |
| Production  | N/A     | das.dk-hostmaster.dk | Not released at this time. | 
| Sandbox     | 1.0.0   | das-sandbox.dk-hostmaster.dk | Released on 2013.20.05 |

# Appendices

## HTTP Status Codes

| Status code | Message | Description |
| 200 | OK | Service returned a valid response |
| 400 | Bad request | The request could not be fulfilled due to missing parameters or malformed syntax |
| 401 | Unauthorized | Authentication failed |
| 403 | Forbidden | Not authorized |
| 404 | Page not found | The request assumes a service (URL) not provided or unsupported at this time |
| 415 | Unsupported Media Type | The requested media type is unsupported, see section on Media Types |
| 429 | Too many attempts | Rate limiting triggered, please see section on Rate Limiting |
| 500 | Server Error | Service malfunction |
| 503 | Service Unavailable Maintenance mode | 