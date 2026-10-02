![Role of AI in Security - Use cases](./images/Screenshot_20261002-085042.png)

Token theft is the act of stealing authentication tokens to gain unauthorized access without possessing passwords or completing MFA challenges. Unlike traditional credential attacks, token theft exploits the fundamental design of modern authentication: once a user successfully authenticates and receives a token, that token grants access until expiration or revocation. Attackers steal these tokens through phishing kits, browser compromise, malware, or supply chain breaches, then replay them to access SaaS applications, APIs, and cloud resources without triggering authentication controls. The attack bypasses MFA entirely because the token represents proof of already completed authentication.

# Key Takeaways

- Token theft bypasses passwords and MFA entirely by stealing authentication tokens issued after successful login, granting immediate access without triggering security alerts
- OAuth and session tokens function as bearer credentials, meaning whoever possesses the token can use it regardless of authentication method or location
- Refresh tokens survive password resets and MFA invalidation, allowing attackers to maintain persistent access even after incident response actions
- AiTM phishing attacks increased 146% over the past year, with nearly 40,000 token theft incidents detected daily across Microsoft environments
- Token theft accounted for 31% of Microsoft 365 breaches in 2025, making it the primary attack vector surpassing traditional credential compromise
- Detection requires behavioral monitoring focused on IP deviation, User-Agent changes, geographic anomalies, and data access patterns rather than authentication logs
- Traditional security controls fail against token abuse because stolen tokens are valid tokens that bypass SSO, MFA, and Conditional Access policies
- Supply chain token compromise amplifies impact, with single vendor breaches extending attacker access through OAuth integrations into hundreds of customer environments
# What is Token Theft and Why Does It Bypass MFA?

Token theft is the extraction and unauthorized use of authentication tokens that systems issue after successful login. These tokens serve as proof that a user has already passed authentication checks, including passwords and MFA prompts. Once issued, tokens accompany subsequent requests, eliminating the need to re-authenticate for every action.

The critical vulnerability lies in how tokens function as bearer credentials. Most SaaS applications implement OAuth tokens and session cookies as bearer tokens, something like a key. Whoever has this key can use it. The system trusts the token itself, not the person or device presenting it.

This design creates a fundamental bypass of MFA. Multi-factor authentication protects the initial login event, requiring users to prove their identity through something they know (password), something they have (authenticator app), or something they are (biometric). But once authentication succeeds and the system issues a token, that token contains no biometric data, no continuous verification, and no requirement for second factor re-validation.

## Why traditional MFA fails against token theft:

- MFA validates identity at login, not during subsequent token-based requests
- Tokens issued after MFA completion bypass all future authentication challenges
- Session cookies and OAuth tokens contain no embedded MFA requirements
- Systems trust the token as proof of completed authentication

## Attackers never trigger MFA prompts because they're using valid, already-authenticated sessions

The Salesloft-Drift incident demonstrated this risk at scale. OAuth tokens stolen from a vendor breach extended attacker access through trusted connections into over 700 customer environments. The CircleCI compromise similarly exposed API tokens that attackers leveraged to access customer systems without ever encountering authentication barriers.

Organizations believe their SSO and MFA implementations protect them from unauthorized access. In reality, authentication tokens function independently of those controls. Once issued, tokens become the primary access mechanism, and whoever has the token can use it, no questions asked.

## How Attackers Steal Authentication Tokens
Attackers employ multiple techniques to extract tokens from browsers, applications, devices, and network traffic. Understanding these methods reveals why token theft has become the dominant attack vector in 2026.

Adversary-in-the-Middle (AiTM) Phishing
AiTM attacks position a malicious proxy between the victim and the legitimate authentication service. When users enter credentials and complete MFA on what appears to be a real login page, the attacker's infrastructure captures both the authentication data and the session tokens issued upon successful login.

## The AiTM attack flow:

