# Interface specification - Automated Data Agreement (ADA)

## Table of contents
- [1.0   Introduction](#10introduction)
- [2.0   Actors Involved in a Data Agreement Workflow](#20actors-involved-in-a-data-agreement-workflow)
- [3.0   DID Method](#30did-method)
- [4.0   DIDComm Protocol](#40didcomm-protocol)
  * [4.1  Definition and Preparation of Data Agreements](#41definition-and-preparation-of-data-agreements)
      + [4.1.1   Create Data Agreement](#411create-data-agreement)
      + [4.1.2   Read Data Agreement](#412read-data-agreement)
      + [4.1.3   Update Data Agreement](#413update-data-agreement)
      + [4.1.4   Delete Data Agreement](#414delete-data-agreement)
  * [4.2  Capture of Data Agreements](#42capture-of-data-agreements)
      + [4.2.1 Negotiate Data Agreement](#421-negotiate-data-agreement)
      + [4.2.2 Data Agreement Context Decorator](#422-data-agreement-context-decorator)
  * [4.3  Auditing or Proof for Data Agreements](#43auditing-or-proof-for-data-agreements)
- [5.0   Failure Scenarios](#50failure-scenarios)


# 1.0	Introduction

This document elaborates on the various interfaces supported by the automated data agreement (ADA) component. The two key components in ADA are the decentralised identifier (DID) method and the DIDComm protocols.

In this interface specification, [chapter 3.0 DID Method](#30---did-method) elaborates on the DID method while [chapter 4.0](#40---didcomm-protocol) provides the DIDComm protocols supported by the ADA component.

# 2.0	Actors Involved in a Data Agreement Workflow

These are the actors using ADA services: 

* a Data Source, the organisation collecting private data, (typically a data controller). [SSI: Issuer]
* a Data Subject or Individual. [SSI: Holder]
* a Data Using Service, an organisation processing personal data from one or more data sources to deliver a service. [SSI: Verifier]
* an Assessor reviews the practices of an organisation, conducts a DPIA and drafts data agreements and inter-company agreements for third parties.
* an Auditor may be called in to review the data agreements and ensure they are in place in case of data breaches or regular inspection. 


# 3.0	DID Method

The DID method operations are:

* Create DID
* Resolve DID
* Update DID: Happens on the DID document and not the identifier itself.
* Revoke DID

The owner of the DID can sign or countersign data agreements using the crypto material associated with it. The Data Agreement DID method specification that will be produced during the project will elaborate the above methods further.

# 4.0	DIDComm Protocol	

## 4.1	Definition and Preparation of Data Agreements	
In the data agreement workflow [For details, please ref. [ADA Functional Specification](https://gitlab.grnet.gr/essif-lab/infrastructure_2/igrantio/deliverables/-/blob/master/functional_specification.md)], the **Definition** and **Preparation** involves the operations described in the subchapters. These operations are used by any data protection impact assessment (DPIA) to “convert” DPIA output to standardised data agreements.

**Relevant actors:** Assessor and administrator of an organisation (for example, a data source or data using service)

### 4.1.1	Create Data Agreement

An organisation intending to create a data agreement must construct a create-data-agreement DIDComm message and encrypt it using the DID and send it to the ADA component. An example of a `create-data-agreement` DIDComm message is:

```json 
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/create-data-agreement",
  "data_agreement": {
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
  },
  "data_agreement_meta": {
	"version": "1.0",
	"creation_time": "2021-05-09T10:39:50+0000",
      "revocation_status" : false
  }
}
```
On failure, a problem report DIDComm message is triggered, refer chapter [5.0 Failure scenarios](#5.0-failure-scenarios).

If the data agreement is successfully created, the actor who initiated the `create-data-agreement` message becomes the data agreement owner. A c`reate-data-agreement-response` DIDComm message is triggered on successful creation of the data agreement. An example is:

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

### 4.1.2	Read Data Agreement

An organisation can perform a read operation on a data agreement to retrieve a data agreement by ID. An example of a `read-data-agreement` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/read-data-agreement",
  "data_agreement_id": "45988dd2-62b9-4ede-8189-fb99c64b42d1"
}
```

An example of a `read-data-agreement-response` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/read-data-agreement-response",
  "data_agreement": {
    …
  },
  "data_agreement_meta": {
    ...
  }
}
```

### 4.1.3	Update Data Agreement

When an organisation performs an update operation on a data agreement, a copy of the data agreement is created and the updates are applied to the copy. The version number is incremented to represent the change.

An example of an `update-data-agreement` DIDComm message is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/update-data-agreement",
  "data_agreement_id": "<data_agreement_id>",
  "data_agreement": {
    ...
  }
}
```

On a successful update, an `update-data-agreement-response` message is triggered. This message returns the new data agreement ID. An example is:

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/update-data-agreement-response",
  "data_agreement_id": "<data_agreement_id>"
}
```

### 4.1.4	Delete Data Agreement

When an organisation performs a delete operation on a data agreement, the delete operation doesn’t remove the data agreement from the persistence, instead it marks the data agreement as revoked.

```json
{
  "@id": "b589c166-e0f0-44eb-9b41-ee2c09b26bec",
  "@type": "https://didcomm.org/data-agreements/1.0/delete-data-agreement",
  "data_agreement_id": "<data_agreement_id>"
}
```

## 4.2	Capture of Data Agreements

### 4.2.1 Negotiate Data Agreement

The negotiate-data-agreement protocol involves a series of messages between an organisation and a data subject to reach a two party agreement. On the successful negotiation of an agreement, a data exchange under the constraints of the agreement can occur between the parties. 

The protocol consists of the following DIDComm messages:

* `propose-data-agreement`
* `offer-data-agreement`
* `accept-data-agreement`
* `decline-data-agreement`

After an organisation and a data subject have successfully reached an agreement, the hash of the countersigned document will be stored in the data agreement registry for dispute resolution.

### 4.2.2 Data Agreement Context Decorator

A decorator is an optional chunk of JSON that conveys metadata and is very similar to headers in HTTP. If a DIDComm message contains `data-agreement-context` decorator, it is to be assumed that a data agreement has been agreed and the current message is within the context of the agreement. 

An example usage of decorator is:

```json
{
  "~data-agreement-context": {
	"@type": "https://didcomm.org/signature/1.0/ed25519Sha512_single",
	"agreement_sig_data": "base64URL(64bit_integer_from_unix_epoch|counter_signed_agreement_hash)",
	"agreement_signature": "base64URL(digital signature function output)",
	"agreement_signer": "base64URL(signer public key)"
  },

...
Rest of the DIDComm message
...
}
```

Inside the decorator there are four fields, 

* **@type** - the type of signature scheme used to generate the signatures.
* **agreement_sigdata** - the data that was signed. Here the data that was signed is a concatenation of a UTC timestamp and a hash of a countersigned data agreement
* **agreement_signature** - the output of the signature function
* **agreement_signer** - the public key of the DID used to sign the data agreement

## 4.3	Auditing or Proof for Data Agreements

After an organisation and a data subject sign off on a data agreement, they each hold a copy of the countersigned document.

In case of a dispute, an auditor can:

* Check the ownership of the DIDs that were used to sign the document to check if both parties were in agreement.
* Verify the hash with the one in the data agreement registry to determine whether or not tampering has occurred

# 5.0	Failure Scenarios
In case of failure, a problem-report DIDComm message is triggered. An example is:

```json
{
  "@type"        	: "https://didcomm.org/data-agreements/1.0/problem-report",
  "@id"          	: "d47e6db6-9272-4132-af04-02bff8808db6",
  "~thread"      	: {
	"thid": "b589c166-e0f0-44eb-9b41-ee2c09b26bec"
  },
  "description"  	: { "en": "Failed to create data agreement", "code": "creation-failure" }
}
```
