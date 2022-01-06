# ADA RFC 0002: Data Agreement Protocol 1.0

## 3.1	Summary

This specification defines DIDComm protocol for performing Read operation on Data Agreements instances (receipts)

## 3.2	Motivation

We need a standard protocol to perform Read operation on Data Agreements.

Data Agreement is an agreement between organisations and individuals in the use of personal data. Data Agreement can have any of the legal basis that is outlined as per data protection law or regulation, such as the GDPR. The agreement with individuals could be with a  Data Source (issuer) or a Data Using Service. 

The focus of this specification is to enable a Data Subject / Auditor to resolve Data Agreement instances from a Data controller's data agreement instance (receipt) registry.

## 3.3	Tutorial

The protocol described in this document is a request-response protocol [10]. This involves two parties, with the `requester` making the first move, and the responder completing the interaction. The responder role is assumed by ADA microservice hosted by the Data Controller (Data Source or Data Using Service). The requester (Data Subject or Auditor) can perform Read operation on Data Agreement instances.

The following actors identified as part of the Data Agreement specification can assume the `requester` role:

* **Data Subject**
* **Auditor**

### 3.3.1	Interaction

The ADA service will be exposing a DIDComm agent. CRUD operations on DID can then be performed by the requester using available DIDComm messages. The ADA service itself will be allocated a pairwise DID, and a connection invitation message with pairwise DID as one of the recipientKey will be publicly available at an established DID configuration endpoint (`"/.well-known/did-configuration.json"`) of the web server.  A sample configuration is given below.

