---
title: "Applicability of TVR YANG Data Models for Scheduling of Network Resources"
abbrev: "Applicability Statement"
category: info

docname: draft-zdm-tvr-applicability-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Routing"
workgroup: "Time-Variant Routing"
keyword:
 - schedule
 - YANG module
 - applicability


author:
-
  fullname: Li Zhang
  organization: Huawei
  email: zhangli344@huawei.com
-
   fullname: Jie Dong
   organization: Huawei
   email: jie.dong@huawei.com
-
   fullname: Mohamed Boucadair
   organization: Orange
   email: mohamed.boucadair@orange.com

contributor:
-
  fullname: Daniel King
  organization: Lancaster University
  country: United Kingdom
  email: d.king@lancaster.ac.uk
-
  fullname: Charalampos (Haris) Rotsos
  organization: Lancaster University
  email: c.rotsos@lancaster.ac.uk
-
  fullname: Peng Liu
  organization: China Mobile
  email: liupengyjy@chinamobile.com
-
  fullname: Tony Li
  organization: Juniper Networks
  email: tony.li@tony.li

normative:

informative:

--- abstract

TODO Abstract

--- middle

# Introduction

The Time-Variant Routing (TVR) Working Group addresses a growing need in modern network environments where
predictable variations in topology - such as the restoration, activation, or loss of network elements, are
part of normal operations. This approach is essential in dynamic networks with mobile nodes, where links may
be frequently disrupted and re-established due to mobility, or in networks with highly predictable traffic
patterns, where links may be powered down to conserve or reduce energy.

This document provides examples of implementing TVR scheduling capabilities in identified use cases. It
demonstrates the applicability of the TVR data model, methods for disseminating the TVR schedule, and the
necessary IETF ancillary technologies for network environments, such as time synchronization and policy,
that support TVR capabilities.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Applicability and Use Case Examples
## Tidal Network Use Case

Tidal network is a typical scenario of Energy Efficient case ({{Section 3 of ?I-D.ietf-tvr-use-cases}}).
The tidal network means that the volume of traffic in the network changes periodically like the ocean tide.
These changes are mainly affected by human activities. Therefore, this tidal effect is obvious in
human-populated areas, such as campuses and airports.

In the context of a tidal network, if the network maintains all the devices up to guarantee a maximum
throughput all the time, a lot of power will be wasted. The energy-saving methods may include the deactivation
of some or all components of network nodes. These activities have the potential to alter network topology and
impact data routing/forwarding in a variety of ways. Interfaces on network nodes can be selectively disabled
or enabled based on traffic patterns, thereby reducing the energy consumption of nodes during periods of low
network traffic. The following clauses will describe how to use TVR YANG model{{?I-D.ietf-tvr-schedule-yang}} in Tidal Network.

# Applicability of TVR Yang Model in Tidal Network

## Network Model

According to the description of {{Section 3.1 of ?I-D.ietf-tvr-requirements}}, the scheduling generation locality
and execution locality may be centralized or distributed.

When the schedule is generated and executed in a centralized manner, the changes are sent to routing applications
in wall-clock time via a management interface, which does not need to be delivered by YANG model. This can be
implemented by the existing management plane technology. Therefore, this scenario is outside of the scope of this document.

When the schedule is generated and executed in a distributed manner, there is also no need for delivering the
schedule by YANG model. Therefore, this scenario is also outside of the scope of this document.

When a schedule is generated in a centralized manner and executed in a distributed manner, the YANG module
needs to be used to deliver the schedule to the managed device. Depending on the location of the routing
application, the network model can be devided into two types: distributed and centralized.

### Centralized Model

In the centralized model, the network managing device generates and maintains schedules, the routing application
is deployed in the network controller, and the network devices execute the schedules and routing results.
The following figure shows the components of centralized model.

~~~
 +-----------------------------------------------------------------+
 |                        Managing Device                          |
 +-----------------------------------------------------------------+
            |                                           |
        Data Model                                  Data Model
            |                                           |
 +---------\|/----------+                     +--------\|/--------+
 |  Network Controller  |---Routing Results-->|  Network Devices  |
 +----------------------+                     +-------------------+
