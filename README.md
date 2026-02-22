# VeriTrust: AI-Driven Decentralized KYC Verification

VeriTrust is an institutional-grade, decentralized identity verification protocol engineered for the Stacks blockchain. I have developed this system to address the critical need for "trustless" identity validation in the Web3 space, replacing centralized, opaque KYC providers with a transparent, distributed network of AI-driven verifier agents. By leveraging Clarity’s predictable smart contract environment, I ensure that identity state transitions are immutable, auditable, and economically secured.

---

## Technical Philosophy

In designing VeriTrust, I prioritized **Sovereign Privacy** and **Crypto-economic Integrity**. The system is built on the premise that while identity verification is necessary for regulatory compliance, the underlying process should be decentralized to prevent single points of failure and censorship.

### Key Design Principles:

* **Hashed Commitment**: I utilize 32-byte cryptographic hashes to ensure that no sensitive PII (Personally Identifiable Information) is ever stored directly on the blockchain.
* **Algorithmic Accountability**: Verifiers are not just participants; they are stakeholders. I have implemented a staking mechanism that ties their financial interest to the accuracy of their AI models.
* **Escalated Resolution**: I built a sophisticated branching logic system that identifies "grey area" cases, escalating them from automated AI consensus to a manual review status when scores are ambiguous.

---

## 1. System Constants & Global Parameters

I have established a set of immutable constants that define the security bounds and operational logic of the protocol. These parameters are designed to maintain a high bar for verification while ensuring the network remains performant.

| Constant | Value | Technical Purpose |
| --- | --- | --- |
| `verification-threshold` | `u3` | Minimum number of unique AI agent votes required to trigger finalization. |
| `passing-score` | `u85` | The threshold out of 100 required for a user to be granted "VERIFIED" status. |
| `kyc-duration` | `u52560` | The lifespan of a verification (approx. 1 year, assuming 10-minute block times). |
| `min-stake` | `u1000` | The minimum commitment required to participate as a trusted agent. |
| `slashing-penalty` | `u500` | The amount of stake forfeited by agents who deviate from consensus. |

---

## 2. Private Functions (Internal Engine)

These internal functions handle the "behind-the-scenes" logic of the contract. I have encapsulated these to ensure that state changes are consistent and cannot be bypassed by unauthorized external calls.

### `is-authorized-verifier`

I use this function to verify the credentials of an agent before they are allowed to influence the KYC state. It checks the `verifier-stats` map to confirm the agent is both "trusted" and maintains a `stake-amount` greater than or equal to the `min-stake`.

### `update-verifier-reputation`

This function is the heart of the reputation system. I designed it to reward accuracy and penalize negligence.

* If an agent's vote aligns with the final consensus, I increment their `reputation-score` by 10 points (capped at 1000).
* If an agent is incorrect, I decrement their score by 10.
* This ensures that over time, the most reliable AI agents gain the most influence within the ecosystem.

### `min-uint` & `max-uint`

Mathematical helpers I implemented to handle integer bounds safely, ensuring that reputation scores and stakes never underflow or overflow.

---

## 3. Public Functions (Transactional Interface)

The public functions represent the interface for users, AI agents, and the contract administrator. These functions are responsible for the primary lifecycle of the KYC process.

### System Governance

* **`set-emergency-shutdown`**: A critical security feature I included to allow the `contract-owner` to pause the contract in the event of a black-swan event or detected vulnerability.

### Agent Staking & Management

* **`register-verifier`**: The entry point for AI agents. I require an initial stake to be committed, which initializes the agent's reputation at a neutral level (500).
* **`unstake-verifier`**: Allows agents to exit the network. I have ensured that agents can only withdraw what they have committed, protecting the pool from unauthorized withdrawals.

### User Lifecycle Management

* **`submit-kyc-application`**: I designed this function to accept a `data-hash`. It includes safeguards to prevent multiple active applications from the same user, though it explicitly allows resubmission if a previous application was `REJECTED` or `EXPIRED`.
* **`renew-kyc`**: A dedicated function for returning users. I built this to reset the consensus metrics, allowing a user to refresh their "VERIFIED" status once it has lapsed.
* **`vote-on-kyc`**: This is where the AI agents submit their findings. I implemented a double-vote protection mechanism to ensure each authorized agent only contributes a single score per user application.

### The Consensus Core

* **`finalize-verification-consensus`**: This is the most complex function in the contract. I have programmed it to calculate the average agent score and transition the user status based on a nested conditional structure:
1. **APPROVED**: If the score is , the user is marked `VERIFIED` and an expiry block is set.
2. **REJECTED**: If the score is , the user is denied access.
3. **REVIEW**: If the score falls in the middle, the status is set to `REVIEW`, flagging the application for high-tier intervention.



### Dispute Resolution

* **`file-appeal`**: I provided a mechanism for rejected users to request a second look by providing a `reason-hash`.
* **`process-appeal`**: Only the contract owner can resolve appeals in this version. This allows for a final human-in-the-loop check to correct any potential AI hallucinations or errors.

---

## 4. Contribution Guidelines

I am committed to the continuous improvement of VeriTrust. If you wish to contribute:

1. **Code Consistency**: Maintain the Clarity 2.0 standards used throughout the contract.
2. **Logic Integrity**: Any changes to the `finalize-verification-consensus` logic must be thoroughly documented.
3. **Testing**: Ensure all public functions are covered by unit tests in a local Clarinet environment before submitting a Pull Request.

---

## 5. License

**The MIT License (MIT)**

Copyright (c) 2026 VeriTrust Protocol

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

---