```json
{
  "ServiceEndpoint": "https://happyshopping.com",
  "RoutingKey": "",
  "Invitation": {
    "label": "Happy Shopping AB",
    "serviceEndpoint": "https://happyshopping.com",
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

### 3.3.2	Messages

The Data Agreements protocol consists of these messages:

* `data-agreements/1.0/read-data-agreement`
* `data-agreements/1.0/read-data-agreement-response`
* `data-agreements/1.0/problem-report`


#### 3.3.2.2	Read Data Agreement

An Auditor or a Data Subject can perform a read operation on a data agreement instance from data agreement instance (receipt) registry hosted by Data Controller (Data Source or Data Using Service). The read operation allows fetching a data agreement instance by it's identifier. An example of a `data-agreements/1.0/read-data-agreement` DIDComm message is given below.

```json
{
    "@type": "https://didcomm.org/data-agreements/1.0/read-data-agreement",
    "@id": "fc2a046d-10c3-41bf-8484-7b990ed06e2d",
    "from": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
    "to": "did:mydata:z6MkpzHAuPc4N2jgXg23ikZjp2tQSTEHqnzzr9683AQ2EvCV",
    "created_time": "1639289716",
    "body": {
        "data_agreement_id": "d900a281-31f0-4bd5-a647-2c95136250b5"
    }
}
```

An example of a `data-agreements/1.0/read-data-agreement-response` DIDComm message is given below.


```json
{
    "@type": "https://didcomm.org/data-agreements/1.0/read-data-agreement-response",
    "@id": "b9703401-56af-461a-b4a0-43b575b3a534",
    "~thread": {
        "thid": "fc2a046d-10c3-41bf-8484-7b990ed06e2d"
    },
    "created_time": "1639289716",
    "from": "did:mydata:z6MkpzHAuPc4N2jgXg23ikZjp2tQSTEHqnzzr9683AQ2EvCV",
    "body": {
        "data_agreement": {
            "@context": [
                "https://raw.githubusercontent.com/decentralised-dataexchange/automated-data-agreements/main/interface-specs/data-agreement-schema/v1/data-agreement-schema-context.jsonld",
                "https://w3id.org/security/v2"
            ],
            "id": "d900a281-31f0-4bd5-a647-2c95136250b5",
            "version": 1,
            "template_id": "6f5c0c86-40cb-4683-a993-e8ba8cbbdaa9",
            "template_version": 1,
            "data_controller_name": "Happy Shopping AB",
            "data_controller_url": "https://www.happyshopping.com",
            "purpose": "Customer loyalty program",
            "purpose_description": "Issuing loyalty cards for customers.",
            "lawful_basis": "consent",
            "method_of_use": "data-source",
            "data_policy": {
                "data_retention_period": 365,
                "policy_URL": "https://clarifyhealth.com/privacy-policy/",
                "jurisdiction": "Sweden",
                "industry_sector": "Healthcare",
                "geographic_restriction": "Europe",
                "storage_location": "Europe"
            },
            "personal_data": [
                {
                    "attribute_id": "be99f24e-a7fe-452b-aac5-cd564f4700b6",
                    "attribute_name": "Name",
                    "attribute_sensitive": true,
                    "attribute_category": "Personal",
                    "attribute_description": "Name of the user"
                }
            ],
            "dpia": {
                "dpia_date": "2021-12-12T10:19:46.259870+00:00",
                "dpia_summary_url": "https://org.com/dpia_results.html"
            },
            "event": [
                {
                    "id": "did:mydata:z6Mkkmdx9iNHeKGYyV4Wi4uZLLpcmCKmh8uw369a6xGrY4iX#1",
                    "time_stamp": "2021-12-12T11:31:38.749729+00:00",
                    "did": "did:mydata:z6Mkkmdx9iNHeKGYyV4Wi4uZLLpcmCKmh8uw369a6xGrY4iX",
                    "state": "offer"
                },
                {
                    "id": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND#2",
                    "time_stamp": "2021-12-12T11:32:05.047266+00:00",
                    "did": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
                    "state": "accept"
                }
            ],
            "proofChain": [
                {
                    "id": "did:mydata:z6Mkkmdx9iNHeKGYyV4Wi4uZLLpcmCKmh8uw369a6xGrY4iX#1",
                    "type": "Ed25519Signature2018",
                    "created": "2021-12-12T11:31:38.751698+00:00",
                    "verificationMethod": "did:mydata:z6Mkkmdx9iNHeKGYyV4Wi4uZLLpcmCKmh8uw369a6xGrY4iX",
                    "proofPurpose": "contractAgreement",
                    "proofValue": "eyJhbGciOiAiRWREU0EiLCAiYjY0IjogZmFsc2UsICJjcml0IjogWyJiNjQiXX0..MI54Jf_8BtdmsMr80nRuBbMib8aupMCFL28Nl1oliqp7pxPrRPlgywvkK63z2U29wIGd7DldDh5zHIQ406TFCA"
                },
                {
                    "id": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND#2",
                    "type": "Ed25519Signature2018",
                    "created": "2021-12-12T11:32:05.048210+00:00",
                    "verificationMethod": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
                    "proofPurpose": "contractAgreement",
                    "proofValue": "eyJhbGciOiAiRWREU0EiLCAiYjY0IjogZmFsc2UsICJjcml0IjogWyJiNjQiXX0..pBaGGj6LcZLwUA7kl7ABfyLLi0v0YXzqNjnY03DUqcWWPWf2TPiCKlmIBcIZCkOGZbLmyLl4j_0vncsJrRYSAg"
                }
            ],
            "data_subject_did": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND"
        }
    },
    "to": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND"
}
```

#### 3.3.2.5	Problem Report

If a problem arises while handling the `data-agreements/1.0/read-data-agreement` message, ADA service will respond with a problem report message that conforms to Aries RFC 0035 [9]. An example is given below.

```json
{
    "@type": "https://didcomm.org/data-agreements/1.0/problem-report",
    "@id": "14525b0d-284f-42de-85ed-a2ca66a4d51c",
    "~thread": {
        "thid": "2ab0914c-9209-4b4a-8bf4-8df329ecbd3b"
    },
    "created_time": "1639289960",
    "from": "did:mydata:z6MkpzHAuPc4N2jgXg23ikZjp2tQSTEHqnzzr9683AQ2EvCV",
    "to": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
    "problem-code": "data_agreement_not_found",
    "explain": "Data agreement not found; Failed to process read-data-agreement message data agreement: d900a281-31f0-4bd5-a647-2c95136250b6"
}
```
