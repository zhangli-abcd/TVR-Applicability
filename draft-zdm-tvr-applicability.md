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

TODO Introduction

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Overview of Tidal Network Use Case

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

{{I-D.ietf-tvr-schedule-yang}} defines TVR nodes and topology Yang modules. The node Yang module describes the
power state and interface attributes (such as available, bandwidth, and neighbor) of a node at different moments.
The topology Yang module describes the availability of nodes at different time points and the attributes of links
at different time points (such as link-available, bandwidth, delay, and destination node) in the topology. Both TVR
YANG modules can be used in Tidal networks to control node and interface attributes and distribution of time-varying
topology information.

## Encoding of Yang Model

How/what do we encode from YANG to the messenger protocol, which protocol, and why?

## NetConf Example

TODO

## RESTConf Example

TODO

# Time Synchronization

According to {{Section 3.1.3 of ?I-D.ietf-tvr-requirements}}, no matter whether the schedules are executed in a centralized
or distributed mode, a mechanism is required to keep the time synchronization between different devices.

Different time-variant scenarios may require different granularities of time synchronization. For example, the
period of traffic and topology changes in tidal networks is usually day or week. Therefore, a second-level time synchronization is enough.

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
consistent. There are at least two ways to make the content of the same schedule entry in different schedule databases consistent:

 - All the schedule entries are generated at a specific device;

 - Schedule entries are generated at different devices, but there is a synchronization mechanism to synchronize the schedule databases among devices.

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
The detailed format and fields of different types of schedule entries could reference to the definitions of corresponding YANG modules.

## Operations

Schedule database should support the add, update, and delete operations.

When adding or updating a schedule entry, the execution node needs to check whether resource conflicts exist between the
current schedule and existing schedules. If a conflict exists, the operation is failed.

Schedules are updated and deleted based on schedule IDs. Therefore, schedule IDs must be unique in a time-variant domain.
This can be handled, e.g., by a dedicated allocation agent within the time-variant domain.


# Operational Considerations

TODO

# Security Considerations

 What steps should we take to address security considerations (from the TVR Requirements I-D)?

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
