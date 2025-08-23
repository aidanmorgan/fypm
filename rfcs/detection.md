# RFC-CAP-004: Agentic AI Detection Protocol

**CAP:** 004  
**Title:** Agentic AI Detection Protocol  
**Author:** CAP Working Group  
**Status:** Draft  
**Type:** Standards Track  
**Category:** Core  
**Created:** 2025-08-23  

## Abstract

The Content Authenticity Protocol AI Agent Detection System (CAP-AID) establishes a framework for identifying agentic AI accessing CAP-protected content and implementing micropayment requirements through HTTP 402 Payment Required responses. The protocol employs multi-layered detection mechanisms including user-agent analysis, browser fingerprinting, behavioral pattern recognition, and TLS/HTTP2 fingerprinting to identify automated agents. Upon detection, the system presents a requirement for a micropayment in ALGO cryptocurrency. This creates an economic barrier for AI agents while maintaining accessibility for human users and legitimate crawlers. The system integrates with CAP's payment infrastructure to ensure content creators are compensated when their verified human-created content is consumed by AI systems for training or other purposes.

## 1. Introduction

### 1.1 Background

The rapid proliferation of AI agents that can browse the web, extract content, and learn from human-created materials has created an economic imbalance. Content creators invest significant effort in producing high-quality, verified human content, yet AI systems can freely consume this content for training without compensation.

As AI agents become more sophisticated—using headless browsers, realistic fingerprints, and human-like interaction patterns—distinguishing them from legitimate human users becomes increasingly difficult. The HTTP 402 status code, reserved since the early days of the web for micropayments, provides an ideal framework for implementing such a system.

### 1.3 Goals

CAP-AIDS aims to:
- Accurately detect agentic AI accessing protected content
- Implement fair micropayment requirements for AI content consumption
- Ensure legitimate human users and search engines remain unaffected
- Create sustainable economics for human content creators

### 1.4 Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

## 2. Terminology

**Agentic AI**: Autonomous AI system capable of browsing and extracting web content  
**Browser Fingerprint**: Unique characteristics identifying a browser instance  
**Payment Token**: Cryptographic proof of micropayment completion  
**Challenge Nonce**: Unique value preventing challenge reuse  
**Detection Score**: Composite metric indicating AI agent probability  
**TLS Fingerprint**: Cryptographic handshake characteristics  

## 3. System Architecture

### 3.1 Detection and Response Flow

```
┌─────────────────────────────────────────────────────┐
│                  Client Request                      │
└────────────────────┬─────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│              Detection Layer                         │
│  (User-Agent, Fingerprint, Behavior, TLS)           │
└────────────────────┬─────────────────────────────────┘
                     │
                     ▼
              ┌──────────────┐
              │ Score > 0.7? │
              └──────┬───────┘
                     │
         ┌───────────┴───────────┐
         │ No                    │ Yes
         ▼                       ▼
┌─────────────────┐   ┌─────────────────────────┐
│  Serve Content  │   │  HTTP 402 Response      │
└─────────────────┘   │  + Challenge/Payment    │
                      └────────┬─────────────────┘
                               │
                               │
                      ┌────────▼────────┐
                      │ Submit Payment  │
                      └────────┬────────┘
                               ▼
                      ┌─────────────────┐
                      │  Verify & Serve │
                      └─────────────────┘
```

### 3.2 Component Architecture

TBD

## 4. AI Agent Detection Mechanisms

### 4.1 User-Agent Analysis

Detect known AI agent patterns and inconsistencies, based on known AI agent identifiers and detection of headless browser user agents.

### 4.2 Browser Fingerprinting

Analyze JavaScript capabilities and rendering characteristics to provide browser fingerprinting scoring.


### 4.3 TLS and HTTP/2 Fingerprinting

Analyze protocol-level characteristics, based on TLS fields comparing with known support based on user-agents.


## 5. HTTP 402 Payment Required Implementation

### 5.1 Response Structure