1. Victim receives phishing email with link to attacker-controlled proxy
2. Proxy presents legitimate Microsoft/Google/Okta login interface
3. User enters credentials and completes MFA challenge
4. Proxy relays credentials to real authentication service
5. Real service validates credentials and issues session token
6. Proxy intercepts token before forwarding user to legitimate service
7. Attacker now possesses valid session token with full access
   
AiTM attacks increased 146% over the past year, with nearly 40,000 incidents detected daily. Over 90% of credential compromise attacks are expected to involve sophisticated automated phishing kits by the end of 2026.

The sophistication lies in real-time coordination. Modern phishing kits automate OTP interception and relay within the 30-60 second validity window, making detection through traditional failed login monitoring completely ineffective.

## OAuth Device Code Flow Exploitation

A campaign active since December 2025 exploits the OAuth 2.0 Device Authorization Grant flow through phishing that directs victims to the legitimate Microsoft domain (microsoft.com/devicelogin) to enter attacker-supplied device codes.

The attack bypasses MFA by occurring after successful user authentication and MFA completion. Attackers poll the token endpoint to capture OAuth access and refresh tokens once the victim authorizes the device code. This technique is highly concentrated in North America, with 44%+ of victims in the US, specifically targeting tech, manufacturing, and financial services sectors.

## ConsentFix: Browser-Based OAuth Phishing

ConsentFix represents a sophisticated evolution in token theft, targeting Microsoft Entra ID through social engineering that tricks users into providing OAuth authorization codes via drag-and-drop or copy-paste actions. The technique requires no password theft and triggers no MFA prompts.

Attackers redeem stolen authorization codes through POST requests to Microsoft's token endpoint within a 10-minute window, granting delegated access to the victim's account. The attack bypasses Conditional Access policies because the initial sign-in is legitimate and token redemption occurs from the attacker's infrastructure, making traditional prevention controls ineffective.

## Token Extraction from Browser Storage

Malware and browser extensions extract tokens directly from browser local storage, session storage, and cookie databases. Modern browsers store authentication tokens in easily accessible locations that malicious code can read without elevated privileges.

## Common token storage locations:

- Browser local storage (OAuth tokens, API keys)
- Session storage (temporary session tokens)
- Cookie databases (session cookies, refresh tokens)
- Browser extension storage (application-specific tokens)
- IndexedDB and WebSQL databases

Attackers also target application configuration files, code repositories where developers accidentally commit tokens, log files containing authentication data, and backup systems that preserve token-containing files.

## Supply Chain Token Compromise

Third-party integrations and vendor breaches create invisible attack paths. When attackers compromise a SaaS vendor, they gain access to OAuth tokens that connect the vendor's systems to customer environments. These tokens grant inherited permissions that extend far beyond the compromised vendor's direct access.

The risk amplifies through SaaS-to-SaaS lateral movement. A single compromised OAuth integration can provide access to multiple downstream systems, each with its own set of permissions and data access rights.

## Why Stolen Tokens Are More Dangerous Than Stolen Passwords
Token theft fundamentally differs from credential compromise in ways that make it more dangerous and harder to detect.

## Tokens Survive Incident Response Actions
When security teams respond to suspected account compromise, standard procedures include password resets and MFA invalidation. These actions stop attackers who rely on stolen credentials. They do nothing against attackers who possess stolen tokens.

Refresh tokens persist even after:

- Password changes
- MFA device removal
- Account suspension (in some implementations)
- SSO session termination
- User logout actions
  
Refresh tokens are especially risky because, like service accounts, they operate outside traditional login flows. They automatically generate new access tokens without requiring re-authentication, providing attackers with long-lived persistence until explicit revocation.

## Invisible to Traditional Security Controls
Token theft and replay attacks do not trigger the security alerts organizations rely on for breach detection:

- No failed login attempts
- No MFA prompt failures
- No impossible travel alerts (if attacker uses VPN matching user location)
- No new device registration
- No password change notifications
  
