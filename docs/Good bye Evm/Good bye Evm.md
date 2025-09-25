Goodbye EVM, Hello RISC-V 
The Road to 10,000 TPS and ZK-Snarking Everything
TL;DR
Ethereum is preparing for its most significant architectural shift since launch: replacing the EVM with RISC-V.
The reason is simple — in a ZK-first future, the EVM is the bottleneck:
zkEVMs today rely on an interpreter → 50–800x slowdown,
precompiles have bloated the protocol with complexity and risk,
the 256-bit stack design is highly inefficient in proofs.
RISC-V solves this:
minimalist (~47 base instructions) + mature LLVM ecosystem (Rust, C++, Go),
already the de-facto zkVM standard (9/10 projects),
formal SAIL spec (vs ambiguous Yellow Paper) → enables strict verification,
hardware proving path (ASICs/FPGAs) already being tested (SP1, Nervos, Cartesi).
Migration comes in 3 stages:
RISC-V as precompile replacement (low-risk testing).
Dual-VM era: EVM + RISC-V with full interop.
EVM re-implemented inside RISC-V (Rosetta strategy).
Ecosystem impact:
Optimistic rollups remain intact.
Fraud proofs don’t disappear with a RISC-V L1 — the RISC-V VM can be compiled into the existing proof programs (MIPS-based today).
Migration path = extend current fault-proof infra to target RISC-V, not a full rebuild.
ZK rollups win big (Polygon, zkSync, Scroll → cheaper, faster, simpler).
Developers gain Rust/Go/Python libraries directly on L1.
Users get ~100x cheaper proofs → path to Gigagas L1 (~10k TPS).
In the end, Ethereum evolves from a “smart contract VM” into a minimal, verifiable trust layer for the internet, where “the finish line = ZK-snark everything.”
Ethereum at a Crossroads
**jaehaerys.eth (@0xJaehaerys) 引用 Vitalik Buterin 的观点指出：**

The ZK endgame is inevitable, and the thesis is simple: Ethereum is being rebuilt from the ground up on Zero-Knowledge proofs. This represents the protocol's technical finish line— re-architecture of the entire system to be ZK-native.
With this vision as the finish line, Ethereum now stands at the threshold of its most significant architectural evolution since launch. The discussion is no longer about incremental upgrades but a fundamental re-architecting of its computational core: the replacement of the Ethereum Virtual Machine (EVM). This initiative is a cornerstone of the broader
"Lean Ethereum" vision, which aims for a systematic simplification of the entire protocol, breaking it down into three core components: Lean Consensus, Lean Data, and Lean Execution . At the heart of Lean Execution lies one critical question: is the EVM, the very engine that powered the smart contract revolution, now the primary bottleneck for Ethereum's future?
As Justin Drake from the Ethereum Foundation puts it, the long-term goal has always been to "Snarkify everything," a powerful tool to enhance every layer of the protocol. For a long time, this was "a little bit of a pie in the sky because specifically what we needed was this concept of real-time proving" . Now that real-time proving is becoming a reality, the theoretical inefficiencies of the EVM have become a practical and urgent problem to solve.
This analysis explores the compelling technical and strategic arguments for migrating Ethereum's L1 to the RISC-V Instruction Set Architecture (ISA), a move that promises to unlock unprecedented scalability, simplify the protocol, and align Ethereum with the future of verifiable computation.
What Exactly Is Changing?
Before diving into the "why," it's essential to understand the "what."
The EVM is the runtime environment for smart contracts on Ethereum. It's the "world computer" that processes transactions and updates the state of the blockchain. For years, its design was revolutionary, creating a permissionless platform that spawned the entire DeFi and NFT ecosystems. However, its bespoke, nearly decade-old architecture now carries significant technical debt. 
RISC-V, on the other hand, is not a product but an open standard—a free and universal "alphabet" for designing processors. Its key principles, as highlighted by Jeremy Bruestle during the Ethproofs call, are what make it uniquely suited for this role:
Minimalism: The base instruction set is incredibly simple, containing only around 40-47 instructions. This makes it, in Jeremy's words, "almost like perfect for the use case that we had of a super minimalist general purpose machine" .
Modularity: More complex functionality is added via optional extensions. This is critical because it allows for a simple core that can be expanded as needed, without forcing unnecessary complexity into the base protocol.
Open Ecosystem: It's supported by a massive, mature toolchain, including the LLVM compiler, which allows developers to use mainstream languages like Rust, C++, and Go. As Justin Drake mentioned, "there's a lot of tooling around compilers and compilers are extremely difficult to build... And so there's a lot of value to having this compiler tooling" . RISC-V allows Ethereum to inherit this work for free.
The Interpreter Overhead Problem
The imperative to replace the EVM isn't driven by a single flaw but by a confluence of fundamental limitations that have become impossible to ignore in the context of a ZK-native future. These issues range from crippling performance bottlenecks in ZK-proving systems to the accumulation of dangerous complexity within the protocol itself.

