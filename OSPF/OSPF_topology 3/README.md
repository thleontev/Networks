##OSPF_topology 3: Authentication, Virtual Links, and Optimization

Virtual Links (VLs) are used to correct specific area issues
- Areas that are not connected to area 0
- Repairing a discontiguous area 0

Primary Issues:
- If there is no area 0, the router will not consider itself an ABR
- If there is an area 0, the router will consider itself an ABR
  - In this case, OSPF loop prevention will be in effect which says a type 3 LSA is not allowed to be used (routing bit will not be set) if it arrives on a non area 0 interface

VLs should be used to correct and fix temporary situations such as outages and mergers
VLs are only a control plane tunnel
VLs can only traverse a nornal area
VLs are always a member of area 0
VLs can only traverse one area at a time, but can be chained together

If Area 0 is authenticated don't forget the key on the virtual link
These area run as demand circuit where hellos are suppressed and the database is not refreshed
DNA (Do Not Age) is set on updates received across the virtual link
VLs are configured between routers IDs, not IP addresses
