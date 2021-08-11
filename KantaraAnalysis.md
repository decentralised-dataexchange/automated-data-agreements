## Gap Analysis for Kantara consent receipts.

This document aims to compare the struct and vision of the [Kantara consent receipts](https://kantarainitiative.org/download/7902/?wpdmdl=9888&refresh=60cb01e041f181623917024) to [Gataca's Verifiable Consent](https://gataca-io.github.io/verifier-apis/#verifiable-consent) in order to adjust and make both compatible; or analyze the impact of a migration.

### Terminology comparison

| Kantara Consent Receipt | Verifiable Consent |
|---|---|
| PII  | Claims  |
| PII Controller | Authorized Entity |
| PII Principal Id | Approver Subject |
| consentReceiptID | Consent Id |

### Structure comparison

| Kantara Consent Receipt  | Verifiable Consent  |
|---|---|
| PII Principal Id  |  approverSubject |
| consentReceiptID | id |
| _Jurisdiction_ | - |
| _PolicyURL_ | - |
| Collection Method |  - (is it needed? is it not always the same?) |
| Sensitive - SPICat | - (not explicit) | 
| Public Key |  - (Not explicit, deducible from DID) |
| **-**  | **Proof (signature of the consent document by both parties)** |
| Multiple receivers | 1 Receiver of the data  |
| Multiple services | 1 Service per consent |
| Data of the receiver (contact, address, url...) | - (not explicit, might be obtainable from a public registry) |
| _Allow on behalf processing_ | _-_ |
| Purposes | Claims |
| PrimaryPurpose | - (not explicitely: purpose application) |
| Third party disclosure | purpose = third party sharing |
| Purpose Category + purpose | purpose (no human readable translation inside doc) |
| Pii Category | type |
| _Termination_ | - |
| Consent Type | - (not explicitely: always EXPLICIT by design?) |
| **- (no segregated data id per purpose, a global data identifier at doc level)** | credential Id |

`` Mayor changes are bold; missing info in italic
``

### Mayor gaps

There are 2 mayor gaps, considering compatibilities on the two models:
- Signature of the consent autocontained in the document itself
- Uniquely identifying each PII knowing the credential Ids

There are minor gaps:
- new data fields that were not directly present but inferable on the Verifiable Consent
- new data fields that were not being registered or used
- different structurization of equivalent fields
- bigger flexibility on multiple services and receivers (not sure about the use case)