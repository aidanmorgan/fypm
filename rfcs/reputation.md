# RFC-CAP-001: Content Authenticity Protocol

**CAP:** 001  
**Title:** Content Authenticity Protocol via Byzantine Consensus  
**Author:** CAP Working Group  
**Status:** Draft  
**Type:** Standards Track  
**Category:** Core  
**Created:** 2025-08-23  

## Abstract

The Content Authenticity Protocol (CAP) establishes a blockchain-based reputation system for verifying human-created content versus AI-generated content through Byzantine fault-tolerant consensus mechanisms. Authors attest their content creation without Large Language Models (LLMs), while autonomous systems may challenge authenticity by staking ALGO cryptocurrency. The protocol employs HotStuff Byzantine consensus for voting, an ELO-based reputation scoring system with 18-month decay periods, and economic incentives aligned through progressive staking requirements. Failed challenges result in staked ALGO loss and reputation damage, while successful challenges distribute rewards based on author reputation curves. The system implements ban mechanics with escalating stake requirements and maintains a core CAP wallet for sustainable challenge payouts.

## 1. Introduction

### 1.1 Background

The proliferation of AI-generated content has created an authenticity crisis in digital media. Distinguishing human-created content from machine-generated material has become increasingly difficult, threatening the integrity of creative works, journalism, and academic discourse. Existing verification methods rely on centralized authorities or easily-manipulated metadata, lacking the transparency and immutability required for trustworthy attestation.

### 1.2 Motivation

Current content verification systems suffer from three critical limitations: centralized control creating single points of failure, lack of economic incentives for accurate verification, and absence of reputation-based accountability. These deficiencies enable bad actors to game the system while honest participants lack sufficient rewards for maintaining integrity.

### 1.3 Goals

CAP aims to:
- Provide decentralized, transparent verification of content authenticity
- Create economic incentives for accurate challenge and verification
- Establish reputation-based trust through mathematical scoring
- Ensure system sustainability through proper tokenomics
- Enable Byzantine fault tolerance against adversarial actors

### 1.4 Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 and RFC 8174.

## 2. Terminology

**Attestation**: Author's cryptographic declaration that content was created without LLM assistance  
**Byzantine Fault**: Arbitrary failures including malicious behavior by consensus participants  
**Challenge**: Staked dispute of content authenticity by autonomous agents  
**ELO Score**: Reputation rating based on challenge/verification history  
**HotStuff**: Linear-complexity Byzantine fault-tolerant consensus algorithm  
**Slashing**: Penalty mechanism for failed challenges or malicious behavior  
**Stake**: ALGO cryptocurrency locked as collateral for participation  

## 3. System Architecture

### 3.1 Core Components

The CAP system consists of five primary smart contracts deployed on Algorand:

1. **Attestation Registry**: Stores content hashes with author attestations
2. **Challenge Manager**: Handles challenge initiation and stake management
3. **Consensus Engine**: Implements HotStuff BFT for vote aggregation
4. **Reputation Tracker**: Maintains ELO scores with decay mechanics
5. **Treasury Manager**: Controls core wallet and reward distribution

### 3.2 Data Flow

```
Author → Attestation → Registry
         ↓
Challenger → Stake ALGO → Challenge Manager
         ↓
Validators → Vote via HotStuff → Consensus Engine
         ↓
Resolution → Reputation Update + Reward/Slash → Treasury
```

### 3.3 State Management

Global state:
- Current challenge queue
- Active voting periods
- Reputation scores
- Treasury balance metrics
- Ban lists and escalation counters

## 4. Consensus Protocol

### 4.1 HotStuff Implementation

The system employs HotStuff consensus for its linear communication complexity O(n) in both normal operation and view changes, critical for supporting 8-hour to 3-day voting periods.

**Three-Phase Protocol:**
1. **Prepare Phase**: Leader broadcasts challenge details
2. **Pre-Commit Phase**: Validators signal readiness
3. **Commit Phase**: Final votes recorded on-chain


### 4.2 Dynamic Validator Participation

Validators may join or leave during voting periods through stake-weighted participation

