# Data Agreement - DIDComm Protocol Specification

## Abstract

A Data Agreement (DA) records the conditions for an organization to process personal data in accordance with data regulations such as the GDPR. This DIDComm protocol implements signing and verification of the Data Agreement between an organisation and the individual.

## Status of This Document

Version 1.0

## Authors
Mr. George Padayatti (iGrant.io, Sweden)  
Mr. Lal Chandran (iGrant.io, Sweden)  

## Contributors and Reviwers
Mr. Mr. Jan Lindquist (Linaltec, Sweden)  
Ms. Lotta Lundin (iGrant.io, Sweden)  

## Table of Contents

- [1.0   ADA RFC 0001: MyData DID Protocol 1.0](#10ada-rfc-0001--mydata-did-protocol-10)
  * [1.1 Summary](#11-summary)
  * [1.2 Motivation](#12-motivation)
  * [1.3 Tutorial](#13-tutorial)
    + [1.3.1   Interaction](#131interaction)
    + [1.3.2   Messages](#132messages)
      - [1.3.2.1    Create Operation](#1321create-operation)
      - [1.3.2.2    Read Operation](#1322read-operation)
      - [1.3.2.3    Update Operation](#1323update-operation)
      - [1.3.2.4    Delete Operation](#1324delete-operation)
- [2.0   ADA RFC 0002: Data Agreement Protocol 1.0](#20ada-rfc-0002--data-agreement-protocol-10)
  * [2.1  Summary](#21summary)
  * [2.2  Motivation](#22motivation)
  * [2.3  Tutorial](#23tutorial)
    + [2.3.1   Interaction](#231interaction)
    + [2.3.2   Messages](#232messages)
      - [2.3.2.1    Create Data Agreement](#2321reate-data-agreement)
      - [2.3.2.1    Read Data Agreement](#2321read-data-agreement)
      - [2.3.2.3    Update Data Agreement](#2323update-data-agreement)
      - [2.3.2.1    Delete Data Agreement](#2321delete-data-agreement)
- [3.0   ADA RFC 0003: Data Agreement Offer Protocol 1.0](#30ada-rfc-0003--data-agreement-offer-protocol-10)
  * [3.1  Summary](#31summary)
  * [3.2  Motivation](#32motivation)
  * [3.3  Tutorial](#33tutorial)
    + [3.2.1   Interaction](#321interaction)
    + [3.2.2   Messages](#322messages)
      - [3.2.2.1    Offer](#3221offer)
      - [3.2.2.2    Accept](#3222accept)
      - [3.2.2.3    Reject](#3223reject)
      - [3.2.2.4    Problem Report](#3224problem-report)
- [4.0   ADA Protocol 0004: Data Agreement Proofs Protocol 1.0](#40ada-protocol-0004--data-agreement-proofs-protocol-10)
  * [4.1  Summary](#41summary)
  * [4.2  Motivation](#42motivation)
  * [4.3  Tutorial](#43tutorial)
    + [4.3.1   Interaction](#431interaction)
    + [4.3.2   Messages](#432messages)
      - [4.3.2.1    Verify Request](#4321verify-request)
      - [4.3.2.2    Verify Response](#4322verify-response)
      - [4.3.2.3    Problem Report](#4323problem-report)
- [5.0   ADA Protocol 0005: Data Agreement Termination Protocol 1.0](#50ada-protocol-0005--data-agreement-termination-protocol-10)
  * [5.1  Summary](#51summary)
  * [5.2  Motivation](#52motivation)
  * [5.3  Tutorial](#53tutorial)
    + [5.3.1   Interaction](#531interaction)
    + [5.3.2   Messages](#532messages)
      - [5.3.2.1    Request withdrawal](#5321request-withdrawal)
      - [5.3.2.2    Confirm withdrawal](#5322confirm-withdrawal)
      - [5.3.2.3    Problem Report](#5323problem-report)
- [6.0   ADA RFC 0006: Data Agreement Context Decorator](#60ada-rfc-0006--data-agreement-context-decorator)
  * [6.1  Summary](#61summary)
  * [6.2  Motivation](#62motivation)
  * [6.3  Tutorial](#63tutorial)
- [7.0   Reference](#70reference)


# 1.0	ADA RFC 0001: MyData DID Protocol 1.0

## 1.1 Summary

This specification defines a DIDComm protocol for performing CRUD operations on `did:mydata`.

## 1.2 Motivation

The protocol allows different objects in iGrant.io automated data agreements (ADA) specification to be treated as valid DIDs. The functionalities available for DID subjects once they own a DID are the following:

*   Sign Data Agreement (s)
*   Authenticate and verify proof chain in Data Agreement VC
*   Tie data exchange transactions to Data Agreement (s) which can be verified by an independent auditor or any party of the agreement


## 1.3 Tutorial

The protocol described in this document is a request-response protocol [10]. This involves two parties, with the requester` `making the first move, and the responder completing the interaction. The responder role is assumed by ADA microservice. The requester can perform CRUD operations on ``did:mydata``.

The following actors are identified as part of ADA specification and can assume the requester` `role:

*   a **Data Source**, the organisation collecting private data, (typically a data controller).  [SSI: Issuer]
*   a **Data Subject** or **Individual**. [SSI: Holder]
*   a **Data Using Service (DUS)**, an organisation processing personal data from one or more data sources to deliver a service. [SSI: Verifier]
*   an** Assessor r**eviews the practices of an organisation**, **conducts a DPIA and drafts data agreements and inter-company agreements for third parties.
*   an** Auditor** may be called in to review the data agreements and ensure they are in place in case of data breaches or regular inspection.

### 1.3.1	Interaction

The ADA service will be exposing a DIDComm agent. CRUD operations on DID can then be performed by the requester using available DIDComm messages. The ADA service itself will be allocated a DID and an associated DID document will be publicly available at an established DID configuration endpoint (`"/.well-known/did-configuration.json"`) of the web server. The DID configuration document conforms to the DID-CORE specification [1]. The public key specified in the DID configuration document is used for constructing DIDComm encryption envelopes by the requester. His envelope is sent to the DIDComm agent mentioned in the service endpoints section within the DID configuration document to communicate with the responder.

### 1.3.2	Messages

The MyData DID protocol consists of these messages:

*   ``create-did``
*   ``create-did-response``
*   ``read-did``
*   ``read-did-response``
*   ``delete-did``
*   ``delete-did-response``
*   ``problem-report``

There are 3 possible operations using the above messages. The 3 operations are - Create, Read and Delete. How to use the above messages are explained below for each operation.  

#### 1.3.2.1	Create Operation

The Create Operation is initiated by the requester by sending the `create-did` DIDComm message to the responder (ADA service). The responder responds with a `create-did-response` message if the operation is successful or with a `problem-report` message if the operation fails.

Creation of the DID follows the sequence below: 

1. The (Public key, Private key) pair is created based on `Ed25519Signature2018` suite which is specified in Linked Data Cryptographic Suite Registry [5]. The Ed25519 algorithm is available in common crypto libraries, for example NACL. 
2. Multicodec [3] prefix for Ed25519 public key is concatenated to raw public key bytes and encoded using base58-btc encoding to obtain the Multibase [2] value (`mb-value`).
3. Create the DID document based on the format specified in Section [3.	DID document](https://docs.google.com/document/d/1c35vyo5zqvfyIO_154-2rQS_pjPkqQnGujd0z0qOOTs/edit#heading=h.26bg4pr79mnu).
4. Construct a DIDComm plaintext message conforming to DIDComm message specification [4] with type - [https://didcomm.org/mydata-did/1.0/create-did](https://didcomm.org/mydata-did/1.0/create-did) as given in the example below:

```json
{
  "@id": "6a956d0d-b117-41d5-841e-7190ee0e5d5f",
  "@type": "https://didcomm.org/mydata-did/1.0/create-did",
  "to" : "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "did": {
      "@context": "https://w3id.org/did/v1",
      ....
      Rest of the DID document
      ....
    }
  },
  "~transport": {
    "return_route": "all"
  }
}
```

*   **<code>id</code></strong> is a unique value to the sender which indicates the message ID.
*   <strong><code>type</code></strong> indicates the message type. Message type decides how the message contents must be processed.
*   <strong><code>from</code></strong> is a valid DID which identifies the sender. This can be the same as the DID that is getting created.
*   <strong><code>to</code></strong> is a valid DID which identifies the recipient. It is the value of ADA service’s DID. This is obtained from the DID document available at the root endpoint of ADA service. 
*   <strong><code>created_time</code></strong> indicates the message created time. This is represented in UTC epoch seconds.
*   <strong><code>body</code></strong> contains the actual message body. In this case it contains the DID document that is getting created and stored in the DID registry.
*   <strong><code>~transport</code></strong> decorator is optional. This decorator conforms to ARIES RFC 0092 [6]. If it’s specified and the value of <code>return_route </code>is <code>all</code>, then it means ADA service must respond to the respective DIDComm message using the same HTTP connection. This is useful when the client sending a DIDComm message doesn’t have any inbound route available.
5. A DIDComm encryption envelope is constructed according to the steps documented in ARIES RFC 0019 [7] to wrap the above plaintext message. Encryption envelope uses a standard format built on JSON Web Encryption (JWE) [8].  The public key used is fetched from the DID document available at the well-known DID configuration endpoint of the ADA service. Mode of encryption when creating JWE is anoncrypt. 
6. The encryption envelope is transmitted to the ADA service by performing HTTP POST operation. For ADA service to process the incoming message, the <code>content-type</code> HTTP header provided is <code>application/didcomm-encrypted+json</code>. 
7. The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/create-did-response](https://didcomm.org/mydata-did/1.0/create-did-response). An example is given below:

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/create-did-response",
  "@id": "4f4fbc78-be45-4029-ad18-a808c3a36ac2",
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "created_time": "1622649143",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "body": {
    "did": {
      "@context": "https://w3id.org/did/v1",
      ....
      Rest of the DID document
      ....
    },
    "version": "1.0"
  }
}
```

If a problem arises while handling the <code>create-did</code> message, the ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "description": "Chosen DID exists, try again with a different one",
  "description~l10n": {
    "code": "did-exists"
  },
  "problem_items": [
    {
      "did": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
    }
  ]
}
```

#### 1.3.2.2	Read Operation

The Read Operation is initiated by the requester by sending the `read-did` DIDComm message to the responder (ADA service). The responder responds with a `read-did-response` message if the operation is successful or with a `problem-report` message if the operation fails.

To resolve a DID and fetch the associated DID document from the DID registry, a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/read-did](https://didcomm.org/mydata-did/1.0/read-did) must be constructed. An example is given below:

```json
{
  "@id": "3e914e45-28f0-4009-b9c2-e2df2ba165b8",
  "@type": "https://didcomm.org/mydata-did/1.0/read-did",
  "to" : "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "did": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
  },
  "~transport": {
    "return_route": "all"
  }
}
```

The **<code>did</code></strong> attribute in the message body represents the DID that will be resolved.

The above example requests the ADA service to resolve `did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E` Notice the message json+ld doesn’t contain a **<code>from</code></strong> key, since the read operation can be performed without owning a DID. The packing algorithm used for constructing the DIDComm encryption envelope should be anoncrypt.

The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/read-did-response](https://didcomm.org/mydata-did/1.0/create-did-response). An example is given below:

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/read-did-response",
  "@id": "4f4fbc78-be45-4029-ad18-a808c3a36ac2",
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "~thread": {
    "thid": "3e914e45-28f0-4009-b9c2-e2df2ba165b8"
  },
  "body": {
    "did": {
      "@context": "https://w3id.org/did/v1",
      ....
      Rest of the DID document
      ....
    },
    "version": "1.0"
  }
}
```

If a problem arises while handling the `read-did` message, the ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "3e914e45-28f0-4009-b9c2-e2df2ba165b8"
  },
  "description": "DID not found",
  "description~l10n": {
    "code": "did-not-found"
  },
  "problem_items": [
    {
      "did": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
    }
  ]
}
```

#### 1.3.2.3	Update Operation

In the current version of this specification, Update operation on DID documents is not permitted  at the moment because it would mean the DID controllers could change the public key associated with DID. Our `did:mydata` DID uses a public key to create the method specific identifier. That means if the public key changes, the method specific identifier should also change.

#### 1.3.2.4	Delete Operation

The Delete Operation is initiated by the requester by sending the `delete-did` DIDComm message to the responder (ADA service). The responder responds with a `delete-did-response` message if the operation is successful or with a `problem-report` message if the operation fails.

In the current version of this specification, delete is relevant only when triggered by a DID controller to renew the existing DID to a new one (e.g. when a mobile device with the digital wallet which includes the private key is lost).  In this case, the old DID is marked as revoked. It could then be replaced with the newly created DID. To delete a DID, a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/delete-did](https://didcomm.org/mydata-did/1.0/read-did) must be constructed. An example is given below:

```json
{
  "@id": "6a956d0d-b117-41d5-841e-7190ee0e5d5f",
  "@type": "https://didcomm.org/mydata-did/1.0/delete-did",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to" : "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "did": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
  },
  "~transport": {
    "return_route": "all"
  }
}
```

The **<code>did</code></strong> attribute in the message body represents the DID that will be marked as revoked.

The packing algorithm used for constructing the DIDComm encryption envelope should be authcrypt.

The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/delete-did-response](https://didcomm.org/mydata-did/1.0/create-did-response). An example is given below:

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/delete-did-response",
  "@id": "4f4fbc78-be45-4029-ad18-a808c3a36ac2",
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "created_time": "1622649143",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "body": {
    "status": "revoked"
  }
}
```

If a problem arises while handling the `delete-did` message, ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
"from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "description": "DID not found",
  "description~l10n": {
    "code": "did-not-found"
  },
  "problem_items": [
    {
      "did": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
    }
  ]
}
```

# 2.0	ADA RFC 0002: Data Agreement Protocol 1.0

## 2.1	Summary

This specification defines DIDComm protocol for performing CRUD operations on Data Agreements.

## 2.2	Motivation

We need a standard protocol to perform Create, Read, Update, Delete operations on Data Agreements.

Data Agreement is an agreement between organisations and individuals in the use of personal data. Data Agreement can have any of the legal basis that is outlined as per data protection law or regulation, such as the GDPR. The agreement with individuals could be with a  Data Source (issuer) or a Data Using Service. 

The focus of this specification is on the Data Agreement, how it is defined and prepared by an organisation wishing to use personal data.

## 2.3	Tutorial

The protocol described in this document is a request-response protocol [10]. This involves two parties, with the requester` `making the first move, and the responder completing the interaction. The responder role is assumed by ADA microservice. The requester can perform CRUD operations on Data Agreements.

The following actors identified as part of the Data Agreement specification can assume the requester` `role:

*   a **Data Source**, the organisation collecting private data, (typically a data controller).  [SSI: Issuer]
*   a **Data Using Service (DUS)**, an organisation processing personal data from one or more data sources to deliver a service. [SSI: Verifier]
*   an** Assessor r**eviews the practices of an organisation**, **conducts a DPIA and drafts data agreements and inter-company agreements for third parties.

### 2.3.1	Interaction

In the data agreement workflow [Ref. ADA Functional Specification for details], the “Definition” and “Preparation” phases involve the operations described in the subchapters. These operations are used by any data protection impact assessment (DPIA) to “convert” DPIA output to standardised data agreements.

An ADA service (responder) will be exposing a DIDComm agent. CRUD operations on Data Agreement can then be performed by the requester using available DIDComm messages. The ADA service itself will be allocated a DID and an associated DID document will be publicly available at an established DID configuration endpoint (`"/.well-known/did-configuration.json"`) of the web server. DID configuration document conforms to the DID-CORE specification [1]. The public key specified in the DID configuration document is used for constructing DIDComm encryption envelopes by the requester and this envelope is sent to the DIDComm agent mentioned in the service endpoints section within the DID configuration document to communicate with the responder.

### 2.3.2	Messages

The Data Agreements protocol consists of these messages:

*   ``create-data-agreement``
*   ``create-data-agreement-response``
*   ``read-data-agreement``
*   ``read-data-agreement-response``
*   ``update-data-agreement``
*   ``update-data-agreement-response``
*   ``delete-data-agreement``
*   ``delete-data-agreement-response``
*   ``problem-report``

#### 2.3.2.1	Create Data Agreement

An organisation (requester) intending to create a data agreement must construct a `create-data-agreement `DIDComm message and encrypt it using the DID and send it to the ADA service (responder). An example of a `create-data-agreement` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/create-data-agreement",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "data_agreement": {
      "@context": "https://schema.igrant.io/data-agreements/v1",
      "usage_purpose": "Customized shopping experience",
      "usage_purpose_description": "Collecting user data for offering custom tailored shopping experience",
      "data_policy": {
        "data_retention_period": "365"
      },
      "personal_data": {
        "attribute_names": [
          "Name",
          "Age"
        ]
      },
      "code_of_conduct": {
        "dpia_conducted": true,
        "dpia_passed": true,
        "dpia_date": "2021-05-08T08:41:59+0000",
        "dpia_verification_url": "https://org.com/dpia_results.html"
      },
      "data_sharing": {
        "exchange": true,
        "role": "<null/issuer/verifier>"
      }
    }
  }
}
```

On receiving the above message, the ADA service (responder) will store the Data Agreement to a persistence (for e.g. a distributed ledger) and respond with a DIDComm message of type `create-data-agreement-response`. An example is given below:

```json
{
  "@id": "89bb4c34-c834-4e2a-b2bd-1a459e145100",
  "@type": "https://didcomm.org/data-agreements/1.0/create-data-agreement-response",
  "~thread"      	: {
	"thid": "b589c166-e0f0-44eb-9b41-ee2c09b26bec"
  },
  "data_agreement_id": "45988dd2-62b9-4ede-8189-fb99c64b42d1"
}
```

If a problem arises while handling the `create-data-agreement` message, ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:


```
{
  "@type": "https://didcomm.org/data-agreements/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "description": "Failed to create data agreement",
  "description~l10n": {
    "code": "creation-failure"
  }
}
```

#### 2.3.2.1	Read Data Agreement

An organisation (requester) can perform a read operation on a data agreement that it created. The read operation allows fetching a data agreement by ID. An example of a `read-data-agreement` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/read-data-agreement",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "data_agreement_id": "45988dd2-62b9-4ede-8189-fb99c64b42d1"
}
```

An example of a `read-data-agreement-response` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/read-data-agreement-response",
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "data_agreement": {
    "@context": "https://schema.igrant.io/data-agreements/v1",
     ...
  },
  "data_agreement_meta": {
     ...
  }
}
```

If a problem arises while handling the `read-data-agreement` message, ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```json
{
  "@type": "https://didcomm.org/data-agreements/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "description": "Data agreement not found",
  "description~l10n": {
    "code": "data-agreement-not-found"
  }
}
```

#### 2.3.2.3	Update Data Agreement

When an organisation performs an update operation on a data agreement, a copy of the data agreement is created and the updates are applied to the copy. The version number is incremented to represent the change.

An example of an `update-data-agreement` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/update-data-agreement",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "data_agreement_id": "<data_agreement_id>",
  "data_agreement": {
    ...
  }
}
```

On a successful update, an `update-data-agreement-response` message is triggered. This message returns the new data agreement ID. An example is:

```
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/update-data-agreement-response",
  "data_agreement_id": "<data_agreement_id>"
}
```

#### 2.3.2.1	Delete Data Agreement

When an organisation performs a delete operation on a data agreement, the delete operation doesn’t remove the data agreement from the persistence, instead it marks the data agreement as revoked.

```
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/delete-data-agreement",
  "data_agreement_id": "<data_agreement_id>"
}
```

# 3.0	ADA RFC 0003: Data Agreement Offer Protocol 1.0

## 3.1	Summary

This specification defines DIDComm protocol for an Organisation (Data Source or Data Using Service) to send a Data Agreement offer to an Individual (Data Subject). Individuals receiving the Data Agreement Offer can decide to accept or reject it. 

## 3.2	Motivation

We need a standard protocol to perform the Data Agreement Offer lifecycle.

## 3.3	Tutorial

The protocol described in this document is a request-response protocol [10]. This involves two parties, with the requester` `making the first move, and the responder completing the interaction. The responder role is assumed by **Data Subject**.

Following actors identified as part of ADA specification can assume the requester` `role:

*   a **Data Source**, the organisation collecting private data, (typically a data controller).  [SSI: Issuer]
*   a **Data Using Service (DUS)**, an organisation processing personal data from one or more data sources to deliver a service. [SSI: Verifier]

### 3.2.1	Interaction

Using an already established pairwise connection and agent-to-agent communication, the requester sends a Data Agreement offer to the responder. The responder can decide to accept or reject it. If the responder accepts the offer, it will result in credential issuance or proof presentation based on the mode of data exchange as described in the Data Agreement.

### 3.2.2	Messages

The Data Agreement Offer protocol consists of these messages:

*   ``offer``
*   ``accept``
*   ``reject``
*   ``problem-report``

#### 3.2.2.1	Offer

An organisation (requester) intending to send a Data Agreement offer to a Data Subject (responder) must construct an `offer `DIDComm message and encrypt it using the DID and send it to the Data Subject (responder) using an already established pairwise connection and agent-to-agent communication. An example of an `offer` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreement-offer/1.0/offer",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:1:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "data_agreement_offer": {
      "@context": [
        "https://www.w3.org/2018/credentials/v1"
      ],
      "id": "69f7f38a-b7d1-4d13-9de4-84a256703890",
      "type": [
        "VerifiableCredential",
        "DataAgreementOffer"
      ],
      "issuer": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
      "issuanceDate": "2010-01-01T19:73:24Z",
      "credentialSubject": {
        "id": "did:mydata:1:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
        "data_agreement": {
          "@context": "https://schema.igrant.io/data-agreements/v1",
          "usage_purpose": "Customized shopping experience",
          "usage_purpose_description": "Collecting user data for offering custom tailored shopping experience",
          "data_policy": {
            "data_retention_period": "365"
          },
          "personal_data": {
            "attribute_names": [
              "Name",
              "Age"
            ]
          },
          "code_of_conduct": {
            "dpia_conducted": true,
            "dpia_passed": true,
            "dpia_date": "2021-05-08T08:41:59+0000",
            "dpia_verification_url": "https://org.com/dpia_results.html"
          },
          "data_sharing": {
            "exchange": true,
            "role": "<null/issuer/verifier>"
          }
        }
      },
      "proof": [
        {
          "type": "Ed25519VerificationKey2018",
          "created": "2017-06-18T21:19:10Z",
          "proofPurpose": "assertionMethod",
          "verificationMethod": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
          "jws": "eyJhbGciOiJQUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19
      ..DJBMvvFAIC00nSGB6Tn0XKbbF9XrsaJZREWvR2aONYTQQxnyXirtXnlewJMB
      Bn2h9hfcGZrvnC1b6PgWmukzFJ1IiH1dWgnDIS81BH-IxXnPkbuYDeySorc4
      QU9MJxdVkY5EL4HYbcIfwKj6X4LBQ2_ZHZIu1jdqLcRZqHcsDF5KKylKc1TH
      n5VRWy5WhYg_gBnyWny8E6Qkrze53MR7OuAmmNJ1m1nN8SxDrG6a08L78J0-
      Fbas5OjAQz3c17GY8mVuDPOBIOVjMEghBlgl3nOi1ysxbRGhHLEK4s0KKbeR
      ogZdgt1DkQxDFxxn41QWDw_mmMCjs9qxg0zcZzqEJw"
        }
      ]
    }
  }
}
```

#### 3.2.2.2	Accept

On receiving the `offer` message, the Data Subject (responder) is displayed the contents of the Data Agreement offer, and can decide to accept it or reject it. If the Data Subject decides to accept the offer:

1. Data Agreement Offer is represented as a verifiable credentia which conforms to W3C VC-DATA-MODEL recommendation.
2. The proof chain in the Data Agreement Offer is verified and authenticated as per the algorithm specified in W3C LINKED DATA PROOF 1.0 specification.
3. The Data Subject should add the counter signature to the proof chain in the Data Agreement Offer using the proof algorithm.
4. An `accept` DIDComm plain-text message is constructed. An example is as given below:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreement-offer/1.0/accept",
  "from": "did:mydata:1:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:0:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "body": {
    "data_agreement_offer": {
      "acceptance_proof": [
        {
          "type": "Ed25519VerificationKey2018",
          "created": "2017-06-18T21:19:10Z",
          "proofPurpose": "assertionMethod",
          "verificationMethod": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
          "jws": "eyJhbGciOiJQUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19
      ..DJBMvvFAIC00nSGB6Tn0XKbbF9XrsaJZREWvR2aONYTQQxnyXirtXnlewJMB
      Bn2h9hfcGZrvnC1b6PgWmukzFJ1IiH1dWgnDIS81BH-IxXnPkbuYDeySorc4
      QU9MJxdVkY5EL4HYbcIfwKj6X4LBQ2_ZHZIu1jdqLcRZqHcsDF5KKylKc1TH
      n5VRWy5WhYg_gBnyWny8E6Qkrze53MR7OuAmmNJ1m1nN8SxDrG6a08L78J0-
      Fbas5OjAQz3c17GY8mVuDPOBIOVjMEghBlgl3nOi1ysxbRGhHLEK4s0KKbeR
      ogZdgt1DkQxDFxxn41QWDw_mmMCjs9qxg0zcZzqEJw"
        }
      ]
    }
  }
}
```

#### 3.2.2.3	Reject

On receiving the `offer` message, the Data Subject (responder) is displayed the contents of the Data Agreement offer, and can decide to accept it or reject it. If the Data Subject decides to reject the offer, a `reject` DIDComm message must be constructed and sent to the requester. This message is to notify the requester that the Data Subject rejected the Data Agreement Offer. An example of `reject` message is as given below:

```json
 {
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreement-offer/1.0/reject",
  "from": "did:mydata:1:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:0:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  }
}
```

#### 3.2.2.4	Problem Report

Errors might occur in various places. All errors are modeled with problem-report messages. If a problem arises, the agent will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```json
{
  "@type": "https://didcomm.org/data-agreements/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "description": "Signatures verification failed",
  "description~l10n": {
    "code": "authentication-failure"
  }
}
```

# 4.0	ADA Protocol 0004: Data Agreement Proofs Protocol 1.0


## 4.1	Summary

This specification describes a DIDComm protocol to verify and authenticate the proof chain associated with a Data Agreement Offer.


## 4.2	Motivation

We need a standard protocol to verify and authenticate the proof chain associated with a Data Agreement Offer.


## 4.3	Tutorial

The protocol described in this document is a request-response protocol [10].This involves two parties, with the requester` `making the first move, and the responder completing the interaction. The responder role is assumed by ADA microservice. The requester can verify and authenticate proof chains associated with the Data Agreement offer.

Following actors identified as part of ADA specification can assume the requester role:

*   an **Auditor** may be called in to review the data agreements and ensure that an agreement is in place in case of data breaches or regular inspection.


### 4.3.1	Interaction

An ADA service (responder) will be exposing a DIDComm agent. Requester can verify and authenticate proof chain associated with a Data Agreement Offer using available DIDComm messages. The ADA service itself will be allocated a DID and an associated DID document will be publicly available at a well-known DID configuration endpoint (`"/.well-known/did-configuration.json"`) of the web server. The DID configuration document conforms to DID-CORE specification [1]. The public key specified in the DID configuration document is used for constructing DIDComm encryption envelopes by the requester and this envelope is sent to the DIDComm agent mentioned in the service endpoints section within the DID configuration document to communicate with the responder.

### 4.3.2	Messages

The Data Agreement Offer protocol consists of these messages:

*   ``verify-request``
*   ``verify-response``
*   ``problem-report``

#### 4.3.2.1	Verify Request

An organisation (Data Using Service or Data Source) or an individual (Data Subject) can start an audit process by providing a Data Agreement Receipt ID and DID(s) used for creating proof chains through an out of band process. This receipt ID is assigned by the ADA service when the Data Agreement Offer was persisted into the receipt registry. Auditor (requester) intending to verify the proof chain associated with the Data Agreement Offer must construct an `verify-request `DIDComm message and encrypt it using the DID and send it to the ADA service (responder. An example of a `verify-request `DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreement-proofs/1.0/verify-request",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "data_agreement_receipt_id": "bc8ef3aa-f12b-4dc3-beab-6e9ad56be183",
    "did": [
      "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
      "did:mydata:1:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw"
    ]
  }
}
```

#### 4.3.2.2	Verify Response

ADA after receiving the above message processes the request, the verification of the proof chain conforms to the proof verification algorithm specified in LINKED DATA PROOFS 1.0 specification.

The following algorithm specifies how to check the authenticity and integrity of a counter signed Data Agreement (signed linked data document) by verifying its digital proof. This algorithm takes a counter signed Data Agreement (signed linked data document) and outputs a `true` or `false` value based on whether or not the digital proof on the signed document was verified. Whenever this algorithm encodes strings, it _MUST_ use UTF-8 encoding.

*   Get the public key by dereferencing its URL identifier in the `proof` node of the default graph of the signed document. Confirm that the linked data document that describes the public key specifies its owner and that its owner's URL identifier can be dereferenced to reveal a bi-directional link back to the key. Ensure that the key's owner is a trusted entity before proceeding to the next step.
*   Let the document be a copy of the counter signed Data Agreement.
*   Remove any `proof` nodes from the default graph in the document and save it as proof.
*   Generate a canonicalized document by canonicalizing the document according to the canonicalization algorithm (e.g. the _URDNA2015_ [RDF-DATASET-NORMALIZATION] algorithm).
*   Create a value tbv that represents the data to be verified, and set it to the result of running the Create Verify Hash Algorithm, passing the information in proof.
*   Pass the proofValue, tbv, and the public key to the proof algorithm (e.g. JSON Web Proof using _RSASSA-PKCS1-v1_5_ algorithm). Return the resulting boolean value.

ADA service (responder) will respond with a DIDComm message of type `verify-response`. An example is given below:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreement-proofs/1.0/verify-response",
  "from": "did:mydata:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:0:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "body": {
    "verification_status": "verified"
  }
}
```

#### 4.3.2.3	Problem Report

Errors might occur in various places. All errors are modeled with problem-report messages. If a problem arises, the agent will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```json
{
  "@type": "https://didcomm.org/data-agreement-proofs/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "from": "did:mydata:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "description": "Signatures verification failed",
  "description~l10n": {
    "code": "authentication-failure"
  }
}
```

# 5.0	ADA Protocol 0005: Data Agreement Termination Protocol 1.0


## 5.1	Summary

This specification describes a DIDComm protocol for a Data Subject to terminate a Data Agreement and notify the Organisation (Data Using Service or Data Source)


## 5.2	Motivation

The Data Agreement may be terminated in a number of ways. Here are a few of the scenarios:

1. Data Agreement expired and the service is no longer applicable
2. Updated Data Agreement with new purpose or changes to collected pii categories
3. Individual requests to terminate the service before expiration date
4. Individual requests not only to terminate but to have their personal data erased

Note, the erasure may come after termination of the Data Agreement.

## 5.3	Tutorial

The protocol described in this document is a request-response protocol [10].This involves two parties, with the requester` `making the first move, and the responder completing the interaction. The responder role is assumed by an Organisation (Data Source or Data Using Service). The requester can initiate the Data Agreement termination.

Following actors identified as part of ADA specification can assume the requester role:

*   a **Data Subject** or **Individual**. [SSI: Holder]

Once a Data Agreement is terminated, the proofs associated with termination are recorded to the ADA service which acts as a central source of truth.

### 5.3.1	Interaction

Using an already established pairwise connection and agent-to-agent communication requester can initiate the data agreement termination process. The responder (Organization) accepts the request by responding with a Data Agreement termination offer signed by `did:mydata`.

### 5.3.2	Messages

The Data Agreement Termination protocol consists of these messages:

*   ``request-withdrawal``
*   ``confirm-withdrawal``
*   ``problem-report``

#### 5.3.2.1	Request withdrawal

A Data Subject (requester) intending to terminate a Data Agreement initiates the termination process by sending a request for withdrawal to the Organisation (responder). Data Subject (requester) must construct a `request-withdrawal `DIDComm message and encrypt it using the DID and send it to the Organisation (responder) using an already established pairwise connection and agent-to-agent communication. An example of a `request-withdrawal `DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreement-termination/1.0/request-withdrawal",
  "from": "did:mydata:1:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:0:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "body": {
    "data_agreement_withdrawal_request": {
      "@context": [
        "https://www.w3.org/2018/credentials/v1"
      ],
      "id": "69f7f38a-b7d1-4d13-9de4-84a256703890",
      "type": [
        "VerifiableCredential",
        "DataAgreementWithdrawalRequest"
      ],
      "issuer": "did:mydata:1:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
      "issuanceDate": "2010-01-01T19:73:24Z",
      "credentialSubject": {
        "id": "did:mydata:0:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
        "data_agreement_receipt_id": "bc8ef3aa-f12b-4dc3-beab-6e9ad56be183",
      "proof": [
        {
          "type": "Ed25519VerificationKey2018",
          "created": "2017-06-18T21:19:10Z",
          "proofPurpose": "assertionMethod",
          "verificationMethod": "did:mydata:1:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
          "jws": "eyJhbGciOiJQUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19
      ..DJBMvvFAIC00nSGB6Tn0XKbbF9XrsaJZREWvR2aONYTQQxnyXirtXnlewJMB
      Bn2h9hfcGZrvnC1b6PgWmukzFJ1IiH1dWgnDIS81BH-IxXnPkbuYDeySorc4
      QU9MJxdVkY5EL4HYbcIfwKj6X4LBQ2_ZHZIu1jdqLcRZqHcsDF5KKylKc1TH
      n5VRWy5WhYg_gBnyWny8E6Qkrze53MR7OuAmmNJ1m1nN8SxDrG6a08L78J0-
      Fbas5OjAQz3c17GY8mVuDPOBIOVjMEghBlgl3nOi1ysxbRGhHLEK4s0KKbeR
      ogZdgt1DkQxDFxxn41QWDw_mmMCjs9qxg0zcZzqEJw"
        }
      ]
    }
  }
}
```

#### 5.3.2.2	Confirm withdrawal

On receiving the `request-withdrawal` message, the organisation (responder) must construct a `confirm-withdrawal `DIDComm message and encrypt it using the DID and send it to the Data Subject (responder) using an already established pairwise connection and agent-to-agent communication. The organisation is informing the Data Subject and the associated data is no longer bound to the Data Agreement. An example of a `confirm-withdrawal` DIDComm message is:

```json
{
  "@id": "6a956d0d-b117-41d5-841e-7190ee0e5d5f",
  "@type": "https://didcomm.org/data-agreement-termination/1.0/confirm-withdrawal",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "to": "did:mydata:1:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "created_time": "1622649143",
  "~thread": {
    "thid": "b589c166-e0f0-44eb-9b41-ee2c09b26bec"
  },
  "body": {
    "confirm_withdrawal": {
      "confirmation_proof": [
        {
          "type": "Ed25519VerificationKey2018",
          "created": "2017-06-18T21:19:10Z",
          "proofPurpose": "assertionMethod",
          "verificationMethod": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
          "jws": "eyJhbGciOiJQUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19
      ..DJBMvvFAIC00nSGB6Tn0XKbbF9XrsaJZREWvR2aONYTQQxnyXirtXnlewJMB
      Bn2h9hfcGZrvnC1b6PgWmukzFJ1IiH1dWgnDIS81BH-IxXnPkbuYDeySorc4
      QU9MJxdVkY5EL4HYbcIfwKj6X4LBQ2_ZHZIu1jdqLcRZqHcsDF5KKylKc1TH
      n5VRWy5WhYg_gBnyWny8E6Qkrze53MR7OuAmmNJ1m1nN8SxDrG6a08L78J0-
      Fbas5OjAQz3c17GY8mVuDPOBIOVjMEghBlgl3nOi1ysxbRGhHLEK4s0KKbeR
      ogZdgt1DkQxDFxxn41QWDw_mmMCjs9qxg0zcZzqEJw"
        }
      ]
    }
  }
}
```

#### 5.3.2.3	Problem Report

Errors might occur in various places. All errors are modeled with problem-report messages. If a problem arises, the agent will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:


```json
{
  "@type": "https://didcomm.org/data-agreement-proofs/1.0/problem-report",
  "@id": "2c8579d3-ecdd-4427-9e2f-7f911917de6c",
  "~thread": {
    "thid": "6a956d0d-b117-41d5-841e-7190ee0e5d5f"
  },
  "from": "did:mydata:1:z6MkpFAimMHJSwxWcm9rmt7XHjwkdLFNeZ5eRhxr6b4xK8rw",
  "to": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "description": "Cannot process the termination request at the moment.",
  "description~l10n": {
    "code": "termination-failure"
  }
}
```

# 6.0	ADA RFC 0006: Data Agreement Context Decorator

## 6.1	Summary

The ~data-agreement-context decorator describes the associated Data Agreement protocol or holds references to a counter-signed Data Agreement document inline to a DIDComm message.

## 6.2	Motivation

A DIDComm message should be capable of carrying additional metadata about the data agreement associated with the transaction.

## 6.3	Tutorial

Usage looks like this,

```json
{
  "~data-agreement-context": {
    "message_type": "protocol or data_agreement_receipt_id",
    "message": {
      
    }
  }
}
```

Core properties of a ~data-agreement-context decorator are:

1. `message_type `- Indicates the type of message embedded in the decorator. Possible types are 1) protocol - Indicates that message embedded is an ADA protocol and it must be processed accordingly 2) data_agreement_receipt_id - Indicates that message contains reference to a data agreement offer.
2. `message `- Hold the message body.

~data-agreement-context decorator could carry an ADA protocol message inside it or a reference to data agreement offer. When it carries a reference to a data agreement offer, it is understood that the outlying DIDComm message is occurring in reference to a data agreement that was signed by both requester (Data Source or Data Using Service) and responder (Data Subject). The context decorator could also carry an ADA protocol message, for example, If Data Source would like to send Data Agreement Offer to a Data Subject when offering a credential preview, it could do so by embedding `https://didcomm.org/data-agreement-offer/1.0/offer` DIDComm message. The Data Subject can respond while making a presentation request by embedding the response  `https://didcomm.org/data-agreement-offer/1.0/offer-response`  DIDComm message  in the decorator.


# 7.0	Reference

1. DID core specification: [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/)
2. IETF Multibase Data Format specification: [https://tools.ietf.org/html/draft-multiformats-multibase](https://tools.ietf.org/html/draft-multiformats-multibase) 
3. Multicodec - Compact self-describing codecs: [https://github.com/multiformats/multicodec](https://github.com/multiformats/multicodec)
4. DIDComm message specification: [https://identity.foundation/didcomm-messaging/spec/](https://identity.foundation/didcomm-messaging/spec/) 
5. Linked Data Cryptographic Suit Registry: [https://w3c-ccg.github.io/ld-cryptosuite-registry/](https://w3c-ccg.github.io/ld-cryptosuite-registry/) 
6. Aries RFC 0092: Transports Return Route: [https://github.com/hyperledger/aries-rfcs/tree/master/features/0092-transport-return-route](https://github.com/hyperledger/aries-rfcs/tree/master/features/0092-transport-return-route) 
7. Aries RFC 0019: Encryption Envelope:  [https://github.com/hyperledger/aries-rfcs/tree/master/features/0019-encryption-envelope](https://github.com/hyperledger/aries-rfcs/tree/master/features/0019-encryption-envelope)
8. IETF RFC 7516 - JSON Web Encryption: [https://datatracker.ietf.org/doc/html/rfc7516](https://datatracker.ietf.org/doc/html/rfc7516)
9. Aries RFC 0035 - Report Problem Protocol 1.0: [https://github.com/hyperledger/aries-rfcs/tree/master/features/0035-report-problem](https://github.com/hyperledger/aries-rfcs/tree/master/features/0035-report-problem)
10. Aries RFC  003 - Protocols: [https://github.com/hyperledger/aries-rfcs/tree/master/concepts/0003-protocols#types-of-protocols](https://github.com/hyperledger/aries-rfcs/tree/master/concepts/0003-protocols#types-of-protocols) 
