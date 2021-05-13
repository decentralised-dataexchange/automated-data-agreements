<p align="center">
  <img src="https://www.ngi.eu/wp-content/uploads/sites/48/2020/02/191205-eSSIF-Lab-logo-NGI_Tag-rgb.jpg">
</p>

# Working Package Summary

Working Package: 3

Working Package Name: Data Agreement (consent) lifecycle

Status: Open Call

Start date: 12-05-2021

## Participants

> A Working Package must have at least two implementors and one integrator

Type | Name | Contact Name | Contact email
--- | --- | --- | --- 
Coordinator | iGrant.io | Jan Lindquist | jan@linaltec.com
Implementor | iGrant.io | Lal Chandran | lal@igrant.io
Implementor | Gataca | Jose San Juan |jose@gataca.io
Implementor | Visions | matthias | matthias@visionspol.eu
Implementor | HumanColossus | Paul Knowles | paul.knowles@humancolossus.org
Collaborator | LetsTrust.org | Dominik Phil | dominik@letstrust.id phil@letstrust.id 
Integrator | TBD | TBD | TBD

# Terminology

* **A Data Agreement**: Data Agreement or “Consent” exists between organisations and individuals in the use of personal data. This agreement can have any of the legal basis that is outlined as per any data protection regulation, such as the GDPR.

## Scope

The scope of the WP is to follow the lifecycle of a data agreement and automate the process. A data agreement will have the following lifecycle:
1) **Definition**: The data agreement standards schema is added as a template.
2) **Preparation**: Populate a data agreement prior to sharing with a data subject. An input may be an assessment tool or activities like a DPIA.
3) **Negotiation/Capture**: The  individual reviews the data agreement offer and once agreed it is captured in a data agreement record by the organisation and the individual is given a data agreement receipt as evidence of the record.
4) **Proof**: An organisation is able to demonstrate a valid data agreement receipt for performing a data exchange with an individual. This allows an auditor to check and ensure records were in place to process the individual's personal data.

There are two different data agreements in SSI. One between the Issuer and the Holder and another between the Verifier and the Holder. Each one covers the mandatory fields for defining a clear purpose of use, jurisdiction and identifying sensitive fields being processed. The data agreement may have an associated assessment in the form of a DPIA or PIA or ISO 27001 Risk Assessment. The assessment gives additional assurance that regulation is being followed.
The data agreement has the following main requirements:

- Human readable
- Machine readable receipt
- Fully auditable

For the machine readable receipt the Kantara Consent Receipt Specification v 1.1.0 will be used as starting point. The human readability and auditability form of the data agreement will follow the  Richardian contract [6] which is a signed recording of a legal contract. The presentation of the human readable form may be of PDF, HTML or native GUI format. 

## Use Cases

These are the use cases to be worked on during the work package.

1) Create template Data Agreement for both human and machine readable formats
2) Updated Data Agreement in preparation phase based on an assessment (DPIA)
3) Capture Data Agreement receipt in negotiation/capture phase
4) Proof of data agreement during data exchange
5) Data agreement revoked/expired

These use cases are consideration for future phases:

6) Control of data transfer to 3rd party based on data agreement with the issuer. For example jurisdiction requirements or no sharing.

  
## Standard drafts and interoperability specs to be consulted 

These are some of the work surrounding consent and data agreement to be consulted during development of the project.

[1]    Infrastructure-oriented Open Call: Function Specification (ADA)
    ADD LINK WHEN AVAILABLE

[2]    Verifiable Consent    https://essif-lab.pages.grnet.gr/interoperability/verifierapis/#verifiable-consent 

[3]    Aries RFC 0167: Data Consent Lifecycle
https://github.com/hyperledger/aries-rfcs/blob/master/concepts/0167-data-consent-lifecycle/README.md 

[4]    CommonAccord, Consent Demo Intro    http://www.commonaccord.org/index.php?action=list&file=G/GA4GH/Demo/ 

## Additional References

[1]    Kantara Consent Receipt Specification v 1.1.0    https://kantarainitiative.org/file-downloads/consent-receipt-specification-v1-1-0/ 

[2]    ToIP Input and Semantics WG    https://wiki.trustoverip.org/display/HOME/Inputs+and+Semantics+Working+Group 

[3]    Kantara ANCR    https://kantarainitiative.org/confluence/display/WA/Charter 

[4]    W3C DPV: https://dpvcg.github.io/dpv/ 

[5]    GConsent: A consent ontology based on the GDPR by Trinity College Dublin    http://openscience.adaptcentre.ie/ontologies/GConsent/docs/ontology 

[6]    Ricardian contract: https://en.wikipedia.org/wiki/Ricardian_contract 

[7]    
    https://www.iso.org/standard/80392.html


## Participate:

If you are interesting in the work package please contact the coordinator of the work package and 