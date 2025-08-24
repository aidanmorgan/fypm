# RFC-HCCP-001: Human Content Compensation Protocol Identity System (HCCP-IDS)

**HCCP:** 001  
**Title:** Human Content Compensation Protocol Identity System (HCCP-IDS)
**Status:** Draft  
**Created:** 2025-08-23  

## Abstract

The Human Content Compensation Protocol Identity System (HCCP-IDS) establishes a decentralised identity attestation and content signing framework. Content authenticity is proven by signed attestations that link to the author's verified identity. The system supports a federated identity model, enabling authors to maintain control over their identity whilst participating in the content authenticity ecosystem.

By linking a persistent identity to their work, creators can assert their ownership over content by signing it using their established identity. This signature is the foundational layer upon which all other aspects of the protocol—reputation, detection, and payment—are built.

## 1. Introduction

### 1.1 Goals

The goals of the Human Content Compensation Protocol Identity System (HCCP-IDS) are to:
*   Establish a framework for portable digital identity for content creators.
*   Provide a verifiable, cryptographic link between an author's identity and their specific works.
*   Allow for pseudonymous authorship, enabling creators to establish a persistent, reputable identity without disclosing their real-world information.

### 1.3 Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

## 2. Requirements

Identity Creation and Management
* ID_001: The HCCP system shall provide a mechanism for an author to create and control a W3C Decentralised Identifier (DID).
* ID_002: The HCCP system shall allow an author to link their DID to one or more external Identity Providers (IdPs) for verification.
* ID_003: The HCCP system shall store DID documents and Verifiable Credentials on the Algorand blockchain.
* ID_004: Whilst an author is managing their profile, the HCCP system shall allow them to selectively disclose which identity attributes are public.

Content Signing and Verification
* ID_005: The HCCP system shall enable an author to cryptographically sign their content using the private key associated with their DID.
* ID_006: The HCCP system shall provide a mechanism to embed the author's DID and content signature into a webpage via either HTTP headers or HTML meta tags.
* ID_007: When verifying content, the HCCP system shall check the embedded signature against the public key stored in the author's DID document.

Payment and Hierarchy
* ID_008: The HCCP system shall use a hierarchical namespace to manage and resolve DIDs.
* ID_009: The HCCP system shall allow an author to define rules for distributing micropayments for their content to multiple DIDs.

Security
* ID_010: If an Identity Provider is known to be compromised, then the HCCP system shall provide a mechanism to revoke or flag attestations from that provider.


## 3. Progressive Identity Bootstrap

The transition from the current identity norms to a fully decentralised identity system will be a blocker to the adoption of the HCCP unless we take an intelligent, staged approach to establishing identity. Rather than waiting for perfect DID infrastructure or universal adoption of Content Authenticity standards, HCCP will implement a progressive bootstrapping strategy that meets creators where they whilst building toward the decentralised future. This approach also recognises that different creators will have different capabilities and comfort levels with cryptographic systems.

### 3.1 The Web of Trust Foundation

At the heart of the bootstrap system lies a web of trust model managed on-chain by the HCCP Identity Provider Bridge. This bridge acts as a translator between the familiar identity systems creators already use and the cryptographic infrastructure required for the protocol. Rather than forcing creators to immediately adopt complex key management systems, the bridge allows them to start with something as simple as proving they control a domain or social media account, then progressively strengthen their identity over time.

The web of trust operates on the principle that multiple weak proofs can combine to create strong confidence. A creator who has verified their domain ownership, proven control of an email address, and received attestations from other verified creators presents a far more trustworthy identity than any single proof could provide. This accumulated trust is recorded on-chain, creating an immutable history of verification events that builds reputation over time.

### 3.2 Domain-Based Identity

For creators who own domains, the bootstrap process begins with DNS TXT records. This approach leverages the existing domain name system as a root of trust, recognising that domain ownership already represents a significant investment and commitment to an online presence. A creator adds a specially formatted TXT record to their domain:

```
_hccp.example.com TXT "v=HCCP1; pk=ed25519:[public_key]; wallet=ALGO:[address]; sig=[signature]; ts=[timestamp]"
```

This does several things simultaneously. It proves current control of the domain, establishes a cryptographic public key for content signing, and links an Algorand wallet for receiving payments. The timestamp and signature prevent replay attacks and prove the declaration was made at a specific time. The HCCP Identity Bridge monitors these DNS records, and when verified, creates an on-chain attestation linking the domain to the cryptographic identity.

