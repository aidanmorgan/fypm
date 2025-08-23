# RFC-CAP-003: Hierarchical Identity Attestation and Content Signing Protocol

**CAP:** 003  
**Title:** Hierarchical Identity Attestation and Content Signing Protocol  
**Author:** CAP Working Group  
**Status:** Draft  
**Type:** Standards Track  
**Category:** Core  
**Created:** 2025-08-23  

## Abstract

The Content Authenticity Protocol Identity System (CAP-IDS) establishes a hierarchical, decentralized identity attestation and content signing framework that bridges traditional identity providers with blockchain-based verification. The protocol integrates SAML 2.0 and W3C Decentralized Identifiers (DIDs) to enable content producers to prove their identity through cryptographic signatures linked to their content. Using a DNS-like hierarchical resolution system, CAP-IDS distributes identity verification load across multiple tiers while maintaining cryptographic trust chains. Content authenticity is proven through HTTP headers or HTML meta tags containing signed attestations that link to the author's verified identity. The system supports multiple identity providers through a federated model, enabling authors to maintain control over their identity while participating in the content authenticity ecosystem.

## 1. Introduction

### 1.1 Background

Current content authentication systems face a fundamental challenge: how to verify that content was created by a specific human author without relying on centralized authorities that can be compromised, censored, or become single points of failure. 

### 1.2 Goals

CAP-IDS aims to:
- Bridge traditional identity providers (SAML/OIDC) with decentralized verification
- Enable hierarchical, DNS-like resolution for scalable identity verification
- Provide cryptographic proof of content authorship via standard web protocols
- Support multiple identity attestation methods and providers
- Maintain author sovereignty while enabling institutional verification

### 1.3 Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

## 2. Terminology

**Attestation Authority**: Entity that verifies and signs identity claims  
**Content Signature**: Cryptographic proof linking content to an author's DID  
**DID (Decentralized Identifier)**: W3C standard for self-sovereign identifiers  
**Identity Bridge**: Service that translates between SAML assertions and DIDs  
**Resolution Hierarchy**: DNS-like system for distributing identity lookups  
**Verifiable Credential**: Cryptographically signed claim about an identity  
**Zone Authority**: Entity managing a namespace in the resolution hierarchy  

## 3. System Architecture

### 3.1 Hierarchical Resolution Structure

The CAP identity system mirrors DNS architecture for scalability:

```
                    [Root Zone]
                    cap://identity
                         |
            +------------+------------+
            |            |            |
      [Institution]  [Government]  [Independent]
       .edu.cap      .gov.cap       .self.cap
            |            |              |
      [University]   [Country]     [Personal]
       blah.cap      genovia.gov.cap alice.self.cap
            |            |              |
        [Author]     [Author]       [Author]
      alice@blah     bob@genovia    alice@self
```

### 3.2 Component Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Content Creator                    │
└────────────────────┬─────────────────────────────────┘
                     │ 1. Authenticate
                     ▼
┌─────────────────────────────────────────────────────┐
│              Identity Provider (IdP)                 │
│         (SAML/OIDC                        )          │
└────────────────────┬─────────────────────────────────┘
                     │ 2. SAML Assertion
                     ▼
┌─────────────────────────────────────────────────────┐
│              CAP Identity Bridge                     │
│         (Translates SAML → DID + VC)                │
└────────────────────┬─────────────────────────────────┘
                     │ 3. Issue DID
                     ▼
┌─────────────────────────────────────────────────────┐
│              Algorand Blockchain                     │
│         (Stores DID Documents + VCs)                 │
└────────────────────┬─────────────────────────────────┘
                     │ 4. Sign Content
                     ▼
