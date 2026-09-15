**Bcrypt and Argon2 are slow, computationally expensive hashing functions designed specifically for password storage**. Unlike fast hashes (like SHA-256) which prioritize throughput, these algorithms are built to slow down brute-force and dictionary attacks by requiring significant hardware resources per attempt. Today, OWASP recommends Argon2id as the primary standard for modern applications, while **bcrypt** remains a secure, highly compatible, and battle-tested secondary option.

---

### Core Comparison

| Feature | Bcrypt | Argon2 (Argon2id) |
| :--- | :--- | :--- |
| **Primary Resource Cost** | **CPU Bound** (Computationally heavy) | **Memory-Hard** + CPU Bound |
| **GPU / ASIC Resistance** | Moderate (Vulnerable to massive parallel cracking) | **High** (Memory requirement neutralizes GPU speed) |
| **Tuning Parameters** | Single parameter: **Work Factor (Cost)** | Three parameters: **Memory, Time, and Parallelism** |
| **Input Constraints** | Strict **72-byte limit** on passwords | Practically no input limit |
| **Underlying Primitive** | Blowfish block cipher | BLAKE2b cryptographic hash |
| **Industry Standing** | Battle-tested since 1999; widely adopted legacy choice | Winner of the 2015 Password Hashing Competition; standard default |

---

### Deep Dive: Bcrypt

Developed in 1999 by Niels Provos and David Mazières, bcrypt relies on a modified key schedule from the Blowfish encryption algorithm. 

* **The Work Factor:** Bcrypt’s configuration relies on an exponential scale ($2^{\text{cost}}$ iterations). An increase of `1` in the work factor doubles the computation time. OWASP recommends a minimum work factor of `10`, though `12` or `13` is common for modern server processors.
* **The 72-Byte Truncation Hard Limit:** Bcrypt silently ignores any characters in a password past the 72nd byte. To bypass this, developers often pre-hash passwords with SHA-256 before sending them to bcrypt, though this adds architectural complexity.
* **The Vulnerability:** Because bcrypt relies mostly on processing power rather than memory space, attackers can easily offload cracking attempts onto highly parallelized custom hardware like GPUs or ASICs, which can run thousands of parallel checks simultaneously.

---

### Deep Dive: Argon2

Released in 2015 (and standardized in RFC 9106), Argon2 was designed from scratch to defend against custom hardware attacks by forcing the system to allocate significant random-access memory (RAM). 

#### Three Main Variants
1. **Argon2i:** Uses data-independent memory access. It is optimized to resist side-channel timing attacks but is slightly more vulnerable to time-memory trade-off (TMTO) attacks.
2. **Argon2d:** Uses data-dependent memory access. It maximizes defense against GPU/ASIC cracking but can leak timing data on shared hardware environments.
3. **Argon2id:** A hybrid variant that combines both approaches—running Argon2i for the first pass and Argon2d for the remaining passes. **This is the recommended default for user passwords**.

#### Granular Parameters
* **Memory Cost (m):** The amount of RAM dedicated to the process (OWASP recommends a minimum of 19 MiB).
* **Time Cost (t):** The number of execution passes or iterations over that memory block (OWASP recommends a minimum of 2 passes).
* **Parallelism (p):** The number of concurrent threads to spawn during computation (OWASP recommends a minimum of 1 thread, scaled to the hardware capacity).

---

### When to Use Which?

* **Use Argon2id for New Applications:** It provides maximum security against modern hacking rigs and handles ultra-long passwords natively without manual pre-hashing steps.
* **Keep Bcrypt for Legacy/Constraint Systems:** If you have an established codebase using bcrypt, there is no urgent need to undergo a massive data migration unless you explicitly require memory-hardness or have issues with the 72-byte barrier. Bcrypt is also highly efficient on embedded or low-memory systems where allocating multi-megabyte RAM structures per login attempt is impossible.
