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

Time-Variant Routing (TVR) is a routing system that can support the predicted topology changes caused by internal or
external reasons. Typical use cases include mobility networks with moving nodes, resource constraints such as power,
and tidal traffic demand networks.
This document provides examples of how to implement the TVR scheduling capabilities for key use cases. It describes
which part of the TVR data model is used and why, and it outlines operational and security considerations when deploying
TVR-based technologies.

--- middle

# Introduction

The Time-Variant Routing (TVR) Working Group addresses a growing need in modern network environments where
predictable variations in topology - such as the restoration, activation, or loss of network elements, are
part of normal operations. This approach is essential in dynamic networks with mobile nodes, where links may
be frequently disrupted and re-established due to mobility or in networks with highly predictable traffic
patterns, where links may be powered down to conserve or reduce energy.

This document provides examples of implementing TVR scheduling capabilities in identified use cases. It
demonstrates the applicability of the TVR data model, methods for disseminating the TVR schedule, and the
necessary IETF ancillary technologies for network environments, such as time synchronization and policy,
that support TVR capabilities.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Use Case Examples

## Tidal Network Use Case

A tidal network is a typical scenario of an Energy Efficient case ({{Section 3 of ?I-D.ietf-tvr-use-cases}}).
The tidal network means that the volume of traffic in the network changes periodically, like the ocean tide.
These changes are mainly affected by human activities. Therefore, this tidal effect is obvious in
human-populated areas, such as campuses and airports.

In the context of a tidal network, if the network maintains all the devices up to guarantee a maximum
throughput all the time, often, power will be wasted on network resources that are not being used. Energy-saving methods may include the deactivation
of some or all components of network nodes. These activities have the potential to alter network topology and
impact data routing/forwarding in a variety of ways. Interfaces on network nodes can be selectively disabled
or enabled based on traffic patterns, thereby reducing the energy consumption of nodes during periods of low
network traffic.

## Dynamic Reachability Use Case

Dynamic Reachability referred to the scenarios where a node is placed on a mobile platform, the mobility of the
platform may cause changes to the topology of the network over time{{Section 4 of ?I-D.ietf-tvr-use-cases}}. As the relative mobility between and among nodes in the network and the impacts of the environment on the signal
propagation can be predicted, the associated loss and establishment of adjacencies can also be planned for.

The typical detailed use cases of Dynamic Reachability include but not limited to satellite network, predictable moving vessels and vehicles.

# Applicability of TVR Yang Model

## Network Model

According to the description of {{Section 3.1 of ?I-D.ietf-tvr-requirements}}, the scheduling generation locality
and execution locality may be centralized or distributed.

When the schedule is generated and executed in a centralized manner and within the same device, the changes are sent to routing applications
in wall-clock time via a management interface, which does not need to be delivered by the YANG model. This can be
implemented using the existing management plane technology. Therefore, this scenario is outside of the scope of this document.

When the schedule is generated and executed in a distributed manner, which means that each node generates its specific schedules. In this scenario, there is also no need for delivering the
schedule by the YANG model. Therefore, this scenario is also outside of the scope of this document.

When a schedule is generated in a centralized manner and executed in a distributed manner, the YANG module
needs to be used to deliver the schedule to the managed device. Depending on the location of the routing
application, the network model can be divided into two types: distributed and centralized.

### Centralized Model

In the centralized model, the network managing device generates and maintains schedules, the routing application
is deployed in the network controller, and the network devices execute the schedules and routing results.
The following figure shows the components of the centralized model.

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
is deployed in the network devices which also executes schedules and route calculation.

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

### Centralized Model {#centralized-model}

A centralized model involves the interaction between the managing device and network controller, the managing
device and network devices, and the controller and network devices.

The managing device needs to deliver node-specific schedules to network devices by TVR Schedule Node YANG
module {{Section 5.2 of ?I-D.ietf-tvr-schedule-yang}}, and the network devices need to report their own status
data to the management device.

The managing device needs to deliver schedules of the network topology to the network controller by the TVR Network
Topology YANG module{{Section 5.3 of ?I-D.ietf-tvr-schedule-yang}}, so that the routing application in the controller
can consider the impact of topology changes on routes when calculating routes.

The network controller should deliver the route calculation result to the network devices. The format of the routing
results depend on the protocols deployed (The typical protocols include BGP, PCEP, etc.). The routing result for a period in the future could be sent to
the network devices in wall-clock time or be packed and sent at some special points.

### Distributed Model {#distributed-model}

The distributed model only involves the interaction between managing devices and network devices.