Successful login events appear identical whether legitimate or compromised. Attackers maintain access through session tokens until explicit revocation, often remaining undetected for extended periods.

Traditional security tools struggle to distinguish legitimate usage from malicious activity because stolen tokens are valid tokens. The authentication was real, the MFA challenge was completed, and the token issuance followed proper protocols.

## Broader Access Than Credentials

OAuth tokens often grant broader permissions than the user who authorized them intended. When users click "Allow" on third-party application requests, they rarely understand the full scope of access they're delegating.

OAuth tokens can provide:

- Read/write access to email and calendar
- Full access to file storage (OneDrive, Google Drive)
- Permission to send email as the user
- Access to contacts and organizational data
- Ability to create and modify SharePoint content
- API access to connected SaaS applications
- Once attackers possess these tokens, they can perform any action within the granted scope without additional authorization.
- The attack surface extends beyond a single compromised account to every system and integration that account connects to.

## The Token Theft Kill Chain in SaaS Environments

Understanding how attackers operationalize stolen tokens reveals the full scope of risk in modern cloud environments.

### Phase 1: Token Acquisition
Attackers obtain tokens through phishing campaigns, malware deployment, browser compromise, or supply chain breaches. The initial compromise may target a single user or an entire vendor organization.

### Phase 2: Token Validation and Enumeration
Once attackers possess a token, they validate its permissions and enumerate accessible resources. This reconnaissance phase determines what data they can access, what actions they can perform, and what lateral movement opportunities exist.

#### Enumeration activities:

- Testing token scope against various API endpoints
- Identifying connected SaaS applications
- Mapping OAuth integration relationships
- Discovering service accounts and non-human identities
- Locating high-value data repositories
  
### Phase 3: Lateral Movement Through Integrations
Attackers leverage OAuth integrations as invisible bridges between SaaS applications. A token granting access to one system often provides indirect access to connected systems through trusted relationships.

This SaaS-to-SaaS lateral movement bypasses network security controls entirely. The connections exist at the application layer, authenticated through OAuth tokens that security teams often have no visibility into.

### Phase 4: Persistence Establishment
Attackers establish persistence through multiple mechanisms:

- Creating additional OAuth applications with broad permissions
- Generating new API tokens with extended expiration
- Adding authentication methods to compromised accounts
- Establishing webhook listeners for ongoing data exfiltration
- Deploying malicious browser extensions for continued token theft
  
### Phase 5: Data Exfiltration and Impact
With persistent access established, attackers execute their objectives. Token theft enables various malicious activities depending on attacker motivation:

- Intellectual property theft through API-based data extraction
- Business email compromise using delegated send permissions
- Ransomware deployment through SaaS application access
- Supply chain compromise by pivoting to customer environments
- Regulatory compliance violations through unauthorized data access
- Detection Strategies for Token Theft and Abuse

Traditional authentication monitoring fails to detect token theft because the authentication was legitimate. Detection requires behavioral analysis that identifies anomalies in how tokens are used, not how they were obtained.

#### Behavioral Indicators of Token Abuse
Security teams must monitor for deviations from normal user behavior patterns:

- IP address and geolocation anomalies:

  - Token usage from unexpected geographic locations
  - ASN (Autonomous System Number) changes indicating different network providers
  - Rapid geographic transitions impossible for human travel
  - Access from residential proxies or VPN services
  - User-Agent and device fingerprint changes:

- Different browser or application User-Agent strings
  - Operating system mismatches from established patterns
  - Device fingerprint deviations
  - Simultaneous sessions from incompatible device types
  - Data access pattern anomalies:

- Unusual volume of API calls
  - Access to resources outside normal scope
  - Bulk download activities
  - Systematic enumeration of files or emails
  - Off-hours access inconsistent with user timezone
  - Permission and scope anomalies:

