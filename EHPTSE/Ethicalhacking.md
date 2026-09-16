# Ethical hacking bulleted notes

## First principles

1. Sequencing of topics to understand theoretical implications and practical implementation
2. Contextualizing topics
3. Rigorous Definitions
4. Operationalize the definition through tools / framework / formula.
5. Outcome / data as an input to Decision, Tasks or another topic


## Concept - Ethical hacking

1. Differences between Hacking and Ethical hacking.
2. Differences between penetration testing and Ethical hacking.
3. What is social engineering in the context of Ethical hacking.

## Concept 2

### Definitions

1. Cyber asset - Connected IT assets accessible over the network. 
2. Cyber threats - Actions by a hacker that could compromise the intended security posture of a cyber asset.
3. Vulnerabilities - Inherent weaknesses in a cyber asset permitting exploitation by the cyber threats.

### Operationalizing the definitions

1. Cyber asset
   a. Categorize assets into Hardware, Software, Network, Application, Data
   b. Every asset presents itself as an attack surface to a hacker.
   c. Each category of asset specifically have a set of configuration and implementation procedures to achieve an intended security posture.
2. Cyber Threat
   a. Tactics , Techniques and procedures.
   b. Threat actor, Threat agent, Threat vector, payload and delivery
   c. Threat intelligence and threat hunting
   d. Threat knowledgebase / Frameworks
3. Vulnerabilities
   a. Vulnerability knowledgebase
   b. Co-ordinated disclosure of vulnerability by product developer / manufacturer.
   c. Patching

### Frameworks / tools for implementing the 'operationalized definitions'

1. Cyber asset
   a. Secure configuration checklists and procedures.
   b. Hardening of the asset.
2. Cyber Threat
   a. MITRE ATT&CK Framework knowledgebase.
   b. CEH framework
   c. Kill Chain Framework.
   d. Threat Modeling
4. Vulnerability
   a. CVE
   b. CERT-IN
   c. Vulnerability testing tools Nessus, Metasploit etc.,
   d. Vulnerability reports, patching and re-testing procedures.


## Concept - Threat Modeling

1. Threat Model - Threat model is essential for designing countermeasures.
2. Vulnerability report is essential for identifying patches for Known vulnerabilities.

### Steps to achieve Threat Model
1. Network view of the enterprise
2. Understand what possible attacks a hacker could plan. (Attacks relevant to the cyber
assets in the enteprise. Even though there is a threat out there , if it is not relevant
for an asset then it doesn’t matter)
3. Capturing What threats are relevant is the primary objective of a threat model.
4. Threat model further envisages to break down the Threat into, Threat actor, Payload,
Threat vector, Delivery and Threat target.

> Question: How to Operationalize the topic ?

#### Threat modeling framework STRIDE

STRIDE is a framework developed by Microsoft. It stands for six categories as follows:
1. Spoofing: Attacker hides their identity impersonating as another user or system.
2. Tampering: Nodification of data within networks or systems for malicious intent.
3. Repudiation: Performing transactions and later deny the action. If the enterprise doesn’t have non-repudiation controls then this type of attack happens.
4. Information disclosure: Leakage or exposure of data to unauthorized users.
5. Denial of service (DoS): Flooding system with genuine requests, thus preventing legitimate users from being able to use that system.
6. Elevation of privileges: Attacker takes advantage of a lack of control or vulnerability in such a way that they have privileges beyond the scope of what they should have.This allows the attacker to make changes and manipulate the system in some malicious way.

There exists, a number of tools for actually doing threat modeling, but it can be confusing and frustrating as to what to choose. The good news is you do not necessarily have to choose one—or any, for that matter. A simple spreadsheet or diagramming tool with documents could also be employed. Here are some of the most popular threat modeling tools available today:
1. Microsoft’s Threat Modeling Tool was designed to be simple and can be used by non-security experts. The tool works based on the STRIDE threat modeling classification. The tool can be downloaded from Microsoft’s site here:
https://docs.microsoft.com/en-us/azure/security/develop/threat-modeling-tool 