The managing device must deliver node-specific schedules to network devices by TVR Schedule Node YANG Module and
deliver network topology schedules to all the network devices by TVR Network Topology Yang module for route
calculation. The network devices must report their status data to the managing device.
Editor’s note: In future versions of this document, we will provide a more detailed procedure for both the distributed
and centralized approach.

## Encoding of the YANG Model

The TVR data model {{?I-D.ietf-tvr-schedule-yang}} can manage network resources and topologies with scheduled
attributes. There are modules defined in the TVR data model, these are:

- The “ietf-tvr-schedule” module contains the schedule YANG definitions. This module uses groupings from {{?I-D.ietf-netmod-schedule-yang}} data model;
- The “ietf-tvr-topology” module defines a network topology with a time-variant availability schedule;
- The “ietf-tvr-node” module is to be used to manage the scheduled attributes of a single node.

To create a schedule, the following TVR data model objects and subsequent branches are used:

- ‘node-schedule’
- ‘interface-schedule’
- ‘attribute-schedule’

A TVR scenario example is provided below, where a wireless link is shut down for 12 hours, from 19:00 to 7am the next day.
The schedule is identified using a unique identifier that is conveyed in ‘schedule-id’, and the recurring schedule can be applied for multiple days using Coordinated
Universal Time (UTC).
A more detailed example of the json code is provided in this documents Appendix.

~~~

{
   "ietf-tvr-node:node-schedule":[
      {
         "node-id":1234567890,
         "node-power-schedule":{
            "power-default":true,
         },
         "interface-schedule":[
            {
               "name":"Wlan0",
               "default-available":false,
               "attribute-schedule":{
                  "schedules":[
                     {
                        "schedule-id":111111,
                        "recurrence-first":{
                           "utc-start-time":"2025-12-01T19:00:00Z",
                           "duration":43200
                        },
                        "utc-until":"2026-12-01T00:00:00Z",
                        "frequency":"ietf-schedule:daily",
                        "interval":1,
                        "attr-value":{
                           "available":true
                        }
                     }
                  ]
               }
            }
         ]
      }
   ]
}
~~~

The methods for disseminating and propagating the generated schedule are discussed in the following subsections.

## Management Protocols for TVR

The TVR data model is designed to be accessed via YANG-based management protocols such as NETCONF {{?RFC6241}} and RESTCONF
{{?RFC8040}}. This section discusses the applicability of these protocols for configuring time-variant network resources
using the TVR YANG data models.

NETCONF provides a robust mechanism for managing complex network configurations, particularly when transactional integrity
and operational consistency are required. Its ability to execute atomic transactions ensures that schedules involving
multiple resources are applied fully, preventing partial updates that could lead to configuration inconsistencies. This
feature is important for time-sensitive scheduling in TVR environments. Additionally, NETCONF supports the validation of
configurations prior to commitment, allowing operators to verify the correctness of schedules before they are applied.
It also includes rollback capabilities, such as restoring a previous configuration during scheduling errors.

In contrast, RESTCONF offers a simpler, stateless method for interacting with network devices, making it suitable for use
cases requiring lightweight, rapid configuration. RESTCONF utilizes a RESTful interface over HTTP, providing a streamlined
approach to network configuration and management. Therefore, RESTCONF may be advantageous in scenarios where quick adjustments
to schedules are needed or where integration with web-based or cloud-native systems is a priority.

Depending on the type of node in the TVR network, NETCONF would be the preferred protocol for large-scale, critical scheduling
operations requiring validation and rollback mechanisms. For smaller-scale or isolated scheduling tasks, RESTCONF provides an
efficient and straightforward option without the need for the transactional features offered by NETCONF. The choice of protocol
to use with the TVR YANG model should be driven by the specific requirements of the network environment and the complexity of
the scheduling tasks involved.

The security aspects of both NETCONF and RESTCONF, including their strengths and weaknesses, are discussed further in
{{security-considerations}} of this document.

# Time Synchronization

According to {{Section 3.1.3 of ?I-D.ietf-tvr-requirements}}, no matter whether the schedules are executed in a centralized
or distributed mode, a mechanism is required to keep the time synchronization between different devices.

Different time-variant scenarios may require different granularities of time synchronization. For example, the
period of traffic and topology changes in tidal networks is usually a day or week. Therefore, a second-level time
synchronization is enough. However, for the dynamic reachability scenarios, a fine-granularity time synchronization may be necessary, as the nodes may moving very fast in some cases(the moving speed of a low earth orbit satellite is more than 7900 m/s)

