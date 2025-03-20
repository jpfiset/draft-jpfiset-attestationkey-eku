---
title: "Extended Key Usage (EKU) for X.509 Certificates associated with Attestation Keys"
abbrev: "EKU for Attestation Keys"
category: info

docname: draft-jpfiset-lamps-attestationkey-eku-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: ""
workgroup: "Limited Additional Mechanisms for PKIX and SMIME"
keyword:
 - Internet-Draft
 - RATS
 - Extended Key Usage
venue:
  group: "Limited Additional Mechanisms for PKIX and SMIME"
  type: ""
  mail: "spasm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: "jpfiset/draft-jpfiset-attestationkey-eku"

author:
  - name: Jean-Pierre Fiset
    org: Crypto4A Inc.
    abbrev: Crypto4A
    street: 1550A Laperriere Ave
    city: Ottawa, Ontario
    country: Canada
    code: K1Z 7T2
    email: jp@crypto4a.com

  - name: Mike Ounsworth
    org: Entrust Limited
    abbrev: Entrust
    street: 2500 Solandt Road - Suite 100
    city: Ottawa, Ontario
    country: Canada
    code: K2K 3G5
    email: mike.ounsworth@entrust.com

  - name: Hannes Tschofenig
    organization: University of Applied Sciences Bonn-Rhein-Sieg
    abbrev: H-BRS
    country: Germany
    email: Hannes.Tschofenig@gmx.net

  - name: Monty Wiseman
    org: Beyond Identity
    country: USA
    email: monty.wiseman@beyondidentity.com

normative:

informative:


--- abstract

As specified in {{!RFC5280}}, key usages are specified in X.509 certificates using the
certificate extensions "Key Usage" and "Extended Key Usage". This document defines
an Extended Key Usage (EKU) relating to keys that are reserved for the purpose of
signing attestation evidence as introduced in {{!RFC9334}}.



--- middle

# Introduction

Attesters, as defined in Remote Attestation Procedures
(RATS) in {{!RFC9334}}, can use cryptographic private keys to identify the origin of
the evidence and protect its integrity. Those private keys are referred to as
Attestation Keys.

Attestation Keys can be endorsed by a Certification Authority (CA) by issuing
X.509 certificates (see {{!RFC5280}}). Those certificates SHOULD include an extended
key usage to indicate that the associated key is dedicated to the purpose of attesting
evidence. This allows recipients of signed evidence to trust that the associated key is
controlled according to the constraints specified in this document.

# Extended Key Usage for Attestation Key

This specification defines the KeyPurposeId id-kp-attestationKey. This KeyPurposeId
is reserved for attestation keys.

The term "signing attestation evidence" refers to performing a digital signature
using an attestation key over content that includes claims about the target
environment (see {{!RFC9334}}).

An attestation key must be associated with the "digital signing" key usage, as any
other keys used to performed digital signature. Furthermore, an attestation key must
adhere to the following constraints:

* An attestation key SHOULD be used only by an attester to digitally sign claims that
the attester can be observed in the target environment. The attester SHOULD not use the
attestation key for any other purpose (dedication).

* An attestation key MUST not be controlled by any entity other than the associated
attester. This constraint is to ensure that other entity can not impersonate the
attester (non-repudiation).

## Including the EKU for Attestation Key in Certificates

When the EKU id-kp-attestationKey is included in a X.509, other considerations should
be taken:

* The X.509 extension "key usage" MUST be set to "digital signature". In other words,
the value of the associated field includes the bit "digitalSignature" set.

* The X.509 extension "extended key usage" SHOULD not include usage other than the
one defined in this document (id-kp-attestationKey).

## Implication for a Certificate Authority

When a Certificate Authority issues a X.509 certificate that includes the extended key
usage defined in this document, certain additional considerations MUST be taken to ensure
that the constraints defined in this document are respected.

Issuing a X.509 certificate with the extended key usage id-kp-attestationKey
equates to providing an endorsement of the attester as defined in the RATS architecture.
Therefore, the procedures and practices employed by a Certificate Authority MUST be
adjusted to take into account RATS architecture.

In particular, it is not sufficient for a CA to verify that the subject of the certificate
has possession of the subject key. It MUST also ensure that the subject is the only
entity that controls the key. This can be accomplished (but not restricted to) by using
a key confined to specialized hardware under the control of the subject.

## Implication for the RATS Verifier

In {{!RFC9334}}, the Verifier is the role that consumes the evidence produced by an
attester. As part of the verification process, the Verifier assesses endorsements, among
other things. A X.509 certificate containing the EKU id-kp-attestationKey is an
endorsement of the attester by the issuing authorities.

While assessing an endorsement provided this way, a Verifier must follow all practices
to validate the veracity of the certificate.

## Implication for Cryptographic Modules

Attestation keys are instantiated and operated on by cryptographic modules. These modules
MUST provide the services required to restrict the use of an attestation key to its
associated Attester.

The mechanisms used to perform those restrictions are out of scope for this document.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

For attestation evidence to be valuable, coordination between the various roles is required:

* The cryptographic module must restrict the use of the attestation key to the Attester.

* The CA MUST ensure that the Attester and the attestation key respect the constraints outlined
in this document.

* A Verifier must perform the assessment of the presented evidence using all the procedures
required to ascertain as to the origin and validity of the attester.

The risks associated with a failure of this coordination is that the claims reported by
the Attester can no be trusted.




# IANA Considerations

IANA needs to assign an object identifier (OID) for this purpose.


--- back

# Appendix A. ASN.1 Module
{:numbered="false"}

The following ASN.1 module provides the complete definition of the
Attestation Key KeyPurposeId.

~~~

  DEFINITIONS EXPLICIT TAGS ::=

  BEGIN

  -- EXPORTS ALL --

  -- IMPORTS NOTHING --

  -- OID Arc --

  id-kp  OBJECT IDENTIFIER  ::= {
    iso(1) identified-organization(3) dod(6) internet(1)
    security(5) mechanisms(5) pkix(7) kp(3) }

  -- Attestation Key Extended Key Usage --

  id-kp-attestationKey OBJECT IDENTIFIER ::= { id-kp XX }

  END
~~~

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
