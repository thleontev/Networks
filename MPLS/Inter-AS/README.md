RFC 4364: BGP/MPLS IP Virtual Private Networks (VPNs)

Option A
- Section 10a
- VRF-to-VFR (Back-to-Back VRF)
- Simplest and most secure - limited scalability
- PEs/ASBRs ‘stitch’ together the VPN across Autonomous System (AS) boundaries
   - No MPLS/labels between provider/AS PEs
   - Static/IGP/eBGP (eBGP is recommended/best practice)
   - PEs/ASBRs treat each other as CE routers
- Physical or sub-interfaces
   - 1:1 ratio of VPNs to (sub) interfaces

Option B:
- ASBR-to-ASBR with MP-BGP
- Single MP-eBGP session for all VPNs
   - Unlike Option A there is NO per-VPN configuration on ASBRs
   - ASBRs no longer maintaining all individual VRF/VPN RIBs
- Label Distribution Protocol (LDP) not required
- Option B-1: Next-Hop-Self Approach
   - Next hop that is set to internal PE router!
   - Each ASBR is the next hop for routes learned from the other ASBR
   - Next hop value changed TWICE!
      - When route is advertised from one ASBR to other
      - From ASBR to its internal BGP peers
      - New VPN label generated
- Option B-2: Redistribute Connected Approach
   - Next hop and label only changed ONCE
   - Creation of a /32 host route
   - Redistribution of connected network into IGP
   - Load balancing considerations
- Option B-3: Multihop MP-eBGP Approach
   - Loopback addresses are used for the MP-eBGP peering between ASBRs
   - Load balancing across multiple links
   - MPLS/LDP is enabled between ASBRs
      - Security concerns
   - Can use either ‘next-hop-self’ or ‘redistribute connected’
   - Need to redistribute static subnets for ASBR loopback reachability

Option C:
- Section 10c
- Multihop MP-eBGP Between Route Reflectors (RR)
   - Most scalable option due in large part to RR
   - No VPNv4 routing information ASBRs
- Most commonly used by the same provider with multiple ASes
- Single connection between ASBRs
- Requires that the next hop of the VPNv4 routes not be changed
   - VPNv4 next hop IP must be known in both ASes
- IPs of RR are exchanged
- IPs of RR are redistributed into IGP
