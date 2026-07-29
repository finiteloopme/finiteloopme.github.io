---
layout: post
title: "Unpacking the Magic behind Zero-Knowledge Proofs"
published: true
comments: true
categories:
    - technical
tags:
    - cryptography
    - zero-knowledge-proofs
    - blockchain
---

# Unpacking the Magic behind Zero-Knowledge Proofs

*Originally published on [Medium](https://medium.com/@kunallimaye/unpacking-the-magic-behind-zero-knowledge-proofs-c6bc2380e386), 14 December 2025.*

Imagine acquiring an ancient treasure map. You seek to assure the world of its authenticity — that you possess the map — without revealing the map itself, else the treasure will be compromised. Zero-Knowledge Proofs (ZKPs) resolve this fundamental paradox:

> *How can one successfully prove knowledge of a secret without simultaneously exposing the secret itself?*

A ZKP is a form of *cryptography*, but it is *not an encryption technique*. Rather, it serves as a robust mechanism for verifying computational integrity and data accuracy. This capability allows sensitive data to be processed without requiring custodian control, thereby mitigating the threat of data breaches. This enables applications, such as:

- Proving a bank's solvency without access to its complete ledger.
- Validating a vote count without compromising the voter's identity.
- Demonstrating sufficient funds for a settlement without disclosing the account balance.

Essentially, ZKP encodes underlying *rules* into a mathematical *receipt*, commonly referred to as a *proof*. Crucially, this proof discloses no secret information to the *verifier* — the verifier need only check the mathematical integrity to confirm correctness.

The aim of this guide is to demystify the mathematics underpinning ZKPs. We will methodically construct the necessary mathematical framework, beginning with simple logarithms, advancing through discrete logarithms, and culminating with elliptic curves. Through verbose, step-by-step evaluation of the core equations, this guide provides a foundational understanding of ZKP systems.

## Features of Necessary Computation

For ZKP systems to operate flawlessly, the underlying computation must satisfy two critical conditions:

### 1. Absolute precision

Conventional computer division introduces inherent error propagation.

- In standard math, `1 ÷ 3 = 0.33333…` (This reliance on floating-point numbers yields an approximation).
- In cryptography, the verification must be exact; even a tiny floating-point error will cause the proof to fail, demanding exact precision.

### 2. One-Way Function (The Trapdoor Property)

- The computation must be easy to perform in one direction but computationally infeasible (very hard) to reverse.
- Consequently, the verifier must not be able to generate the secret using the proof.

## The Scenario: The Smart Car and the Signal Thief

Envision using your phone to initiate the unlocking sequence for your Smart Car. Your phone retains a confidential *Secret Digital Key*, while the vehicle possesses the requisite *Public Lock* counterpart.

**The Vulnerability:** Should your phone merely broadcast the Secret Key to the car, an opportunistic thief — equipped with a radio receiver nearby — can intercept and record this transmission. Crucially, they do not need to decipher the key; they can simply re-transmit the recorded signal later to compromise your vehicle's security. This attack vector is universally termed a *Replay Attack*.

**The ZKP Solution:** Leveraging Zero-Knowledge Proofs, your phone is never necessitated to transmit the actual key. Instead, the vehicle initiates the protocol by generating a unique, random *Challenge* (a mathematical problem) for every authentication attempt. Your phone subsequently utilizes its *Secret Key* to synthesize a specific, ephemeral answer, known as a *proof*. The thief's eavesdropped recording captures only a one-time, transient answer. Should they attempt to replay this signal later, the vehicle will have issued a new challenge, rendering the compromised data useless.

We will explore this concept by progressively upgrading the security infrastructure for a hypothetical **Smart Car Lock** system.

- **The Prover:** Your Phone (Custodian of the Secret Key).
- **The Verifier:** The Car (Custodian of the Public Lock).
- **The Adversary:** A Thief with a radio frequency scanner.

## 1. The Static Key (Simple Logarithm)

The mobile device must affirm it holds the *secret key*, represented by the value *x*, to the vehicle. However, during the verification process, the phone cannot simply transmit the *Secret Key* directly, as an adversary lurking nearby can intercept and record that signal.

### Formula

Instead the phone shares *y* calculated using following equation:

```
y = g^x
```

Where,

- `g` is a *generator* and is distributed as a public element
- `y` is a *public key* and is distributed as a public element
- `x` is a *private key* and kept confidential

### Example

```
2^x = 4096
```

We publish *y=4096* and *g=2*, while safeguarding *x* as the secret key.

Unfortunately this math is inherently **reversible**. An attacker sees the result (4096) and can trivially use a logarithm function to compute `x = log2(4096)`

The attacker instantaneously ascertains `x=12`; consequently, the *secret key* is compromised.

Even when *x* assumes a large value, the *attacker* can systematically deduce the key due to the predictable and monotonic correspondence between *x* and *y*:

- x=2, y=4
- x=3, y=8
- x=4, y=16
- x=5, y=32
- x=6, y=64
- x=7, y=128
- x=8, y=256
- x=9, y=512
- x=10, y=1024

## 2. The Scramble using Trapdoor (Discrete Log)

To stop the adversary from calculating the secret key *x* (our private key), we must fundamentally alter the arithmetic space. Instead of relying on ever-increasing numbers, we enforce a cyclical domain by implementing *Clock* arithmetic (*modular arithmetic*); where numbers wrap around a *Finite Field*. This arithmetic operates within a boundary established by a *Modulus*, typically a large prime number *p*.

This specific application of finite field arithmetic establishes a computationally *one-way* relationship, often termed a "*trapdoor*" function. While computing the resultant value *y* from the secret *x* is trivial, deducing the secret exponent *x* by reversing the function from *y* is computationally infeasible.

### Formula

```
y = g^x (mod p)
```

Where *p* is the prime modulus.

### Example

```
3^x (mod 17)
```

So that when,

- x = 1, 3 (mod 17) → `y = 3`
- x = 2, 9 (mod 17) → `y = 9`
- x = 3, 27 (mod 17) → `y = 10`
- x = 4, 81 (mod 17) → `y = 13`
- x = 5, 243 (mod 17) → `y = 5`

**The Security Implication.** Calculating the resultant *y* (the remainder) is computationally efficient. Conversely, as the exponent *x* increases, the corresponding output *y* exhibits an arbitrary and unpredictable pattern. This chaos fundamentally hinders the reverse operation. So the only way to find *x* from *y* requires checking every conceivable value for *x* — a *Brute Force* attack.

> *If the prime number used for the modulo math is small (e.g., 17), the calculation remains simple. However, in real-world applications, we generally use prime numbers with 77 digits (256 bits), which can take computers thousands of years to solve.*

## 3. The Authentication Protocol

### Interactive Protocol (Schnorr)

We have successfully secured the secret key, yet two critical challenges persist in completing the Zero-Knowledge Proof:

1. Since the Car cannot reverse-engineer the secret key, how does it affirm the key's validity?
2. How is the threat of a *replay attack* effectively nullified?

To counteract the *replay attack*, the transmission must be unique for every authentication attempt. We use the *Schnorr Protocol* for this, which orchestrates the exchange between your phone (the *Prover*) and the vehicle (the *Verifier*).

- **Commitment (R):** Your phone selects a transient random number (*r*) and transmits a commitment, calculated as: `R = g^r`
- **Challenge (c):** The Car generates a random math challenge (e.g. 7)
- **Response (z):** The phone computes the final *proof* using the *challenge* and its *secret key*: `z = r + c · x`

**The Verification:** The vehicle validates the integrity of the calculation via the equation: `g^z ≡ R · y^c`

If this relationship holds true, it is mathematically confirmed that the phone utilized its valid secret key *x*.

### Example

We have secured the secret key (`x=4`), but now we must prove we know it without revealing it.

**Setup:**

- **Modulus (p):** `23`
- **Generator (g):** `5`
- **Your Secret (x):** `4`
- **Public Key (y):** `4` (since `5^4 (mod 23) = 4`)

**Proof Generation:**

- **Commitment (R):** The phone picks a random number *r = 3*. Commitment is calculated as: `R = 5^3 (mod 23)`.

```
R = 125 (mod 23) = 10
```

- Commitment transmitted by the *phone* to the *car* is: **10**.
- **Challenge (c):** The car chooses a random number: **2**.
- **Response (z):** The phone uses the challenge and its secret key to calculate the final response: `z = r + c·x`

```
z = 3 + (2·4) = 11
```

- Final response from the *phone* to the *car*: **11**.

**The Verification:** The car checks if the math balances: `g^z ≡ R·y^c (mod p)`

- **Left Side (`g^z`):** `5^11 (mod 23) = 22`
- **Right Side (`R·y^c`):** `10 · 4^2 = 10·16 = 160 (mod 23) = 22`

**Match!** *22 = 22.* The car is convinced the phone posses the secret key *x* (4), without ever receiving the secret itself.

> *For the next authentication attempt, the Car will pick a different challenge (e.g., "99"). Consequently, the thief's replay attack is rendered mathematically obsolete.*

### Non-Interactive Protocol

The inherent nature of the *Interactive Protocol* necessitates that both the prover and the verifier must be simultaneously prepared for real-time interaction.

Consequently, an alternative mechanism is required for *issuing* the challenge number to facilitate offline verification. A self-chosen challenge (e.g., "Multiply by 17") would render the system susceptible to mathematical fraud; hence, we require an intrinsically reliable method for generating a non-manipulable random challenge.

We circumvent this need for continuous interaction by substituting the verifier's (car's) challenge with a *Hash Function* (known as the "Fiat-Shamir Heuristic"). This modification establishes the following revised authentication sequence:

