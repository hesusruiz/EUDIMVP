# EUDIMVP

# Issuance: Profile 1

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

Before requesting a new credential, the End User has to authenticate with the Issuer with whatever mechanism is already implemented by the Issuer. This profile does not require that it is based on OIDC, Verifiable Credentials or any other specific mechanism. However, the Issuer may implement the authentication mechanism using Verifiable Credentials and OIDC4VP described later in this document.

The level of assurance (LoA) of this authentication mechanism is one of the factors that will determine the confidence that the Verifiers can have on the credentials received by them from a given Issuer.


## Credential Offer

![Credential offer](images/issuance_credentialoffer.svg)

In this profile the wallet does not have to implement the Credential Offer Endpoint described in section 4 of [OpenID for Verifiable Credential Issuance](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html) (OIDCVCI).

Instead, the Credential Issuer renders a QR code containing a reference to the Credential Offer that can be scanned by the End-User using a Wallet, as described in section 4.1 of OIDCVCI.

According to the spec, the Credential Offer object is a JSON object containing the Credential Offer parameters and can be sent by value or by reference. To avoid problems with the size of the QR, this profile requires that the QR contains the `credential_offer_uri`, which is a URL using the `https` scheme referencing a resource containing a JSON object with the Credential Offer parameters. The `credential_offer_uri` should be implemented by the Issuer backend.

### Credential Offer Parameters

This profile restricts the options available in section 4.1.1 of OIDC4VCI. The profile defines a Credential Offer object containing the following parameters:

- `credential_issuer`: REQUIRED. The URL of the Credential Issuer that will be used by the Wallet to obtain one or more Credentials.

- `credentials`: REQUIRED. A JSON array, where every entry is a JSON string. The string value MUST be one of the id values in one of the objects in the `credentials_supported` Credential Issuer metadata parameter (described later). When processing, the Wallet MUST resolve this string value to the respective object.

- `grants`: REQUIRED. A JSON object indicating to the Wallet the Grant Type `pre-authorized_code`. This grant is represented by a key and an object, where the key is `urn:ietf:params:oauth:grant-type:pre-authorized_code`.

The grant object contains the following values:

- `pre-authorized_code`: REQUIRED. The code representing the Credential Issuer's authorization for the Wallet to obtain Credentials of a certain type. This code MUST be short lived and single-use. This parameter value MUST be included in the subsequent Token Request with the Pre-Authorized Code Flow.

- `user_pin_required`: RECOMMENDED. Boolean value specifying whether the Credential Issuer expects presentation of a user PIN along with the Token Request in a Pre-Authorized Code Flow. Default is false. This PIN is intended to bind the Pre-Authorized Code to a certain transaction in order to prevent replay of this code by an attacker that, for example, scanned the QR code while standing behind the legit user. It is RECOMMENDED to send a PIN via a separate channel. If a PIN is required, a PIN value MUST be sent in the `user_pin` parameter with the respective Token Request.

The following non-normative example shows a Credential Offer object where the Credential Issuer offers the issuance of one Credential ("UniversityDegree"):

```json
{
   "credential_issuer": "https://credential-issuer.example.com",
   "credentials": [
      "UniversityDegree"
   ],
   "grants": {
      "urn:ietf:params:oauth:grant-type:pre-authorized_code": {
         "pre-authorized_code": "adhjhdjajkdkhjhdj",
         "user_pin_required": true
      }
   }
}
```

### Contents of the QR code

Below is a non-normative example of the Credential Offer displayed by the Credential Issuer as a QR code when the Credential Offer is passed by reference, as required in this profile:

```
https://credential-issuer.example.com/credential-offer?
  credential_offer_uri=https%3A%2F%2Fserver%2Eexample%2Ecom%2Fcredential-offer%2F5j349k3e3n23j
```

The Issuer MUST make sure that every Credential Offer URI is unique for all credential offers created.


## Credential Issuer Metadata

