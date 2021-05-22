# Functional specification - Automated Data Agreement (ADA)

## Table of contents

- [1.0 Background](#10-background)
  * [1.1 Actors involved in a data agreement workflow](#11-actors-involved-in-a-data-agreement-workflow)
  * [1.2 Data agreements](#12-data-agreements)
  * [1.3 Data exchange, agreements and compliance landscape](#13-data-exchange-agreements-and-compliance-landscape)
    + [1.3.1   Agreement between two organisations](#131agreement-between-two-organisations)
    + [1.3.2   Agreement between an organisation and their suppliers](#132agreement-between-an-organisation-and-their-suppliers)
  * [1.4  Data Protection Impact Assessment (DPIA)](#14data-protection-impact-assessment-dpia)
  * [1.5  Scope](#15scope)
- [2.0   Problem statement](#20problem-statement)
- [3.0   Functional overview](#30functional-overview)
  * [3.1  Automated data agreement workflow in SSI](#31automated-data-agreement-workflow-in-ssi)
  * [3.2  Delegation/Guardianship data exchange](#32delegationguardianship-data-exchange)
  * [3.3  Architecture](#33architecture)
    + [3.3.1   High-level overview](#331high-level-overview)
    + [3.3.2   ADA component architecture](#332ada-component-architecture)
  * [3.4 Interfaces and APIs](#34-interfaces-and-apis)
    + [3.4.1   Data agreement](#341data-agreement)
    + [3.4.2   Delegated consents](#342delegated-consents)
- [Glossary](#glossary)

# 1.0 Background

This project (also referred to as the ADA) focuses on building key governance elements in the SSI stack that accelerates SSI adoption and aims to remove the regulatory pain points for SSI solution providers within the eSSIF-Lab community and beyond by:

1. Standardising data agreement schemas across SSI solution providers.
2. Developing an open source component that automates the process for data agreement handling that can be linked to the issuance and verification of verifiable credentials. This solution is aligned with data regulatory requirements for data exchange and can be plugged into any existing SSI stack workflow.
3. Addressing delegated data agreements or consents for children and elderly citizens.

Throughout this specification we mean requirements pertaining to the GDPR when referring to regulatory compliance, data protection regulation, regulation, data requirements etc, unless otherwise stated.

## 1.1 Actors involved in a data agreement workflow

These are the actors using ADA services: 

* a **Data Source**, the organisation collecting private data, (typically a data controller).  [SSI: Issuer]
* a **Data Subject** or **Individual**. [SSI: Holder]
* a **Data Using Service**, an organisation processing personal data from one or more data sources to deliver a service. [SSI: Verifier]
* an **Assessor** reviews the practices of an organisation, conducts a DPIA and drafts data agreements and inter-company agreements for third parties.
* an **Auditor** may be called in to review the data agreements and ensure they are in place in case of data breaches or regular inspection. 

## 1.2 Data agreements

In this project, we will refer to three types of data agreements:

* A **Data Agreement** exists between organisations and individuals in the use of personal data. This agreement can have any of the legal basis that is outlined as per any data protection regulation.
* A **Data Using Agreement** is a new term introduced here to capture the agreement between two companies on how data is shared and used, that can be realised as a contract or terms of use.
* A **Data Processing Agreement** is a legally binding contract, either in written or electronic form, entered into between a data processor and a data controller that states the rights and obligations of each party concerning the protection of personal data. The agreement is legally binding in the context of any data protection regulation.

## 1.3 Data exchange, agreements and compliance landscape 
This section explains the regulatory, privacy-related assessment and commitment to the individual (data subject) which will help understand the actors involved and the relationships between them.

The following sections introduce two different sets of relationships between organisations, depending on their roles in the personal data usage scenarios. In the first, both organisations are data controllers, albeit with different roles. In the other, only the data controller has an agreement with the individual and executes separate agreements with the data processors and sub-processors. The two perspectives determine the relationship between the various organisation roles and whether they should be subject to a data using agreement or a data processing agreement.

### 1.3.1	Agreement between two organisations 

![](images/data-agreements-ds-dus.png)

In this case, both organisations share the role as data controller and are responsible for managing their compliance requirements as such. They may have made their own privacy risk assessment, documented in a data protection impact assessment (DPIA) report, and concluded whether any additional mitigation effort was required.

In order for the two organisations to perform a data exchange we introduce a new term, a data using agreement, which, although not yet standardised, captures the understanding between two companies on how data is shared and what obligation each party has and that can be realised as a contract or in terms of use.

The individual (data subject) is engaged with both organisations and for each there is a standard data agreement and associated privacy policy that explains the purpose of processing personal data, what personal data is collected, data subject rights, etc.

### 1.3.2	Agreement between an organisation and their suppliers 

![](images/data-agreements-org-supplier.png)

In this view, there is a vertical relationship between a data controller and suppliers in the form of data processors and data sub-processors. For a higher level of accountability between these businesses a data processing agreement is set up, which lays out what routines are required to be in place: for example, data processor’s obligations  in case of data breaches or how rights of the data subject, such as access rights, are supported, among other policies and routines. An auditor should also be able to inspect an organisation, and use the data processing agreement as reference during the inspection.

As depicted in the diagram above, the data agreement with the individual is bound to the top of the hierarchy, i.e. the data controller or organisation .

## 1.4	Data Protection Impact Assessment (DPIA)

A Data Protection Impact Assessment (DPIA) is a process to help an organisation identify and minimise the data protection risks involved in the use of personal data. It ensures that an organisation is compliant to data regulations. 

Article 35 of the GDPR requires organisations to conduct DPIAs, especially when the processing is likely to result in high risk to the rights and freedoms of natural persons such as in the case when processing involves using new technologies and is extensive as well as when especially sensitive categories of personal data are being processed (e.g. health-related data). Organisations can also conduct DPIAs voluntarily, even if the processing does not meet the criteria set out in the GDPR.
Some EU Member State data protection authorities, such as the Finnish data protection ombudsman, have recommended using dataflow maps when conducting DPIAs. Dataflow maps visualize the flows of personal data across systems, organisations, and jurisdictions, which provide an overview of the nature and scope of the processing and identify risks.

## 1.5	Scope
The ADA project focuses on the data agreement, which is defined as an agreement between organisations and individuals in the use of personal data. This agreement can have any of the legal basis that is outlined as per data protection law or regulation.

# 2.0	Problem statement

The problems addressed by the ADA components are as described below. These issues need to be addressed by any organisation (be it an issuer or a verifier) before they can commercially roll out an SSI based data exchange. 

* **Lack of fully auditable, standardised data agreements that are signed between the individual and organisations:** There are no standardised ways to handle the relationship between the data agreements and the verified credentials that are issued and verified. The schema definitions of the personal data that is being exchanged and the data agreement schemas are not aligned and are vendor dependent. 
* **Legal compliance and governance challenges** are preventing organisations from using SSI technology in their commercial systems to enable third party data sharing and exchange for any organisation, for example, with consents as legal basis. 
* **Vendor lock-in on how data agreements are handled and understood by end users:** A comprehensive data agreement that is easy to understand by individuals is inhibiting a unified user experience and scalable roll out of an SSI enabled data exchange solution. 

# 3.0	Functional overview

The key functional areas addressed in this project are:

1. **Automated data agreement handling in SSI:** The ADA (Automated data agreement) component will be an component that can be deployed into any DPIA tooling or any data exchange service. Any SSI based data exchange solution could use this component to bring in legitimacy from a GDPR perpective. 

2. **Human-readable presentation and data agreement sign off:** To plugin data agreements to the existing presentation of any data schema in a comprehensive and readable form to the individual with a mobile app reference implementation.

3. **Delegation/guardianship data exchange:** To introduce mechanisms to support delegation/guardianship required for providing consent / delegated data agreements.

Legal and privacy experts will ensure compliance as to what data is required to be shared and the decision process (tree) to identify the attributes required to be in a verifiable credential. The ADA project  will collaborate closely with Aries, Trust over IP (ToIP), Kantara, DIF, MyData, W3C and ISO.

The ADA component will have the following deployement features: 

* It will incorporate the outcome from a DPIA and can be used by all eSSIF-Lab participants who wish to reduce legal liability during an SSI-enabled data exchange.
* The component is privacy-by-design and provides automated tooling for organisations, taking care of the entire data agreement lifecycle.
* Goes beyond technology: Presentation of agreements and forms beyond a schema, providing enhanced end user experience and trust with standardised agreement presentation and handling, making SSI technology adoptable by organisations and consumable by individuals without adversely impacting the user experience.

## 3.1	Automated data agreement workflow in SSI

The data agreement lifecycle containing the functionalities envisioned in this project are as given below: 

![](images/data-agreement-workflow-to-ssi.png)

1. **Definition:** The data agreement standards schema is added as a template.

2. **Preparation:** Converting the definition as outlined in the DPIA to an agreement that is legally valid and is published for individuals and Data Using Services..

3. **Negotiation/Capture:** The  individual reviews the data agreement offer and once agreed, it is captured as a data agreement receipt by the organisation which is counter signed by the individual. 

4. **Proof:** An organisation is able to demonstrate a valid data agreement receipt for performing a data exchange with an individual. This allows an auditor to check and ensure records were in place to process the individual's personal data.

The data agreement receipts generated during an SSI flow will include a reference to the last DPIA that was conducted before the receipt was created. This provides increased auditability and traceability with the organisations involved. 

The table below summarises the actors and how they are involved in the different phases of the data agreement lifecycle.

| Actors             	| Definition                      	| Preparation    	| Capture        	| Proof                                                 	|
|:--------------------	|:---------------------------------	|:----------------	|:----------------	|:-------------------------------------------------------	|
| Assessor           	| Specification of data agreement 	|        -       	|        -       	| Specification of data agreement by data using service 	|
| Data Source        	|                -                	| Data agreement 	| Data agreement 	| Data agreement receipt (read only)                    	|
| Individual         	|                -                	|        -       	| Data agreement 	| Data agreement receipt (read only)                    	|
| Data Using Service 	|                -                	| Data agreement 	| Data agreement 	| Data agreement receipt (read only)                    	|
| Auditor            	|                -                	|        -       	|        -       	| Data agreement receipt (read only)                    	|

## 3.2	Delegation/Guardianship data exchange

Following are the delegation scenarios which we intend to address as part of the project  including delegated consents and transfer of data control: 

* Delegated consent: 
  * For children under a certain age at any time and any situation, consent is delegated to their parents or guardians. 
  * For the elderly or incapacitated, on site, on demand, or during an emergency scenario, such as an accident, consent is delegated to anyone with power of attorney

* Transfer of data control:
  * Temporary transfer of control of an IoT device data, for example, during a rental, or valet parking service. 

## 3.3	Architecture

### 3.3.1	High-level overview

The overall iGrant.io platform with software components delivering iGrant.io services are as illustrated below.

![](images/mdo-platform-overview.png)

In this project, we will work on creating a common component out of the “**Permissions and consents**” architectural block and connect it to the ‘**Data exchange**’ and ‘**SSI roles**’ architectural blocks as described below (ADA component architecture). 

### 3.3.2	ADA component architecture

The high-level component architecture illustrated below follows a microservice architecture that can be plugged into existing systems, starting with iGrant.io SSI workflows. The core components deliver ADA services, exposed as RESTFul APIs. 

The pluggable component can exist independently in any service provider where ADA core services can be plugged into. 

![ada-component-arch.png](images/ada-component-arch.png)

## 3.4 Interfaces and APIs

### 3.4.1	Data agreement

The following are the key interfaces for the ADA.

“**Definition**” and “**Preparation**” of Data Agreements has the following methods:

1. CRUD (Create/Read/Update/Delete) data agreement(s)
2. Publish/unpublish data agreement(s) towards deployments

“**Negotiation/Capture**” has the following methods:

3. Individual Sign and Revoke (Opt-out) data agreement that result in a data agreement receipt

“**Proof**” has the following method: 

4. Data agreement receipt resolver to resolve any data agreement receipt (Data agreement + signature)

It also provides the ability to filter and search in a data agreement registry. 

5. Search data agreement registry (companies using specification) or Read existing data agreement definitions.

In the future, the project will look at revoking data agreements, if they are based on a  verified credential model. It will also investigate further how to create/read/search data agreement templates. 

### 3.4.2	Delegated consents

This component addresses delegated data agreements/consents for children, elderly citizens and for anyone during an emergency scenario.

1. CRUD user delegation (relationship)
2. Key exchange during delegation


# Glossary

| Abbr  	| Expanded form                      	|
|:------	|:------------------------------------------------	|
| ADA  	| Automated Data Agreements                      	|
| CRUD 	| Create / Read / Update / Delete                	|
| DID  	| Decentralized Identifier (according to W3C)    	|
| DPIA 	| Data Protection Impact Assessment              	|
| EEA  	| European Economic Area                         	|
| EU   	| European Union                                 	|
| GDPR 	| General Data Protection Regulation             	|
| ISO  	| International Organization for Standardization 	|
| JSON 	| JavaScript Object Notation                     	|
| SDK  	| Software Development Kit                       	|
| SSI  	| Self Sovereign Identity                        	|
| ToIP 	| Trust over Internet Protocol                   	|
| UC   	| Use case                                       	|
| VC   	| Verifiable credentials                         	|
| W3C  	| World wide web consortium                      	|


**Terminologies** used in the document are as below:

| Term                                      	| Description                                                                                                                                                                                                                                                                                                                                                             	|
|:-------------------------------------------	|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Data Agreement                            	| A Data Agreement exists between organisations and individuals in the use of personal data. This agreement can have any legal basis that is outlined according to any data protection regulation, such as the GDPR.                                                                                                                                                      	|
| Decentralized IDentifier (DID)            	| A DID is a new type of identifier that is globally unique, resolvable with high availability, and cryptographically verifiable. DIDs are typically associated with cryptographic material, such as public keys, and service endpoints, for establishing secure communication channels                                                                                   	|
| Data Processing Agreement                 	| A Data Processing Agreement is a legally binding contract, either in written or electronic form, entered into between a data processor and a data controller that states the rights and obligations of each party concerning the protection of personal data. The agreement will be legally binding in the context of any data protection regulation, such as the GDPR. 	|
| Data Source                               	| The role responsible for collecting, storing, and controlling personal data which persons, operators, and data using services may wish to access and use. This is defined according to MyData.                                                                                                                                                                          	|
| Data Using Service                        	| The role responsible for processing personal data from one or more data sources to deliver a service. This is defined according to MyData.                                                                                                                                                                                                                              	|
| Data Using Agreement                      	| A Data Using Agreement is a new term introduced here to capture the understanding between two companies on how data is shared, how the Data Using Service will be provided, and that can be realised as a contract or in terms of use.                                                                                                                                  	|
| Data Protection Impact Assessment (DPIA)  	| A process designed to help systematically analyse, identify and minimise the data protection risks of a project or plan.                                                                                                                                                                                                                                                	|
| General Data Protection Regulation (GDPR) 	| The GDPR is an EU regulation on data protection and privacy applicable in the EU and the EEA                                                                                                                                                                                                                                                                            	|
| Individual                                	| A natural, living human being, in the GDPR also referred to as a Data Subject                                                                                                                                                                                                                                                                                           	|
| Self Sovereign Identity                   	| A model for managing digital identities where individual identity holders can fully create and control their verifiable credentials, without being forced to request permission of an intermediary or centralised authority and gives control over how their personal data is shared and used                                                                           	|

Below table summarises the terminology translations across communities or standards:

| MyData Operator    	| GDPR                                 	| Self Sovereign Identity       	|
|:--------------------	|:--------------------------------------	|:-----------	|
| Data Source        	| Data controller                      	| Issuer    	|
| Data Using Service 	| Data co-controller or Data processor 	| Verifier  	|
| Individual         	| Data Subject                         	| Holder    	|