2. CVSS 3.0 is used for CVSS modeling and can score vulnerabilities identified from vulnerability assessments. It is provided by NIST at https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator.
    
## Concept - Reconnaissance techniques


### Reconnaissance: 

An information-gathering stage of a hacking process that collects data about the target system. The goal of reconnaissance is to identify as many potential attack vectors as possible. Data collected from reconnaissance can include security policies, network infrastructure (such as IP address range and subnet mask), employee contact details (such as email addresses and phone numbers) and host information (such as operating system type and version). All these can be used to find vulnerabilities.


### Footprinting: 

The process of accumulating data about a specific network environment, usually for the purpose of completing the footprinting process, mapping the attack surface, and finding ways to intrude into the environment. Fingerprinting can be categorized as either active or passive. Active fingerprinting is more accurate but also more easily detected. Passive fingerprinting is the act of identifying systems without injecting traffic or packets into the network. EC Council defines footprinting as a part of a larger process known as reconnaissance.



### Scanning: 

The identification of active machines that is accomplished by means of ping sweeps and port scans. Both aid in an analysis of understanding whether the machine is actively connected to the network and reachable. After all details of a network and its operations have been recorded, the attacker can then identify vulnerabilities that could possibly allow access or act as an entry point.

### Enumeration: 

A technique of extracting valid usernames, machine names, directory names, and so on, from a system. Enumeration gives attackers a lot of information that can be used to exploit vulnerabilities. Enumeration collects detailed information about a target system, such as the operating system and network details. Enumeration can be used in an offensive and defensive manner. Enumeration is important in ethical hacking because it gives hackers the necessary information that can be used to launch an attack.

### Tools used for Scanning and Enumeration

1. nmap
2. metasploit
3. Shodan
4. Dmitry


|Technique|Subtechnique|
|--|--|
|Active Scanning|Scanning IP Blocks|
|Vulnerability Scanning|Gather Victim Host Information|
|Client Configurations|Firmware,Hardware.Software|
Gather Victim Identity Information|Credentials,Email Addresses,Employee Names|
|Gather Victim Network Information|DNS,Domain Properties,IP Addresses,Network Security Appliances,Network Topology,Network Trust Dependencies|
|Gather Victim Org Information|Business Relationships,Determine Physical Locations,Identify Business Tempo,Identify Roles|
|Phishing for Information| Spearphishing Attachment,Spearphishing Link,Spearphishing Service,Search Closed Sources,Purchase Technical Data,Threat Intel Vendors,Search Open Technical Databases,,CDNs,Digital Certificates, DNS/Passive DNS,Scan Databases,WHOIS,Search Open Websites/Domains,Search Engines,Social Media,Search Victim-Owned Websites|

NOTE: The output of scanning and enumeration is a report leveraged by the hacker to plan the granular details of the attack such as vector, target, payload and expected outcome.


### Concept - Systems Hacking - Windows OS

Identified Attack vectors

1. Analysis of Address Resolution Protocol (ARP)
2. Simple Network Management Protocol (SNMP)
3. Server Message Block (SMB)
4. NetBIOS

Focus

1. What tactics could be achieved using the above vectors
2. What would be tools used
3. What is the payload
4. What is the intended outcome / data to be collected

### Concept - Systems Hacking - Gaining Access

Focus

1. Use social engineering techniques to guess user identity information
2. Next step is to guess the password through online technique (Brute force) or offline (Steal the password database)

### Roadmap

1. Privilege Escalation
2. Vulnerability scanning and report analysis and assessment.

Other hacking contexts

1. System hacking
2. Mobile platform hacking
3. Wireless hacking
4. Social Engineering
5. Cloud Hacking
6. Web Apps
7. Log monitoring, Telemetry and Incident response