~~~
{: #ref-to-fig1  title="Components of Centralized Model"}

### Distributed Model

In the distributed model, the managing device generates and maintains schedules, the routing application
is deployed in the network devices who also executes schedules and route calculation.

~~~
 +-------------------------------------------------+
 |                  Managing Device                |
 +-------------------------------------------------+
                          |
                      Data Model
                          |
 +-----------------------\|/-----------------------+
 |         Managed Device (Network Devices)        |
 +-------------------------------------------------+
~~~
{: #ref-to-fig2  title="Components of Distributed Model"}

## Interaction Between Devices

### Centralized Model

Centralized model involves the interaction between the managing device and network controller, the managing
device and network devices, and the controller and network devices.

The managing device needs to deliver node-specific schedules to network devices by TVR Schedule Node YANG
module {{Section 5.2 of ?I-D.ietf-tvr-schedule-yang}}, and the network devices need to report their own status
data to the management device.

The managing device needs to deliver schedules of the network topology to the network controller by the TVR Network
Topology YANG module{{Section 5.3 of ?I-D.ietf-tvr-schedule-yang}}, so that the routing application in the controller
can consider the impact of topology changes on routes when calculating routes.

The network controller needs to delivers the route calculation result to the network devices(The format of the routing
results depending on the protocols deployed). The routing result for a period of time in the future could be sent to
the network devices in wall-clock time or be packed and send at some special points.

### Distributed Model

The distributed model only involves the interaction between managing device and network devices.

The managing device needs to deliver node-specific schedules to network devices by TVR Schedule Node YANG Module, and
deliver network topology schedules to the all the network devices by TVR Network Topology Yang module for route
calculation. The network devices need to report their own status data to the managing device.

## Yang Model for Tidal Network

The following provides a list of applicable YANG modules that can be used to exchange data between devices:

The "ietf-tvr-topology" YANG module in {{?I-D.ietf-tvr-schedule-yang}} is used to manage the node and deliver network
topology with time-varying attributes (e.g., node/link availability, link bandwidth, or delay).

The "ietf-tvr-node" YANG module in {{?I-D.ietf-tvr-schedule-yang}} which is a device model, is designed to manage a
single node with scheduled attributes (e.g., powered on/off).

## Encoding of Yang Model

The TVR data model {{?I-D.ietf-tvr-schedule-yang}} can be used to manage network resources and topologies with scheduled
attributes. There modules defined in the TVR data model, these are:

- Module ietf-tvr-schedule.yang contains the schedule YANG definitions. This module uses groupings from {{?I-D.ietf-netmod-schedule-yang}} data model;
- Module ietf-tvr-topology.yang defines a network topology with time-variant availability schedule;
- Module ietf-tvr-node.yang is to be used to manage scheduled attributes of a single node.

To create a schedule the following TVR data model objects and subsequent branches are used:

- node-schedule
- interface-schedule
- attribute-schedule

An example TVR scenario is provided below, where a a wireless link is shut down for 12 hours, from 19:00 to 7am the next day.
The schedule is identified using a schedule-id, and the recurring schedule can be applied for multiple days using Coordinated
Universal Time (UTC).

~~~
   module: ietf-tvr-node
     +--rw node-schedule
        +--rw node-id? "192.168.1.101"
        ...
        +--rw interface-schedule
           +--rw interfaces*
              +--rw name "Wlan0"
              ...
              +--rw attribute-schedule
                 +--rw schedules*
                    +--rw schedule-id "0123456789"
                    +--rw (schedule-type)?
                       +--:(period)
                         ...
                          +--rw period-start "2024-10-19T19:00:00"
                          +--rw time-zone-identifier? "Europe/Paris"
                          +--rw (period-type)?
                             ...
                             +--:(duration)
                                +--rw duration? "43200"
                                     ...
                                     +--rw attr-value
                          +--rw available? "false"

~~~

The methods for disseminating and propagating the generated schedule are discussed in the following subsections.

## Management Protocols for TVR

The TVR data model is designed to be accessed via YANG-based management protocols such as NETCONF {{?RFC6241}} and RESTCONF
{{?RFC8040}}. This section discusses the applicability of these protocols for configuring time-variant network resources
using the TVR YANG data models.

NETCONF provides a robust mechanism for managing complex network configurations, particularly when transactional integrity
and operational consistency are required. Its ability to execute atomic transactions ensures that schedules involving
multiple resources are applied in full, preventing partial updates that could lead to configuration inconsistencies. This
feature is important for time-sensitive scheduling in TVR environments. Additionally, NETCONF supports validation of
configurations prior to commitment, allowing operators to verify the correctness of schedules before they are applied.
It also includes rollback capabilities, enabling the restoration of a previous configuration in the event of scheduling errors.

In contrast, RESTCONF offers a simpler, stateless method for interacting with network devices, making it suitable for use
cases requiring lightweight, rapid configuration. RESTCONF utilizes a RESTful interface over HTTP, providing a streamlined
approach to network configuration and management. Therefore, RESTCONF may be advantageous in scenarios where quick adjustments
to schedules are needed or where integration with web-based or cloud-native systems is a priority.

Depending on the type of node in the TVR network, NETCONF would be the preferred protocol for large-scale, critical scheduling
operations requiring validation and rollback mechanisms. For smaller-scale or isolated scheduling tasks, RESTCONF provides an
efficient and straightforward option without the need for the transactional features offered by NETCONF. The choice of protocol
to use with the TVR YANG model should be driven by the specific requirements of the network environment and the complexity of
the scheduling tasks involved.

The security aspects of both NETCONF and RESTCONF including the strengths and weaknesses, are discussed further in Section 8
{{security-considerations}} of this document.

# Time Synchronization

According to {{Section 3.1.3 of ?I-D.ietf-tvr-requirements}}, no matter whether the schedules are executed in a centralized
or distributed mode, a mechanism is required to keep the time synchronization between different devices.

Different time-variant scenarios may require different granularities of time synchronization. For example, the
period of traffic and topology changes in tidal networks is usually day or week. Therefore, a second-level time
synchronization is enough.

Existing clock synchronization protocols can be classified into hardware-based protocols and software-based protocols.
Hardware-based protocols often rely on dedicated hardware to ensure clock synchronization, such as Global Positioning
System (GPS) and Precision Time Protocol (PTP). Software-based protocols, on the other hand, synchronize
clocks through software packages running on systems, such as Network Time Protocol (NTP) {{?RFC5905}} and Simple Network
Time Protocol (SNTP) {{?RFC4330}}.

Hardware-based protocols typically have higher precision and stability, but also have higher cost due to the dedicated
hardware. Software-based protocols are simple and applicable to common hardware devices, but have lower precision (For
example, the NTP can realize the synchronization at tens of milliseconds level). Considering the tidal network time
synchronize requirements, the software-based protocols is enough.

## NTP

NTP uses a hierarchical structure of time sources. Each level of this hierarchy is termed a stratum. Generally, an NTP
server synchronized to an authoritative clock runs at stratum 1. Such NTP server functions as the primary time server
to provide clock synchronization for other devices on the network. Stratum 2 servers obtain time from stratum 1 servers,
stratum 3 servers obtain time from stratum 2 servers, and so on.

In a tidal network, the managing device functions as a level-1 NTP server and synchronized to an authoritative clock
source. The network controller and network devices behave as clients to obtain accurate time from the managing device.
{{ref-to-fig3}} shows an NTP deployment scenario for obtaining clock from a GPS clock source.

~~~
                           +--------------------+
                           |  GPS Clock Source  |
                           +--------------------+
                                     |
               +--------------------\|/------------------+
Stratum 1      |             Managing Device             |
               +-----------------------------------------+
                     |                             |
                     |                             |
                     |                             |
          +---------\|/----------+       +--------\|/--------+
Stratum 2 |  Network Controller  |       |  Network Devices  |
          +----------------------+       +-------------------+

~~~
{: #ref-to-fig3  title="Deployment Case of NTP in Tidal Networks"}

## SNTP

SNTP is a subset of the NTP used to synchronize computer clocks in the Internet. It simplifies the complex NTP
synchronization function and is suitable for networks with limited resources and loose precision requirements.
Compared with NTP, SNTP has lower clock precision, but the synchronization precision still can be guarded under
seconds. Therefore, SNTP also meets the time synchronization requirements of tidal networks and can be used as
an alternative clock synchronization protocol.

# Schedule Database

The schedule database is used to store and maintain schedules, the database may be deployed on a managing device
and managed devices based on requirements.

The schedule source of the schedule database may be diversified, for example, configuration from an administrator
or YANG model from the management interface. The schedule entries of different databases may be different, but the
content of the same schedule entry in the schedule databases of different devices in the same domain must be
consistent. There are at least two ways to make the content of the same schedule entry in different schedule databases
consistent:

 - All the schedule entries are generated at a specific device;

 - Schedule entries are generated at different devices, but there is a synchronization mechanism to synchronize the
schedule databases among devices.

Option 1 is simplest and easy to implement. In a time-variant domain, the managing device may receive scheduling
requests and generate all schedule entries. Then the schedule entries are delivered to the necessary network devices
in the domain through the TVR YANG model.

Option 2 relies on advertisement mechanisms (such as routing techniques) to advertise the scheduling data generated
by itself to other devices. This could be achieved using extensions to existing routing schemes and techniques.

## Data Structure

{{ I-D.ietf-tvr-schedule-yang}} defines a TVR Node and TVR Topology YANG modules. The Node YANG module
includes node power schedule and interface schedule. The Topology YANG module includes nodes schedule and links schedule.

Based on the preceding four schedule types, the schedule database should contain four types of schedule entries in
different formats: Node power schedule entry, Interface schedule entry, nodes schedule entry, and links schedule entry.
The detailed format and fields of different types of schedule entries could reference to the definitions of corresponding
YANG modules.

## Operations

Schedule database should support the add, update, and delete operations.

When adding or updating a schedule entry, the execution node needs to check whether resource conflicts exist between the
current schedule and existing schedules. If a conflict exists, the operation is failed.

Schedules are updated and deleted based on schedule IDs. Therefore, schedule IDs must be unique in a time-variant domain.
This can be handled, e.g., by a dedicated allocation agent within the time-variant domain.

# Operational Considerations

## Schedule Execution Consideration

Schedules execution means that a component (e.g., device) undertakes an action (e.g., allocates and deallocates resources)
at specified time points. In a tidal network, the schedule execution indicates to power on/off specific network components
(such as interfaces or entire network devices) directly or by commands.

The schedule executor should understand the consequences of the schedule execution. The power on/off of network components
usually affects the network topology, the addition and deletion of the topology need to be considered separately.

A link coming up or a node joining a topology should not have any functional change until the change is proven to be fully
operational. The routing paths may be pre-computed but should not be installed before all of the topology changes are
confired to be operational. The benefits of this pre-computation appear to be very small. The network may choose to
not do any pre-installation or pre-computation in reaction to topological additions, at a small cost of some operational
efficiency.

Topological deletions are an entirely different matter. If a link or node is to be removed from the topology, then the
network should act before the anticipated change to route traffic around the expected topological change. Specifically,
at some point before the planned topology change, the routing paths should be pre-computated and installed before the
topology change takes place. The required time to perform such planned action will vary depending on the exact network
and configuration. When using an IGP or other distributed routing protocols, the affected links may be set to a high
metric to direct traffic to alternate paths. This type of change does require some time to propagate through the
network, so the metric change should be initiated far enough in advance that the network converges before the actual
topological change.

# Security Considerations

The integration of time-variant mechanisms in network operations presents distinct security challenges that require thorough
analysis to safeguard the network’s integrity, availability, and confidentiality. Networks that rely on time-sensitive data
for routing and forwarding decisions are particularly susceptible to attacks that exploit timing dependencies.

The "Security Considerations" section of {{?I-D.ietf-tvr-requirements}} outlines various threat vectors and categories specific
to time-variant environments. In this document, a tidal demand use case has been presented, highlighting the need for robust
security measures in the processes of generating, disseminating, and applying schedules to control tidal interfaces.
It is essential to assess security risks and implement mitigation strategies to ensure the security of both the network and
individual nodes throughout the scheduling process.

Future iterations of this document will provide detailed security techniques and best practices to address these challenges.

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# Appendix A: Code Examples
{:numbered="false"}

## Code Examples for Tidal Network

Provide a YANG and XML example here