1. The phone unilaterally selects an ephemeral random number (the *Commitment*).
2. This *Commitment* is utilized as input into a *cryptographic hash function*, which consequently outputs a chaotic, random number derived from the input (e.g., "192").
3. It is of critical importance that the output generated by the hash function is mathematically unpredictable.
4. The phone must now employ "192" (*commitment*) as the challenge. The resulting proof is then computed using this challenge and subsequently transmitted to the verifier.
5. Crucially, the verification phase may now be conducted offline.
6. The phone has effectively challenged itself, compelling honesty through the non-reversibility of the hash function.

Thus, the phone generates the challenge by hashing its Commitment combined with Public Data.

#### Formula

The challenge calculation is defined as: `c = Hash( R + y )`

This turns the ZKP into a Digital Signature.

## 4. Translation (Encoding)

So far, our secret (*x*) has been a number like `4` or `12345`. In practical contexts, information is conveyed as text, and we aspire to issue specific, complex commands — for instance, "OPEN TRUNK" — beyond a simple unlock function.

Since cryptographic engines do not process text natively, we require a standardized methodology to transform linguistic data into a numerical equivalent.

Assuming the command is **"TRUNK"**, this string must be converted into numerical format suitable for calculation.

The underlying sequence of bytes representing "TRUNK" is consolidated into a singular, large integer.

