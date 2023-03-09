---
v: 3

title: Direct Anonymous Attestation for the Remote Attestation Procedures Architecture
abbrev: DAA for RATS
docname: draft-ietf-rats-daa-latest
wg: RATS Working Group
area: Security
stream: IETF
kw: Internet-Draft
cat: info

# colour neighbour randomise
# [OXFORD_SPELLING_Z_NOT_S] Would you like to use the Oxford spelling “randomize”? The spelling ‘randomise’ is also correct. -> (randomize)

venue:
  group: Remote ATtestation ProcedureS (rats)
  mail: rats@ietf.org
  github: ietf-rats-wg/draft-ietf-rats-daa


author:
- name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany
- name: Christopher Newton
  org: University of Surrey
  email: cn0016@surrey.ac.uk
- name: Liqun Chen
  org: University of Surrey
  email: liqun.chen@surrey.ac.uk
- name: Dave Thaler
  org: Microsoft
  email: dthaler@microsoft.com
  country: USA

normative:
  RFC5280:
  DAA: DOI.10.1145/1030083.1030103

informative:
  I-D.ietf-rats-architecture: RATS
  I-D.ietf-rats-reference-interaction-models: models
  PBA: DOI.10.1007/978-3-540-85886-7_3

--- abstract

This document maps the concept of Direct Anonymous Attestation (DAA) to the Remote Attestation Procedures (RATS) Architecture.
The protocol entity DAA Issuer is introduced and its mapping with existing RATS roles in DAA protocol steps is specified.

--- middle

# Introduction

Remote ATtestation procedureS (RATS, {{-RATS}}) describe interactions between well-defined architectural constituents in support of Relying Parties that require an understanding about the trustworthiness of a remote peer.
The identity of an Attester and its corresponding Attesting Environments play a vital role in RATS.
A common way to refer to such an identity is the Authentication Secret ID as defined in the Reference Interaction Models for RATS {{-models}}.
The fact that every Attesting Environment can be uniquely identified in the context of the RATS architecture is not suitable for every application of remote attestation.
Additional issues may arise when Personally identifiable information (PII) -- whether obfuscated or in clear text -- are included in attestation Evidence or even corresponding Attestation Results.
This document illustrates how Direct Anonymous Attestation (DAA) can mitigate the issue of uniquely (re-)identifiable Attesting Environments.
To accomplish that goal, the protocol entity DAA Issuer as described in {{DAA}} is introduced and its duties as well as its mappings with other RATS roles are specified.

# Terminology

This document uses the following set of terms, roles, and concepts as defined in {{-RATS}}:
Attester, Verifier, Relying Party, Endorser, Conceptual Message, Evidence, Attestation Result, Attesting Environment.

Additionally, this document uses and adapts, as necessary, the following concepts and information elements as defined in {{-models}}:
Attester Identity, Authentication Secret, Authentication Secret ID

A PKIX Certificate is an X.509v3 format certificate as specified by {{RFC5280}}.

{::boilerplate bcp14-tagged}

# Direct Anonymous Attestation

Two protocols as described in {{DAA}} are illustrated: the Join Protocol and the DAA-Signing Protocol. This section specifies the mapping of the protocol entity DAA Issuer described in {{DAA}} as an actor in the Join Protocol as well as an actor in the corresponding DAA-Signing Protocol to roles specified in the RATS Architecture.

In the Join Protocol, the protocol entity DAA Issuer takes on the RATS roles of Verifier and associated Relying Party. The mapping is illustrated in Figure {{join-mapping}}.

~~~~ aasvg
    .--------.     .---------.       .--------.       .-------------.
   | Endorser |   | Reference |     | Verifier |     | Relying Party |
    '-+------'    | Value     |     | Owner    |     | Owner         |
      |           | Provider  |      '---+----'       '----+--------'
      |            '-----+---'           |                 |
      |                  |               |                 |
      | Endorsements     | Reference     | Appraisal       | Appraisal
      |                  | Values        | Policy for      | Policy for
      |                  |               | Evidence        | Attestation
       '-----------.     |               |                 | Results
                    |    |               |                 |
               .----|----|---------------|-----------------|------.
               |    |    |               |                 |      |
               |    v    v               v                 |      |
               |  .-------------------------.              |      |
         .------->|         Verifier        +-----.        |      |
        |      |  '-------------------------'      |       |      |
        |      |                                   |       |      |
        |  Evidence                    Attestation |       |      |
        |      |                       Results     |       |      |
        |      |                                   |       |      |
        |      |                                   v       v      |
  .-----+----. |                               .---------------.  |
  | Attester | |                               | Relying Party |  |
  '----------' |    DAA Issuer                 '---------------'  |
               '--------------------------------------------------'