Existing clock synchronization protocols can be classified into hardware-based protocols and software-based protocols.
Hardware-based protocols often rely on dedicated hardware to ensure clock synchronization, such as Global Positioning
System (GPS) and Precision Time Protocol (PTP). Software-based protocols, on the other hand, synchronize
clocks through software packages running on systems, such as Network Time Protocol (NTP) {{?RFC5905}} and Simple Network
Time Protocol (SNTP) {{?RFC4330}}.

The security aspects of hardware-based and software-based time synchronization mechanisms are discussed further in {{security-considerations}} of this document.

## Hardware-based Time Synchronization Mechanisms

Hardware-based protocols typically have higher precision and stability, but also have higher cost due to the dedicated hardware. GPS and PTP are the typical hardware-based time synchronization mechanisms.

GPS provides a precise time synchronization service based on the signals transmitted by the GPS satellites. GPS can realize the micro-second level time synchronization among the devices installed with GPS reviver hardware.

PTP is a network protocol that complies with the IEEE 1588 standard and is used to implement high-precision time synchronization between network nodes. PTP implements time synchronization by transmitting synchronization messages between master and slave devices. Based on the hardware timestamp, the precision of time synchronization is much higher than that of NTP, and can reach the sub-microsecond level or even tens of nanoseconds.  When deploying PTP in TVR networks, the managing devices should be the master and the network devices and controller should be the slaves which get time from the master.

Both GPS and PTP can realize micro-second level time synchronization. Depending on the features of TVR network, the GPS would be the preferred mechanisms for
large-scale, high dynamic and open-air networks, especially networks with unreliable links as it does not require network links to exchange time information.
For the small-scale networks with stable links but have high-precision time synchronization requirements, the PTP is much preferred.

## Software-based Time Synchronization Protocols

Software-based protocols are simple and applicable to common hardware devices, but have lower precision (For
example, the NTP can realize the synchronization at tens of milliseconds level).

### NTP

NTP is fundamental for ensuring that TVR mechanisms, which depend on highly accurate timing, function as intended across
an entire network. Misalignment in time could lead to serious routing issues, including inefficiency in path forwarding,
instability in routing tables, and traffic outages.

NTP will be used to ensure:

 - Coordination of Planned Network Events;

 - Verification of TVR Data Model Time Stamps

 - Accurate Scheduling of Paths;

 - Fault Tolerance.

NTP uses a hierarchical structure of time sources. Each level of this hierarchy is termed a stratum. Generally, an NTP
server synchronized to an authoritative clock runs at stratum 1. Such NTP server functions as the primary time server
to provide clock synchronization for other devices on the network. Stratum 2 servers obtain time from stratum 1 servers,
stratum 3 servers obtain time from stratum 2 servers, and so on.

In TVR use cases, the managing device functions as a level-1 NTP server and synchronized to an authoritative clock
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

NTP is preferred in large-scale networks with reliable links and long-term changes, which dose not require a high-precision time synchronization.

### SNTP

SNTP is a subset of the NTP used to synchronize computer clocks in the Internet. It simplifies the complex NTP
synchronization function and has lower clock precision, but the synchronization precision still can be guarded under
seconds. SNTP is also preferred in large-scale networks with reliable links, long-term changes, and loose synchronization precision. In addition, it is more suitable for networks with limited resources than NTP.

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

These options will be discussed with the TVR Working Group, and agreed approaches will be documented in future versions
of this Internet Draft.

## Data Structure

{{!I-D.ietf-tvr-schedule-yang}} defines a TVR Node and TVR Topology YANG modules. The Node YANG module
includes node power schedule and interface schedule. The Topology YANG module includes nodes schedule and links schedule.

Based on the preceding four schedule types, the schedule database should contain four types of schedule entries in
different formats:

 - Node power schedule entry;

 - Interface schedule entry;

 - Node schedule entry;

 - Links schedule entry;

The detailed format and fields of different types of schedule entries could reference to the definitions of the corresponding
YANG modules.

Editor’s note: Code examples will be provided here in future versions of this document.

## Schedule Operations

This section provides general requirements for using the TVR schedule.

The schedule database should support the add, update, and delete operations.

When adding or updating a schedule entry, the execution node needs to check whether resource conflicts exist between the
current schedule and existing schedules. If a conflict exists, the operation should be failed.

Schedules are updated and deleted based on schedule IDs. Therefore, schedule IDs must be unique in a time-variant domain.
This can be handled, e.g., by a dedicated allocation agent within the time-variant domain.

Editor’s note: Future versions of this document will expand on the schedule operations requirements and best practices.

# Operational Considerations

Several operational considerations exist when using TVR techniques and data models in a network. This section provides
some high-level observations and more detailed sub-sections for specific consideration related to schedule dissemination,
execution, and recovery in case of failure to apply a schedule or partial change.

 - Coordinated Network Events: TVR often coordinates routing changes anticipating events like predictable low-traffic