### Example

We want to work with secret password "TRUNK":

- **'T'** = 84 (`0x54`)
- **'R'** = 82 (`0x52`)
- **'U'** = 85 (`0x55`)
- **'N'** = 78 (`0x4E`)
- **'K'** = 75 (`0x4B`)

Stitching these bytes together, "TRUNK" becomes the integer **362,128,797,259**.

> *(5452554E4B)16 = (362128797259)10*

This substantial numerical value is now utilized as the secret parameter *x* within our ZKP protocol.

**Note:** catch here is that this *encoded* number must remain smaller than the (modulo) prime number selected for the finite field arithmetic.

Alternative encoding methodologies worthy of consideration include hashing, decomposition into smaller segments, and vectorisation.

## 5. Upgrading to Bitcoin Standard (Elliptic Curves)

Our conventional "Clock Math" is robust; however, relying solely on simple integer modulo (analogous to RSA) mandates extraordinarily large keys (e.g., 3072-bits) to maintain cryptographic integrity, which substantially impedes computational speed.

To achieve greater efficiency, we transition to the security paradigm provided by *Elliptic Curves*. Instead of utilizing simple integers, the cryptographic elements are expressed as *Points (x, y)* on the geometric curve.

This refined method retains the fundamental "Trapdoor" property of discrete logarithms but introduces an exponentially greater difficulty to reverse, permitting the use of significantly smaller keys (e.g., 256-bit).

### Formula

The canonical curve equation is:

