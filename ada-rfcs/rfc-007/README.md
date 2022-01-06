# ADA RFC 0007: Support for attribute level updates to Data Agreements

## Summary

This RFC proposes udpates to existing ADA protocols to support attribute level opt-in and opt.outs

## Motivation

This proposal is to support the following:
* Ensure privacy rights of individuals to enable control of persoanl data at attribute level
* Support for selective disclosure of data during an offer acceptance stage

# Existing Solution

Data Agreements, today, can be in following states:

* **offer**: This DA is offered to the individual
* **reject**: Rejecting at the DA level
* **accept**: Accepting at the DA level
* **terminate**: Terminating at the DA level

The above states currently do not allow partial rejection or partial acceptance of a DA at the attribute level.

# Proposed Solution

The current DA protocol shall be extended to support the following new requirements: 

* Organisations can offer a DA with “consent” as a legal basis with partial acceptance or rejection at attribute level of a DA (Selective disclosure)
* Individuals can accept the DA for only a selected attributes. E.g. Marketing and Campaign, the individual can opt-out of the use of phone number during a first time offer
* Individuals can modify an existing accepted DA at the attribute level where a particular attribute is accepted or rejected. E:g. Marketing and Campaign, the individual opts out of use of email
* Individuals can modify a rejected DA at attribute level where a particular attribute is accepted. E.g. Marketing and Campaign is rejected or terminated state. The individual can opt in to only email marketing

With this change, Data Agreements will have the following states:

* **offer**: This DA is offered to the individual
* **reject**: Rejecting at the DA level
* **accept**: Accepting at the DA level
* **update-attribute**: Updated the DA at attribute level, applicable only for consent as legal basis
* **terminate**: Terminating at the DA level

The event and proof will stay at DA level while the event will contain the attributes that are opted in if CONSENT is the legal basis. 

For `update-attribute`, following rules shall apply:
1. Is applicable for for CONSENT as the legal basis
2. The event shall list the attribute ids consented to be used

# Example

```json
{
  "@context": [
    "https://raw.githubusercontent.com/decentralised-dataexchange/automated-data-agreements/main/interface-specs/data-agreement-schema/v1/data-agreement-schema-context.jsonld",
    "https://w3id.org/security/v2"
  ],
  "id": "d7216cb1-aedb-471e-96f7-7fef51dedb76",
  "version": "v1.0",
  "template_id": "91be609a-4acd-468f-b37a-0f379893b65c",
  "template_version": "v1.0",
  "data_controller_name": "Happy Shopping AB",
  "data_controller_url": "www.happyshopping.com",
  "data_policy": {
    "policy_URL": "https://happyshoping.com/privacy-policy/",
    "jurisdiction": "Sweden",
    "industry_sector": "Retail",
    "data_retention_period": "30",
    "geographic_restriction": "Europe",
    "storage_location": "Europe"
  },
  "purpose": "Customized shopping experience",
  "purpose_description": "Collecting user data for offering custom tailored shopping experience",
  "lawful_basis": "<consent/legal_obligation/contract/vital_interest/public_task/legitimate_interest>",
  "method_of_use": "<null/data-source/data-using-service>",
  "personal_data": [
    {
      "attribute_id": "f216cb1-aedb-571e-46f7-2fef51dedb54",
      "attribute_name": "Name",
      "attribute_sensitive": "True",
      "attribute_category": "Name"
    },
    {
      "attribute_id": "f216cb1-aedb-571e-46f7-2fef51dedb54",
      "attribute_name": "Age",
      "attribute_sensitive": "True",
      "attribute_category": "Age"
    }
  ],
  "dpia": {
    "dpia_date": "2021-05-08T08:41:59+0000",
    "dpia_summary_url": "https://org.com/dpia_results.html"
  },
  "event": [
    {
      "id": "did:mydata:z6MkiTBz1ymuepAQ4HEHYSF1H8quG5GLVVQR3djdX3mDooWp#1",
      "time-stamp": "2021-05-08T08:41:59+0000",
      "did": "did:mydata:z6MkiTBz1ymuepAQ4HEHYSF1H8quG5GLVVQR3djdX3mDooWp",
      "state": "<Offer/Accept/Reject/Terminate>"
    },
    {
      "id": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP#2",
      "time-stamp": "2021-05-08T08:41:59+0000",
      "did": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP",
      "state": "<Offer/Accept/Reject/Terminate"
    },
    {
      "id": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP#2",
      "time-stamp": "2022-01-01T08:41:59+0000",
      "did": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP",
      "state": "Update-Attribute",
      "atteributes": "<List of attribute IDs which is allowed to be used>"
    }
  ],
  "proof": [
    {
      "id": "did:mydata:z6MkiTBz1ymuepAQ4HEHYSF1H8quG5GLVVQR3djdX3mDooWp#1",
      "type": "Ed25519Signature2020",
      "created": "2021-05-08T08:41:59+0000",
      "verificationMethod": "did:mydata:z6MkiTBz1ymuepAQ4HEHYSF1H8quG5GLVVQR3djdX3mDooWp",
      "proofPurpose": "contractAgreement",
      "proofValue": "z6MkwW6aqMnjgrhJXFUko3NnZPGzVpkNzhYK7yEhnsibmLwLz6MkwW6aqMnjgrhJXFUko3NnZPGzVpkNzhYK7yEhnsibmLwL"
    },
    {
      "id": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP#2",
      "type": "Ed25519Signature2020",
      "created": "2021-05-08T08:41:59+0000",
      "verificationMethod": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP",
      "proofPurpose": "contractAgreement",
      "proofValue": "z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZPz6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP"
    }
  ],
  "principle-did": "did:mydata:z6MkGskxnGjLrk3gKS2mesDpuwRBokeWcmrgHxUXfnncxiZP"
}
```