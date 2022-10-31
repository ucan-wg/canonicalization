# UCAN Canonicalization Spec v0.1.0

## Editors

* [Brooklyn Zelenka](https://github.com/expede), [Fission](https://fission.codes)

## Authors
    
* [Hugo Dias](https://github.com/hugomrdias), DAG House
* [Irakli Gozalishvili](https://github.com/Gozala), DAG House
* [Daniel Holmgren](https://github.com/dholms), [Bluesky](https://blueskyweb.org/)
* [Brooklyn Zelenka](https://github.com/expede), [Fission](https://fission.codes)

## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119).

# 0 Abstract

Per the [core UCAN spec](https://github.com/ucan-wg/spec), all implementations MUST support JWT encoding. This provides a common representation that all implementations can understand. JWT canonicalization allows for alternate encodings to convert to and from the standard JWT format, retain the JWT signature scheme, and so on. This enables broad compatibility.

# 1 Motivation

The base UCAN spec is given as a JWT, which is a very open format aimed at broad adoption, and with mature tooling. JWT does not specify aspects like whitespace, key ordering, and capitalization. There is no technical reason that the information contained in a UCAN could not be expressed in a different format, such as [CWT](https://datatracker.ietf.org/doc/html/rfc8392) or [IPLD](https://ipld.io/). By forcing a rigid format, JWT canonicalization provides a deterministic way to convert to and from JWT and other formats, while staying fully compatible with JWT validators.

# 2 Canonicalization

To canonicalize a UCAN, the JSON segments MUST fulfill the following requirements:

1. All `can` fields MUST be lowercase
2. All unused optional fields (such as `fct`) that are empty MUST be omitted
3. [`dag-json`](https://ipld.io/specs/codecs/dag-json/spec/) encoding MUST be used
4. The resulting JWT MUST be [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoded per [RFC 7519]
5. All segments MUST be joined with `.`s, per [RFC 7519](https://www.rfc-editor.org/rfc/rfc7519)

UCAN canonicalization is signalled by CID. If no canonicalization is used, the CID MUST use the [raw multicodec](https://github.com/multiformats/multicodec/blob/master/table.csv#L39). Canonicalized UCANs that wish to signal this encoding MUST use [any other CID codec](https://github.com/multiformats/multicodec/blob/master/table.csv), including but not limited to `dag-json` and `dag-cbor`.

## 2.1 Non-Canonical Validator CID Handling

Validators that have not implemented this specification MUST be provided JWT-encoded UCANs. These validators will be unable to validate the CID in the proofs field. This is not strictly a problem in a semi-trusted scenario, as UCAN only depends on the existence (not the specific CID) of a valid proof for the capabilities being claimed. The security risk is for a malicious peer to provide very long but ultimately invalid proof chains as a denial-of-service vector. This is the case for any validator that does not check the CID hash upon receipt.

# 4 Acknowledgments

Thanks to [Philipp Krüger](https://github.com/matheus23) for his feedback on canonicalization signalling.