periods or link downtimes (e.g., scheduled maintenance or traffic demand).

 - Accurate Scheduling of Paths: TVR schedule capable routers and network nodes will dynamically adjust forwarding paths
based on planned changes in link availability or network conditions.

 - Time-Stamped Data Models: TVR will require the use time-stamped data models (e.g., schedules for link changes or
availability windows) to make interface management decisions. This ensures that all TVR nodes interpret the timing of
events consistently and implement time-based policies correctly.

Therefore, network time accuracy and time-stamped data models are critical to ensure that coordinated network events
and scheduled path decisions across the network are based on a consistent time reference. Without accurate time sync,
nodes could apply different schedules, causing routing inconsistencies, path flapping, or packet loss.

## Schedule Execution Consideration

Schedules execution means that a component (e.g., device) undertakes an action (e.g., allocates and deallocates resources)
at specified time points. In a tidal network, the schedule execution indicates powering on/off specific network components
(such as interfaces or entire network devices) directly or by commands.

The schedule executor should understand the consequences of the schedule execution. The power on/off network components
usually affects the network topology, the addition and deletion of the topology need to be considered separately.

A link coming up or a node joining a topology should not have any functional change until the change is proven to be fully
operational. The routing paths may be pre-computed but should not be installed before all the topology changes are
confirmed to be operational. The benefits of this pre-computation appear to be very small. The network may choose to
not do any pre-installation or pre-computation in reaction to topological additions at a small cost of some operational
efficiency.

Topological deletions are an entirely different matter. If a link or node is to be removed from the topology, then the
network should act before the anticipated change to route traffic around the expected topological change. Specifically,
at some point before the planned topology change, the routing paths should be pre-computed and installed before the
topology change takes place. The required time to perform such planned action will vary depending on the exact network
and configuration. When using an IGP or other distributed routing protocols, the affected links may be set to a high
metric to direct traffic to alternate paths. This type of change does require some time to propagate through the
network, so the metric change should be initiated far enough in advance that the network converges before the actual
topological change.

Editor's Note: multi-manager scenarios need to be considered.

# Security Considerations {#security-considerations}

The integration of time-variant mechanisms in network operations presents distinct security challenges that require thorough
analysis to safeguard the network’s integrity, availability, and confidentiality. Networks that rely on time-sensitive data
for routing and forwarding decisions are particularly susceptible to attacks that exploit timing dependencies.

The "Security Considerations" section of {{?I-D.ietf-tvr-requirements}} outlines various threat vectors and categories specific
to time-variant environments.

## Denial-of-Service (DoS) Attack {#dos-attack}

In a time variant network, malicious actors could attack the network by disrupting or manipulating the time synchronization process. For example, an attacker could intentionally delay or corrupt time signals exchanged within the network, leading to routing errors and widespread denial-of-service (DoS) attacks.

This kind of attack could be addressed by the redundancy time synchronization mechanisms, for example, multiple NTP sources or multiple time synchronization protocols could be deployed in a TVR network. The network devices could guarantee the correctness of the time by checking whether the time signals from different sources or protocols.

In addition, the identification authentication is also an important way to protect the time signals being tampered by attackers. Some security extensions for time synchronization protocols (such as NTS (Network Time Security)) are recommended to be applied.

## Traffic Analysis and Path Prediction {#traffic-analysis}

In a time variant network, if time information is not adequately protected, attackers could conduct traffic analysis to infer routing decisions, network load, or usage patterns. The schedule ability could enable attackers to launch highly targeted attacks, such as selectively overloading certain links or intercepting sensitive communications.

This kind of attack could be addressed by the encryption of schedules and the authentication of managing devices. For the networks using NETCONF to deliver schedules, NETCONF over TLS{{?RFC7589}} is recommended to achieve the bidirectional authentication and encryption of YANG model data. RESTCONF supports TLS originally, so it can be deployed without additional configurations or modifications.

In addition, in time variant networks with centralized model{{centralized-model}}, the encryption of routing path information is also necessary to avoid the fake routing information. Considering the most typical protocols used to deliver the routing path information between controller and network devices are BGP and PCEP, and both are based on TCP. Therefore, the TLS is recommended to be applied for the conservation.

## Activity Identification and Privacy {#activity-identification}

In certain scenarios, precise time information exchanged within the network could be correlated with specific user or device behavior, inadvertently revealing private information.

This risk could also be mitigated by the solutions mentioned in {{activity-identification}}.

## Spoofing and Manipulation of Time Information