For sites that publish content, the bridge also verifies that content signed with the declared key actually appears on the domain. This creates a bidirectional proof: the domain claims the key, and content signed with the key appears on the domain. This makes it extremely difficult for an attacker to falsely claim ownership of another's content, as they would need to compromise both the DNS records and the web server.

### 3.3 Social Proof Aggregation

Not all creators own domains, but nearly all have some form of social media presence. The bootstrap system embraces this reality by allowing creators to build identity through social proof aggregation. This process, inspired by services like Keybase but implemented in a decentralised manner, allows creators to post cryptographically signed proofs across multiple platforms.

On Bluesky, a creator might post: "Verifying my HCCP identity: [signature] #HCCP_Identity". On GitHub, they create a gist with their public key and wallet address. On LinkedIn, they add HCCP verification to their profile. Each platform has its own verification method, adapted to what that platform allows. The HCCP Identity Bridge crawls these proofs, verifies the signatures, and records successful verifications on-chain.

This approach leverages not any single proof but an aggregate. An attacker might compromise one social media account, but compromising multiple accounts across different platforms becomes exponentially more difficult. Furthermore, because these proofs are public and timestamped on-chain, they create a verifiable history. A creator who has maintained consistent proofs across multiple platforms for months or years presents a much stronger identity than one newly created.

### 3.4 OAuth Integration

The largest barrier to adoption for any cryptographic system is key management. Most users are not prepared to safely manage private keys, and forcing them to do so immediately would severely limit adoption. The OAuth integration provides a bridge between the familiar world of "Sign in with Google" and the cryptographic requirements of HCCP.

When a creator signs up through OAuth, the HCCP Identity Bridge generates a key pair on their behalf, encrypting the private key with a key derived from their OAuth tokens. This means the creator can access their HCCP identity simply by signing in with their provider, while still maintaining the cryptographic properties needed for the protocol. The bridge acts as a temporary custodian, but creators can export their keys at any time, allowing them to transition to self-custody when ready.

This custodial approach is intentionally temporary and progressive. As creators become more comfortable with the system and their content generates revenue, they're incentivised to take control of their keys. The bridge provides educational resources and progressive security features, such as adding hardware key backup or transitioning to a multi-signature arrangement. The on-chain record shows this progression, with self-custody creators earning higher reputation scores than those still using custodial services.

### 3.5 Trust Score

All these verification methods feed into a unified trust score calculated on-chain. The score isn't simply additive; it uses a sophisticated algorithm that considers:

- **Verification Diversity**: Multiple different types of verification score higher than multiple verifications of the same type
- **Temporal Consistency**: Verifications maintained over time score higher than newly created ones
- **Cross-Attestation**: Verifications that reference each other (e.g., a domain that links to a Twitter account that links back) score higher
- **Community Attestation**: Endorsements from other verified creators, weighted by their own trust scores
- **Behavioural Factors**: Consistent content signing, lack of challenges, and positive AI agent feedback all contribute

This trust score becomes the foundation for the reputation system described in RFC-HCCP-002. Creators start building reputation from their first verification, even before publishing any content, creating an incentive for early adoption.

### 3.6 Migration Path to Full Decentralisation

The bootstrap system is explicitly conceptualised as a bridge. As W3C DID standards mature and gain adoption, the HCCP Identity Bridge will progressively migrate bootstrap identities to fully decentralised identifiers. This migration is designed to be seamless and preserve all accumulated reputation and verification history.

The migration process allows creators to maintain their bootstrap identity even after creating a DID, with the bootstrap proofs serving as additional attestations to the DID. This creates a graceful transition period where both systems coexist, and early adopters aren't penalised for starting with simpler verification methods.


## Future Optionality: Proof of Personhood Integration

Whilst the base protocol allows for identity verification through existing providers, a significant future evolution would be the direct integration of a Proof of Personhood (PoP) system. This would allow an author to prove, cryptographically, that their identity corresponds to a single, unique human being. Integrating a PoP mechanism (in whatever form that takes in the future) would provide the strongest possible guarantee against Sybil attacks and sophisticated bot networks. This would add a new, higher tier of trust to the system, enabling AI agents to specifically seek out content with an incontrovertible, human-proven origin.

