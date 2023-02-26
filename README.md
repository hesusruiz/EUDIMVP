# EUDIMVP

# Issuance: Profile 1

[Prueba](https://viewer.diagrams.net/?tags=%7B%7D&highlight=0000ff&edit=_blank&layers=1&nav=1&title=issuance_all.drawio#Uhttps%3A%2F%2Fdrive.google.com%2Fuc%3Fid%3D1Bd0BVDR8LjtReK-aFV9v_VcPM4Do83tK%26export%3Ddownload)

## Main components in the profile

![Main components](images/issuance_background.svg)

**End user**: this profile is valid for both natural and juridical persons.

**Wallet**

Web application with a wallet backend. Private key management and most sensitive operations are performed in a backend server, operated by an entity trusted by the end user. Other profiles will support native and PWA mobile applications, for end users.

This type of wallet supports natural persons, juridical persons and natural persons who are legal entity representatives of juridical persons. For juridical persons the wallet is usually called an `enterprise wallet` but we will use here just the term `wallet` unless the distinction is required.

In this profile we assume that the wallet is not previously registered with the Issuer and that the wallet does not expose public endpoints that are called by the Issuer, even if the wallet has a backend server that could implement those endpoints. That makes the wallet implementations in this profile to be very similar in interactions to a full mobile implementation, making migration to a full mobile implementation easier.

In other words, from the point of view of the Issuer, the wallet in this profile is almost indistinguisible from a full mobile wallet.

**User Laptop**

For clarity of exposition, we assume in this profile that the End User starts the interactions with the Issuer with an internet browser (user agent) in her laptop. However, there is nothing in the interactions which limits those interactions to a laptop form factor and the End User can interact with any internet browser in any device (mobile, tablet, kiosk).

**Issuer**

In this profile we assume that the Issuer is composed of two components:

- Issuer backend: the main server implementing the business logic as a web application and additional backend APIs required for issuance of credentials.
- Authorization server: the backend component implementing the existing authentication/authorization functionalities for the Issuer entity.

The Issuer backend and the Authorization server could be implemented as a single component in a real use case, but we assume here that they are separated to make the profile more general.

**Authentication of End User and previous Issuer-End User relationship**

We assume that the Issuer and End User have a previous relationship and that the Issuer has performed the KYC required by regulation and needed to be able to issue Verifiable Credentials attesting some attributes about the End User. We assume that there is an existing trusted authentication mechanism (not necessarily related to Verifiable Credentials) that the End User employs to access protected resources from the Issuer. For example, the user is an employee or a customer of the Issuer, or the Issuer is a Local Administration and the End User is a citizen living in that city.



## Authentication

![Issuance authentication](images/issuance_authentication.svg)

Before requesting a new credential, the End User has to authenticate with the Issuer with the mechanism already implemented by the Issuer. This profile does not require that it is based on OIDC or any other specific mechanism.

The level of assurance (LoA) of this authentication mechanism is one of the factors that will determine the LoA or confidence that the Verifiers can have on the credentials received by them from a given Issuer.


## Credential Offer

![Credential offer](images/issuance_credentialoffer.svg)

## Issuer Metadata

![Issuer Metadata](images/issuance_issuermetadata.svg)

## Access Token

![Access Token](images/issuance_accesstoken.svg)

## Request and receive Credential

![Request and receive Credential](images/issuance_requestcredential.svg)



## Overview of the interactions

![Issuance flow](images/issuance_all.drawio.svg)

<a href="https://app.diagrams.net/#Hhesusruiz%2FEUDIMVP%2Fmain%2Fimages%2Fissuance_all.drawio.svg" target="_blank">Edit in diagrams.net</a>

<a href="https://www.draw.io/?lightbox=1&edit=_blank#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Fhesusruiz%2FEUDIMVP%2Fmain%2Fimages%2Fissuance_all.drawio.svg" target="_blank">View in diagrams.net</a>
