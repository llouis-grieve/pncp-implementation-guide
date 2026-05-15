# 2 Technical implementation guidelines

## 2.1 PNCP principles

PNCP protocol is based on the same principles as OCPI protocol, to reduce the complexity for actors using OCPI for roaming (like CPOs and eMSPs).

The following basic principles are inherited from this "proximity" with OCPI.

- GIREVE Trust Platform APIs use Json/Rest standards.
- GIREVE Trust Platform APIs are structured in "modules" that group APIs on a functional basis.

But some OCPI mechanisms are not relevant for the PNCP features and thus have not been integrated in the protocol:

- There is no credential-like module in PNCP.
- The authentication is done through OAuth2 mechanism.

> **ℹ️**
> Note that, for security reasons, the GIREVE Roaming platform and the GIREVE Trust Platform do not share a unique partner authentication mechanism nor a common credential management.

## 2.2 PNCP Module pattern

To fulfil the principle of "proximity" with OCPI, GIREVE has implemented 4 custom modules[^1]:

- **PNCGenericCertificate module**: Module used to manage x509 certificates.
- **PNCRootCACertificate module**: Module used to manage ISO-15118-Plug&Charge Root-CA Certificates.
- **PNCProvisioningCertificate module**: Module used to manage ISO-15118-Plug&Charge Provisioning Certificates of EVs.
- **PNCContractCertificate module**: Module used to manage ISO-15118-Plug&Charge contract certificates.

Custom modules defined by GIREVE for PNCP, following the OCPI module pattern.

## 2.3 URLs

The PNCP URLs are structured by the following pattern:

- URL = `https://<environment>.gireve.com/pncp/<API_Version>/<module_name>`

- with
  - `<environment>` which describes the production or preproduction environment.
    - Production environment: `"trust-api"`
    - Preproduction environment: `"pp-trust-api"`
  - `<API_Version>` which designate the version of the protocol.
    - Structure following the pattern `"x.y.z"`
  - `<module_name>` which designates the module:
    - `"PNCContractCertificate"`
    - `"PNCProvisioningCertificate"`
    - `"PNCRootCACertificate"`
    - `"PNCGenericCertificate"`

- Example
  - `https://trust-api.gireve.com/pncp/1.0.2/PNCContractCertificate`
  - is a valid URL for
    - `"PNCContractCertificate"` module
    - Version `"1.0.2"`
    - On production environment

## 2.4 Concepts derived from the OCPI protocol

### 2.4.1 Headers "X-Request-ID" and "X-Correlation-ID"

Used for tracing and debugging, the headers *"X-Request-ID"* and *"X-Correlation-ID"* introduced in OCPI 2.2 are used in GIREVE Trust Platform APIs.

*Please see <https://github.com/ocpi/ocpi/blob/2.2.1/transport_and_format.asciidoc#12-unique-message-ids> (from OCPI standard description on Github).*

> **ℹ️**
> NoteThe implementation of the headers *"X-Request-ID"* and *"X-Correlation-ID"* are mandatory.

### 2.4.2 Response format

Responses returned by GIREVE Trust Platform services are compliant with the standard OCPI response format.

*Please see <https://github.com/ocpi/ocpi/blob/2.2.1/transport_and_format.asciidoc#117-response-format> (from OCPI standard description on Github).*

### 2.4.3 Status codes returned

Status codes returned by GIREVE Trust Platform APIs are compliant with the OCPI standard.

*Please see <https://github.com/ocpi/ocpi/blob/2.2.1/transport_and_format.asciidoc#117-response-format> (from OCPI standard description on Github).*

### 2.4.4 Pagination

Some PNCP services, such as GetRootCA, reuse OCPI paging principles by using offset and limit parameters in the request as well as the "link", "X-Total-Count" and "X-limit" headers in the response.

*Please see <https://github.com/ocpi/ocpi/blob/2.2.1/transport_and_format.asciidoc#pagination> (from OCPI standard description on Github).*

## 2.5 Gireve Trust Platform environments

Two main environments are available: "Preproduction environment" and "Production environment".

### 2.5.1 Preproduction environment

This environment enables partners to test, refine and validate their implementation before going live. It can also be used to create proof-of-concept tools or demonstrations.

All Root-CAs, Sub-CAs and leaf certificates managed in the Preproduction environment can only be used for test or demonstration purposes. They cannot be used in a production context.

URL to access Preproduction environment are like <https://pp-trust-api.gireve.com/...>

### 2.5.2 Production environment

This is the environment for service delivery.

URL to access Production environment are like <https://trust-api.gireve.com/...>

## 2.6 Communication partner and operator



### 2.6.1 Communication partner

A Communication Partner is a system actor which is technically connected to the GIREVE's Trust Platform and exchanges messages (requests and responses) with it. A Communication Partner is a client in the client/server communication.

To be authorised to be connected to the GIREVE's Trust platform, a Communication Partner must obtain the necessary security credentials from GIREVE and communicate the IP addresses of its physical machines.

A Communication Partner may host one or many Operators (CPO, eMSP, OEM, CCP, PCP).

**Example:** A CPO system or an eMSP "back-end" system, connected to the GIREVE's Platform, is a Communication Partner.

### 2.6.2 Operator

