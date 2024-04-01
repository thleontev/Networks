## OSPF_topology 3: Authentication, Virtual Links, and Optimization

### Authentication

OSPF Authentication shoud be used to ensure that unauthorized routers cannot form neighbor relationships with legitimate routers

Authentication does not involve any encryption of OSPF Traffic

Authentication can be enabled at the area or the link level

Authentication can be one of three types:
- O: None (off)
- 1: Plan Text
- 2: MD5 Hash

Highly recommend using MD5 Authentication.
If your version of IOS support RFC 5709 (OSPFv2 HMAC-SHA Cryptographic Authentication), OSPFv2 can use HMAC-SHA algorithms.

Keychain management allows you to create and maintain keychains, which are sequences of keys. You can use keychains with features that secure communications with other devices
by using key-based authentication. The device allows you to configure multiple keychains.

### Virtual Links
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


### Timers

One of the easiest ways to increase OSPF perfomance is to adjust its default timers

Remember that the default timers for many protocols including OSPF were created during another time of netwoking technologies and quipment

The fist time we can adjust determines how quickly we can detect the failure of a neighbor
- This is important so we can route around the issue if possible
- The two timers involved are the Hello and Dead timers
- It is recommended that these be kept at 4:1 ratio
- The default for these values varies based on the OSPF network type
  - Most modern network  types will defalut to 10/40
  - Some tranditional WAN network types default to 30/120 (point-to-multipoint)
- Timers are set per interface
- Default values should rarely be used

The other timers on the router can also be optimized
- Manually
- Automatically using “routing-default-optimeze”
  - On by default in IOS XE 16.5.1b

### NSF & NSR
Non-Stop Forwarding (NSF) and Non-Stop Routing (NSR) are both technologies to aid with stability during RP switchovers

NSF - Allows forwading to continue using known routes. There are Cisco and IETF versions.

Routers fall into three categories:
- NSF Capable
  - Dual RP
  - Relearns OSPF neighbors without causing neighbor resets
  - Requires the LSDB
  - Signal (link-local LSA) other routers that a failover is occuring
- NSF Aware
  - Recognizes the signal from the failing router
  - Preserves the OSPF LSDB and routing table
  - Rebuilds neighborship without a reset
- Non-NSF Aware
  - Do not participate in NSF
  - After the failover OSPF must be reestablished as normal

NSR - Allows Failover, process restart, or ISSU without effecting neighbors
Use warm standby extensions
- Standby OSPF process is run on the standby RP

NSR alleviates the requirement for NSF