The Interpreter Overhead Problem
The most urgent driver for this transition is the inherent inefficiency of the EVM within zero-knowledge proving systems. As Ethereum moves to a model where the L1 state is verified by ZK proofs, prover performance becomes the ultimate bottleneck.
**jaehaerys.eth (@0xJaehaerys) 引用 Vitalik Buterin 的观点进一步分析：**

For most people, the buzz around Vitalik's "Long-term L1 execution layer proposal: replace the EVM with RISC-V" sounded like black magic. But what if it's actually much simpler, and it affects everyone?

The current zkEVM approach creates unnecessary complexity.
The problem lies in how current zkEVMs work. They don't prove the EVM directly. Instead, they prove an interpreter of the EVM that is itself compiled to RISC-V. Vitalik Buterin explains this fundamental issue directly:
“…if the way zkVMs are implemented is by taking EVM execution and compiling it down into something that eventually becomes RISC-V code, then why not just expose the underlying RISC-V directly to smart contract developers? That way you can completely remove an entire outer layer of virtual machine overhead.”
This extra layer of interpretation imposes a massive performance penalty. Estimates suggest this creates a 50–800x slowdown compared to proving a native program. After other bottlenecks like hashing are optimized (e.g., by switching to Poseidon), this "block execution" portion will consume 80–90% of all proving time, making the EVM the final and most formidable barrier to scaling L1. By removing this layer, Vitalik estimates a potential 100x increase in execution efficiency.
**Uma Roy (@pumatheuma) 的观点：**

New Vitalik Buterin blog post on replacing the EVM with RISC-V in the long-term.

I am a huge fan of this direction for Ethereum's execution layer.

Today, RISC-V zkVMs like SP1 are the clear endgame solution for "ZK-ifying" Ethereum and quickly becoming the de-facto solution for
The Precompile Debt Trap
To work around the EVM's poor performance in specific cryptographic operations, Ethereum introduced precompiled contracts—specialized functions hard-coded directly into the protocol. While a pragmatic solution at the time, this has led to what Vitalik Buterin calls a “horrible” situation:
“Precompiles have been horrible for us… they massively bloat the trusted code base of Ethereum… they’ve been responsible for some of our worst near-misses in terms of consensus failures.”
The complexity is staggering. Vitalik illustrates this by comparing the wrapper code for a single precompile, modexp, to an entire RISC-V interpreter—observing that the precompile’s logic is actually far more complex. Adding new precompiles requires a slow and politically fraught hard fork process, which stifles innovation for applications that need new cryptographic primitives. This leads Vitalik to a firm conclusion:
“I actually think we should just have a moratorium on any new precompiles starting today.”
Ethereum’s Architectural Technical Debt
The EVM’s core design reflects the priorities of a bygone era and is ill-suited for modern computation.
The 256-bit architecture, chosen for handling cryptographic values, is massively inefficient for the majority of smart contract operations, which typically use 32- or 64-bit integers. This inefficiency is especially costly in ZK systems. As Vitalik explains:
“When you’re using smaller numbers, you don’t actually get any savings from each individual number, and the complexity blows up by a factor of somewhere between two and four.”
Furthermore, its stack-based architecture is less efficient than the register-based model of RISC-V and modern CPUs. It requires more instructions to perform the same operations and complicates compiler optimizations.
These combined factors—the ZK proving bottleneck, the complexity of precompiles, and outdated architectural choices—create a compelling and urgent case for Ethereum to evolve beyond the EVM.
The RISC-V Blueprint — Building on Stronger Foundations

**Vitalik Buterin (@VitalikButerin) 在《Simplifying the L1》中指出：**

