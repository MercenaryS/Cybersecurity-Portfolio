After Successful completion of [P1.1] moving to next important steps:
    Establishing Enterprise AD & DNS Baseline to provide core identity, authentication and name resolution services across lab environment.

This lab does cover some of the ADDS skills and group policy skills as one will navigate through specific settings and policies which will grant basic understanding of what,How and Why something is done by group policy and using AD in a real evironment.

For our lab The core directory infrastructure was deployed utilizing a Windows Server Domain Controller (Windows-DC) assigned the static IP address x.x.x.x within the Private-LAN subnet (x.x.x.x/24). Key implementation details and security baselines include:

    Active Directory Domain Setup: A dedicated enterprise domain (cyberlab.local) was provisioned, establishing centralized user identity management and domain-wide security policies.

    DNS Forward & Reverse Resolution: The Windows-DC was configured as the primary authoritative DNS server for internal endpoints, with reverse lookup pointer (PTR) records verified to correctly map IP addresses (such as x.x.x.x) back to hostnames (DC.cyberlab.local).

Refer:  "Enterprise AD & DNS Baseline\Take-a-peek\Windows-DC Server Manager Roles & IP Config.png" & "Take-a-peek\Client Workstation DNS & Domain Join Proof.png"


    Workstation Integration: Internal endpoints (Win-Desktop-01 at x.x.x.x and Win-Desktop-02 at x.x.x.x) were configured to point exclusively to the Domain Controller for DNS resolution and successfully joined to the domain.

Refer: "Take-a-peek\Active Directory Users and Computers (ADUC).png"

    Security Baselines & GPOs: Group Policy Objects were implemented to enforce stringent password complexity rules, account lockout thresholds, and advanced audit policies to capture essential security telemetry.

Refer:
    "Take-a-peek\Group Policy Management Console (GPMC) & Settings.png"
    "Group Policy Management Console (GPMC) & Settings-2.png"
    "Group Policy Management Console (GPMC) & Settings-3.png"

Visit Steps Document to have little clue of I implemented it.