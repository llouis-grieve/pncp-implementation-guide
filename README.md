# PNCP Implementation Guide

This repository contains a public, version-controlled implementation guide for the Plug aNd Charge Protocol (PNCP) used on Gireve's Trust Platform. PNCP is an open protocol that defines how an operator's software platform interacts with a PKI ecosystem service provider to enable Plug and Charge services (PKI services, V2G Root CA, Certificate Provisioning Service and Pool services). It is used to support secure deployment of ISO‑15118 Plug and Charge in the EV charging ecosystem.  

The guide is intended for technical teams (system administrators, developers, project managers, etc.) integrating their systems with Gireve's Trust Platform via PNCP. To follow the recommendations, readers should already be familiar with PKI and digital certificates, as well as the basic principles of the ISO‑15118‑2 standard. The guide also references OCPI, which is useful but not strictly required.  

The original source for this documentation is the internal PNCP Implementation Guide (version 1.2.0), which describes both the generic protocol concepts and the specific implementation details on Gireve's Trust Platform.

## Document structure

The content of the original implementation guide has been split into several Markdown files under the `docs/` directory for easier navigation and maintenance:

- [`docs/01-introduction.md`](docs/01-introduction.md)  
  General introduction to the Gireve Trust Platform and PNCP, aims of the guide, intended audience, prerequisites, and definitions/abbreviations used throughout the document.

- [`docs/02-technical-guidelines.md`](docs/02-technical-guidelines.md)  
  Technical implementation guidelines for PNCP: core principles, module pattern, URL structure, OCPI-derived concepts, environment URLs, security (TLS, IP filtering, WAF, PKI), and client authentication and identification mechanisms.

- [`docs/03-functional-guidelines.md`](docs/03-functional-guidelines.md)  
  Functional implementation guidelines by actor role (CPO, eMSP, OEM, etc.), including PNCP services by role, permissions on the Gireve Trust Platform, and detailed workflows for PKI, RCP, notification, CPO, eMSP and OEM services.

- [`docs/04-pki-and-certificates.md`](docs/04-pki-and-certificates.md)  
  Background material on PKI and digital certificates: what a digital certificate is, how certificates are created, certificate chains and their validation, as well as revocation and renewal concepts.

- [`docs/05-iso15118-plug-and-charge.md`](docs/05-iso15118-plug-and-charge.md)  
  Overview of ISO‑15118‑2 and the Plug and Charge environment, including the V2G data link, Plug and Charge feature, ecosystem roles and actors, certificate types, and the main end‑to‑end use cases.

## Source and versioning

This repository is a Markdown transcription and restructuring of the PNCP Implementation Guide version 1.2.0 (dated 2026‑04‑28), which adds support for enrolled certificate consultation, metadata search, and expiry notifications compared to previous versions. Future changes to PNCP or Gireve's Trust Platform will be reflected here via standard Git versioning and tagged releases.