The case for RISC-V isn't just about the EVM's shortcomings; it's about the inherent strengths of RISC-V's design philosophy. Its architecture provides a robust, simple, and verifiable foundation perfectly suited for a high-stakes environment like Ethereum.
Why Open Standards Beat Bespoke Designs
Unlike bespoke, custom-built ISAs that require building an entire software ecosystem from scratch, RISC-V is a mature, open standard. This provides three critical advantages:
A Mature Ecosystem. By adopting RISC-V, Ethereum leverages decades of collective progress in computer science. As Justin Drake explained, this provides immediate access to world-class tools:
“There is an infrastructure component called LLVM, a compiler toolchain that allows you to take high-level programming languages and compile them to a wide range of backends. One of the supported backends is RISC-V. So if you support RISC-V, you automatically support all the high-level languages that LLVM enables.”
This dramatically lowers the barrier to entry for millions of developers proficient in languages like Rust, C++, and Go.
Simplicity by Design. The minimalism of RISC-V is a deliberate feature, not a limitation. With a base of only ~47 instructions, the core of the virtual machine remains incredibly simple. This simplicity is a profound advantage for security, as a smaller trusted codebase is easier to audit and formally verify.
De-Facto Standardization in the ZK Space. Crucially, the zkVM ecosystem has already made its choice. As Justin Drake highlighted, a clear pattern has emerged from the Ethproofs data:
“RISC-V is the leading ISA for zkVM backends.”
Out of ten zkVMs capable of proving Ethereum blocks, nine already target RISC-V. This market convergence is a powerful signal; by adopting RISC-V, Ethereum is not making a speculative bet but aligning with a standard already battle-tested and validated by the very projects building its ZK future.
Designed for Trust, Not Just Execution
Beyond its ecosystem, the internal architecture of RISC-V is uniquely suited for building secure and verifiable systems.
First, it has a formal, machine-readable specification called SAIL. This is a monumental improvement over the EVM’s specification, which exists primarily as a prose document (the Yellow Paper) that can be ambiguous. The SAIL spec serves as a “gold standard,” enabling mathematical proofs of correctness essential for a protocol securing immense value. As Alex Hicks from the EF noted on the Ethproofs call, this allows zkVM circuits to be verified directly “against the official RISC-V specification.”
Second, RISC-V includes a privileged architecture, a feature often overlooked but critical for security. It defines different levels of operation, primarily User Mode (for untrusted applications like smart contracts) and Supervisor Mode (for a trusted “Execution Kernel”). Diego from Cartesi explained its importance:
“The operating system itself must protect itself from other code. It needs to keep the different programs running separate from each other, and all of these mechanisms are part of the RISC-V standard.”
In this model, a smart contract running in User Mode cannot directly access the blockchain’s state. Instead, it must make a request via a special ECALL (Environment Call) instruction to the trusted Kernel running in Supervisor Mode. This creates a hardware-enforced security boundary — a far more robust and verifiable model than the purely software-based sandboxing of the EVM.
Vitalik's Vision
The transition is envisioned as a gradual, multi-stage process to ensure stability and backward compatibility. This approach, outlined by Vitalik Buterin, is designed to be evolutionary, not revolutionary.
Step 1: Precompile Replacement. The first, most conservative step is to introduce the new VM in a limited capacity. As Vitalik suggests, "we start off by using the new VM in limited situations. So for example to replace pre-compiles". This would involve placing a moratorium on new EVM precompiles and instead implementing any needed functionality as whitelisted RISC-V programs. This allows the new VM to be battle-tested on mainnet in a low-risk environment.) interface, where the Ethereum client acts as a mediator between the two execution environments.
Step 2: Dual-VM Coexistence. The next phase would "make this new VM directly available to users." Contracts could be deployed with a flag indicating whether their bytecode is EVM or RISC-V. The critical feature is ensuring seamless interoperability: "the two types of contracts would be able to call each other". This would be achieved through a system call (ECALL
Step 3: EVM as an Emulated Contract (The "Rosetta" Strategy). The endgame is to achieve ultimate protocol simplification. In this stage, "we make the EVM an implementation inside of the new VM." The canonical EVM would become a formally verified smart contract running on the native RISC-V L1. This ensures perpetual support for legacy applications while allowing client developers to maintain only a single, minimal execution engine.
Ripple Effects Across the Ecosystem
The proposed transition from the EVM to RISC-V extends far beyond the core protocol, sending profound ripples across the entire Ethereum ecosystem. It promises to reshape the developer experience, fundamentally alter the competitive landscape for Layer-2 solutions, and unlock new economic models for proving.
Rollup Realignment: Optimistic vs. ZK
A shift to a RISC-V execution layer on L1 would have profound and divergent consequences for the two primary categories of rollups.
Optimistic Rollups (Arbitrum, Optimism) rely on fraud proofs that re-execute disputed transactions on L1. With a shift to RISC-V on Ethereum L1, these systems do not fundamentally change. As an Optimism co-founder explained: “Optimistic chains don’t break if we switch Ethereum to RISC-V. You just compile the RISC-V VM into the proof program. No need to use Asterisc either. The existing MIPS-based proof system will not break — you just compile the RISC-V VM into MIPS.”
This means the fraud-proof model remains intact. The adjustment is technical: compiling the new RISC-V VM into the existing infrastructure, not redesigning the system from scratch. The remaining challenges are engineering details such as gas metering, efficiency, and cost.
ZK Rollups, by contrast, gain a massive strategic advantage. The vast majority already converge on RISC-V as their internal ISA. An L1 that speaks the same native language enables much tighter and more efficient integration. Justin Drake describes a future of “native rollups,” where an L2 is essentially a specialized instance of the L1’s own execution environment, using the enshrined L1 VM for seamless settlement. This alignment would:

Simplify the Tech Stack: L2 teams no longer need to bridge the complex gap between their internal RISC-V execution and the EVM.
Enable Tooling and Code Reuse: Compilers, debuggers, and formal verification tools developed for the L1 RISC-V environment can be directly reused by L2s.
Align Economic Incentives: L1 gas costs would more accurately reflect the actual cost of ZK-proving RISC-V execution, creating a more rational economic model.
A New Era for Developers and Users
For those building on Ethereum, the transition promises to be evolutionary rather than disruptive.
For Developers, the key benefit is access to a broader, more mature world of software development. As Vitalik Buterin noted, developers will “become able to write contracts in Rust, and both of these options start to coexist with each other.” At the same time, he predicts that “Solidity and Vyper will continue to be popular for a long time” due to their elegance for smart contract logic. The ability to use mainstream languages and their vast libraries via the LLVM toolchain is a transformative shift. It opens the door to what he describes as a “NodeJS type of experience, where you basically use the same language to write on-chain code as you do to write off-chain code.”
For Users, the ultimate payoff is a cheaper and more capable network. The projected ~100x reduction in proving costs — from dollars to fractions of a cent per transaction — directly translates into lower fees on L1 and for L2 settlement. This economic viability unlocks the “Gigagas L1” vision, targeting ~10,000 TPS on L1, enabling a future of more complex, high-value on-chain applications.
**jaehaerys.eth (@0xJaehaerys) 在其报告《ETH zkEVM-L1 100x Scaling: Ethereum Unveils its Roadmap to 10M TPS》中总结道：**

Executive Summary: This report presents a technical analysis of Ethereum's fundamental transformation aimed at multiplying the network's capacity through ZK-native architecture.
Succinct Labs and SP1: Proving the Future, Today
https://blog.ethereum.org/2025/07/31/lean-ethereum
The theoretical benefits of RISC-V are already being demonstrated in practice by teams like Succinct Labs. Their work serves as a powerful case study for the entire proposal.
Succinct’s SP1 is a high-performance, open-source zkVM built on RISC-V that validates the new architectural approach. Its “precompile-centric” philosophy perfectly illustrates how to solve the EVM’s cryptographic bottlenecks. Instead of relying on slow, hard-coded precompiles, SP1 offloads intensive operations such as Keccak hashing to specialized, hand-optimized ZK circuits invoked via a standard ECALL instruction. This provides the performance of custom hardware with the flexibility of software.
**Succinct Labs (@SuccinctLabs) 的实际应用展示了这一愿景：**

INTRODUCING: SP1 Hypercube
The practical impact is already visible. The OP Succinct product uses SP1 to “ZK-ify” Optimistic Rollups. As Uma Roy, co-founder of Succinct, explained:
“Your OP Stack rollup, instead of having to wait seven days for finality and withdrawal… now achieves one-hour finality. It’s much faster finality, and that’s super awesome.”
This solves a major pain point for the entire OP Stack ecosystem. Moreover, Succinct’s infrastructure — the Succinct Prover Network — is designed as a decentralized marketplace for proof generation, demonstrating a viable economic model for the future of verifiable computation. Their work is not just a proof of concept; it is a functioning blueprint for the future described in this article.
How Ethereum Mitigates the Risks
A key advantage of RISC-V is that it makes the holy grail of formal verification—mathematically proving a system’s correctness—an achievable goal. The EVM, specified in natural language via the Yellow Paper, is notoriously difficult to formalize. RISC-V, by contrast, has an official, machine-readable SAIL specification, which provides an unambiguous “golden reference” for its behavior.
This enables a clear path to stronger security. As Alex Hicks from the Ethereum Foundation noted, work is already underway to formally “verify zkVM RISC-V circuits against the official RISC-V specification extracted into Lean.” This is a monumental step forward, shifting trust from fallible human implementations to verifiable mathematical proof.
The Primary Risks of the Transition
Despite its advantages, a RISC-V-based L1 introduces new and formidable challenges.
Gas Metering. Creating a deterministic and fair gas model for a general-purpose ISA is one of the most difficult unsolved problems. A simple instruction count is vulnerable to denial-of-service attacks (e.g., an attacker could craft a program that repeatedly misses the cache, causing high resource usage for a very low gas fee).
Toolchain Security & the Reproducible Builds Problem. This is perhaps the most significant and underappreciated risk. The security model shifts from trusting the on-chain VM to trusting the off-chain compilers (such as LLVM) used by every developer. These compilers are incredibly complex and known to contain bugs. An attacker could potentially exploit a compiler bug to generate malicious bytecode from seemingly benign source code. Furthermore, ensuring that a compiled binary on-chain corresponds exactly to a specific public source code—the “Reproducible Builds” problem—is exceptionally difficult, as minor differences in the build environment can produce different binaries.
Mitigation Strategies
The path forward requires a multi-layered defense strategy.
Phased Rollout. The gradual, multi-stage transition plan is the primary risk mitigation strategy. By introducing RISC-V first as a precompile replacement and then in a dual-VM environment, the community can gain operational experience and build confidence in a low-risk setting before any irreversible changes are made.
Total Audit: Fuzzing and Formal Methods. While formal verification provides the ultimate goal, it must be complemented by continuous, aggressive testing. As Valentine from Diligence Security demonstrated on the Ethproofs call, their Argus fuzzer has already discovered 11 critical soundness and completeness bugs in leading zkVMs. This proves that even the most well-designed systems have vulnerabilities that can only be uncovered through rigorous, adversarial testing.
Standardization. To avoid ecosystem fragmentation, it is critical for the community to converge on a single, standard RISC-V profile. This will likely be RV64GC with a Linux-compatible ABI, as this combination offers the broadest support from mainstream languages and tools, maximizing the benefits of the new ecosystem.
The Dawn of a Verifiable Future
The proposal to replace the Ethereum Virtual Machine with RISC-V represents a pivotal and audacious vision for the future of the network. It is not a mere incremental upgrade but a fundamental re-architecting of Ethereum’s execution layer, aimed at resolving deep-rooted scalability bottlenecks, simplifying protocol complexity, and aligning the platform with the broader world of general-purpose computing. While the path is fraught with immense technical and social challenges, the long-term strategic benefits are compelling enough to justify this ambitious undertaking.
The transition hinges on a set of core trade-offs: the immense performance gains of a ZK-native architecture versus the critical need for backward compatibility; the security benefits of a simpler protocol versus the inertia of the EVM’s vast network effect; and the power of a general-purpose ecosystem versus the risks of relying on complex third-party toolchains.
Ultimately, this architectural shift is the key to fulfilling the “Lean Execution” promise of the broader “Lean Ethereum” vision. It transforms the L1 from a simple smart contract platform into a highly efficient and secure settlement and data availability layer, purpose-built to support a universe of verifiable computation. The journey is long, but the direction is clear. As Vitalik Buterin stated, the ultimate goal is:
“The finish line includes… ZK-snarking everything.”
Initiatives like Ethproofs provide the objective data and collaborative forum necessary to navigate this path, while practical implementations from teams such as Succinct Labs with their SP1 zkVM serve as a working blueprint for what this future looks like. By embracing RISC-V, Ethereum is not merely solving its own scaling bottlenecks; it is positioning itself to become the foundational trust layer for the next generation of the internet, powered by the third great cryptographic primitive after hashes and signatures: the SNARK.
Prove the world’s software.
Details:
Vitalik explains: https://www.youtube.com/watch?v=Iu7SJYMpOsE
ETHProofs Call #4: https://www.youtube.com/watch?v=rJiEV7jJFl4