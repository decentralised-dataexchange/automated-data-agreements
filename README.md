<h1 align="center">
    Automated Data Agreements - Making data transactions trustworthy, auditable and immutable
</h1>

<p align="center">
    <a href="/../../commits/" title="Last Commit"><img src="https://img.shields.io/github/last-commit/decentralised-dataexchange/automated-data-agreements?style=flat"></a>
    <a href="/../../issues" title="Open Issues"><img src="https://img.shields.io/github/issues/decentralised-dataexchange/automated-data-agreements?style=flat"></a>
    <a href="./LICENSE" title="License"><img src="https://img.shields.io/badge/License-Apache%202.0-green.svg?style=flat"></a>
</p>


<p align="center">
  <a href="#about">About</a> •
  <a href="#release-status">Release Status</a> •
  <a href="#contributing">Contributing</a> •
  <a href="#licensing">Licensing</a>
</p>

## About

This repository contains the deliverables for Automated Data Agreement (ADA) Project. This is part of NGI-eSSIF-Lab project that has received funding from the European Union’s Horizon 2020 research and innovation programme under grant agreement No 871932.

This ADA project is an open source conrtibution lead by iGrant.io (Sweden), Linaltec (Sweden) and PrivacyAnt (Finland). Read more on the project at NGI eSSIF-Lab page: https://essif-lab.eu/automated-data-agreements-to-simplify-ssi-work-flows-by-igrant-io/

The Automated Data Agreements solution (ADA) was added to the [Digital Public Goods Alliance Registry](https://digitalpublicgoods.net/registry/automated-data-agreement.html) and recognised as a critical solution contributing to digital public goods (DPGs).

## Release Status (Specifications deliverables)

The first version of the DA specification is released and implemented. The table summarises the key documentations delivered during the NGI-eSSIF Lab project which originally sponsored this work.

| Date             | Deliverables                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| :--------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 15 May 2021      | 1. [Functional specification](https://github.com/decentralised-dataexchange/automated-data-agreements/blob/main/docs/functional_specification.md)<br>2. [Interface specification](https://github.com/decentralised-dataexchange/automated-data-agreements/blob/main/docs/interface_specification.md) <br>3. [Envisioned interoperability (eSSIF-Lab git)](https://gitlab.grnet.gr/essif-lab/infrastructure_2/igrantio/deliverables/-/blob/master/envisioned_interoperability_with_others.md)                                                                                                                                     |
| 15 December 2021 | 1. [Demo](https://youtu.be/Mq4oXEaOTwg)<br>2. [Data Agreement specification](https://github.com/decentralised-dataexchange/automated-data-agreements/blob/main/docs/data-agreement-specification.md)<br>3. [Data Agreement DID method specification](https://github.com/decentralised-dataexchange/automated-data-agreements/blob/main/docs/did-spec.md)<br>4. [Data Agreement DIDComm protocol specification](https://github.com/decentralised-dataexchange/automated-data-agreements/blob/main/docs/didcomm-protocol-spec.md)<br>5. [Data Agreement APIs Rel. 2.0.0](https://app.swaggerhub.com/apis-docs/iGrant.io/ADA/2.0.0) |
| 15 January 2022  | 1. [Project report (eSSIF-Lab git) ](https://gitlab.grnet.gr/essif-lab/infrastructure_2/igrantio/deliverables/-/blob/master/M9_feasibility_report.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
## Sourcecode deliverables

| Repository                                                                                                              | Description                                                                                                                                                                                                                                                                                                                                                  |
| :---------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [acapy-mydata-did-protocol](https://github.com/decentralised-dataexchange/acapy-mydata-did-protocol)                    | This repository hosts source code for did:mydata DIDCOMM protocol plugin for ACA-Py. This protocol will enable an ACA-Py instance to become a verifiable data registry for did:mydata.                                                                                                                                                                       |
| [aries-playground](https://github.com/decentralised-dataexchange/aries-playground/tree/master/automated-data-agreement) | This repository contains the updated aries-playground (originally created by the iGrant.io team). This provides a set-up for developers to perform API call flows during a data agreement enabled verified data exchange process using Hyperledger Indy as the distributed ledger registry and Aries agent as the client app.                                |
| [universal-resolver](https://github.com/decentralised-dataexchange/universal-resolver)                                  | The Universal Resolver resolves Decentralized Identifiers (DIDs) across many different DID methods, based on the W3C DID Core 1.0 and DID Resolution specifications. It is a work item of the DIF Identifiers & Discovery Working Group. As part of the ADA project, we have added the did:mydata driver that can be resolved at https://dev.uniresolver.io/ |
| [mydata-did-driver](https://github.com/decentralised-dataexchange/mydata-did-driver)                                    | This repository hosts the source code for the universal resolver driver for did:mydata.                                                                                                                                                                                                                                                                      |

## Other resources

* iGrant.io Whitepaper: [A Sustainable Data Exchange - An ethical approach to sharing personal data](https://igrant.io/papers/iGrant.io_Sustainable_Data_Exchange_v1.pdf)
* [DIF Interop WG: Enabling exchange of X.509 signed personal data using VC](https://us02web.zoom.us/j/87258415110?pwd=cFhwYkRsUjRsYnFZZFgyQVR6Zk0xZz09)
* [Consent Agreement Demo - Special Meeting of the DIF C&C WG](https://www.youtube.com/watch?v=Mq4oXEaOTwg)
* `DID:mydata` at [W3C DID Registry](https://www.w3.org/TR/did-spec-registries/)
## Contributing

Feel free to improve the plugin and send us a pull request. If you found any problems, please create an issue in this repo.

## Licensing
Copyright (c) 2021-25 LCubed AB (iGrant.io), Sweden

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License.

You may obtain a copy of the License at https://www.apache.org/licenses/LICENSE-2.0.

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the LICENSE for the specific language governing permissions and limitations under the License.