┌─────────────────────────────────────────────────────┐
│              Content with Signatures                 │
│      (HTTP Headers / HTML Meta Tags)                 │
└──────────────────────────────────────────────────────┘
```

## 4. Identity Provider Integration

### 4.1 SAML 2.0 Bridge

The system accepts SAML assertions and converts them to verifiable credentials:

```xml
<saml:Assertion xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"
                ID="_8e8dc5f69a98cc4c1ff3427e5ce34606fd672f91e6"
                Version="2.0"
                IssueInstant="2025-08-23T09:22:05Z">
    <saml:Issuer>https://idp.example.edu/</saml:Issuer>
    <ds:Signature>...</ds:Signature>
    <saml:Subject>
        <saml:NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">
            alice@example.edu
        </saml:NameID>
    </saml:Subject>
    <saml:AttributeStatement>
        <saml:Attribute Name="urn:oid:2.5.4.42">
            <saml:AttributeValue>Alice Smith</saml:AttributeValue>
        </saml:Attribute>
        <saml:Attribute Name="cap:attestation:human">
            <saml:AttributeValue>verified</saml:AttributeValue>
        </saml:Attribute>
    </saml:AttributeStatement>
</saml:Assertion>
```

### 4.2 DID Document Structure

Converted SAML assertions become DID documents (TBD)

### 4.3 Verifiable Credential Format

Identity attestations are stored as W3C Verifiable Credentials (TBD)


## 5. Hierarchical Resolution Protocol

### 5.1 Resolution Hierarchy

The system implements DNS-like hierarchical resolution with caching... TBD

### 5.2 Zone Authority Structure

Each zone maintains authority records similar to DNS... TBD


### 5.3 Multiple-author distribution

The author of the content may provide a requirement for how the payment should be distributed, either between multiple authors, or potentially to other organisations (e.g. charities)

## 6. Content Signing Protocol

### 6.1 Signature Generation

Content is signed using the author's DID-associated private key and recorded on the blockchain.

### 6.2 HTTP Header Implementation

Content authenticity via HTTP headers:

```http
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
CAP-Author-DID: did:cap:algo:genovia.gov.cap
CAP-Content-Hash: sha3-256:<<>>
CAP-Signature: <<>>
CAP-Timestamp: 2025-08-23T19:23:24Z
CAP-Attestation-Level: government
```

### 6.3 HTML Meta Tag Implementation

Alternative implementation via HTML meta tags:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Authenticated Content</title>
    
    <!-- CAP Identity Attestation -->
    <meta name="cap:author-did" 
          content="did:cap:algo:alice.genovia.gov.cap">
    
    <meta name="cap:content-hash" 
          content="sha3-256:...">
    
    <meta name="cap:signature" 
          content="...">
    
    <meta name="cap:timestamp" 
          content="2025-08-23T19:23:24Z">
    
    <meta name="cap:attestation-level" 
          content="government">
    
   
    <script type="application/ld+json">
    {
        "@context": "https://cap.protocol/ld/v1",
        "@type": "AuthenticatedContent",
        "author": {
            "@type": "Person",
            "did": "did:cap:algo:alice.genovia.gov.cap",
            "name": "Alice Smith",
            "affiliation": "Genovia"
        },
        "contentHash": "sha3-256:..",
        "signature": "...",
        "verificationInstructions": "https://cap.protocol/verify"
    }
    </script>
</head>
<body>
    <!-- Content body -->
</body>
</html>
```

## 7. Smart Contract Implementation

### 7.1 Algorand DID Registry

TBD

### 7.2 Zone Authority Contract

TBD

### 7.3 Verification Contract

TBD

## 8. Security Considerations

### 8.1 Identity Provider Trust

The system's security depends on the trustworthiness of identity providers:

- **IdP Compromise**: If an identity provider is compromised, false attestations could be issued
- **Mitigation**: Multi-factor verification, reputation scoring, time-delayed attestations
- **Recovery**: Revocation mechanisms and reputation penalties for compromised attestations

The system should allow an author to be associated with more than one IdP.

### 8.2 Key Management

Private key security is critical for content authenticity, authors should never reveal their private keys, but should be able to provide cryptographic proof to link their identity with an IdP. The system should be designed to prevent an individual IdP from being able to own the authors identity, it is critical that the author maintains their identity, but may delegate to 1 (or more) IdP's to validate on their behalf.


### 8.3 Privacy Considerations

- **Selective Disclosure**: Authors can choose which attributes to reveal
- **Correlation Resistance**: Different DIDs for different contexts