- Token usage exceeding typical permission boundaries
- Access to applications rarely used by the account
- OAuth scope expansion requests
- Service account token usage from unexpected sources
- Technical Detection Mechanisms
- Organizations need detection capabilities that go beyond traditional SIEM correlation:

- Token lifecycle monitoring:
  - Track token issuance, usage, and expiration
  - Alert on tokens used beyond expected lifetime
  - Identify refresh token usage patterns
  - Monitor token revocation and reissuance frequency
  - OAuth application behavior analysis:

- Baseline normal OAuth application access patterns
  - Detect new or suspicious OAuth grants
  - Monitor for consent phishing indicators
  - Track OAuth application permission changes
  - Session anomaly detection:

- Identify concurrent sessions from different locations
  - Detect session token reuse across multiple IP addresses
  - Monitor for session hijacking indicators
  - Track session duration and activity patterns
  
For organizations seeking comprehensive visibility into token-based threats, specialized platforms like those detailed in our ITDR (Identity Threat Detection and Response) guide provide behavioral detection specifically designed for the hidden layer between SaaS applications.

### Prevention and Mitigation Strategies
Preventing token theft requires a multi-layered approach that acknowledges MFA alone is insufficient protection.

#### Implement Token-Binding and Device-Bound Credentials
Token-binding cryptographically ties tokens to specific devices, preventing stolen tokens from functioning on attacker infrastructure. When tokens are bound to device hardware, attackers cannot replay them from different systems.

#### Device-bound credential implementations:

- Windows Hello for Business
- FIDO2 hardware security keys
- Platform authenticators (TPM-backed)
- Certificate-based authentication

Organizations should enforce device-bound tokens where possible, particularly for privileged accounts and access to sensitive resources.

#### Deploy Phishing-Resistant MFA
While phishing-resistant MFA doesn't eliminate token replay attacks, it significantly reduces the attack surface by preventing initial credential compromise. Hardware-bound authenticators make AiTM phishing substantially more difficult.

#### Phishing-resistant MFA methods:

- FIDO2 security keys
- Platform authenticators (Windows Hello, Touch ID)
- Smart cards with PKI
- Certificate-based authentication

Traditional MFA methods vulnerable to AiTM attacks include SMS codes, authenticator app OTPs, and push notifications that users can approve without verification.

#### Enforce Conditional Access Policies
Conditional Access policies add contextual requirements beyond authentication:

#### Require compliant devices for access
- Enforce trusted network locations
- Mandate specific operating system versions
- Require approved client applications
- Block legacy authentication protocols

However, security teams must understand that Conditional Access evaluates at authentication time. Once a token is issued, subsequent token usage may not re-evaluate all conditions. This creates opportunities for attackers to bypass policies through token replay.

#### Implement Continuous Access Evaluation (CAE)
CAE enables near real-time revocation of access tokens when security-relevant events occur:

- User account disabled or deleted
- Password changed
- High-risk user detected
- Token issuing tenant policy changes
- Critical security events

CAE reduces the window of opportunity for attackers using stolen tokens by enabling immediate revocation rather than waiting for token expiration.

#### Token Hygiene and Lifecycle Management
Organizations must treat tokens as credentials requiring active management:

Token management best practices:

- Minimize token lifetime and scope
- Implement automatic token rotation
- Revoke unused or stale tokens
- Audit OAuth application grants regularly
- Remove dormant integrations
- Monitor non-human identity token usage

Security teams should conduct regular OAuth application audits to identify overprivileged grants, unused integrations, and suspicious third-party applications. Our guide on integration security provides frameworks for managing these toxic combinations.

#### Network-Based Protections
While tokens bypass many network controls, certain protections still provide value:

- Deploy TLS inspection to detect credential harvesting
- Monitor for connections to known phishing infrastructure
- Block access to suspicious OAuth authorization endpoints
- Implement DNS filtering for phishing domains
- Use browser isolation for high-risk users
- Incident Response for Token Compromise