![Issuer Metadata](images/issuance_issuermetadata.svg)

### Credential Issuer Identifier

A Credential Issuer is identified in this context by a case sensitive URL using the https scheme that contains scheme, host and, optionally, port number and path components, but no query or fragment components. No DID is used in this context.

### Credential Issuer Metadata Retrieval

The Credential Issuer's configuration can be retrieved using the Credential Issuer Identifier.

Credential Issuers MUST make a JSON document available at the path formed by concatenating the string `/.well-known/openid-credential-issuer` to the Credential Issuer Identifier. If the Credential Issuer value contains a path component, any terminating / MUST be removed before appending `/.well-known/openid-credential-issuer`.

`openid-credential-issuer` MUST point to a JSON document compliant with this specification and MUST be returned using the `application/json` content type.

### Credential Issuer Metadata Parameters

The object contained in `openid-credential-issuer` contains the following:

- `credential_issuer`: REQUIRED. The Credential Issuer's identifier.

- `credential_endpoint`: REQUIRED. URL of the Credential Issuer's Credential Endpoint. This URL MUST use the https scheme and MAY contain port, path and query parameter components.

- `credentials_supported`: REQUIRED. A JSON array containing a list of JSON objects, each of them representing metadata about a separate credential type that the Credential Issuer can issue. The JSON objects in the array MUST conform to the structure of the Section XXXX.

TODO: define a global directory of credentials supported to eliminate requirememt for each individual Issuer to publish it sown list.

This profile does not make use of the following parameters:

- `authorization_server` parameter, because it uses the `pre-authorized_code` Grant type.

- `batch_credential_endpoint` parameter. It indicates that the Credential Issuer does not support the Batch Credential Endpoint.

- `display` parameter.


## OAuth 2.0 Authorization Server Metadata

This specification also defines a new OAuth 2.0 Authorization Server metadata [RFC8414] parameter to publish whether the AS that the Credential Issuer relies on for authorization, supports anonymous Token Requests with the Pre-authorized Grant Type. It is defined as follows:

- `pre-authorized_grant_anonymous_access_supported`: OPTIONAL. A JSON Boolean indicating whether the issuer accepts a Token Request with a Pre-Authorized Code but without a client id. The default is false.


## Access Token

![Access Token](images/issuance_accesstoken.svg)

The Wallet invokes the Token Endpoint implemented by the Authorization Server, which issues an Access Token and, optionally, a Refresh Token in exchange for the Pre-authorized Code that the wallet obtained in the Credential Offer.

### Token Request

After the wallet receives the Credential Issuer Metadata, a Token Request is made as defined in Section 4.1.3 of [RFC6749].

The following are the extension parameters to the Token Request used in a Pre-Authorized Code Flow as used in this profile:

- `pre-authorized_code`: REQUIRED. The code representing the authorization to obtain Credentials of a certain type.

- `user_pin`: OPTIONAL. String value containing a user PIN. This value MUST be present if user_pin_required was set to true in the Credential Offer. The string value MUST consist of maximum 8 numeric characters (the numbers 0 - 9).

In this profile the Wallet does not have to authenticate when using the Token Endpoint, because we are using the Pre-Authorized Code Grant Type, given the level of trust between the Issuer and the End User and that authentication was already performed at the beginning of the flow.

Below is a non-normative example of a Token Request:

```
POST /token HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded

  grant_type=urn:ietf:params:oauth:grant-type:pre-authorized_code
  &pre-authorized_code=SplxlOBeZQQYbYS6WxSbIA
  &user_pin=493536
```

### Successful Token Response

Token Responses are made as defined in [RFC6749].

In addition to the response parameters defined in [RFC6749], the Authorization Server returns the following parameters:

- `c_nonce`: REQUIRED. JSON string containing a nonce to be used to create a proof of possession of key material when requesting a Credential. The Wallet MUST use this nonce value for its subsequent credential requests until the Credential Issuer provides a fresh nonce.

