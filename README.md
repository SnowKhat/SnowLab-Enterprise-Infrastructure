# SnowLab-Enterprise-Infrastructure


# USS SNOWLAB
### Enterprise Infrastructure Project

> "Every expert was once the person who clicked 'Next' for the first time."

This repository documents my journey from learning Windows Server to building and administering an enterprise Active Directory environment.

Rather than following a single tutorial, I'm treating this project as if I were responsible for deploying infrastructure inside a small business. Every configuration, mistake, and lesson learned is documented as the environment grows.


# Captain's Log

Stardate: 2026.188

The SnowLab infrastructure project officially entered service following the successful deployment of Active Directory Domain Services on Windows Server 2022.

The initial objective was to create a functioning enterprise domain that could be expanded over time into a realistic IT administration environment.

Current focus has shifted from deployment to administration as the next phase introduces Organizational Units, Group Policy, Windows clients, and PowerShell automation.


                   Internet
                       │
               Oracle VirtualBox
                       │
                ┌─────────────┐
                │ SNOWLAB-DC01│
                │Domain Control│
                └──────┬──────┘
                       │
               snowlab.local
                       │
          Future Enterprise Systems

          
Current Status

🟢 Domain Controller Online

🟢 Active Directory Operational

🟢 DNS Operational

🟡 Building Enterprise Structure

🔵 Windows Client Deployment (Next Mission)



Hostname

SNOWLAB-DC01

Operating System

Windows Server 2022

Role

Domain Controller

Forest

snowlab.local

Primary Services

• Active Directory
• DNS




[✓] Windows Server Installed



[✓] Static Networking



[✓] Active Directory Installed



[✓] Forest Created



[✓] DNS Configured



[✓] Domain Controller Promotion


### SNOWLAB-DC01 enters service

The server was successfully promoted to the first Domain Controller for the SnowLab forest.


[✓] Domain Authentication



[ ] Organizational Units



[ ] Windows Client



[ ] Group Policy



Operation 002

Enterprise Organizational Units

Operation 003

Windows 11 Domain Join

Operation 004

Group Policy Deployment

Operation 005

PowerShell Automation





One thing that surprised me during deployment was how dependent Active Directory is on DNS.

Before this project, I understood that DNS resolved hostnames. Building my own domain showed me that Active Directory relies on DNS for locating domain controllers, authentication, and service discovery.

Understanding why the DNS delegation warning appeared was one of the biggest lessons from this deployment.