~~~~
{: #join-mapping title="RATS Architecture for the Join Protocol"}

The Join Protocol is essentially an enrollment protocol that consumes Evidence from the Attester (therefore the mapping to the Verifier role). Appraisal Policies for Evidence specifically dedicated to that appraisal procedure are used to decide whether to issue a DAA credential to an Attester or not.

In the DAA-Signing Protocol, the RATS role Endorser is then taken on by the DAA Issuer protocol entity. The mapping is illustrated in Figure {{sign-mapping}}.

~~~~aasvg
.---------------.
| DAA Issuer    |
|   .--------.  |  .---------.       .--------.       .-------------.
|  | Endorser | | | Reference |     | Verifier |     | Relying Party |
|   '-+------'  | | Value     |     | Owner    |     | Owner         |
|     |         | | Provider  |      '---+----'       '----+--------'
'-----|---------'  '-----+---'           |                 |
      |                  |               |                 |
      | Endorsements     | Reference     | Appraisal       | Appraisal
      |                  | Values        | Policy for      | Policy for
      |                  |               | Evidence        | Attestation
       '-----------.     |               |                 | Results
                    |    |               |                 |
                    v    v               v                 |
                  .-------------------------.              |
          .------>|         Verifier        +-----.        |
         |        '-------------------------'      |       |
         |                                         |       |
         | Evidence                    Attestation |       |
         |                             Results     |       |
         |                                         |       |
         |                                         v       v
   .-----+----.                                .---------------.
   | Attester |                                | Relying Party |
   '----------'                                '---------------'
~~~~
{: #sign-mapping title="RATS Architecture for the DAA-Signing Protocol"}

The DAA Issuer acts as the Endorser for the Group Public Key that is used by the Verifier for the appraisal of evidence of anonymized Attesters that use the DAA credentials and associated key material to produce Evidence.

In consequence, DAA provides a signature scheme that allows the privacy of users that are associated with an Attester (e.g., its owner) to be maintained.
Essentially, DAA can be seen as a group signature scheme with the feature that given a DAA signature no-one can find out who the signer is, i.e., the anonymity is not revocable.
To be able to sign anonymously, an Attester has to obtain a credential from a DAA Issuer.
The DAA Issuer uses a private/public key pair to generate credentials for a group of Attesters <!-- this could be phrased a bit confusing as below it is stated that the key-pair is used for a group of Attesters --> and makes the public key (in the form of a public key certificate) available to the Verifier in order to enable it to validate the Evidence received.

In order to support these DAA signatures, the DAA Issuer MUST associate a single key pair with a group of Attesters <!-- is it clear enough what exactly "a group of Attesters" means? --> and use the same key pair when creating the credentials for all of the Attesters in this group.
The DAA Issuer’s group public key certificate replaces the individual Attester Identity documents during authenticity validation as a part of the appraisal of Evidence conducted by a Verifier.
This is in contrast to intuition that there has to be a unique Attester Identity per device.

For DAA, the role of the Endorser is essentially the same, but it now provides Attester endorsements to the DAA Issuer rather than directly to the Verifier. These Attester endorsements enable the Attester to obtain a credential from the DAA Issuer.

# DAA changes to the RATS Architecture

In order to enable the use of DAA, a new conceptual message, the Credential Request, is defined and a new role, the DAA Issuer role, is added to the roles defined in the RATS Architecture.

Credential Request:

: An Attester sends a Credential Request to the DAA Issuer to obtain a credential. This request contains information about the DAA key that the Attester will use to create evidence and, together with Attester endorsement information that is provided by the Endorser, to confirm that the request came from a valid Attester.

DAA Issuer:

: A RATS role that offers zero-knowledge proofs based on public-key certificates used for a group of Attesters (Group Public Keys) {{DAA}}. How this group of Attesters is defined is not specified here, but the group must be large enough for the necessary anonymity to be assured.

Effectively, these certificates share the semantics of Endorsements, with the following exceptions:

* Upon receiving a Credential Request from an Attester, the associated group private key is used by the DAA Issuer to provide the Attester with a credential that it can use to convince the Verifier that its Evidence is valid.
To keep their anonymity, the Attester randomizes this credential each time that it is used. Although the DAA Issuer knows the Attester Identity and can associate this with the credential issued, randomization ensures that the Attester's identity cannot be revealed to anyone, including the DAA Issuer.
* The Verifier can use the DAA Issuer's group public key certificate, together with the randomized credential from the Attester, to confirm that the Evidence comes from a valid Attester without revealing the Attester's identity.
* A credential is conveyed from a DAA Issuer to an Attester in combination with the conveyance of the group public key certificate from DAA Issuer to Verifier.

# Additions to Remote Attestation principles

In order to ensure an appropriate conveyance of Evidence via interaction models in general, the following prerequisite considering Attester Identity MUST be in place to support the implementation of interaction models.
<!-- This is a weird MUST: It is not clear who MUST do what here. -->

Attestation Evidence Authenticity:

: Attestation Evidence MUST be correct and authentic.

: In order to provide proofs of authenticity, Attestation Evidence SHOULD be cryptographically associated with an identity document that is a randomized DAA credential.

The following information elements define extensions for corresponding information elements defined in {{-models}}, which are vital to all types of reference interaction models.
Varying from solution to solution, generic information elements can be either included in the scope of protocol messages (instantiating Conceptual Messages defined by the RATS architecture) or can be included in additional protocol parameters of protocols that facilitate the conveyance of RATS Conceptual Messages.
Ultimately, the following information elements are required by any kind of scalable remote attestation procedure using DAA with one of RATS's reference interaction models.

Attester Identity ('attesterIdentity'):

: *mandatory*

: In DAA, the Attester's identity is not revealed to the Verifier. The Attester is issued with a credential by the DAA Issuer that is randomized and then used to anonymously confirm the validity of their evidence.
The evidence is verified using the DAA Issuer’s group public key.

Authentication Secret IDs ('authSecID'):

: *mandatory*

: In DAA, Authentication Secret IDs are represented by the DAA Issuer’s group public key that MUST be used to create DAA credentials for the corresponding Authentication Secrets used to protect Evidence.

: In DAA, an Authentication Secret ID does not identify a unique Attesting Environment but is associated with a group of Attesting Environments.
This is because an Attesting Environment should not be distinguishable and the DAA credential which represents the Attesting Environment is randomized each time it used.

# Privacy Considerations

As outlined above, for DAA to provide privacy for the Attester, the DAA group must be large enough to stop the Verifier identifying the Attester.

Randomisation of the DAA credential by the Attester means that collusion between the DAA Issuer and Verifier, will not give them any advantage when trying to identify the Attester.

For DAA, the Attestation Evidence conveyed to the Verifier MUST not uniquely identify the Attester. If the Attestation Evidence is unique to an Attester other cryptographic techniques can be used, for example, property based attestation [PBA].

# Security Considerations

The anonymity property of DAA makes revocation difficult. Well known solutions include:

1. Rogue Attester revocation -- if an Attester's private key is compromised and known by the Verifier then any DAA signature from that Attester can be revoked.
2. EPID - Intel's Enhanced Privacy ID -- this requires the Attester to prove (as part of their Attestation) that their credential was not used to generate any signature in a signature revocation list.

There are no other special security considerations for DAA over and above those specified in the RATS architecture document {{-RATS}}.

# Implementation Considerations

The new DAA Issuer role can be implemented in a number of ways, for example:

1. As a stand-alone service like a Certificate Authority, a Privacy CA.
2. As a part of the Attester's manufacture. The Endorser and the DAA Issuer could be the same entity and the manufacturer would then provide a certificate for the group public key to the Verifier.


# IANA Considerations

We don't yet.

--- back