- `c_nonce_expires_in`: REQUIRED. JSON integer denoting the lifetime in seconds of the c_nonce.

Below is a non-normative example of a Token Response:

HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

  {
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6Ikp..sHQ",
    "token_type": "bearer",
    "expires_in": 86400,
    "c_nonce": "tZignsnFbp",
    "c_nonce_expires_in": 86400
  }

### Token Error Response

If the Token Request is invalid or unauthorized, the Authorization Server constructs the error response as defined in section 6.3 of OIDC4VCI.


## Request and receive Credential

![Request and receive Credential](images/issuance_requestcredential.svg)


The Wallet backend invokes the Credential Endpoint, which issues a Credential as approved by the End-User upon presentation of a valid Access Token representing this approval.

Communication with the Credential Endpoint MUST utilize TLS.

The client can request issuance of a Credential of a certain type multiple times, e.g., to associate the Credential with different public keys/Decentralized Identifiers (DIDs) or to refresh a certain Credential.

If the Access Token is valid for requesting issuance of multiple Credentials, it is at the client's discretion to decide the order in which to request issuance of multiple Credentials requested in the Authorization Request.

### Binding the Issued Credential to the identifier of the End-User possessing that Credential

The Issued Credential MUST be cryptographically bound to the identifier of the End-User who possesses the Credential. Cryptographic binding allows the Verifier to verify during the presentation of a Credential that the End-User presenting a Credential is the same End-User to whom that Credential was issued.

The Wallet has to provide proof of control alongside key material (proof that did).

### Credential Request

The Wallet backend makes a Credential Request to the Credential Endpoint by sending the following parameters in the entity-body of an HTTP POST request using the `application/json` media type.

- `format`: REQUIRED. This profile uses the Credential format identifier `jwt_vc_json`.

- `proof`: OPTIONAL. JSON object containing proof of possession of the key material the issued Credential shall be bound to. The specification envisions use of different types of proofs for different cryptographic schemes. The proof object MUST contain a proof_type claim of type JSON string denoting the concrete proof type. This type determines the further claims in the proof object and its respective processing rules. Proof types are defined in Section 7.2.1.

The proof element MUST incorporate a c_nonce value generated by the Credential Issuer and the Credential Issuer Identifier (audience) to allow the Credential Issuer to detect replay. The way that data is incorporated depends on the proof type. In a JWT, for example, the c_nonce is conveyd in the nonce claims whereas the audience is conveyed in the aud claim. In a Linked Data proof, for example, the c_nonce is included as the challenge element in the proof object and the Credential Issuer (the intended audience) is included as the domain element.

#### Proof Type

This specification defines the following values for proof_type:

- `jwt`: objects of this type contain a single jwt element with a JWS [RFC7515] as proof of possession. The JWT MUST contain the following elements:

  - in the JOSE Header,

    - `typ`: REQUIRED. MUST be `openid4vci-proof+jwt`, which explicitly types the proof JWT as recommended in Section 3.11 of [RFC8725].

    - `alg`: REQUIRED. A digital signature algorithm identifier such as per IANA "JSON Web Signature and Encryption Algorithms" registry. MUST NOT be none or an identifier for a symmetric algorithm (MAC).

    - `kid`: REQUIRED. JOSE Header containing the key ID. The Credential will be bound to a DID, so the kid refers to a DID URL which identifies a particular key in the DID Document that the Credential will be bound to.

  - in the JWT body,

    - `aud`: REQUIRED (string). The value of this claim MUST be the Credential Issuer URL of the Credential Issuer.

    - `iat`: REQUIRED (number). The value of this claim MUST be the time at which the proof was issued using the syntax defined in [RFC7519].

    - `nonce`: REQUIRED (string). The value type of this claim MUST be a string, where the value is the `c_nonce` provided by the Credential Issuer.

The Credential Issuer MUST validate that the proof is actually signed by a key identified in the JOSE Header.

