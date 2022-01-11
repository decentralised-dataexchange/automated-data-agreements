# ADA RFC 0001: MyData DID Protocol 1.0

## 2.1 Summary

This specification defines a DIDComm protocol for performing CRUD operations on `did:mydata`.

## 2.2 Motivation

The protocol allows different objects in iGrant.io automated data agreements (ADA) specification to be treated as valid DIDs. The functionalities available for DID subjects once they own a DID are the following:

* Sign Data Agreement (s)
* Authenticate and verify proof chain in Data Agreement VC
* Tie data exchange transactions to Data Agreement (s) which can be verified by an independent auditor or any party of the agreement

## 2.3 Tutorial

The protocol described in this document is a request-response protocol [10]. This involves two parties, with the requester` `making the first move, and the responder completing the interaction. The responder role is assumed by ADA microservice. The requester can perform CRUD operations on ``did:mydata``.

The following actors are identified as part of ADA specification and can assume the requester` `role:

* a **Data Source**, the organisation collecting private data, (typically a data controller).  [SSI: Issuer]
* a **Data Subject** or **Individual**. [SSI: Holder]
* a **Data Using Service (DUS)**, an organisation processing personal data from one or more data sources to deliver a service. [SSI: Verifier]
* an **Assessor** reviews the practices of an organisation, conducts a DPIA and drafts data agreements and inter-company agreements for third parties.
* an **Auditor** may be called in to review the data agreements and ensure they are in place in case of data breaches or regular inspection.

### 2.3.1 Interaction

The ADA service will be exposing a DIDComm agent. CRUD operations on DID can then be performed by the requester using available DIDComm messages. The ADA service itself will be allocated a pairwise DID, and a connection invitation message with pairwise DID as one of the recipientKey will be publicly available at an established DID configuration endpoint (`"/.well-known/did-configuration.json"`) of the web server.  A sample configuration is given below.

```json
{
  "ServiceEndpoint": "https://mydata-did-registry.igrant.io",
  "RoutingKey": "",
  "Invitation": {
    "label": "MyData-DID-Registry",
    "serviceEndpoint": "https://mydata-did-registry.igrant.io",
    "routingKeys": [],
    "recipientKeys": [
      "4ZMHW7jX885o6dfXTjff2W8zkDdPXEFrxYauFmJrNwyE"
    ],
    "@id": "8e1cc2f6-f2af-41e7-8475-fa99be1c4c99",
    "@type": "https://didcomm.org/connections/1.0/invitation"
  }
}
```



The recipient key (public key) specified in the connection invitation is used for constructing DIDComm encryption envelopes by the requester. This envelope is sent to the DIDComm agent mentioned in the service endpoints section within the connection invitation document to communicate with the responder.

### 2.3.2 Messages

The MyData DID protocol consists of these messages:

* `mydata-did/1.0/create-did`
* `mydata-did/1.0/create-did-response`
* `mydata-did/1.0/read-did`
* `mydata_did/1.0/read-did-response`
* `mydata-did/1.0/delete-did`
* `mydata-did/1.0/delete-did-response`
* `mydata-did/1.0/problem-report`

There are 3 possible operations using the above messages. The 3 operations are - Create, Read and Delete. How to use the above messages are explained below for each operation.  

#### 2.3.2.1	Create Operation

The Create Operation is initiated by the requester by sending the `mydata-did/1.0/create-did` DIDComm message to the responder (ADA service). The responder responds with a `mydata-did/1.0/create-did-response` message if the operation is successful or with a `mydata-did/1.0/problem-report` message if the operation fails.

Creation of the DID follows the sequence below. 

