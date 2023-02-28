# EUDIMVP

The The [European Digital Identity Wallet Architecture and Reference Framework](https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-wallet-architecture-and-reference-framework) (ARF) provides a set of the specifications needed to develop an interoperable European Digital Identity (EUDI) Wallet Solution based on common standards and practices.

At this moment (version 1.0.0), the ARF includes a lot of flexibility and some areas not yet detailed, so it is difficult for different implementers to achieve true interoperability.

This document defines an initial simple profile of the credential exchange mechanisms in ARF (issuance and presentation) that implementations can follow in order to achieve interoperability across different implementations in the initial stages of the project.

The generic name of the profile is MVP (Minimum Viable Profile). It essentially defines some sensible restrictions and specific definitions in the ARF and the underlying OIDC protocols so we can achieve faster initial interoperability and at the same time enough functionality and with a high degree of security.

The main documents referred to in this profile are the ARF and the OIDC family: [OpenID for Verifiable Credential Issuance](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html#name-credential-request) (OIDC4VCI), [OpenID for Verifiable Presentations](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html) (OIDC4VP) and [Self-Issued OpenID Provider V2](https://openid.bitbucket.io/connect/openid-connect-self-issued-v2-1_0.html) (SIOPv2).

See the detailed documents for the different flows:

- [Credential Issuance](issuance.md)

