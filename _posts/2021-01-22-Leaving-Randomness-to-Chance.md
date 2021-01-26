### Leaving Randomness to Chance: Standards Shortcomings and Buried Backdoors in Random Number Generators

[event](https://citp.princeton.edu/event/cohney/) [link](https://www.youtube.com/watch?v=O4FEmw3I0nU&feature=youtu.be)

**Year**: 2020

**Speaker**: Shaanan Cohney. Currently (Jan 2021) Assistant Prof Melbourne. Previously Postdoc Princeton.

**Precis**: "What I propose is that we think about pre-supply chain operations, which I term PSYCHOs, which encorporates a broader threat model that thinks about threats as coming in at all stages of the standardisation, design, and certification process" (33mins24). 

**Methodology**: Reverse engineering and legal scholarship (course: Masters in Law, UPenn). Case study of certified pseudorandom number generators (PRGs) - gaps in formal models, read certification documents, reverse engineering, attack development. Threat modelling - characterise failures, assess influence of standards, taxonomise attack types.

**Details**:

- True randomness is hard to come by. John von Neumann (also ETH!): ‘anyone who considers arithmetical methods of producing random digits is, of course, in a state of sin’. 

- Pseudorandomness produces "random looking data" (6min21). Cryptographic security requires a good source of environmental entropy to feed pseudorandom generator - sources are low bandwidth (not many numbers at a time), and until comparatively recently, had low adoption e.g. background radiation, fluctuations in electrical cicuitry.

- Standards

  - Federal Information Process Standards (FIPS) defines how govt is to run/procure infosec. Cryptographic Module Validation Program (CMVP) is a third party test of FIPS. The National Institute of Standards in Technology (NIST) issue a certificate to successful products. Desirable for vendors because govt crypto is a big market.
  - FIPS PRG - must be used for all system functions, and be the sole source of randomness for cryptographic protocols
  - ANSI X9.31
    - 1985: DES-based PRG standardised in ANSI X9.17
    - 1992: Adopted as FIPS standard
    - 1998: Kelsey et al. state recovery if key known (brute force timestamp. All procedures in design are invertable). 
    - 2004: Standards updated. ANSI X9.31 still included.
    - 2011: FIPS depreciates ANSI X9.31
    - 2016: ANSI X9.31 PRG removed
    - "The standard did not specify the key should not be hard-coded" (15mins31).  Since FIPS 140 required vendors to document key generation, SC searched policies (public) to find hard coded. 12 vulnerable. (127 no info. 149 not vulnerable.) FortiOSv4 not only hard coded the key, but used the NIST test vector key. 2^25 work to brute force timestamps for state recovery. Replicated in the wild (with help from Fortigate fingerprints leaked by Equation Group / NSA)
  - CTR_DRBG
    - Optional user-provided additional entropy (so doesn't need to be included to be compliant)
    - Vulnerable to side channel attack, but this was not part of the threat model.  Standard: "cryptographic modules may be susceptible to other attacks [...] outside of the scope of the standard"
    - Best empirical example of theoretical threat model? FortiOS v5. 
  - Dual EC DRBG
    - 2004: Draft Proposal
    - 2006 (Early): Distinguishing attack (Schoenmakers and Sidorenko)
    - 2006 (Late): Published in NIST SP 800-90A
    - 2007: Shumow and Ferguson demonstrate backdoor usage
    - Design: requires specification of a curve and two points on it. NIST sets curve as p-256 and defines P,Q. If P and Q a multiple of each other, can move from output to state. 
    - 2013: NSA might have had a hand in design of algorithm, through project named BULLRUN. Dual EC seems good fit for this attack model. For NSA pre-supply chain attacks, see NSA's 2013 budget request: "influence policies, standards and specifications for commercial public key technologies'" in [NYT Archive](https://archive.nytimes.com/www.nytimes.com/interactive/2013/09/05/us/documents-reveal-nsa-campaign-against-encryption.html)
    - 2014: NIST removed Dual EC from standard
    - 2015: Juniper announces unauthorised code modifications (full passive decryption of VPN traffic). But not certified for Dual EC (X9.31). And used non-default points for Dual EC, but couldn't explain where these points had come from. 
    - ScreenOS 6.1.0r7 used ANSI X9.31 and short nonces. ScreenOS 6.2.0r0 (2008) used DualEC to provide input to ANSI X9.31 (a reseed bug exposed raw Dual EC output), and used 32-byte nonces making the attack less difficult. Juniper claimed the attack changed the constant in 6.2.0r15(2012) - so who had the backdoor in 2008? 

- Pre-supply chain attacks

  - For NSA traditional supply chain attacks, see pictures of NSA opening boxes in a document titled "Stealthy Techniques Can Crack Some of SIGINT’s Hardest Targets" e.g. at [EFF](https://www.eff.org/files/2015/01/27/20150117-spiegel-supply-chain_interdiction_-_stealthy_techniques_can_crack_some_of_sigints_hardest_targets.pdf)

  - But more broadly: standards and certification are targets. The examples above are not necessarily proven pre-supply chain atttacks. A taxonomy:
    - Regulatory rollover: fragility as a side effect of requirements
    - Direct design flaw: subtle flaw, including intentional omission of details
    - Parameter manipulation: advocated fixed, malicious parameters that would otherwise be implementer chosen
    - Intentional complexity: make complex to encourage bugs (just in standard, not to increase regulatory burden)
    - Threat model exclusion: exclude classes of vulnerability from a standards threat model

- Conclusion:

  - Not every flaw is a pre-supply chain attack, but not every one is immune to suspicion (paraphrased, 36min17). May explain rapid increase in NSA capability.
  - Standardisation isn't security, and certification isn't an audit (37min10). Certain vendors were certified as insecure e.g. hard coded key.
  - Always a long tail of old implementations (particularly business and government)

**Directions**:

- Case studies of other potentially broken protocols? 
- Adversarial models of governance: how to build a process not an outcome, and a process which is robust to bad faith actors