When an AI agent is detected, return HTTP 402 with challenge:

```http
HTTP/1.1 402 Payment Required
Content-Type: application/json
CAP-Payment-Amount: 0.1
CAP-Payment-Currency: ALGO
CAP-Payment-Address: 7ZUECA7HFLZTXENRV24SHLU4AVPUTMTTDUFUBNBD64C73F3UHRTHAIOF6Q
WWW-Authenticate: CAP-Challenge realm="Protected Content"

{
  "error": "payment_required",
  "message": "AI agent detected. Payment or proof-of-work required.",
  "payment": {
    "amount": 0.1,
    "currency": "ALGO",
    "address": "7ZUECA7HFLZTXENRV24SHLU4AVPUTMTTDUFUBNBD64C73F3UHRTHAIOF6Q",
    "memo": "CAP:7d865e959b2466918c9863afca942d0f"
  },
  "verification_endpoint": "https://cap.protocol/api/verify",
  "content_preview": {
    "title": "Protected Human-Created Content",
    "snippet": "This content requires payment for AI access...",
    "author": "did:cap:algo:alice.blah.com",
    "human_verified": true
  }
}
```


## 6. Micropayment Processing

### 6.1 Payment Verification

TBD - need to work out a way to do this that allows access quickly (so no waiting for the chain to close), but is secure....

### 6.2 Payment Distribution

Payments are distributed to authors as per [RFC-CAP-002: Fair Payment Distribution Protocol for Content Retrieval](payment.md)

## 7. Challenge Token Management

### 7.1 Token Generation and Storage

Need to provide token uniqueness, storage and validation that payment has been performed.

### 7.2 Access Token Generation

Create time-limited access tokens for verified agents, agent should be allowed time-limited access to the content after a payment has been made or work has been completed.


## 8. Smart Contract Implementation

### 8.1 AI Payment Contract

TBD


## 9. Security Considerations

### 9.1 Attack Vectors and Mitigations

TBD

### 9.2 Privacy Considerations

- No permanent tracking of legitimate users
- Challenge solutions not linked to identity
- Access tokens contain minimal information

### 9.3 Anti-Gaming Mechanisms

- Prevent authors driving up their own payment needs?


### 10.2 Integration with Existing Systems

* Foundation provides a reverse proxy service
* Wordpress plugin
* Reverse proxy implementation (integrate with existing?)

Will need to determine how to effectively distribute updated rules/patterns for identifying AI agent access using a secure, versioned mechanism.

Non-foundation hosted systems will need a mechanism to enroll securely to be able to issue the challenges as well as receive updates to the models.

## 13. Future Enhancements

### 13.1 Advanced Features

- Machine learning models for detection improvement
- Zero-knowledge proofs for private payments
- Cross-chain payment support


## References

### Normative References

1. RFC 2119: Key words for use in RFCs
2. RFC 7231: HTTP/1.1 Semantics and Content
3. RFC 9110: HTTP Semantics
4. W3C WebAuthn Specification

### Informative References

6. "HotStuff: BFT Consensus with Linearity", PODC 2019
7. "Hashcash - A Denial of Service Counter-Measure", Back, 2002
8. "Browser Fingerprinting: A Survey", Laperdrix et al., 2020
9. JA3 TLS Fingerprinting Methodology

## Appendix A: Challenge Response Flow

```
Client                          Server                      Blockchain
  |                               |                             |
  |-------- GET /content -------->|                             |
  |                               |                             |
  |<----- 402 + Challenge --------|                             |
  |                               |                             |
  |--             Pay ALGO ------>|                             |
  |                               |                             |
  |                               |-------- Verify TX --------->|
  |                               |                             |
  |                               |<------ Confirmed -----------|
  |                               |                             |
  |<------ Access Token ----------|                             |
  |                               |                             |
  |------ GET with Token -------->|                             |
  |                               |                             |
  |<------- Content --------------|                             |
```