Below is a non-normative example of a proof parameter (line breaks for display purposes only):

```json
{
  "proof_type": "jwt",
  "jwt": "eyJraWQiOiJkaWQ6ZXhhbXBsZTplYmZlYjFmNzEyZWJjNmYxYzI3NmUxMmVjMjEva2V5cy8
  xIiwiYWxnIjoiRVMyNTYiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJzNkJoZFJrcXQzIiwiYXVkIjoiaHR
  0cHM6Ly9zZXJ2ZXIuZXhhbXBsZS5jb20iLCJpYXQiOiIyMDE4LTA5LTE0VDIxOjE5OjEwWiIsIm5vbm
  NlIjoidFppZ25zbkZicCJ9.ewdkIkPV50iOeBUqMXCC_aZKPxgihac0aW9EkL1nOzM"
}
```

where the JWT looks like this:

```json
{
  "typ": "openid4vci-proof+jwt",
  "alg": "ES256",
  "kid":"did:example:ebfeb1f712ebc6f1c276e12ec21/keys/1"
}.
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "iat": 1659145924,
  "nonce": "tZignsnFbp"
}
```

### Credential Response

This profile restricts Credential Response to be Synchronous and Deferred response is not used. The Credential Issuer MUST be able to immediately issue a requested Credential and send it to the Client.

The following claims are used in the Credential Response:

- `format`: REQUIRED. JSON string denoting the format of the issued Credential. This profile uses the format identifier `jwt_vc_json`.

- `credential`: REQUIRED. Contains issued Credential. MUST be a JSON string.

- `c_nonce`: OPTIONAL. JSON string containing a nonce to be used to create a proof of possession of key material when requesting a Credential. When received, the Wallet MUST use this nonce value for its subsequent credential requests until the Credential Issuer provides a fresh nonce.

- `c_nonce_expires_in`: OPTIONAL. JSON integer denoting the lifetime in seconds of the c_nonce.

Below is a non-normative example of a Credential Response:

```json
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "format": "jwt_vc_json",
  "credential" : "LUpixVCWJk0eOt4CXQe1NXK....WZwmhmn9OQp6YxX0a2L",
  "c_nonce": "fGFF7UkhLa",
  "c_nonce_expires_in": 86400
}
```

### Credential Error Response

When the Credential Request is invalid or unauthorized, the Credential Issuer constructs the error response as defined in section 7.3.1 of OIDC4VCI.

### Credential Issuer Provided Nonce

Upon receiving a Credential Request, the Credential Issuer MUST require the Wallet to send a proof of possession of the key material it wants a Credential to be bound to. This proof MUST incorporate a nonce generated by the Credential Issuer. The Credential Issuer will provide the client with a nonce in an error response to any Credential Request not including such a proof or including an invalid proof.

Below is a non-normative example of a Credential Response with the Credential Issuer requesting a Wallet to provide in a subsequent Credential Request a proof that is bound to a `c_nonce`:

```json
HTTP/1.1 400 Bad Request
Content-Type: application/json
Cache-Control: no-store

{
  "error": "invalid_or_missing_proof",
  "error_description":
       "Credential Issuer requires proof to be bound to a Credential Issuer provided nonce.",
  "c_nonce": "8YE9hCnyV2",
  "c_nonce_expires_in": 86400
}
```


## Summary: Overview of the interactions

![Issuance flow](images/issuance_all.drawio.svg)

<a href="https://app.diagrams.net/#Hhesusruiz%2FEUDIMVP%2Fmain%2Fimages%2Fissuance_all.drawio.svg" target="_blank">Edit in diagrams.net</a>

<a href="https://www.draw.io/?lightbox=1&edit=_blank#Uhttps%3A%2F%2Fraw.githubusercontent.com%2Fhesusruiz%2FEUDIMVP%2Fmain%2Fimages%2Fissuance_all.drawio.svg" target="_blank">View in diagrams.net</a>