In a time variant network, if an attacker were to inject false or manipulated time data into the network, it could cause routers and devices to make incorrect decisions, potentially leading to traffic misrouting, network partitions, or inefficient use of resources.

This risk could also be mitigated by the solutions mentioned in {{dos-attack}}.

## Replay Attacks on Time-Sensitive Data

Time variant network data and schedule updates may be susceptible to replay attacks, which could cause network devices to act on outdated information, leading to inconsistent routing decisions, misaligned schedules, or security gaps. In particular, attackers could exploit replay attacks to force devices into outdated configurations or interfere with the synchronization of schedules across the network.

This kind of attack could be addressed by encrypting time signals, schedules and routing path data, and adding a unique number to the encrypted section of a packet. This has been implemented in existing protocols, for example, the NTS supports unique identifier extension field (EF) containing a random number, the TLS supports Message Authentication Code generated from sequence number.

## Compromised Time Sources

The reliance on external time sources for synchronization purposes presents a potential attack surface for time-variant networks. If a trusted time source, such as a GPS signal or an NTP server, is compromised, the attacker could feed erroneous time information to the entire network, disrupting its operation.

This kind of attack could be mitigated by implement multiple, redundant time sources or protocols and regularly verify the integrity of these sources. In addition, alerting mechanisms should be in place to detect significant deviations in time data that could indicate an attack.

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# Appendix A: Code Examples
{:numbered="false"}

## Code Examples for Tidal Network
{:numbered="false"}

Tidal networks usually need to manage the availability of some node or interfaces. {{ex-inf1}} shows the example of a
scheduling node that is powered on from 12 AM, December 1, 2025 to 12 AM, December 1, 2026 in UTC and its interface
named "interface1" is scheduled to be enabled at 7:00 AM and disabled at 1:00 AM, every day, from December 1, 2025 to
December 1, 2026 in UTC.

The JSON encoding is used only for illustration purposes.

~~~~
{
   "ietf-tvr-node:node-schedule":[
      {
         "node-id":12345678,
         "node-power-schedule":{
            "power-default":false,
            "schedules":[
               {
                  "schedule-id":111111,
                  "period-start":"2025-12-01T00:00:00Z",
                  "period-end":"2026-12-01T00:00:00Z",
                  "attr-value":{
                     "power-state":true
                  }
               }
            ]
         },
         "interface-schedule":[
            {
               "name":"interace1",
               "default-available":false,
               "default-bandwidth":1000000000,
               "attribute-schedule":{
                  "schedules":[
                     {
                        "schedule-id":222222,
                        "recurrence-first":{
                           "utc-start-time":"2025-12-01T07:00:00Z",
                           "duration":64800
                        },
                        "utc-until":"2026-12-01T00:00:00Z",
                        "frequency":"ietf-schedule:daily",
                        "interval":1,
                        "attr-value":{
                           "available":true
                        }
                     }
                  ]
               }
            }
         ]
      }
   ]
}
~~~~
{: #ex-inf1 title="An Example of Interface Activation Scheduling" artwork-align="center"}

## Code Examples for Dynamic Reachability Network
{:numbered="false"}

In a dynamic reachability network, the managing device usually needs to deliver the time-variant network topology to the network devices for them could react to
the predictable topology changes in time. {{ex-inf2}} shows the example of a topology with two nodes and one link. Due to the moving of these two nodes, the link between these two nodes will be interrupted from 10:00 AM to 10:05 and 10:10 AM to 10:20 AM, April 1, 2025 in UTC.

The JSON encoding is used only for illustration purposes.

~~~~
{
    "ietf-tvr-topology:topology-schedule": {
        "nodes": [
            {
                "node-id": 10000000,
                "available": {
                    "default-node-available": true,
                    "schedules": []
                }
            },
            {
                "node-id": 10000001,
                "available": {
                    "default-node-available": true,
                    "schedules": []
                }
            }
        ],
        "links": [
            {
                "source-node": 10000000,
                "source-link-id": 32,
                "available": {
                    "schedules": [
                        {
                            "schedule-id": 111111,
                            "period-description": "schedule down1",
                            "period-start": "2025-4-01T10:00:00Z",
                            "period-end": "2025-4-01T10:05:00Z",
                            "attr-vaule": {
                                "link-available": false
                            }
                        },
                        {
                            "schedule-id": 222222,
                            "period-description": "schedule down2",
                            "period-start": "2025-4-01T10:10:00Z",
                            "period-end": "2025-4-01T10:20:00Z",
                            "attr-vaule": {
                                "link-available": false
                            }
                        }
                    ]
                }
            }
        ]
    }
}
~~~~
{: #ex-inf2 title="An Example of Topology with Link Scheduling" artwork-align="center"}

