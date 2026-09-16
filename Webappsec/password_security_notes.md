# Password Security Notes: Hashing, Salting, & Peppering

## Section 1: Bcrypt vs. Argon2

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

================================================================================

## Section 2: Salting vs. Peppering

**Salting and peppering are two distinct cryptographic techniques used to secure password hashes**. While **salting** uniquely identifies a password to defeat precomputed attacks (like rainbow tables), **peppering** adds a secret, application-wide key to protect the database against offline brute-force attacks even if the database is fully leaked. Modern security architectures typically employ **both** techniques simultaneously for defense-in-depth.

---

### Core Comparison

| Feature | Salting | Peppering |
| :--- | :--- | :--- |
| **What it is** | A unique, random string generated per user. | A single, secret string shared across the application. |
| **Storage Location** | **In the database** alongside the hashed password. | **Separately from the database** (e.g., environment variables, HSM, vault). |
| **Primary Purpose** | Prevents **rainbow table attacks** and uniquely hashes identical passwords. | Slows down/prevents **offline brute-force attacks** if the database is breached. |
| **Uniqueness** | Unique per user record. | Static across all users (or scaled by server clusters). |
| **Secrecy** | Public/Non-secret (assume the attacker can read it). | Strictly secret (must be guarded carefully). |

---

### Deep Dive: Salting

Salting ensures that two users with the exact same password (e.g., `Password123`) will have completely different hash values in the database. 

* **The Mechanism:** When a user creates an account, the system generates a cryptographically secure random string (the salt). The system concatenates the salt with the password: `Hash(Password + Salt)`.
* **Why it is necessary:** Without a salt, an attacker who obtains a database leak can use precomputed tables of hashes (rainbow tables) to instantly reverse common passwords. They can also instantly identify every user sharing the same password.
* **Storage Reality:** Salts do **not** need to be hidden. Because they are stored in the database next to the username, an attacker with read access to the database can see them. The defense relies entirely on the uniqueness of the salt, forcing the attacker to compute a custom brute-force calculation for *every single user account* individually.

---

### Deep Dive: Peppering

Peppering adds a secondary layer of encryption or hashing using a secret key that the database does not know about. 

* **The Mechanism:** The system injects a secret application key (the pepper) into the hashing process: `Hash(Password + Salt + Pepper)`. 
* **The "Separation of Concerns" Defense:** If a hacker SQL-injects your database and steals the user table, they get the usernames, the hashes, and the salts. However, because the pepper lives in your environment variables, a separate configuration file, or a Hardware Security Module (HSM), the hacker **cannot start an offline brute-force crack**. Without the pepper, the hashes are mathematically useless to them.
* **Implementation Variations:** 
  * **Secret Key Approach:** The pepper is a high-entropy string treated like an API secret key.
  * **Brute-Force Pepper Approach:** Some developers choose a small pepper (e.g., a single byte, meaning 256 possibilities) and do not store it anywhere. During authentication, the server rapidly checks all 256 possibilities until it finds the match. If the database leaks, the attacker has to guess the extra byte for every attempt, multiplying their hardware cost by 256.

---

### Combined Workflow (Defense-in-Depth)

When implementing a modern authentication flow, the two concepts are layered together:

1. **Registration:**
   $$\text{Password} \rightarrow \text{Combine with Pepper} \rightarrow \text{Combine with Unique Salt} \rightarrow \text{Argon2id/Bcrypt} \rightarrow \text{Store Hash \& Salt}$$
2. **Login Verification:**
   $$\text{Input Password} \rightarrow \text{Fetch Salt from DB} \rightarrow \text{Inject Pepper from Env} \rightarrow \text{Compute Hash} \rightarrow \text{Compare with DB}$$