```
y^2 = x^3 + Ax + B
```

Where A & B are constants defining curve over a field.

We will use Bitcoin curve to demonstrate this math.

### Example

The Bitcoin curve is called *secp256k1*. It is famous for having an incredibly simple equation. Bitcoin sets `A=0` and `B=7` for the above equation, giving us: `y^2 = x^3 + 7`

This simplification makes the math faster because the *Ax* term disappears.

Real Bitcoin uses a number field (Clock) of size 2^256. Let's use the exact same equation but on a tiny Clock size of 17 (`p=17`).

- **Curve**: `y^2 = x^3 + 7`
- **Clock**: `Modulo 17`
- **Our Point (P)**: Let's start at `(1, 5)`
- Check: y^2 = `5^2 = 25 ≡ 8 (mod 17)`.
- Check: x^3 + 7 = `1^3 + 7 ≡ 8 (mod 17)`. (It works).

Now, let's Double the Point (*P+P*) to generate a new public key.

#### Step 1: The Slope (λ)

Since `a=0`, the slope formula `(3x^2 + a) ÷ (2y)` simplifies to just `(3x^2) ÷ (2y)`.

- x=1, y=5
- Numerator: `3(1)^2 = 3`
- Denominator: `2(5) = 10`

We need to calculate `3÷10` on our Clock (*mod 17*).

- The "inverse" of 10 (what you multiply 10 by to get 1) is `12`. (`10 × 12 = 120`. `120 ÷ 17` leaves remainder `1`).
- So, `λ = 3×12 = 36`.
- `36 (mod 17) = 2`.
- The Slope is `2`.

#### Step 2: The New X (xn)

Formula: `xn = λ^2 − 2x`

- `xn = 2^2 − 2(1)`
- `xn = 4 − 2 = 2`

#### Step 3: The New Y (yn)

Formula: `yn = λ (xo − xn) − yo`

Where *xo* and *yo* are respective *old* values.

- `yn = 2(1 − 2) − 5`
- `yn = 2(−1) − 5`
- `yn = -2 - 5 = -7`; wrap around the clock: `−7 + 17 = 10`

#### The Result

Starting at `(1, 5)`, we doubled it and landed on `(2, 10)`.

> *Verification: `10^2 = 100 ≡ 15`. `2^3 + 7 = 15`. Equivalence is maintained!*

## Conclusion

The security paradigm afforded by the Elliptic Curve is currently robust; nevertheless, *Quantum Computers* introduce a unique threat. Their design inherently enables them to discern "Cycles" and "Periods" within data. Consequently, a sufficiently powerful Quantum Computer, executing Shor's Algorithm, could analyze the underlying mathematical "rhythm" of the curve and thereby determine your secret key.

## Post Quantum Cryptography: Lattices (Noise)

To mitigate this risk, cryptographers are systematically transitioning to the domain of Lattice-Based Cryptography. In the realm of Lattice mathematics, we introduce minor, random "Errors" (or Noise) into the algebraic computations.

```
Public = (Secret × G) + Noise
```

This "Noise" destroys the perfect rhythm that Quantum Computers rely on. It makes the math "fuzzy," protecting your secret even against quantum computers.

## Proof of Knowledge vs Proof of Computation

It is important to observe the fundamental distinction between *Proving Knowledge* (Schnorr) and *Proving Computation* (SNARKs). In our *Smart Car* example the *Correctness* is strictly defined by the integrity of the *Public Key*. Such that the correct answer is simply the key that fits the lock.

**"But what if I want to prove I solved a puzzle?"** For a scenario such as "Find the factors of 945," Schnorr protocol alone is inadequate. We need a more advanced ZKP called a zk-SNARK (Zero-Knowledge Succinct Non-Interactive Argument of Knowledge).

## Summary

We have methodically affirmed an identity using a zero-knowledge proof:

1. **Encoding:** "TRUNK" → Number.
2. **Math:** Number → Modular Arithmetic.
3. **Curve:** Modular Number → Point P.
4. **Proof:** Schnorr Protocol.

This is the foundation of the Zero Knowledge domain. From here, you can explore production libraries like `arkworks` or `halo2`, with a good understanding of what is happening under the hood.