### 4.3 Byzantine Fault Tolerance

The protocol tolerates up to f < n/3 Byzantine validators where n ≥ 3f + 1. Safety is maintained through cryptographic commitments and the three-chain rule, while liveness uses optimistic responsiveness.

## 5. Reputation Scoring Algorithm

### 5.1 ELO Calculation

**Expected Outcome:**
```
E_challenger = 1.0 / (1.0 + 10^((R_author - R_challenger) / 400))
E_author = 1.0 / (1.0 + 10^((R_challenger - R_author) / 400))
```

**Rating Update:**
```
R_new = R_old + K * (S_actual - E_expected)
```

### 5.2 K-Factor Schedule

Dynamic K-factors based on reputation level:
- K = 40: New participants (< 20 challenges)
- K = 30: Low reputation (0-1500)
- K = 20: Medium reputation (1500-2000)
- K = 15: High reputation (2000-2500)
- K = 10: Elite reputation (> 2500)

### 5.3 Multi-Party Challenge Resolution

For N participants, use pairwise decomposition (TBD)

### 5.4 Reputation Decay

Penalties decay exponentially over 18 months (TBD)

## 6. Economic Model

### 6.1 Stake Scaling Formula

Required stake increases with reputation to prevent gaming:

```
Required_Stake(reputation_level) = 100 * 1.8^reputation_level ALGO
```

### 6.2 Challenge Economics

**Challenger Requirements:**
- Minimum stake: 50% of author's reputation-based requirement
- Escalating requirements after failed challenges: 2^ban_count multiplier
- Maximum stake: 10,000 ALGO cap to prevent plutocracy

**Author Defense:**
- No stake required for initial attestation
- Optional counter-stake for increased rewards
- Reputation at risk based on challenge outcome

### 6.3 Reward Distribution

Rewards follow a sigmoid curve based on author reputation:
```
Reward(R) = Max_Reward / (1 + e^(-k(R - R_mid)))
```

Where:
- Max_Reward = Challenge stake + treasury contribution
- k = 0.002 (steepness factor)
- R_mid = 1500 (midpoint reputation)

### 6.4 Treasury Sustainability

The core CAP wallet maintains sustainability through:
- 5% fee on all challenge stakes
- 50% capture of slashed stakes from failed challenges, with remainder distributed to participants based on timeliness of verification
- Dynamic fee adjustment based on treasury depletion:

## 7. Smart Contract Specifications

### 7.1 Algorand Implementation

(TBD)

## 8. Security Considerations

### 8.1 Threat Model

(TBD)

### 8.2 Cryptographic Security

(TBD))

### 8.3 Economic Security

(TBD)

## 11. Future Considerations

### 11.1 Scalability Enhancements

- Layer-2 rollups for high-volume attestations
- Sharding for parallel challenge processing
- State channel implementations for micro-challenges

### 11.2 Feature Extensions

- Multi-modal content verification (images, video, audio)
- Cross-chain reputation portability
- AI-assisted challenge detection mechanisms
- Decentralized oracle integration for external data

### 11.3 Research Areas

- Zero-knowledge proofs for private attestations
- Quantum-resistant cryptographic upgrades
- Machine learning for anomaly detection
- Economic mechanism optimization

## References

### Normative References

1. Castro, M., Liskov, B. "Practical Byzantine Fault Tolerance." OSDI 1999.
2. Yin, M., et al. "HotStuff: BFT Consensus with Linearity and Responsiveness." PODC 2019.
3. RFC 2119: Key words for use in RFCs to Indicate Requirement Levels.

### Informative References

4. Elo, A. "The Rating of Chessplayers, Past and Present." Arco Publishing, 1978.

## Appendix A: State Transition Diagram

```
[Idle] → (Content Submitted) → [Attested]
                                    ↓
                            (Challenge Initiated)
                                    ↓
                              [Under Review]
                               ↙         ↘
                    (8hr no activity)  (3 days elapsed)
                           ↓                 ↓
                      [Resolved]        [Resolved]
                           ↘             ↙
                            [Finalized]
```