When token theft is suspected, password resets alone leave attackers with continued access. Effective incident response requires:

- Immediate token revocation: Revoke all active sessions and OAuth grants for compromised accounts
- Refresh token invalidation: Force refresh token rotation or revocation
- OAuth application review: Audit and remove suspicious OAuth grants
- Device compliance check: Verify all registered devices are legitimate

**Behavioral investigation**: Analyze access logs for attacker activity patterns
**Lateral movement assessment**: Identify what integrations and connected systems may be compromised
Organizations experiencing vendor breach notifications should assume OAuth tokens may be compromised and proactively revoke integrations with affected vendors.

#### The Future of Token-Based Attacks
Token theft will continue evolving as attackers adapt to defensive improvements and organizations increase cloud adoption.

#### Emerging Attack Vectors
**AI-powered phishing**: Attackers are leveraging AI to create more convincing phishing campaigns with real-time adaptation to user responses, making AiTM attacks harder to recognize.

**Supply chain token harvesting**: As organizations implement better endpoint protection, attackers increasingly target vendors and integration partners where security controls may be weaker.

**API token exploitation**: The proliferation of API-first architectures creates new token types and storage locations that attackers can target beyond traditional OAuth flows.

**Browser extension compromise**: Malicious or compromised browser extensions provide persistent access to authentication tokens across multiple SaaS applications.

#### Defensive Evolution Required
Organizations must shift from authentication-centric security to behavior-centric monitoring. The question is no longer "did this user authenticate correctly?" but rather "is this token being used in ways consistent with legitimate user behavior?"

This requires security platforms that understand normal patterns for:

- Token usage across applications
- Data access behaviors
- Integration relationships
- Geographic and network context
- Temporal patterns

The behavioral detection approaches that analyze how tokens are used, not just how they were issued, represent the next generation of identity security.

#### Regulatory and Compliance Implications
As token theft becomes the dominant attack vector, regulatory frameworks are beginning to require specific token security controls:

- Mandatory token lifecycle management
- Required token-binding for sensitive data access
- Breach notification triggers for token compromise
- Audit requirements for OAuth grants and integrations
- Documentation of non-human identity inventory
- Organizations in regulated industries should proactively implement token security controls ahead of formal requirements.

#### Moving Beyond Password-Centric Security
Token theft fundamentally challenges the assumption that MFA provides comprehensive protection against unauthorized access. While MFA remains critical for preventing initial credential compromise, it offers no protection once authentication succeeds and tokens are issued.

Security teams must recognize that tokens are the new perimeter. In cloud-first, SaaS-heavy environments, tokens grant access to data and systems without requiring credentials. Whoever has the token has access, regardless of how they obtained it.

#### Immediate actions for security teams:

- Audit OAuth grants and integrations to identify overprivileged or unused tokens
- Implement behavioral monitoring that detects anomalous token usage patterns
- Deploy phishing-resistant MFA to reduce initial compromise risk
- Enforce device-bound credentials for privileged access
- Enable Continuous Access Evaluation to reduce token lifetime
- Update incident response playbooks to include token revocation procedures
- Conduct token hygiene reviews quarterly to remove stale credentials

The shift from password theft to token theft represents a fundamental evolution in how attackers operate. Organizations that continue to focus exclusively on authentication security while ignoring token lifecycle management and behavioral monitoring will find themselves one integration away from compromise.

For security teams seeking to understand the full scope of identity-based threats in modern SaaS environments, our comprehensive guide to SaaS identity attacks provides detailed analysis of attack techniques and defensive strategies.

The uncomfortable truth is that tokens have become more valuable than passwords. They bypass MFA, survive password resets, and often grant broader access than credentials alone. Security programs must evolve to match this reality, implementing controls that protect tokens as the bearer credentials they are and detecting abuse through behavioral analysis rather than authentication monitoring.
