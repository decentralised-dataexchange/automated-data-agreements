# Data Agreement DID Method Specification

## Abstract

A Data Agreement exists between organisations and individuals in the use of personal data. This agreement can have any legal basis that is outlined according to any data protection regulation, such as the GDPR.

## Status of This Document

Draft: Work in progress

## Table of Contents

- [1.    Introduction](#1introduction)
- [2.    The `did:mydata` Format](#2the-didmydata-format)
- [3.    DID document](#3did-document)
- [4.    DID Operation Definitions](#4did-operation-definitions)
  * [4.1  Create (Register)](#41create-register)
  * [4.2  Read (Resolve)](#42read-resolve)
  * [4.3  Update](#43update)
  * [4.4  Delete](#44delete)
- [5.    Security Considerations](#5security-considerations)
- [6.    Privacy Considerations](#6privacy-considerations)
- [7.    Reference Implementation](#7reference-implementation)
- [8.    Resources](#8resources)

## 1.	Introduction

This document proposes a new DID method that allows different objects in iGrant.io automated data agreements (ADA) specification to be treated as valid DIDs. Some of the functionalities available for DID subjects to perform once they own a DID are the following:

*   Sign Data Agreement (s)
*   Authenticate and verify proof chain in Data Agreement VC
*   Bind data exchange transactions to Data Agreement (s) which can be verified by an independent auditor or any one else


## 2.	The `did:mydata` Format

The format for the `did:mydata` method conforms to the DID core specification [1]. It consists of the `did:mydata` prefix, followed by `did-type-integer` value, followed by the `mb-value` value.

The `mb-value` is a Multibase [2] `base58-btc` encoded value of the Multicodec [3] identifier for Ed25519 public key concatenated with raw Ed25519 public key bytes. The iGrant.io DID scheme is defined by the following ABNF:

```
mydata-did = "did:mydata" (":" did-type-integer) ":" mb-value

did-type-integer = 0 / 1 / 2 / 3 / 4
 
mb-value = z[a-km-zA-HJ-NP-Z1-9]+
```

`did-type-integer` is optional and it represents the type of the DID subject identified by the DID. Following are the available types:

*   0 - represents a **Data Source**, an organisation collecting personal data
*   1 - represents a **Data Subject** or **Individual**
*   2 - represents a **Data Using Service**, an organisation processing personal data from one or more data sources to deliver a service
*   3 - represents an** Assessor **who reviews the practices of an organisation**, **conducts a DPIA and drafts data agreements and intercompany agreements for third parties
*   4 - represents an** Auditor** who may be called in to review the data agreements and ensure they are in place in case of data breaches or regular inspection.

Example, a valid MyData/iGrant.io DID could be:

```
did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E
```

The above DID identifies a Data Source (0).

## 3.	DID document

MyData/iGrant.io DID document conforms to DID core specification [1] and supports all core properties. 

Example of MyData/iGrant.io DID document:

```
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
  "verificationMethod": [
    {
      "id": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E#1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
      "publicKeyMultibase": "z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
    }
  ],
  "authentication": [
    {
      "type": "Ed25519VerificationKey2018",
      "publicKey": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E#1"
    }
  ],
  "service": [
    {
      "id": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E;didcomm",
      "type": "DIDComm",
      "priority": 0,
      "recipientKeys": [
        "z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E"
      ],
      "serviceEndpoint": "https://ada-agent.example.com/service-x"
    }
  ]
}
```


The property `service` is optional. The service defined with type `DIDComm` describes an agent that supports DIDComm capabilities which may or may not be iGrant.io specific DIDComm protocols. For e.g. A Data Subject could trigger a `revoke-notification` DIDComm message to inform a Data Using Service’s service endpoint that a Data Agreement is added to the revocation list.

## 4.	DID Operation Definitions

An ADA service will be exposing a DIDComm agent. CRUD operations on DID can then be performed using available DIDComm messages. A new DIDComm protocol ([https://didcomm.org/mydata-did/1.0](https://didcomm.org/mydata-did/1.0)) is specified below for performing CRUD operations. The ADA service itself will be allocated a DID and an associated DID document will be publicly available at a well-known DID configuration endpoint (`"/.well-known/did-configuration.json"`) of the web server. The audience of this document is expected to be familiar with DIDComm message specification [4] and its extensions.

### 4.1	Create (Register)

Creation of the DID follows the sequence below: 

1. The (Public key, Private key) pair is created based on `Ed25519Signature2018` suite which is specified in Linked Data Cryptographic Suite Registry [5]. Ed25519 algorithm is available in common crypto libraries, for example NACL. 
2. Multicodec [3] prefix for Ed25519 public key is concatenated to raw public key bytes and encoded using base58-btc encoding to obtain the Multibase [2] value (`mb-value`).
3. Create the DID document based on the format specified in [Section 3 (DID document)](#3did-document).
4. Construct a DIDComm plaintext message conforming to DIDComm message specification [4] with type - [https://didcomm.org/mydata-did/1.0/create-did](https://didcomm.org/mydata-did/1.0/create-did) as given in the example is given below:

```
{
  "@id": "6a956d0d-b117-41d5-841e-7190ee0e5d5f",
  "@type": "https://didcomm.org/mydata-did/1.0/create-did",
  "from": "did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E",
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

*   <strong><code>id</code></strong> is a unique value to the sender which indicates the message ID.
*   <strong><code>type</code></strong> indicates the message type. Message type decides how the message contents must be processed.
*   <strong><code>from</code></strong> is a valid DID which identifies the sender. This can be the same as the DID that is getting created.
*   <strong><code>to</code></strong> is a valid DID which identifies the recipient. It is the value of ADA service’s DID. This is obtained from the DID document available at the root endpoint of ADA service. 
*   <strong><code>created_time</code></strong> indicates message created time. This is represented in UTC epoch seconds.
*   <strong><code>body</code></strong> contains the actual message body. In this case it contains the DID document that is getting created and stored in the DID registry.
*   <strong><code>~transport</code></strong> decorator is optional. This decorator conforms to ARIES RFC 0092 [6]. If it’s specified and the value of <code>return_route </code>is <code>all</code>, then it means ADA service must respond to the respective DIDComm message using the same HTTP connection. This is useful when the client sending a DIDComm message doesn’t have any inbound route available.

5. A DIDComm encryption envelope is constructed according to the steps documented in ARIES RFC 0019 [7] to wrap the above plaintext message. Encryption envelope uses a standard format built on JSON Web Encryption (JWE) [8].  The public key used is fetched from the DID document available at the well-known DID configuration endpoint of the ADA service. Mode of encryption when creating JWE is anoncrypt. 

6. The encryption envelope is transmitted to ADA service by performing HTTP POST operation. For ADA service to process the incoming message, the <code>content-type</code> HTTP header provided is <code>application/didcomm-encrypted+json</code>. 

7. The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/create-did-response](https://didcomm.org/mydata-did/1.0/create-did-response). An example is given below:

```
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

If a problem arises while handling the <code>create-did</code> message, ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```
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

### 4.2	Read (Resolve)

To resolve a DID and fetch the associated DID document from the DID registry, a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/read-did](https://didcomm.org/mydata-did/1.0/read-did) must be constructed. An example is given below:


```
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

The above example requests ADA service to resolve `did:mydata:0:z6MkfiSdYhnLnS6jfwSf2yS2CiwwjZGmFUFL5QbyL2Xu8z2E` Notice the message json+ld doesn’t contain a **<code>from</code></strong> key, since the read operation can be performed without owning a DID. The packing algorithm used for constructing the DIDComm encryption envelope should be anoncrypt.

The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/read-did-response](https://didcomm.org/mydata-did/1.0/create-did-response). An example is given below:

```
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

If a problem arises while handling the `read-did` message, ADA service will respond with a problem report message that conforms to ARIES RFC 0035 [9]. An example is given below:

```
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

### 4.3	Update

In the current version of this specification, Update operation on DID documents is not permitted.

### 4.4	Delete

In the current version of this specification, delete is relevant only when triggered by a DID controller to renew the existing DID to a new one.  In this case, the old DID is marked as revoked. It could then be replaced with the newly created DID. To delete a DID, a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/delete-did](https://didcomm.org/mydata-did/1.0/read-did) must be constructed. An example is given below:

```
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

```
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

```
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

## 5.	Security Considerations

Conforms to security considerations as documented in DID core specification [1].

Following security considerations are taken up:

*   Stolen DID keys
*   Stolen private keys
*   Loss of device in which the DID agent is running.

## 6.	Privacy Considerations

DID documents in did:mydata does not include any PII, as required by the DID core specification [1], and recommended for privacy reasons.

## 7.	Reference Implementation

Work in progress as part of NGI-Trust eSSIF-Lab [ADA Project](https://essif-lab.eu/automated-data-agreements-to-simplify-ssi-work-flows-by-igrant-io/).

## 8.	Resources

1. DID core specification: [https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/)
2. IETF Multibase Data Format specification: [https://tools.ietf.org/html/draft-multiformats-multibase](https://tools.ietf.org/html/draft-multiformats-multibase) 
3. Multicodec - Compact self-describing codecs: [https://github.com/multiformats/multicodec](https://github.com/multiformats/multicodec)
4. DIDComm message specification: [https://identity.foundation/didcomm-messaging/spec/](https://identity.foundation/didcomm-messaging/spec/) 
5. Linked Data Cryptographic Suit Registry: [https://w3c-ccg.github.io/ld-cryptosuite-registry/](https://w3c-ccg.github.io/ld-cryptosuite-registry/) 
6. Aries RFC 0092 - Transports Return Route: [https://github.com/hyperledger/aries-rfcs/tree/master/features/0092-transport-return-route](https://github.com/hyperledger/aries-rfcs/tree/master/features/0092-transport-return-route) 
7. Aries RFC 0019: Encryption Envelope: [https://github.com/hyperledger/aries-rfcs/tree/master/features/0019-encryption-envelope](https://github.com/hyperledger/aries-rfcs/tree/master/features/0019-encryption-envelope)
8. IETF RFC 7516 - JSON Web Encryption: [https://datatracker.ietf.org/doc/html/rfc7516](https://datatracker.ietf.org/doc/html/rfc7516) 
9. Aries RFC 0035 - Report Problem Protocol 1.0: [https://github.com/hyperledger/aries-rfcs/tree/master/features/0035-report-problem](https://github.com/hyperledger/aries-rfcs/tree/master/features/0035-report-problem)
