# Vulnerable Active Directory Homelab

This project builds a vulnerable Active Directory domain, then attempts to exploit these vulnerabilities via attack vectors such as LLMNR Poisoning, SMB Relay Attacks, Kerberoasting and Token Impersonation. 

Here you'll find a full walkthrough of building the lab from scratch, as well as attack demonstrations. 

This project was created while following TCM Security's **Practical Ethical Hacking** course. You can find the full course [here](https://tcm-sec.com/academy/practical-ethical-hacking/).

## Step 1: Building the Lab
Instructions for building this AD lab can be found in the [AD Lab Build](https://github.com/KC-SecEng/Vuln-AD-Lab/blob/main/AD_Lab_Build.md) file.

The full build includes 4 VMs:
* Kali Linux (attacker)
* Two Windows 11 user machines
* Windows Server 2025 (Domain controller)

### System Requirements
For this lab to run smoothly, you'll need at least **80GB of disk space & 16GB of RAM**.

## Step 2: Attack the Domain
The full list of attack vectors can be found in the [AD Attack Vectors](https://github.com/KC-SecEng/Vuln-AD-Lab/tree/main/AD_Attack_Vectors) folder.

We will gain access to the domain via these attack vectors:
* LLMNR/NBT-NS Poisoning
* SMB Relay
* Shell Access/PSExec
* IPv6 DNS Takeover
* Passback

Once the domain is compromised, we will then enumerate it using tools including:
* ldapdomaindump
* Bloodhound
* Plumhound
* PingCastle

After domain enumeration, we can continue attacking the compromised domain via these attacks:
* Pass Attack
* Kerberoasting
* Token Impersonation
* LNK File Attacks
* GPP/cPassword Attacks
* NTDS.dlt Dumping
* Golden Ticket Attacks
