# 🖖 USS SNOWLAB
### Enterprise Infrastructure Home Lab

> ## "The unknown isn't something to fear. It's something to explore."

USS SnowLab is my hands-on enterprise IT environment built to develop practical experience with Windows administration, networking, security, and technical support.

The project began with a single Windows Server 2022 virtual machine and the deployment of my first Active Directory Domain Controller. As the environment grows, each new mission introduces another part of the infrastructure and another opportunity to learn through hands-on administration, troubleshooting, and documentation.

Rather than following one tutorial from beginning to end, I am building SnowLab as an evolving small-business environment where I am responsible for the infrastructure.

---

# 🛰️ Enterprise Architecture

The environment is currently hosted in Oracle VirtualBox and centered around the `snowlab.local` Active Directory domain.

```text
                         Internet
                            │
                     Home Network
                            │
                    Oracle VirtualBox
                            │
                     USS SNOWLAB
                            │
                  ┌─────────┴─────────┐
                  │                   │
             SNOWLAB-DC01        Future Systems
             Windows Server
                  │
             snowlab.local
                  │
          Active Directory / DNS