An operator is the actor in the system who is functionally connected to the GIREVE platform and exchanges messages with it. This is a business role, and the company to which it is linked has a "Platform Access Contract" with GIREVE.

Operators can have one or more communication partners for the management and monitoring of their data flows.

The separation between the two roles of "Operator" and "Communication Partner" is particularly important when a Communication Partner hosts several Operators. This notion also makes it possible to distinguish all the Communication Partners of an Operator.

**Example:** a CPO or an eMSP is an Operator. A CPO system is a Communication Partner of the CPO.

Some actors use the term "Mandant" to designate functional actor using a given system. In this case "Mandant" is a synonym of "Operator". Some actors use the terms of "Sub-Operator" (Sub-CPO, Sub-eMSP, Sub-OEM ...) to designate functional actor using a given system. In this case "Sub-Operator" is a synonym of "Operator".

## 2.7 Security

### 2.7.1 Communication encryption via TLS

Communication between the client system and the Gireve's Trust platform is secured and encrypted on the transport layer using the TLS protocol (TLSv1.3) which is the standard protocol for secure data transmission. TLS (for Transport Layer Security) establishes a secure and encrypted connection between the client and server, safeguarding against eavesdropping and data tampering.

TLS works on the principle of key exchange and encryption. When a client system connects to the server, it initiates a data exchange process during which the server presents its digital certificate. The client verifies the server's certificate, confirming that it is valid and issued by a trusted entity, thus ensuring that it is communicating with the legitimate trusted server. After verification, the client generates a shared secret, which both parties use to obtain encryption keys. All data that is subsequently exchanged between client and server is then encrypted and can only be decrypted using these keys. This guarantees the confidentiality and integrity of the information exchanged.

Gireve Trust platform presents a server certificate linked to the Google's Root Certificate Authority **"GTS Root R1"** which can be downloaded here: <https://pki.goog/repository/>.

The calling partner system needs to recognize this Root Certificate Authority as a trusted entity to allow the TLS communication.

> **Note:** "GTS Root R1" is already included in most recent trust store (like for java in the cacerts file of the JVM's security module).

> **Note 2:** The client authentication is not done by TLS client certificates but with bearer token as described in [Section 2.8](#28-client-authentication).

### 2.7.2 IP Filtering

Calling partners must provide a list of authorised IP addresses before connecting to the platform.

The Gireve Trust platform also uses customer IP filtering as an additional security measure. This ensures that only trusted sources can access the API, reinforcing security and protecting against unauthorized access attempts.

### 2.7.3 Gireve's web application firewall

One of the rules in place to protect against CRLF injections will rejects request with a JSON body not "minified". This means containing white spaces or line breaks between the JSON attributes.

If you make test calls via Postman on the pre-production platform you need to "minify" your JSON payload, otherwise they'll be rejected by the platform.

Gireve's Trust platforms are protected by a powerful web application firewall (WAF). This firewall features anti-DDoS protection and includes numerous and complex protection rules to prevent any intrusion attempts into the system and guarantee a maximum level of security.

### 2.7.4 PKI security

The storage and use of the certificate authority private keys is secured in some Hardware Secure Modules (HSM). Hardware security modules act as trust anchors that protect the cryptographic infrastructure by securely managing, processing, and storing cryptographic keys inside a hardened, tamper-resistant device.

## 2.8 Client authentication

### 2.8.1 Partner authentication via OAUTH2 tokens

Client authentication and identification on our platform is based on OAuth2 tokens, a technology recognized for its reliability in securing access. These tokens are generated by the client from information previously transmitted to the connection by Gireve, during the technical connexion stage, notably the **client_id** and **client_secret**.

Calling partner systems must generate valid OAuth2 tokens with the credentials provided by Gireve to authenticate to the Gireve Trust platform. If the token is missing or invalid, the call will be rejected.

In addition, tokens have a limited validity period (a few hours). **The calling partner must reuse an existing token as long as it remains valid** and only request a new token upon expiration. This technical requirement would be verified during the certification process.

> **Note:** The generated token must be entered in the "authorization" header with the value `"Bearer {token}"` and must be updated when it reaches its validity limit.

> **Note 2:** The IP filtering implemented on our platform (see [Section 2.7.2](#272-ip-filtering)) is also a means of identifying a calling partner.

### 2.8.2 Operator identification via PNCP headers

As explained in [Section 2.6](#26-communication-partner-and-operator), when a partner system calls us, it does so on behalf of an operator who represents the functional actor (eMSP, CPO, OEM, CCP, PCP).

The operator, source of the request, is identified using the **PNCP-from-country-code** and **PNCP-from-party-id** headers, which are populated respectively by the operator's country code and party id, as defined by the eMI3 group standard.

For each call, calling partner systems must fill in the mandatory headers `PNCP-from-country-code` and `PNCP-from-party-id`. Gireve will then check that the Operator has been declared in its system and that the Communication-Partner is attached to this Operator. If either of these conditions is not met, the call will be rejected.

> **Note:** If the Operator doesn't have the "country code" and "party id" information, Gireve can create them for him.

[^1]: The names of these modules have been prefixed by “PNC” (for “Plug’n Charge”) to avoid any confusion in case of OCPI defines an OCPI standard plug & charge related module and/or object.
