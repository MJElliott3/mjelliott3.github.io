<!--
---
layout: post
title: "Building My Proxmox Homelab: A Sandbox for Networking and Security"
date: 2026-09-30
feature: http://i.imgur.com/Ds6S7lJ.png
categories: [Firewall, Networking]
project: false
---
 
*Part of my [Proxmox Homelab](https://michaelje.com/homelab-overview/) project.*
 
[ONE-PARAGRAPH HOOK: what you set out to do and the result. Example: "The first thing my homelab needed was an edge: one VM that decides what can talk to what. This is how I built a virtual pfSense firewall on Proxmox, what broke along the way, and what the network looks like now."]
 
## The Goal
 
[Why pfSense, and why virtualized? Cover: network segmentation between lab zones, keeping pentesting traffic away from your home network, and learning real firewall rule design. Mention anything you wanted from the design, such as an isolated pentest segment or controlled internet access.]
 
I followed [Ben Heater's Proxmox pfSense guide](https://benheater.com/proxmox-lab-pfsense-firewall/) as the base, and changed the private IPv4 range, and used Parrot Security OS instead of Kali Linux.
 
## Network Design
 
[Describe the plan before the clicking: which networks exist and why.]
 
| Network | Purpose | Subnet |
|---------|---------|--------|
| WAN | Upstream to home router | [DHCP / reserved address, no need to publish it] |
| LAN | [e.g. management / trusted lab hosts] | [e.g. 10.0.0.0/24] |
| [OPT1 name] | [e.g. internet-only egress] | [subnet] |
| [OPT2 name] | [e.g. isolated pentest targets] | [subnet] |
 
![Network diagram of the lab](/assets/img/REPLACE-network-diagram.png)
*[Caption: a simple diagram of home router -> pfSense -> lab networks. Even a hand-drawn one works.]*
 
## Building the VM
 
**Resources:** [CPU cores / RAM / disk size you gave the pfSense VM]
 
**Two virtual NICs:**
- `net0` -> [your WAN bridge, e.g. vmbr0], the WAN side facing the home network
- `net1` -> [your lab bridge, e.g. vmbr1], the LAN side where the lab VMs live
![Proxmox hardware tab for the pfSense VM](/assets/img/REPLACE-proxmox-hardware.png)
*The pfSense VM's hardware tab in Proxmox, showing both NICs.*
 
[Notes worth including: VM ID, whether you set "Start at boot" and boot order (pfSense must come up before the VMs behind it), and how you got the ISO.]
 
## Installing and First Boot
 
[Short summary of the install: ISO, WAN/LAN interface assignment during setup, and the console menu. Keep it brief and focus on decisions rather than every "Next" click.]
 
![pfSense console menu showing assigned interfaces](/assets/img/REPLACE-console-menu.png)
*The console menu after assigning interfaces.*
 
[Then cover: setting the LAN IP, enabling DHCP, and any VLAN sub-interfaces you created and why.]
 
## Getting Into the Web GUI
 
[This is the part where most people get stuck. Describe how you reached the web UI from the WAN side, since pfSense blocks that by default. Be honest about how you handled it and what the security tradeoff is, and say what you did afterward, such as adding a WAN rule limited to your home subnet.]
 
[Mention that you changed the default admin credentials during setup, without sharing the new ones.]
 
![pfSense dashboard](/assets/img/REPLACE-pfsense-dashboard.png)
*The pfSense dashboard. [Blur any WAN IP / MAC info.]*
 
## Firewall Rules
 
[The heart of the post. Walk through your rule design as a policy, not a click-by-click list. A good structure is one short subsection per interface.]
 
**WAN:** [what you allow in, e.g. your home subnet -> lab LAN, and why nothing else]
 
**[Egress network]:** [typical pattern: allow the gateway, allow internet, block private ranges, block everything else. Explain *why* each rule exists.]
 
**[Isolated network]:** [what an isolated segment is allowed to reach, e.g. DNS and the pentest VM only]
 
**Management lockdown:** [if you added a rule to stop lab networks from reaching the firewall's own login page, explain it here.]
 
![pfSense firewall rules for one interface](/assets/img/REPLACE-firewall-rules.png)
*Rules on the [INTERFACE] tab. [One screenshot per interface works well.]*
 
## Putting a VM Behind It: Parrot Security
 
[Connect this to the second half of your update: you put a Parrot Security VM on the lab network and verified the firewall was doing its job.]
 
- **Network:** [which pfSense network/VLAN the Parrot VM is on]
- **Display:** [why you chose SPICE over the default noVNC console, e.g. smoother desktop, clipboard sharing, resolution handling, plus what you had to configure: display type, guest tools]
- **Verification:** [how you proved the rules work, e.g. confirmed internet access works, confirmed the VM can't reach your home network, tested from the home side]
![Parrot Security desktop running over SPICE](/assets/img/REPLACE-parrot-spice.png)
*Parrot Security running on the lab network over SPICE.*
 
## What Broke (and What I Learned)
 
[Don't skip this. It's what readers remember, and it shows real troubleshooting. Use 2-4 short stories in this shape: symptom -> what you checked -> cause -> fix.]
 
1. **[Problem, e.g. web GUI unreachable after setup]** [what you saw, what you tried, what fixed it]
2. **[Problem, e.g. packet loss / connection drops / DNS not resolving]** [same shape]
3. **[Problem]** [same shape]
## Where It Stands Now
 
- [x] pfSense running as the lab's edge firewall
- [x] Parrot Security VM behind it, accessed over SPICE
- [ ] [Anything you intentionally left for later, e.g. static routes from your home network, persistent firewall rules for other subnets, backups of the pfSense config]
## What's Next
 
With the edge in place, the next pieces are an **Active Directory** domain controller and Windows 11 workstation on their own segment, then a **SIEM** to collect logs from the firewall and the rest of the lab. [Adjust to your actual plan.]
 
---
 
*Building something similar, or stuck on a step? Reach out: michael@michaelje.com.*