1. The (Public key, Private key) pair is created based on `Ed25519Signature2018` suite which is specified in Linked Data Cryptographic Suite Registry [5]. The Ed25519 algorithm is available in common crypto libraries, for example NACL. 
2. Multicodec [3] prefix for Ed25519 public key is concatenated to raw public key bytes and encoded using base58-btc encoding to obtain the Multibase [2] value (`mb-value`).
3. Create the DID document based on the format specified in Section [3.	DID document](https://docs.google.com/document/d/1c35vyo5zqvfyIO_154-2rQS_pjPkqQnGujd0z0qOOTs/edit#heading=h.26bg4pr79mnu).
4. Construct a DIDComm plaintext message conforming to DIDComm message specification [4] with type - [https://didcomm.org/mydata-did/1.0/create-did](https://didcomm.org/mydata-did/1.0/create-did) as given in the example below.

```json 
{
    "@type": "https://didcomm.org/mydata-did/mydata-did/1.0/create-did",
    "@id": "53f19e0b-5be2-480a-92bc-fcdeabf69ad3",
    "created_time": "1639125359",
    "to": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
    "from": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s",
    "body~sig": {
        "@type": "https://didcomm.org/signature/1.0/ed25519Sha512_single",
        "signature": "oNecYanvYrKMTpl1G9GkmbTv-v6zbEqotBhgzYxiR_XBr_fBJDrLEiPtpzVsi0f72da2tubjcYISzq9RzlRzDA==",
        "sig_data": "AAAAAGGzXsd7IkBjb250ZXh0IjogImh0dHBzOi8vdzNpZC5vcmcvZGlkL3YxIiwgImlkIjogImRpZDpteWRhdGE6ejZNa28zaHRUZUs5NGppWDRSR0FGenRSZm82NU5qV20zMXkxSGUxU1VuNW90WTdYIiwgInZlcmlmaWNhdGlvbl9tZXRob2QiOiBbeyJpZCI6ICJkaWQ6bXlkYXRhOno2TWtvM2h0VGVLOTRqaVg0UkdBRnp0UmZvNjVOaldtMzF5MUhlMVNVbjVvdFk3WCMxIiwgInR5cGUiOiAiRWQyNTUxOVZlcmlmaWNhdGlvbktleTIwMTgiLCAiY29udHJvbGxlciI6ICJkaWQ6bXlkYXRhOno2TWtvM2h0VGVLOTRqaVg0UkdBRnp0UmZvNjVOaldtMzF5MUhlMVNVbjVvdFk3WCIsICJwdWJsaWNLZXlCYXNlNTgiOiAiejZNa28zaHRUZUs5NGppWDRSR0FGenRSZm82NU5qV20zMXkxSGUxU1VuNW90WTdYIn1dLCAiYXV0aGVudGljYXRpb24iOiBbeyJ0eXBlIjogIkVkMjU1MTlTaWduYXR1cmVBdXRoZW50aWNhdGlvbjIwMTgiLCAicHVibGljS2V5IjogImRpZDpteWRhdGE6ejZNa28zaHRUZUs5NGppWDRSR0FGenRSZm82NU5qV20zMXkxSGUxU1VuNW90WTdYIzEifV0sICJzZXJ2aWNlIjogW3siaWQiOiAiZGlkOm15ZGF0YTp6Nk1rbzNodFRlSzk0amlYNFJHQUZ6dFJmbzY1TmpXbTMxeTFIZTFTVW41b3RZN1g7ZGlkY29tbSIsICJ0eXBlIjogIkRJRENvbW0iLCAicHJpb3JpdHkiOiAwLCAicmVjaXBpZW50S2V5cyI6IFsiejZNa28zaHRUZUs5NGppWDRSR0FGenRSZm82NU5qV20zMXkxSGUxU1VuNW90WTdYIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cDovL2xvY2FsaG9zdDo4MDAyLyJ9XX0=",
        "signer": "9bSqsQ4hjCE3wvRTaRvaphY5ZAEud8iebd6WeW7nyKL9"
    },
    "~transport": {
      "return_route": "all"
    }
}
```

* **id** is a unique value to the sender which indicates the message ID.
* **type** indicates the message type. Message type decides how the message contents must be processed.
* **from** is a valid DID which identifies the sender. This can be the same as the DID that is getting created.
* **to** is a valid DID which identifies the recipient. It is the value of ADA service’s DID. This is obtained from the DID document available at the root endpoint of ADA service. 
* **created_time** indicates the message created time. This is represented in UTC epoch seconds.
* **body** contains the actual message body. In this case it contains the DID document that is getting created and stored in the DID registry. The body is signed as described in [Aries RFC 0234 Signature Decorator](https://github.com/hyperledger/aries-rfcs/blob/main/features/0234-signature-decorator/README.md). An example content of the body before encapsulating it in Signature Decorator is given below.

```json
{
  "@context": "https://w3id.org/did/v1",
  "id": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X",
  "verification_method": [
    {
      "id": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X#1",
      "type": "Ed25519VerificationKey2018",
      "controller": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X",
      "publicKeyBase58": "z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X"
    }
  ],
  "authentication": [
    {
      "type": "Ed25519SignatureAuthentication2018",
      "publicKey": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X#1"
    }
  ],
  "service": [
    {
      "id": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X;didcomm",
      "type": "DIDComm",
      "priority": 0,
      "recipientKeys": [
        "z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X"
      ],
      "serviceEndpoint": "http://localhost:8002/"
    }
  ]
}
```

* **~transport** decorator is optional. This decorator conforms to [Aries RFC 0092 Transport Return Route](https://github.com/hyperledger/aries-rfcs/tree/main/features/0092-transport-return-route) [6]. If it’s specified and the value of `return_route` is `all`, then it means ADA service must respond to the respective DIDComm message using the same HTTP connection. This is useful when the client sending a DIDComm message doesn’t have any inbound route available.


1. A DIDComm encryption envelope is constructed according to the steps documented in Aries RFC 0019 [7] to wrap the above plaintext message. Encryption envelope uses a standard format built on JSON Web Encryption (JWE) [8].  The public key used is fetched from the DID document available at the well-known DID configuration endpoint of the ADA service. Mode of encryption when creating JWE is anoncrypt. 
2. The encryption envelope is transmitted to the ADA service by performing HTTP POST operation. For ADA service to process the incoming message, the `content-type` HTTP header provided is `application/ssi-agent-wire`. 
3. The ADA service will process the incoming message by verifying the signatures after unpacking the message.
4. The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/create-did-response](https://didcomm.org/mydata-did/1.0/create-did-response). An example is given below.

```json 
{
    "@type": "https://didcomm.org/mydata-did/1.0/create-did-response",
    "@id": "4dfd460b-f965-4bf5-923a-9bfd3d30f410",
    "~thread": {
        "thid": "53f19e0b-5be2-480a-92bc-fcdeabf69ad3"
    },
    "body": {
        "did_doc": {
            "@context": "https://w3id.org/did/v1",
            "id": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X",
            "verification_method": [
                {
                    "id": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X#1",
                    "type": "Ed25519VerificationKey2018",
                    "controller": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X",
                    "publicKeyBase58": "z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X"
                }
            ],
            "authentication": [
                {
                    "type": "Ed25519SignatureAuthentication2018",
                    "publicKey": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X#1"
                }
            ],
            "service": [
                {
                    "id": "did:mydata:z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X;didcomm",
                    "type": "DIDComm",
                    "priority": 0,
                    "recipientKeys": [
                        "z6Mko3htTeK94jiX4RGAFztRfo65NjWm31y1He1SUn5otY7X"
                    ],
                    "serviceEndpoint": "http://localhost:8002/"
                }
            ]
        },
        "version": "1",
        "status": "active"
    },
    "from": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
    "created_time": "1639125359",
    "to": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s"
}
```

#### 2.3.2.2	Read Operation

The Read Operation is initiated by the requester by sending the `mydata-did/1.0/read-did` DIDComm message to the responder (ADA service). The responder responds with a `mydata-did/1.0/read-did-response` message if the operation is successful or with a `mydata-did/1.0/problem-report` message if the operation fails.

To resolve a DID and fetch the associated DID document from the DID registry, a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/read-did](https://didcomm.org/mydata-did/1.0/read-did) must be constructed. An example is given below.

```
{
    "@type": "https://didcomm.org/mydata-did/1.0/read-did",
    "@id": "be23b532-589d-4ed4-ae6f-82b89702d2c4",
    "created_time": "1639126418",
    "to": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
    "from": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s",
    "body": {
        "did": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar"
    },
    "~transport": {
      "return_route": "all"
    }
}
```
The **`did** attribute in the message body represents the DID that will be resolved.

The above example requests the ADA service to resolve `did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar`. The packing algorithm used for constructing the DIDComm encryption envelope should be anoncrypt.

The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/read-did-response](https://didcomm.org/mydata-did/1.0/read-did-response). An example is given below.

```json 
{
    "@type": "https://didcomm.org/mydata-did/1.0/read-did-response",
    "@id": "1265c0d5-5199-48b7-acf5-41c6fc766a84",
    "~thread": {
        "thid": "be23b532-589d-4ed4-ae6f-82b89702d2c4"
    },
    "body": {
        "did_doc": {
            "@context": "https://w3id.org/did/v1",
            "id": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar",
            "verification_method": [
                {
                    "id": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar#1",
                    "type": "Ed25519VerificationKey2018",
                    "controller": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar",
                    "publicKeyBase58": "z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar"
                }
            ],
            "authentication": [
                {
                    "type": "Ed25519SignatureAuthentication2018",
                    "publicKey": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar#1"
                }
            ],
            "service": [
                {
                    "id": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar;didcomm",
                    "type": "DIDComm",
                    "priority": 0,
                    "recipientKeys": [
                        "z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar"
                    ],
                    "serviceEndpoint": "http://localhost:8002/"
                }
            ]
        },
        "version": "1",
        "status": "active"
    },
    "from": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
    "created_time": "1639126418",
    "to": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s"
}
```

#### 2.3.2.3	Update Operation

In the current version of this specification, Update operation on DID documents is not permitted  at the moment because it would mean the DID controllers could change the public key associated with DID. Our `did:mydata` DID uses a public key to create the method specific identifier. That means if the public key changes, the method specific identifier should also change.

#### 2.3.2.4	Delete Operation

The Delete Operation is initiated by the requester by sending the `mydata-did/1.0/delete-did` DIDComm message to the responder (ADA service). The responder responds with a `dmydata-did/1.0/delete-did-response` message if the operation is successful or with a `mydata-did/1.0/problem-report` message if the operation fails.

In the current version of this specification, delete is relevant only when triggered by a DID controller to renew the existing DID to a new one (e.g. when a mobile device with the digital wallet which includes the private key is lost).  In this case, the old DID is marked as revoked. It could then be replaced with the newly created DID. To delete a DID, a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/delete-did](https://didcomm.org/mydata-did/1.0/delete-did) must be constructed. An example is given below.

```json
{
    "@type": "https://didcomm.org/mydata-did/1.0/delete-did",
    "@id": "ecb656ad-e52a-4105-8fda-4af06ba436a7",
    "created_time": "1639284769",
    "to": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
    "from": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s",
    "body~sig": {
        "@type": "https://didcomm.org/signature/1.0/ed25519Sha512_single",
        "signature": "N_6jzRzEcYAE4laJmUNXpV_Usf4-YMq3Fz3k_t2p9xK16vZKS3BSl91x0meXMFBmOUhO4kykpcct4SB0mIGjDg==",
        "sig_data": "AAAAAGG1zXl7ImRpZCI6ICJkaWQ6bXlkYXRhOno2TWt3V3lVYVF2cFBpbjVCTFVCVlhTY2JnVWdCQ3RXUE1zTHluanlEbjhKZU5FWSJ9",
        "signer": "J4iRzAgP4BHc4qdUoxUmkavgMdceyUczHmq3PWAHj9TA"
    }
}
```

The body is signed as described in [Aries RFC 0234 Signature Decorator](https://github.com/hyperledger/aries-rfcs/blob/main/features/0234-signature-decorator/README.md). Signature is generated using the `did:mydata` that needs to be revoked. This is done to verify the ownership of the DID. An example of body before signing is given below.

```json
{"did": "did:mydata:z6MkgraJGcncoZhxG1tQvzp36wPUZ995r1H1V2nK8MTYQ4Ar"}
```

The **did** attribute in the message body represents the DID that will be marked as revoked.

The packing algorithm used for constructing the DIDComm encryption envelope should be authcrypt.

The ADA service will respond to the above DIDComm message with an encryption envelope (JWE) which when unpacked will contain a DIDComm plaintext message of type - [https://didcomm.org/mydata-did/1.0/delete-did-response](https://didcomm.org/mydata-did/1.0/delete-did-response). An example is given below.

```json
{
    "@type": "https://didcomm.org/mydata-did/1.0/delete-did-response",
    "@id": "8f560451-02a3-4e84-8441-7016442887b8",
    "~thread": {
        "thid": "ecb656ad-e52a-4105-8fda-4af06ba436a7"
    },
    "to": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s",
    "body": {
        "status": "revoked",
        "did": "did:mydata:z6MkwWyUaQvpPin5BLUBVXScbgUgBCtWPMsLynjyDn8JeNEY"
    },
    "from": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
    "created_time": "1639284769"
}
```

#### 2.3.2.5	Problem Report

If a problem arises while handling any DIDComm message, ADA service will respond with a problem report message that conforms to Aries RFC 0035 [9]. An example is given below.

```json
{
  "@type": "https://didcomm.org/mydata-did/1.0/problem-report",
  "@id": "c98aaa94-8bd4-4c8d-9d05-c25ead35dc62",
  "~thread": {
    "thid": "cf2b1dca-9904-4242-87c6-063745cc86f9"
  },
  "to": "did:mydata:z6MkioNqmrGDEDMA1e5YBH6Tudjt5gQj9kFxzA5DzUwWNS8s",
  "problem-code": "mydata_did_not_found",
  "explain": "DID not found.",
  "from": "did:mydata:z6Mkr85Fb3yUoj2PT1BVfFfVmAuuAe38UX9XnD5Eb9PVA8FG",
  "created_time": "1639284974"
}
```
