# Microsoft Entra Application Proxy

> Readme: Integrate on‑premises applications with Microsoft Entra Application Proxy (formerly Azure AD Application Proxy).

## Table of Contents

- [TLDR](#tldr)  
- [Source and further reading](#source-and-further-reading)  
- [Why implement Microsoft Entra Application Proxy](#why-implement-microsoft-entra-application-proxy)  
- [Core use cases](#core-use-cases)  
- [Benefits (business and technical)](#benefits-business-and-technical)  
- [How it works (high level)](#how-it-works-high-level)  
- [Key concepts](#key-concepts)  
- [When to use Application Proxy vs alternatives](#when-to-use-application-proxy-vs-alternatives)  
- [Traditional scenarios (what orgs used before App Proxy)](#traditional-scenarios-what-orgs-used-before-app-proxy)  
- [Limitations and considerations](#limitations-and-considerations)  
- [Implementation checklist (quick)](#implementation-checklist-quick)  
- [TLDR (expanded with contrasts)](#tldr-expanded-with-contrasts)  
- [Example resources and links](#example-resources-and-links)

---

## TLDR {#tldr}

Entra Application Proxy publishes on‑prem web apps securely through Microsoft Entra so users can access them with SSO and Conditional Access — instead of opening inbound firewall ports or granting broad VPN access.

## Source and further reading {#source-and-further-reading}

- Microsoft Learn: Integrate on-premises apps using Azure AD Application Proxy  
  https://learn.microsoft.com/en-us/training/modules/implement-monitor-integration-of-enterprise-apps-for-sso/4-integrate-premises-apps-use-azure-active-directory-application-proxy

## Why implement Microsoft Entra Application Proxy {#why-implement-microsoft-entra-application-proxy}

- Publish on‑prem web apps to remote users and partners without exposing the apps directly to the internet.  
- Provide SSO and centralized access controls (Conditional Access, MFA) for legacy apps that don’t natively support modern auth.  
- Reduce operational overhead compared to managing VPNs, reverse proxies, or complex network changes.

## Core use cases {#core-use-cases}

- Remote employees need access to internal web apps (intranets, HR portals, reporting tools).  
- Partners or external contractors require scoped access to specific web apps without issuing direct network access.  
- Legacy/internal apps that lack modern authentication but can be fronted by Entra for SSO and access control.  
- Rapidly publishing apps during migrations or cloud adoption pilots without rehosting or reengineering the app.

## Benefits (business and technical) {#benefits-business-and-technical}

- **Identity‑first access**: enforce Conditional Access policies, MFA, and device compliance for on‑prem apps.  
- **No inbound firewall openings**: connectors make outbound connections to Microsoft so you avoid opening inbound ports.  
- **Simpler operations**: fewer VPN tunnels and less network configuration; central policy in Entra.  
- **SSO for legacy apps**: support single sign-on and modern session controls even for apps without native modern auth.  
- **Auditability & compliance**: per-user sign-in logs and access events linked to identities for investigations and reporting.

## How it works (high level) {#how-it-works-high-level}

1. Install the **Application Proxy connector** on one or more Windows servers in the internal network.  
2. The connector establishes an outbound TLS connection to the Entra Application Proxy service in Microsoft’s cloud (no inbound firewall rule required).  
3. In Entra, register an enterprise application and configure internal URL and external URL mapping; set preauthentication mode (Azure AD or Passthrough).  
4. When a remote user requests the external URL, Entra evaluates Conditional Access and preauthentication; approved traffic is sent through the connector to the backend app.

## Key concepts {#key-concepts}

- **Connector**: on‑prem agent that creates outbound tunnels to Entra and proxies approved requests.  
- **Preauthentication**: Entra can preauthenticate users (Azure AD) before forwarding requests, or allow passthrough authentication to the app.  
- **External URL**: the published endpoint users access.  
- **Internal URL**: the backend app address reachable by the connector.

## When to use Application Proxy vs alternatives {#when-to-use-application-proxy-vs-alternatives}

- Use Application Proxy when you need to publish web apps quickly with Entra SSO and Conditional Access but want to avoid opening inbound firewall ports — instead of placing users on a VPN or rehosting apps.  
- Prefer GSA or network tunnels when you need network‑level connectivity (L4) or non‑HTTP protocols — Application Proxy is HTTP(S) focused.  
- Use Entra App Proxy over a traditional reverse proxy when you want cloud‑based preauthentication, centralized Conditional Access, and simplified connector‑managed outbound connectivity.

## Traditional scenarios (what orgs used before App Proxy) {#traditional-scenarios-what-orgs-used-before-app-proxy}

- Reverse proxies / WAP: configure an on‑prem reverse proxy and open inbound ports; maintain TLS certs and external DNS.  
- Publish directly via DMZ with NAT/firewall rules — higher exposure and operational burden.  
- Broad VPN access: give remote users network access to reach internal apps (coarse trust, higher blast radius).

## Limitations and considerations {#limitations-and-considerations}

- Application Proxy primarily supports **web (HTTP/HTTPS)** apps — not arbitrary TCP/UDP protocols.  
- For high throughput or large file transfers, evaluate connector placement, HA, and network capacity.  
- Choose preauthentication mode carefully: **Azure AD preauth** provides the best security posture but may require app adjustments.  
- Plan connector redundancy (multiple connectors and servers) and keep connectors updated.

## Implementation checklist (quick) {#implementation-checklist-quick}

- Identify apps suitable for App Proxy (HTTP/HTTPS, internal URL reachable by connector).  
- Prepare servers for connector installation (Windows Server, outbound TLS allowed).  
- Configure enterprise application in Entra and set external/internal URLs.  
- Configure Conditional Access (MFA, device compliance, session controls) targeting the published app.  
- Test in a pilot, monitor sign-ins and connector logs, then roll out.

## TLDR (expanded with contrasts) {#tldr-expanded-with-contrasts}

- App Proxy grants **identity‑first SSO and Conditional Access** for on‑prem web apps — instead of opening inbound ports and relying on network perimeter controls.  
- App Proxy publishes HTTP(S) apps via **outbound connectors** — instead of requiring VPN tunnels or DMZ NAT.  
- App Proxy enforces preauthentication in Entra — instead of leaving authentication solely to legacy app forms or NTLM-only flows.

## Example resources and links {#example-resources-and-links}

- Microsoft Learn module: Integrate on-premises apps using Azure AD Application Proxy — https://learn.microsoft.com/en-us/training/modules/implement-monitor-integration-of-enterprise-apps-for-sso/4-integrate-premises-apps-use-azure-active-directory-application-proxy  
- Entra Application Proxy overview: https://learn.microsoft.com/en-us/azure/active-directory/app-proxy/

*Last updated: 2026 — Notes for integrating on‑prem apps with Microsoft Entra Application Proxy*
