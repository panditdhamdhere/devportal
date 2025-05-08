---
title: Security model
sidebar_position: 200
sidebar_label: Security model
tags:
  - Rootstock
  - security
  - powpeg
  - architecture
  - federation
  - 2-way peg
description: Achieving security in a Powpegged sidechain using proofs of payment
---

A sidechain is an independent blockchain whose native currency is pegged to the value of another blockchain currency. The peg can be enforced by a protocol or it can be synthetic. A [2-way peg](/concepts/powpeg/) is a protocol-enforced system allowing two currencies to be exchanged freely, automatically, and without incurring in a price negotiation. In Rootstock, the asset that can be freely moved is Bitcoin. When the network where the bitcoins exist is not clear from the context, we refer to RBTC to bitcoins existing in Rootstock.

In practice, when BTC is exchanged for RBTC, no currency is “transferred” between blockchains in a single transaction. The transfer operation is split into two transactions. In the first, some BTCs are locked in Bitcoin and in the second the same amount of RBTC is unlocked in Rootstock. The whole process is called peg-in. When RBTC needs to be converted back into BTC, the reverse process occurs: the RBTC gets locked again in Rootstock and the same amount of BTC is unlocked in Bitcoin. The process is called peg-out.

Fully trust-minimized and third-party-free two-way pegs can be created if two platforms have Turing-complete smart-contracts. But since Bitcoin currently does not support Turing-complete smart-contracts nor native opcodes to validate external SPV proofs, part of the 2-way peg system in Rootstock relies on an autonomous system called PowPeg. This system comprises a smart-contract called Bridge that controls every operation, and a set of third-parties called pegnatories, each one running a software called PowPeg node and a hardware security module called PowHSM. The PowHSM is a tamper-proof device responsible for storing a private key that is part of a multi-signature scheme. In the peg-in process, users send bitcoins to this  multi-signature address.  The PowHSMs are also responsible for choosing the Rootstock best chain based on cumulative proof-of-work, in a security model called SPV, and for signing Bitcoin peg-put transactions in case the Rootstock blockchain consensus requires it. **No single pegnatory can control the locked BTCs, nor access the multi-sig private key stored in the PowHSM. Not even the majority of pegnatories has the ability to release BTC funds**. The PowHSM only proceeds to sign a peg-out transaction upon receiving commands from the Rootstock blockchain, backed by 4000 confirmation blocks, with a cumulative proof-of-work currently equivalent to approximately 100 bitcoin blocks. Note that if a user transfers BTC into RBTC and back, they will normally not receive bitcoins that are directly connected by UTXOs with the original BTC sent. The bitcoins are not locked for specific users, and instead they are locked for use across the entire Rootstock network.

The locking and unlocking of funds is done by the PowPeg without any human intervention. A requirement for being part of the PowPeg is the ability to maintain the PowHSM device online and connected to the Rootstock network with high up-time. It’s also a requirement that pegnatories are capable of auditing, or review third party audits that attest that the software that powers the node behaves as expected. The PowHSM device is manufactured by a top hardware security company and the firmware was developed by RootstockLabs. The PowHSM provides state-of-the-art maximum security for their private keys using a Secure Element (SE).

As of January 2020, the PowPeg comprises 12 well-known, and highly secure pegnatories. Leading Blockchain companies are currently members of the PowPeg.  In exchange for their work, the pegnatories are awarded 1% of the transaction fees generated on Rootstock, in order to cover the hardware and maintenance costs.

## PowPeg Members Update

The PowPeg is governed by a written protocol that establishes when it is possible or required to add or remove a member. If the conditions to change the composition are met, a pegnatory can send a message to the Bridge contract requiring the beginning of a PowPeg composition change. The change involves three phases: a voting period, a delay period and a funds migration period.  All phases are automated and coordinated by the Bridge contract, so the process is open, public, and leaves a cryptographic audit trail.  During the voting phase, each pegnatory can either accept or reject a composition change. Only if the majority of pegnatories accept the change, the next phase begins. This phase is a consensus enforced delay of one week. The delay allows users to transfer the Bitcoins back to the Bitcoin network in case they do not trust the new PowPeg composition. Finally, the composition change is activated and the last phase starts, which is responsible for the migration of the funds from the old PowPeg to the new one.

## The Future

One of the features that has been accepted by the community is adding public attestation of the PowHSM firmware for all users to verify the correctness of the PowHSMs. Another upcoming feature is the introduction of frequent keep-alives to detect as early as possible if a pegnatory is down. Several competing community proposals exist on how to improve the security of the PowPeg. If Bitcoin adds special opcodes or extensibility to validate SPV proofs, and once the new system is proven to be secure and trust-free, the PowPeg role will no longer be necessary, and the Rootstock community may implement the changes to adapt Rootstock to the new trust-free system. For example, members of the Rootstock community proposed in 2016 a drivechain BIP, which represents a trust-minimized alternative to the PowPeg.
