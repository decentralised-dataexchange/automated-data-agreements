# ADA Protocol 0005: Data Agreement Termination Protocol 1.0


## 6.1	Summary

This specification describes a DIDComm protocol for a Data Subject to terminate a Data Agreement and notify the Organisation (Data Using Service or Data Source)


## 6.2	Motivation

The Data Agreement may be terminated in a number of ways. Here are a few of the scenarios:

1. Data Agreement expired and the service is no longer applicable
2. Updated Data Agreement with new purpose or changes to collected pii categories
3. Individual requests to terminate the service before expiration date
4. Individual requests not only to terminate but to have their personal data erased

Note, the erasure may come after termination of the Data Agreement.


## 6.3	Tutorial

The protocol described in this document is a request-response protocol [10].This involves two parties, with the `requester` making the first move, and the responder completing the interaction. The responder role is assumed by an Organisation (Data Source or Data Using Service). The requester can initiate the Data Agreement termination.

Following actors identified as part of ADA specification can assume the `requester` role:

* a **Data Subject** or **Individual**. [SSI: Holder]

Once a Data Agreement is terminated, the proofs associated with termination are recorded to the ADA service hosted by the Organisation (Data Using Service or Data Source) which acts as a central source of truth.

### 5.3.1	Interaction

Using an already established pairwise connection (agent-to-agent communication) requester can initiate the data agreement termination process by sending `terminate` message to the Organisation. The responder (Organization) records the termination event in the Data Agreement receipt and take the necessary actions to fulfill the termination request. The responder acknowledges the termination request by responding with a `terminate-ack` message.

### 5.3.2	Messages

The Data Agreement Termination protocol consists of these messages:

* `data-agreement-termination/1.0/terminate`
* `data-agreement-termination/1.0/terminate-ack`
* `data-agreement-termination/1.0/problem-report`

#### 6.3.2.1	Terminate

A Data Subject (requester) intending to terminate a Data Agreement initiates the termination process by recording a terminate event and corresponding proof in the Data Agreement receipt. This event and proof is then send to the Organisation to notify them about the termination. Data Subject (requester) must construct a `terminate` DIDComm message and send it to the Organisation (responder) using an already established pairwise connectin (agent-to-agent communication). An example of a `terminate` DIDComm message is given below.

```json
{
    "@type": "https://didcomm.org/data-agreement-termination/1.0/terminate",
    "@id": "418cd2fe-d20d-49e1-a73e-7782c51e1dd1",
    "to": "did:mydata:z6MkpzHAuPc4N2jgXg23ikZjp2tQSTEHqnzzr9683AQ2EvCV",
    "body": {
        "id": "d900a281-31f0-4bd5-a647-2c95136250b5",
        "event": {
            "id": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND#3",
            "time_stamp": "2021-12-13T06:25:56.776512+00:00",
            "did": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
            "state": "terminate"
        },
        "proof": {
            "id": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND#3",
            "type": "Ed25519Signature2018",
            "created": "2021-12-13T06:25:56.779880+00:00",
            "verificationMethod": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
            "proofPurpose": "contractAgreement",
            "proofValue": "eyJhbGciOiAiRWREU0EiLCAiYjY0IjogZmFsc2UsICJjcml0IjogWyJiNjQiXX0..GDrsga7RanAgMRLRvlHUSkNlLWauuRkA-uTHfovi_kkA-c9x2ZivW0B-dY3s5nI0xRMX9Sjuqq2fjGgpVaj8CA"
        }
    },
    "from": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
    "created_time": "1639356970"
}
```

#### 6.3.2.2	Terminate Ack

On receiving the `terminate` message and processing it, the organisation (responder) must construct a `terminate-ack` DIDComm message and send it to the Data Subject (responder) using an already established pairwise connection (agent-to-agent communication). The organisation is informing the Data Subject that the associated data is no longer bound to the Data Agreement. An example of a `terminate-ack` DIDComm message is given below.

```json
{
    "@type": "https://didcomm.org/data-agreement-termination/1.0/terminate-ack",
    "@id": "5f5225d4-5df3-480c-b7b5-9029bd0a17c3",
    "~thread": {
        "thid": "418cd2fe-d20d-49e1-a73e-7782c51e1dd1"
    },
    "status": "OK"
}
```

#### 6.3.2.3	Problem Report

Errors might occur in various places. All errors are modeled with problem-report messages. If a problem arises, the agent will respond with a problem report message that conforms to Aries RFC 0035 [9]. An example is given below.


```json
{
    "@type": "https://didcomm.org/data-agreement-termination/1.0/problem-report",
    "@id": "14525b0d-284f-42de-85ed-a2ca66a4d51c",
    "~thread": {
        "thid": "2ab0914c-9209-4b4a-8bf4-8df329ecbd3b"
    },
    "created_time": "1639289960",
    "from": "did:mydata:z6MkpzHAuPc4N2jgXg23ikZjp2tQSTEHqnzzr9683AQ2EvCV",
    "to": "did:mydata:z6MkhnMkWDytfVEL88BRmWGeLN9vGhpdJ612QTgVMt1agqND",
    "problem-code": "data_agreement_not_found",
    "explain": "Data Agreement not found."
}
```
