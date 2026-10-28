---
title: "Applicability of TVR YANG Data Models"
abbrev: "Applicability Statement"
category: info

docname: draft-ietf-tvr-applicability-latest
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
 - time-variant routing

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
-
  fullname: Jing Wang
  organization: China Mobile
  email: wangjingjc@chinamobile.com


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
  organization: Hewlett Packard Enterprise
  email: tony.li@tony.li

normative:

informative:

--- abstract

Time-Variant Routing (TVR) is a routing system that is designed to accommodate predicted topology changes caused by internal or
external factors. Typical use cases include resource preservation networks, operating efficiency networks, and dynamic
reachability networks. TVR data model is defined to manage network resources and topologies with scheduled attribute.

This document describes when and which part of the TVR data model is applicable and why. It also outlines the necessary IETF ancillary technologies, operational and security considerations when deploying TVR-based technologies. Several examples of how to implement the TVR scheduling capabilities for key use cases are alos provided in this document.

--- middle

# Introduction

The Time-Variant Routing (TVR) Working Group addresses the need for network environments that experience predictable variations in topology such as the restoration, activation, or loss of network elements are
part of normal operations. This approach is essential in dynamic networks with mobile nodes, where links may
be frequently disrupted and re-established due to mobility or regular planned events. It is also essential in networks with highly predictable traffic
patterns, where links may be powered down to optimize energy use.

This document describes when and which part of the TVR data model is applicable and why. It also outlines the necessary IETF ancillary technologies, operational and security considerations when deploying TVR-based technologies.
Finally, it provides examples of implementing TVR scheduling capabilities in a set of use cases.

The examples assume YANG instance data encoding per {{!RFC7951}} for JSON and the TVR schedule YANG modules.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the following terms:

Managing Entity:
: A centralized entity responsible for generating
and maintaining TVR schedules.  The managing entity distributes
schedules to network controllers and/or managed entities using the
TVR YANG modules.  In some deployments, the managing entity may
also serve as the network controller.

Network Controller:
: An entity that receives topology schedules from the managing entity and performs TVR route computation based on time-variant network conditions.
The controller then distributes computed routing results to managed entities.

Managed Entity:
: A network device (e.g., router or switch) that
receives schedules and/or routing instructions, and executes them
according to the specified time windows.  Managed entities may
receive schedules directly from the managing entity or routing
results from the network controller.

# Applicability of the TVR YANG Model

The TVR YANG data model {{!I-D.ietf-tvr-schedule-yang}} defines three related modules: "ietf-tvr-schedule", "ietf-tvr-node", and "ietf-tvr-topology".
"ietf-tvr-schedule" is the basic module and can be used in the other two modules.

This section discusses the applicability of "ietf-tvr-node" and "ietf-tvr-topology" separately.

## Applicability of TVR Node YANG Module {#applicability-node-yang}

As specified in {{Section 3.2 of !I-D.ietf-tvr-schedule-yang}}, the "ietf-tvr-node" YANG module is a device model and designed to manage a single node with scheduled attributes. It is not necessary in all TVR use cases.

The applicability of the TVR node YANG module depends on whether scheduled attribute changes are centrally instructed or autonomously determined by the node:

- When attribute changes are driven by local environmental conditions or autonomous local decisions (node self-determined), the node does not need to receive these planned changes via the TVR node YANG module. For example,
when the distance between two nodes is too far to establish a connection, then the link is down. Another case is that when the weather is
not good and leading to the link degradation, then the nodes decide to disconnect the link.

- When attribute changes are centrally planned and instructed by the managing entity,
the node does not autonomously know the timing of attribute adjustments.
In this case, the scheduled attribute changes are delivered to the network device via the TVR node YANG module.

## Applicability of TVR Topology YANG Module {#applicability-topology-yang}

As specified in {{Section 5.3 of !I-D.ietf-tvr-schedule-yang}}, the "ietf-tvr-topology" YANG module describes a network topology
with a time-variant availability schedule. This YANG module is also not applicable for all TVR use cases.

According to the description of {{Section 2.1 of !I-D.ietf-tvr-requirements}}, the scheduling generation locality
and execution locality may be either centralized or distributed:

- When the schedules are generated and executed in distributed manner, which means that each node generates and executes its specific
schedules. In this scenario, the topology YANG module is not necessary, the devices can collect topology schedules by other means.
This scenario is outside of the scope of this document.

- When schedules are generated and executed in a centralized manner within the same device, the topology YANG module is not applicable.
This scenario is also outside of the scope of this document.

- When the schedules are generated and executed in a centralized manner but on different devices. For example, the schedule is
generated by the managing entity, and executed by the network controller. In this scenario, the scheduled topology changes
need to be sent to the execution device through the topology YANG module. This scenario is called "Centralized Scenario".

- When the schedules are generated in a centralized manner and executed in a distributed manner, the YANG module
needs to be used to deliver the scheduled topology changes to the managed entities. This scenario is called "Distributed Scenario".

To summarize the key differences between these scenarios are:

- Centralized Scenario: Schedules are generated by the managing
entity, stored in the network controller, and executed by network
devices.  Route computation runs in the controller, and routing
results are pushed to the network devices.

- Distributed Scenario: Schedules are generated by the managing
entity, stored in and executed by the network devices.  Route
computation runs in the network devices themselves.

### Interactions in Centralized Scenario {#centralized-scenario}

In the centralized scenario, the network managing entity generates and maintains schedules, the routing application
is deployed in the network controller, and the network devices execute the schedules and routing results.
The following figure shows the components of the centralized scenario.

~~~
 +-----------------------------------------------------------------+
 |                        Managing Entity                          |
 +-----------------------------------------------------------------+
            |                                           |
Topology YANG Module                        Node YANG Module(optional)
            |                                           |
 +---------\|/----------+                     +--------\|/--------+
 |  Network Controller  |---Routing Results-->|  Network Devices  |
 +----------------------+                     +-------------------+
~~~
{: #ref-to-fig1  title="Components of Centralized Scenario"}

A centralized scenario involves the interaction between the managing entity and network controller, the managing
entity and network devices, and the controller and network devices.

The managing entity may need to deliver node-specific schedules to network devices by "ietf-tvr-node"
module {{Section 5.2 of !I-D.ietf-tvr-schedule-yang}}. This is optional and only necessary when the node attribute changes
are instructed by the controller. Meanwhile, the network devices need to report their own status data to the managing entity.

The managing entity needs to deliver the schedules of network topology to the network controller by "ietf-tvr-topology" module {{Section 5.3 of !I-D.ietf-tvr-schedule-yang}}, so that the routing application in the controller
can consider the impact of topology changes on routes when calculating routes.

The network controller should deliver the computed routing results to the network devices. The format of the routing
results depend on the protocols deployed (BGP, PCEP, etc.). Routing results MAY be delivered ahead and activated using an explicit time reference (e.g., absolute UTC), or via pre-staging plus an activation trigger.

### Interactions in Distributed Scenario {#distributed-scenario}

In the distributed scenario, the managing entity generates and maintains schedules, the routing application
is deployed in the network devices which also executes schedules and route calculation.

~~~
 +-------------------------------------------------+
 |                  Managing Entity                |
 +-------------------------------------------------+
                          |
               Topology YANG Module and
               Node YANG Module(optional)
                          |
 +-----------------------\|/-----------------------+
 |         Managed Entities (Network Devices)      |
 +-------------------------------------------------+
~~~
{: #ref-to-fig2  title="Components of Distributed Scenario"}

The distributed model only involves the interaction between managing entities and network devices(managed entities).

The managing entity delivers network topology schedules to all the network devices by "ietf-tvr-topology"
module for route calculation. The managing entity may also deliver node-specific schedules to network devices by
"ietf-tvr-node" module, this is optional and only necessary when the node attributes changes are instructed by
the controller. The network devices report their own status data to the managing entity.

## Encoding of the YANG Model

The TVR data model {{!I-D.ietf-tvr-schedule-yang}} can manage network resources and topologies with scheduled
attributes. There are modules defined in the TVR data model, these are:

- The "ietf-tvr-schedule" module contains the schedule YANG definitions. This module uses groupings from {{!RFC9922}} data model;
- The "ietf-tvr-topology" module defines a network topology with a time-variant availability schedule;
- The "ietf-tvr-node" module is to be used to manage the scheduled attributes of a single node.

To create a schedule, the following TVR data model objects and subsequent branches are used:

- 'node-schedule'
- 'interface-schedule'
- 'attribute-schedule'

When using these YANG modules with NETCONF or RESTCONF, implementations SHOULD target the intended configuration datastore
for schedule provisioning and MAY read from the operational datastore
to retrieve execution status and applied schedules.  Clients can use
NMDA (Network Management Datastore Architecture, {{!RFC8342}})
operations to distinguish between intended configuration and actual
operational state.  For example, the managing entity writes schedules
to the intended or running datastore, and network devices report
execution status via the operational datastore.

An example for the encoding of "ietf-tvr-node" is provided below, where a wireless link is shut down for 12 hours, from 19:00 to 7am the next day.
The schedule is identified using a unique identifier that is conveyed in 'schedule-id', and the recurring schedule can be applied for multiple days using Coordinated
Universal Time (UTC).
More detailed examples of the JSON example is provided in this documents Appendix.

~~~

{
  "ietf-tvr-node:node-schedule": {
    "node-id": "example:1234567890",
    "node-power-schedule": {
      "power-default": true,
      "schedule": []
    },
    "interface-schedule": {
      "interface": [
        {
          "name": "Wlan0",
          "default-available": false,
          "attribute-schedule": {
            "schedule": [
              {
                "schedule-id": 111111,
                "recurrence-first": {
                  "start-time-utc": "2025-12-01T19:00:00Z",
                  "duration": 43200
                },
                "utc-until": "2026-12-01T00:00:00Z",
                "frequency": "ietf-schedule:daily",
                "interval": 1,
                "scheduled-attributes": {
                  "available": true
                }
              }
            ]
          }
        }
      ]
    }
  }
}
~~~

The methods for disseminating and propagating the generated schedules are discussed in the following subsection.

## Management Protocols for TVR

The TVR data model is designed to be accessed via YANG-based management protocols such as NETCONF {{?RFC6241}}, RESTCONF
{{?RFC8040}} and CORECONF{{?I-D.ietf-core-comi-19}}. This section discusses the applicability of these protocols for
configuring time-variant network resources using the TVR YANG data models.

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

CORECONF provides a lightweight, stateless method for managing small network devices where saving bytes to transport a
message is very important. CORECONF uses CoAP{{?RFC7252}} methods to access structured data defined in YANG, which is a complementary to RESTCONF.
Unlike RESTCONF, many CORECONF design decisions are motivated by minimizing the message size. Therefore, CORECONF is advantageous
in networks with constrained devices and very limited transmission bandwidth, especially in IoT devices that already deployed CoAP.

Depending on the type of node in the TVR network, NETCONF would be the preferred protocol for large-scale, critical scheduling
operations requiring validation and rollback mechanisms. For smaller-scale or isolated scheduling tasks, RESTCONF provides an
efficient and straightforward option without the need for the transactional features offered by NETCONF. CORECONF is preferred
in resource constrained IoT networks where saving message bytes is a priority. The choice of protocol
to use with the TVR YANG model should be driven by the specific requirements of the network environment and the complexity of
the scheduling tasks involved.

The security aspects of these management protocols, including their strengths and weaknesses, are discussed further in
{{security-considerations}} of this document.

# Time Synchronization

Time Synchronization is fundamental for ensuring that TVR mechanisms, which depend on highly accurate timing, function as intended across
an entire network. Time Synchronization mechanisms will be used to ensure:

 - Coordination of Planned Network Events;

 - Verification of TVR Data Model Time Stamps;

 - Accurate Scheduling of Paths;

Therefore, network time accuracy and time-stamped data models are critical to ensure that coordinated network events and scheduled path decisions across the network are based on a consistent time reference. Without accurate time sync, nodes could apply different schedules, causing routing inconsistencies, path flapping, or packet loss.

Since all schedules are generated by the managing devices, it is necessary to make sure that the managing device and managed devices within a schedule domain ({{Section 2.1.1 of !I-D.ietf-tvr-requirements}}) are synchronized with the same time source. It ensures they have the same notion of when that is.

Different time-variant scenarios may require different granularities of time synchronization. For example, the
period of traffic and topology changes in tidal networks is usually a day or week. Therefore, a second-level time
synchronization is enough. However, for the dynamic reachability scenarios, a fine-granularity time synchronization may
be necessary, as the nodes may move very fast in some cases (the moving speed of a low earth orbit satellite is more than 7900 m/s)

Operators SHOULD derive a maximum acceptable time-error bound based on the schedule granularity, execution jitter tolerance, and
activation window requirements.  For instance, if a schedule has a 1-second activation window and the system can tolerate up to 100ms of
execution jitter, the time synchronization error MUST be kept well below 900ms.  The chosen time synchronization protocol and
configuration MUST be capable of meeting this derived bound under all expected network conditions.

Existing clock synchronization protocols can be classified into hardware-based protocols and software-based protocols.

Hardware-based protocols often rely on dedicated hardware to ensure clock synchronization, such as Satellite Based Timing Service (SBTS)
and Precision Time Protocol (PTP). The SBTS includes but is not limited to Global Position System (GPS),
BeiDou Navigation Satellite System(BDS), Global Navigation Satellite System(GLONASS), and Galileo satellite navigation system.
Software-based protocols, on the other hand, synchronize clocks through software packages running on systems, such as Network
Time Protocol (NTP) {{?RFC5905}} and Simple Network Time Protocol (SNTP) {{?RFC4330}}.

The security aspects of time synchronization mechanisms are discussed further in {{security-considerations}} of this document.

## Hardware-based Time Synchronization Mechanisms

Hardware-based protocols typically have higher precision and stability, but also have higher cost due to the dedicated hardware. SBTS and PTP are the typical hardware-based time synchronization mechanisms.

SBTS provides a precise time synchronization service based on the signals transmitted by the satellites. SBTS can realize the micro-second level time synchronization among the devices installed with SBTS receiver hardware.

PTP is a network protocol that complies with the IEEE 1588 standard and is used to implement high-precision time synchronization between network nodes. PTP implements time synchronization by transmitting synchronization messages between master and slave devices. Based on the hardware timestamp, the precision of time synchronization is much higher than that of NTP, and can reach the sub-microsecond level or even tens of nanoseconds.  When deploying PTP in TVR networks, the managing entities should be the master and the network devices and controller should be the slaves which get time from the master.

Both SBTS and PTP can realize micro-second level time synchronization. Depending on the features of TVR network, the SBTS would be the preferred mechanisms for
large-scale, high dynamic and open-air networks, especially networks with unreliable links as it does not require network links to exchange time information.
For the small-scale networks with stable links but have high-precision time synchronization requirements, the PTP is much preferred.

## Software-based Time Synchronization Protocols

Software-based protocols are simple and applicable to common hardware devices, but have lower precision (For
example, the NTP can realize the synchronization at tens of milliseconds level).

### NTP

NTP uses a hierarchical structure of time sources. Each level of this hierarchy is termed a stratum. Generally, an NTP
server synchronized to an authoritative clock runs at stratum 1. Such NTP server functions as the primary time server
to provide clock synchronization for other devices on the network. Stratum 2 servers obtain time from stratum 1 servers,
stratum 3 servers obtain time from stratum 2 servers, and so on.

In TVR use cases, the managing entity functions as a level-1 NTP server and synchronized to an authoritative clock
source. The network controller and network devices behave as clients to obtain accurate time from the managing entity.
{{ref-to-fig3}} shows an NTP deployment scenario for obtaining clock from a GPS clock source.

~~~
                           +--------------------+
                           |  GPS Clock Source  |
                           +--------------------+
                                     |
               +--------------------\|/------------------+
Stratum 1      |             Managing Entity             |
               +-----------------------------------------+
                     |                             |
                     |                             |
                     |                             |
          +---------\|/----------+       +--------\|/--------+
Stratum 2 |  Network Controller  |       |  Network Devices  |
          +----------------------+       +-------------------+

~~~
{: #ref-to-fig3  title="Deployment Case of NTP in Tidal Networks"}

NTP is preferred in large-scale networks with reliable links and long-term changes, which does not require a high-precision time synchronization.

### SNTP

SNTP is a subset of the NTP used to synchronize computer clocks in the Internet. It simplifies the complex NTP
synchronization function and has lower clock precision, but the synchronization precision still can be kept within
seconds. SNTP is also preferred in large-scale networks with reliable links, long-term changes, and loose synchronization precision. In addition, it is more suitable for networks with limited resources than NTP.

# Schedule Database

The schedule database is used to store and maintain schedules, the database may be deployed on a managing entity
and managed entities based on requirements.

The source of the schedule database may be created from multiple sources, for example, configuration from an administrator
or YANG model from the management interface. The schedule entries of different databases may be different, but the
content of the same schedule entry in the schedule databases of different devices in the same domain must be
consistent. There are at least two ways to make the content of the same schedule entry in different schedule databases
consistent:

 - All the schedule entries are generated at a specific device;

 - Schedule entries are generated at different devices, but there is a synchronization mechanism to synchronize the
schedule databases among devices.

Option 1 is simplest and easy to implement. In a time-variant domain, the managing entity may receive scheduling
requests and generate all schedule entries. Then the schedule entries are delivered to the necessary network devices
in the domain through the TVR YANG model.

Option 2 relies on advertisement mechanisms (such as routing techniques) to advertise the scheduling data generated
by itself to other devices. This could be achieved using extensions to existing routing schemes and techniques.

Detailed schedule distribution mechanisms beyond YANG-based configuration are outside of this document.

## Data Structure

{{!I-D.ietf-tvr-schedule-yang}} defines a TVR Node and TVR Topology YANG modules. The Node YANG module
includes node power schedules and interface schedules. The Topology YANG module includes node schedules and link (termination point) schedules.

Based on the preceding four schedule types, the schedule database should contain four types of schedule entries in
different formats:

 - Node power schedule entry;

 - Interface schedule entry;

 - Node schedule entry;

 - Termination-point schedule entry;

The detailed format and fields of different types of schedule entries could refer to the definitions of the corresponding
YANG modules.

## Schedule Operations

As the core support for the operation of TVR schedules, the schedule database should be equipped with comprehensive operational
capabilities. It should support the addition, update, and deletion of schedule operations, ensuring that schedule data
can be adjusted promptly in accordance with actual requirements so as to meet the demands of dynamic scheduling scenarios.

When conducting operations to add or update schedule entries, the relevant execution devices should strictly verify whether
conflicts exist between the current schedule entry and existing schedule entries stored in the database. If a conflict
is detected, the addition or update operation should be terminated immediately with a failure notification, and the
operation should not be re-executed until the conflict is properly resolved.

Furthermore, both the update and deletion of schedules rely on the schedule IDs as the unique identifier; therefore,
the schedule IDs should remain unique within a time-variant domain. This is a critical prerequisite for ensuring the
accuracy of scheduling operations. For example, ID management can be implemented through a dedicated allocation agent
within the time-variant domain. This agent is responsible for the allocation of schedule IDs, thereby preventing ID
duplication at the source.

# Operational Considerations

Several operational considerations exist when using TVR techniques and data models in a network. This section provides
some high-level observations and more detailed sub-sections for specific consideration related to schedule dissemination,
execution, and recovery in case of failure to apply a schedule or partial change.

## Schedule Dissemination

When distributing schedules, the following problems should be considered:

- The managed entities that receives a schedule should have the ability to execute the schedule. If the device does not support schedule
execution, it SHOULD reject the update with an appropriate error, or ignore it if the management interface does not support error reporting.

- The distributing of a schedule SHOULD be delivered at least x (operator-defined) time before the earliest start time of the schedule, this ensures that the
managed entity has enough time to execute this schedule.

## Schedule Execution

Schedule execution means that a component (e.g., device) undertakes an action (e.g., allocates and deallocates resources)
at specified time points. The schedule execution of Node Module and Topology Module should be considered separately.

### Execution of Node Schedule

Node schedule execution indicates a node to change its node/interfaces availability/power up
and down, and other attributes directly or by commands.

When executing a node schedule, the schedule executor should undertake an action at specified time points as indicated in the schedule.

### Execution of Topology Module Schedule

Topology schedule execution means a node take some measures before or upon the scheduled topology changes.

The schedule executor should understand the consequences of the schedule execution. The execution actions for different kind of topology changes should be considered separately.

- A link coming up or a node joining a topology should not have any functional change until the change is proven to be fully operational. The routing paths may be pre-computed but should not be installed before all the topology changes are confirmed to be operational. The network may choose to not do any pre-installation or pre-computation in reaction to topological additions at a small cost of some operational efficiency.

- If a link or node is to be removed from the topology, then the
network should act before the anticipated change to route traffic around the expected topological change. The required time to perform such planned action will vary depending on the exact network
and configuration. When using an IGP or other distributed routing protocols, the affected links may be set to a high
metric to direct traffic to alternate paths. This type of change does require some time to propagate through the
network, so the metric change should be initiated far enough in advance that the network converges before the actual
topological change.

- If an attribute changes better (such as latency decrease and bandwidth increase), then the executor should take actions later
or until the topology change is proven to be fully operational.

- If an attribute changes worse (such as latency increase and bandwidth decrease), then the node should react to it before the change take place.

## Schedule Recovery

Schedule recovery means when a node lost its specific schedule data, it should be able to recover these schedule data.
Typical scenarios include data loss due to device restart, disconnection from managing entities and failure to receive new
schedules for a long time. In these scenarios, once the connection between the managed entity and the managing entity is
established again, the managing entity needs to re-distribute all schedules that start time is later than the current moment
to the managed entity after time synchronization is finished.

## Consistency Verification

Consistency within a schedule or among different schedules is the precondition for schedule execution. It is very necessary to do verification for inconsistencies. A inconsistency means that some time parameters conflict with other time parameters in the same schedule or in other schedules.

- If the time parameters of a schedule conflict with each other, for example, the period-start later than period-end,
the duration is longer than the product of frequency and interval, or the duration is longer than utc-until, then the schedule
should be discarded and an error should be returned to the schedule originator (e.g., the managing entity or management client).

- If there is a conflict between schedules with different schedule IDs, for example, schedule1 indicates that interface B is closed at time A, but schedule2 indicates that interface B is open at time A, then only the conflicting update should be rejected (retaining the last-known-good schedule). An error should be returned to the schedule originator, and the conflict MUST be logged for audit purposes.

- If there is a conflict between schedules with the same schedule ID, then it is considered as an update of the former schedule. Updates with the same schedule ID SHALL completely replace the previous schedule (full replacement, not merge). Implementations SHOULD support versioning or etag-based mechanisms to detect concurrent updates. Partial updates to a schedule are NOT permitted; clients MUST send the complete schedule definition.

## Early Warning Mechanism

Early warning mechanism may be used to identify the fault schedule, and prevent the execution of fault schedule in some time-variant scenarios with insignificant schedule changes.

When a schedule received by a managed entity or managing entity shows significant short-term discrepancies in its key
values (e.g. the frequency of a schedule changes greatly), the device should send feedback to the schedule generator
to confirm whether this schedule arises from a system failure or operator error. Upon receiving the feedback, the
schedule generator should inspect logs to confirm whether a fault has occurred or notify users to get the confirmation.
Only when the schedule is confirmed, then the receiving device can execute this schedule. The device received schedules
and the schedule generator may be independent devices or independent components within one device.

## Stability Mechanisms

### Fallback Mechanism

A fallback policy defines the operational state that is applied when schedule verification or execution fails.
It allows continued operation in cases where unexpected or inconsistent information is detected

Fallback triggers include, but are not limited to:

- Loss of schedule database after reboot or management plane disconnection;

- Time synchronization failure;

- Validation failure of the currently active schedule entry;

- Managed entity reports that it cannot execute the scheduled attribute (e.g., a scheduled interface cannot be brought up).

The fallback state may be selected from three sources:

- The default attribute value defined in the TVR YANG schedule;

- A statically configured safe state that is independent of TVR schedules;

- The last known operational state before the failing schedule was applied.

Fallback overrides the schedule input for routing computation. It does not delete or modify stored schedule entries.

### Rollback

Rollback is a transactional undo capability for schedule provisioning operations. It restores the schedule database to a previously validated baseline snapshot after a faulty schedule create/update/delete transaction.
Rollback is primarily orchestrated by the managing entity, which maintains versioned, integrity-protected snapshots of the schedule database.
Each snapshot is captured in the prepare phase before applying a batch of schedule changes.
A snapshot includes the set of affected schedule-ids, resource references, transaction identifier, timestamp, and integrity digest for validation. Snapshots are retained according to the domain audit policy.

All rollback actions should be logged for audit. The log records the transaction ID, snapshot reference, trigger reason, list of affected schedule-ids, and per-resource rollback outcome. Rollback status is also reflected in the operational datastore for monitoring.

# Security Considerations {#security-considerations}

The integration of time-variant mechanisms in network operations presents distinct security challenges that require thorough
analysis to safeguard the network’s integrity, availability, and confidentiality. Networks that rely on time-sensitive data
for routing and forwarding decisions are particularly susceptible to attacks that exploit timing dependencies.

The "Security Considerations" section of {{!I-D.ietf-tvr-requirements}} outlines various threat vectors and categories specific
to time-variant environments.

## Denial-of-Service (DoS) Attack {#dos-attack}

In a time-variant network, malicious actors could attack the network by disrupting or manipulating the time synchronization
process. For example, an attacker could intentionally delay or corrupt time signals exchanged within the network, leading
to routing errors and widespread denial-of-service (DoS) attacks.

This kind of attack could be mitigated by the redundant time synchronization mechanisms, for example, multiple NTP sources
or multiple time synchronization protocols could be deployed in a TVR network. The network devices could guarantee the
correctness of the time by cross-checking time signals obtained from different sources or protocols.

In addition, peer authentication is also an important way to protect the time signals being tampered by
attackers. Some security extensions for time synchronization protocols (such as NTS (Network Time Security)) are
recommended to be applied.

## Traffic Analysis and Path Prediction {#traffic-analysis}

In a time-variant network, if time information is not adequately protected, attackers could conduct traffic analysis to
infer routing decisions, network load, or usage patterns. The schedule ability could enable attackers to launch highly
targeted attacks, such as selectively overloading certain links or intercepting sensitive communications.

This kind of attack could be mitigated by the encryption of schedules and the authentication of managing entities. For
the networks using NETCONF to deliver schedules, NETCONF over TLS{{!RFC7589}} is recommended to achieve the bidirectional
authentication and encryption of YANG model data. RESTCONF supports TLS originally, so it can be deployed without
additional configurations or modifications.

In addition, in time variant networks with centralized scenario{{centralized-scenario}}, the encryption of routing path
information is also necessary to avoid the fake routing information. Considering the most typical protocols used to
deliver the routing path information between controller and network devices are BGP and PCEP, and both are based on TCP.
Therefore, the TLS is RECOMMENDED to provide confidentiality and integrity protection.

## Activity Identification and Privacy {#activity-identification}

In certain scenarios, precise time information exchanged within the network could be correlated with specific user or
device behavior, inadvertently revealing private information.

This risk could also be mitigated by the solutions mentioned in {{traffic-analysis}}.

## Spoofing and Manipulation of Time Information

In a time-variant network, if an attacker were to inject false or manipulated time data into the network, it could cause
routers and devices to make incorrect decisions, potentially leading to traffic misrouting, network partitions, or
inefficient use of resources.

This risk could also be mitigated by the solutions mentioned in {{dos-attack}}.

## Replay Attacks on Time-Sensitive Data

Time-variant network data and schedules updates may be susceptible to replay attacks, which could cause network devices
to act on outdated information, leading to inconsistent routing decisions, misaligned schedules, or security gaps. In
particular, attackers could exploit replay attacks to force devices into outdated configurations or interfere with the
synchronization of schedules across the network.

This kind of attack could be mitigated by encrypting time signals, schedules and routing path data, and adding a unique
number to the encrypted section of a packet. This has been implemented in existing protocols, for example, the NTS
supports unique identifier extension field (EF) containing a random number, the TLS supports Message Authentication Code
generated from sequence number.

## Compromised Time Sources

The reliance on external time sources for synchronization purposes presents a potential attack surface for time-variant
networks. If a trusted time source, such as a GPS signal or an NTP server, is compromised, the attacker could feed
erroneous time information to the entire network, disrupting its operation.

This kind of attack could be mitigated by the solutions mentioned in {{dos-attack}}.

## Schedule Tampering and Malicious Schedule Injection


In a time-variant network, attackers may perform unauthorized modification or inject malicious TVR schedules.
Such attacks could cause traffic blackholing by scheduling link or node shutdowns at critical times, trigger network-wide reroutes, degrade SLA performance, or enable targeted interception of sensitive traffic flows.

This kind of attack could be mitigated by the combination of several approachs.
All schedule updates MUST be authenticated to verify the originator identity. Schedules MUST be protected against tampering in transit and at rest via cryptographic integrity mechanisms such as digital signatures or HMAC.
All schedule creation, modification, and deletion operations SHOULD be logged with timestamps, originator identity, and change descriptions for forensic analysis and anomaly detection.
Implementations SHOULD also enforce rate limits on schedule updates and deploy anomaly detection to identify suspicious patterns such as frequent schedule churn or schedules from unexpected sources.

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to thank Ed Birrane, Adrian Farrel, Luis M. Contreras for their valuable comments and suggestions.

# Appendix A: Code Examples
{:numbered="false"}

All examples in this appendix are intended as {{!RFC7951}} JSON instance data, following the YANG instance data encoding rules for JSON.  The duration field values are expressed in seconds unless otherwise noted.

## A.1 Code Examples for "Energy-harvesting Wireless Sensor Network"
{:numbered="false"}

As described in {{Section 2.3 of ?RFC9657}}, in an energy-harvesting wireless sensor network, nodes rely exclusively on
environmental sources for power, such as solar panels. On-board power levels may fluctuate based on various factors.
This example assumes that a node will only power its radio when available power is over some threshold.
In this scenario, the TVR Node YANG Module is not applicable, since the node attributes changes are caused by the environment,
not by the instructions from managing entity. The TVR Topology YANG Module may be necessary to convey the schedule of topology
changes to all the nodes.

Considering a topology with three nodes, the connectivity of this three-node networks changes over time and repeats daily.
The detailed change information is shown in {{ex-inf1}}. The link between node1 and node2 is powered on at 8:00 and powered off at 11:00.
The link between node2 and node3 is powered on at 11:00 and powered off at 16:00.

~~~
      +----------+        +----------+        +----------+
8:00  |  Node 1  |--------|  Node 2  |        |  Node 3  |
      +----------+        +----------+        +----------+

      +----------+        +----------+        +----------+
11:00 |  Node 1  |        |  Node 2  |--------|  Node 3  |
      +----------+        +----------+        +----------+

      +----------+        +----------+        +----------+
16:00 |  Node 1  |        |  Node 2  |        |  Node 3  |
      +----------+        +----------+        +----------+
~~~
{: #ex-inf1 title="An example of topology connectivity of Energy-harvesting Wireless Sensor Network" artwork-align="center"}

The corresponding JSON example is shown in {{ex-inf2}}.

~~~

{
  "ietf-network:networks": {
    "network": [
      {
        "network-id": "network:1",
        "network‑types": {
          "ietf‑tvr‑topology:tvr‑topology": null
        },
        "node": [
          {
            "node-id": "example:192.0.2.1",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-1",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 1,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T08:00:00Z",
                        "duration": 10800
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "destination-nodes": "example:192.0.2.2",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "example:192.0.2.2",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-1",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 2,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T08:00:00Z",
                        "duration": 10800
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "destination-nodes": "example:192.0.2.1",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              },
              {
                "tp-id": "tp-2",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 3,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T11:00:00Z",
                        "duration": 18000
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "destination-nodes": "example:192.0.2.3",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "example:192.0.2.3",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "link1",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 4,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T11:00:00Z",
                        "duration": 18000
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "destination-nodes": "example:192.0.2.2",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          }
        ],
        "ietf-network-topology:link": [
          {
            "link-id": "link-1-2",
            "source": {
              "source-node": "example:192.0.2.1",
              "source-tp": "tp-1"
            },
            "destination": {
              "dest-node": "example:192.0.2.2",
              "dest-tp": "tp-1"
            }
          },
          {
            "link-id": "link-2-3",
            "source": {
              "source-node": "example:192.0.2.2",
              "source-tp": "tp-2"
            },
            "destination": {
              "dest-node": "example:192.0.2.3",
              "dest-tp": "tp-1"
            }
          }
        ]
      }
    ]
  }
}
~~~
{: #ex-inf2 title="JSON example of topology schedule for Energy-harvesting Wireless Sensor Network" artwork-align="center"}

## A.2 Code Examples for "Cellular Network"
{:numbered="false"}

As described in {{Section 3.3 of ?RFC9657}}, the "Cellular Network" is a network where the nodes operating over cellular
connections that charge both peak and off-peak data rates. In this case, individual nodes may be allocated a fixed set of
"peak" minutes such that exceeding that amount of time results in expensive overage charges. As a result, links are just
available at specific "peak" minutes.
In this scenario, both the TVR node YANG module and TVR topology YANG module are applicable to manage the state of node
interfaces and deliver the predicted topology changes to each node.

Considering a topology with three nodes, the connectivity variation of it is shown in {{ex-inf1}}. Taking the node1 as an
example, the corresponding node YANG module JSON example for node1 is shown in {{ex-inf3}}

~~~
{
  "ietf-tvr-node:node-schedule": {
    "node-id": "example:192.0.2.1",
    "node-power-schedule": {
      "power-default": true,
      "schedule": []
    },
    "interface-schedule": {
      "interface": [
        {
          "name": "interface1",
          "default-available": false,
          "attribute-schedule": {
            "schedule": [
              {
                "schedule-id": 100,
                "recurrence-first": {
                  "start-time-utc": "2026-01-01T08:00:00Z",
                  "duration": 10800
                },
                "utc-until": "2027-01-01T00:00:00Z",
                "frequency": "ietf-schedule:daily",
                "scheduled-attributes": {
                  "available": true
                }
              }
            ]
          }
        }
      ]
    }
  }
}
~~~
{: #ex-inf3 title="TVR node YANG module JSON example of node1" artwork-align="center"}

The corresponding topology yang module JSON example is the same as {{ex-inf2}}

## A.3 Code Examples for "Tidal Network"
{:numbered="false"}

As described in {{Section 3.4 of ?RFC9657}}, the "Tidal Network" is a network where traffic volume undergoes significant
fluctuations at different times. In the context of a tidal network scenario, energy-saving methods may include the deactivation
of some or all components of network nodes as planned.
In this scenario, both the TVR node YANG module and TVR topology YANG module are applicable to manage the state of node
(interfaces) and deliver the predicted topology changes to each node. {{ex-inf4}} shows a tidal network topology with 4
nodes.

~~~
    +-----+          +-----+                      +-----+          +-----+
    | N1  |----L1----| N2  |                      | N1  |----L1----| N2  |
    +-----+          +-----+                      +-----+          +-----+
       |   \        /   |                            |                |
       |    \      /    |                            |                |
       |     \    /     |                            |                |
       |      L6 L5     |                            |                |
      L2       \/       L3                          L2                L3
       |       /\       |                            |                |
       |      /  \      |                            |                |
       |     /    \     |                            |                |
       |    /      \    |                            |                |
       |   /        \   |                            |                |
    +-----+          +-----+                      +-----+          +-----+
    | N3  |----L4----| N4  |                      | N3  |----L4----| N4  |
    +-----+          +-----+                      +-----+          +-----+
Topology1 (8:00-23:00 everyday)     Topology 2(23:00-23:59 and 00:00-08:00 everyday)
~~~
{: #ex-inf4 title="An example topology of Tidal Network" artwork-align="center"}

Taking the node N1 as an example, assuming the node-ids of N1, N2, N3, N4 are 2001:db8::1, 2001:db8::2, 2001:db8::3,
and 2001:db8::4. The corresponding node YANG module JSON example for it is shown in {{ex-inf5}}

~~~
{
  "ietf-tvr-node:node-schedule": {
    "node-id": "example:2001:db8::1",
    "node-power-schedule": {
      "power-default": true,
      "schedule": []
    },
    "interface-schedule": {
      "interface": [
        {
          "name": "interface2",
          "default-available": false,
          "attribute-schedule": {
            "schedule": [
              {
                "schedule-id": 100,
                "recurrence-first": {
                  "start-time-utc": "2026-01-01T08:00:00Z",
                  "duration": 54000
                },
                "utc-until": "2027-01-01T00:00:00Z",
                "frequency": "ietf-schedule:daily",
                "scheduled-attributes": {
                  "available": true
                }
              }
            ]
          }
        }
      ]
    }
  }
}
~~~
{: #ex-inf5 title="TVR node YANG module JSON example of node N1" artwork-align="center"}

The corresponding topology YANG module JSON example is shown in {{ex-inf6}}

~~~
{
  "ietf-network:networks": {
    "network": [
      {
        "network-id": "network:ipv6-4node",
        "network-types": {
          "ietf-tvr-topology:tvr-topology": null
        },
        "node": [
          {
            "node-id": "example:2001:db8::1",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-l1",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l2",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l6",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 100,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T08:00:00Z",
                        "duration": 54000
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "example:2001:db8::4",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "example:2001:db8::2",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-l1",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l3",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l5",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 200,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T08:00:00Z",
                        "duration": 54000
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "example:2001:db8::3",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "example:2001:db8::3",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-l2",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l4",
                "available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l5",
                "available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 300,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T08:00:00Z",
                        "duration": 54000
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "example:2001:db8::2",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "example:2001:db8::4",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-l3",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l4",
                "ietf-tvr-topology:available": {
                  "default-link-available": true
                },
                "schedule": []
              },
              {
                "tp-id": "tp-l6",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 405,
                      "recurrence-first": {
                        "start-time-utc": "2026-01-01T08:00:00Z",
                        "duration": 54000
                      },
                      "utc-until": "2027-01-01T00:00:00Z",
                      "frequency": "ietf-schedule:daily",
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "example:2001:db8::1",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          }
        ],
        "ietf-network-topology:link": [
          {
            "link-id": "L1",
            "source": {
              "source-node": "example:2001:db8::1",
              "source-tp": "tp-l1"
            },
            "destination": {
              "dest-node": "example:2001:db8::2",
              "dest-tp": "tp-l1"
            }
          },
          {
            "link-id": "L2",
            "source": {
              "source-node": "example:2001:db8::1",
              "source-tp": "tp-l2"
            },
            "destination": {
              "dest-node": "example:2001:db8::3",
              "dest-tp": "tp-l2"
            }
          },
          {
            "link-id": "L3",
            "source": {
              "source-node": "example:2001:db8::2",
              "source-tp": "tp-l3"
            },
            "destination": {
              "dest-node": "example:2001:db8::4",
              "dest-tp": "tp-l3"
            }
          },
          {
            "link-id": "L4",
            "source": {
              "source-node": "example:2001:db8::3",
              "source-tp": "tp-l4"
            },
            "destination": {
              "dest-node": "example:2001:db8::4",
              "dest-tp": "tp-l4"
            }
          },
          {
            "link-id": "L5",
            "source": {
              "source-node": "example:2001:db8::2",
              "source-tp": "tp-l5"
            },
            "destination": {
              "dest-node": "example:2001:db8::3",
              "dest-tp": "tp-l5"
            }
          },
          {
            "link-id": "L6",
            "source": {
              "source-node": "example:2001:db8::1",
              "source-tp": "tp-l6"
            },
            "destination": {
              "dest-node": "example:2001:db8::4",
              "dest-tp": "tp-l6"
            }
          }
        ]
      }
    ]
  }
}
~~~
{: #ex-inf6 title="JSON example of topology schedule for Tidal Network" artwork-align="center"}

## A.4 Code Examples for "Mobile Satellites"
{:numbered="false"}
As described in {{Section 4.3 of ?RFC9657}}, the "Mobile Satellites" generally refers to the Low Earth Orbit(LEO) network,
which includes hundreds to thousands of spacecrafts that can communicate both with their orbital neighbors as well as
down to any ground station that they happen to be passing over. The connection between the spacecrafts and the ground
station depend on the flight trajectories of the spacecrafts, so the link changes between them is predictable.

{{Section 4.3 of ?RFC9657}} introduces a scenario with 3 spacecrafts and 1 ground station. The changes of topology are
shown in {{ex-inf7}}. The ground station connects to spacecraft N3 at time t1, connects to N2 at time t2, and connects
to N1 at time t3. The duration of the connection depends on the satellite altitude and the elevation angle.
According to {{Section 2.1 of ?I-D.lj-rtg-sat-routing-consideration}}, for the spacecrafts at the 500km
altitude, and the connection between the spacecraft and ground station can keep for 7 minutes.

~~~
    +------+          +------+
t1  |  N2  |----------|  N3  |
    +------+          +---+--+
                          |
                         /|\
                        \___/
                         / \
                       Ground
                       Station
------------------------------------------------------------------
    +------+          +------+          +------+
t2  |  N1  |----------|  N2  |----------|  N3  |
    +------+          +---+--+          +------+
                          |
                         /|\
                        \___/
                         / \
                       Ground
                       Station
------------------------------------------------------------------
                      +------+          +------+          +------+
t3                    |  N1  |----------|  N2  |----------|  N3  |
                      +---+--+          +------+          +------+
                          |
                         /|\
                        \___/
                         / \
                       Ground
                       Station
------------------------------------------------------------------
~~~
{: #ex-inf7 title="An example topology for Mobile Satellites" artwork-align="center"}

In this scenario, the TVR topology YANG module is applicable to deliver the predicted topology changes to each node.
However, the TVR node YANG module is not applicable, this depends on whether the link changes are controlled by satellites
themselves or by the managing entity. Here, we provide the JSON examples for TVR topology YANG module and node
 YANG module as a reference.

Taking the spacecraft N1 as an example, assuming the time t1 is 10:00:00 1 July 2025 and the node-ids of N1, N2, N3, and
ground station is 2001:db8::1, 2001:db8::2, 2001:db8::3, and 2001:db8::4.,
then the corresponding node YANG module JSON example for it is shown in {{ex-inf8}}.

~~~
{
  "ietf-tvr-node:node-schedule": {
    "node-id": "example:2001:db8::1",
    "node-power-schedule": {
      "power-default": true,
      "schedule": []
    },
    "interface-schedule": {
      "interface": [
        {
          "name": "satellite2ground-interface",
          "default-available": false,
          "attribute-schedule": {
            "schedule": [
              {
                "schedule-id": 100,
                "period-start": "2025-07-01T10:00:00Z",
                "duration": 420,
                "scheduled-attributes": {
                  "available": true
                }
              }
            ]
          }
        }
      ]
    }
  }
}
~~~
{: #ex-inf8 title="TVR node YANG module JSON example of spacecraft N1" artwork-align="center"}

Assuming that time t1 is 10:00:00 1 July 2025, time t2 is 10:10:00 1 July 2025, and time t3 is 10:20:00 1 July 2025,
then the corresponding topology YANG module JSON example is shown in {{ex-inf9}}.

~~~

{
  "ietf-network:networks": {
    "network": [
      {
        "network-id": "network:ipv6-satellite",
        "network-types": {
          "ietf-tvr-topology:tvr-topology": null
        },
        "node": [
          {
            "node-id": "satellite:2001:db8::1",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-gs",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 100,
                      "recurrence-first": {
                        "start-time-utc": "2025-07-01T10:20:00Z",
                        "duration": 420
                      },
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "gs:2001:db8::4",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "satellite:2001:db8::2",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-gs",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 200,
                      "recurrence-first": {
                        "start-time-utc": "2025-07-01T10:10:00Z",
                        "duration": 420
                      },
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "gs:2001:db8::4",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "satellite:2001:db8::3",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-gs",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 300,
                      "recurrence-first": {
                        "start-time-utc": "2025-07-01T10:00:00Z",
                        "duration": 420
                      },
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "gs:2001:db8::4",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          },
          {
            "node-id": "gs:2001:db8::4",
            "ietf-tvr-topology:available": {
              "default-node-available": true,
              "schedule": []
            },
            "termination-point": [
              {
                "tp-id": "tp-1",
                "ietf-tvr-topology:available": {
                  "default-link-available": false,
                  "schedule": [
                    {
                      "schedule-id": 400,
                      "recurrence-first": {
                        "start-time-utc": "2025-07-01T10:00:00Z",
                        "duration": 420
                      },
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "satellite:2001:db8::3",
                        "strict-destination-checking": true
                      }
                    },
                    {
                      "schedule-id": 401,
                      "recurrence-first": {
                        "start-time-utc": "2025-07-01T10:10:00Z",
                        "duration": 420
                      },
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "satellite:2001:db8::2",
                        "strict-destination-checking": true
                      }
                    },
                    {
                      "schedule-id": 402,
                      "recurrence-first": {
                        "start-time-utc": "2025-07-01T10:20:00Z",
                        "duration": 420
                      },
                      "link-attributes": {
                        "link-available": true,
                        "destination-nodes": "satellite:2001:db8::1",
                        "strict-destination-checking": true
                      }
                    }
                  ]
                }
              }
            ]
          }
        ],
        "ietf-network-topology:link": [
          {
            "link-id": "gs-s1",
            "source": {
              "source-node": "satellite:2001:db8::1",
              "source-tp": "tp-gs"
            },
            "destination": {
              "dest-node": "satellite:2001:db8::4",
              "dest-tp": "tp-1"
            }
          },
          {
            "link-id": "gs-s2",
            "source": {
              "source-node": "satellite:2001:db8::2",
              "source-tp": "tp-gs"
            },
            "destination": {
              "dest-node": "satellite:2001:db8::4",
              "dest-tp": "tp-1"
            }
          },
          {
            "link-id": "gs-s3",
            "source": {
              "source-node": "satellite:2001:db8::3",
              "source-tp": "tp-gs"
            },
            "destination": {
              "dest-node": "satellite:2001:db8::4",
              "dest-tp": "tp-1"
            }
          }
        ]
      }
    ]
  }
}
~~~
{: #ex-inf9 title="JSON example of topology schedule for Mobile Satellites" artwork-align="center"}

## A.5 Code examples for "Predictable Moving Vessels"
{:numbered="false"}

As described in {{Section 4.4 of ?RFC9657}}, the "Predictable Moving Vessels" involves the movement of vessels with
predictable trajectories, such as ferries or planes. These endpoints often rely on a combination of satellite and
terrestrial systems for Internet connectivity. Consider a scenario where a vessel uses satellites to access the Internet,
including a ship and three satellites. As the satellite and the vessel move, the vessel establishes connections with the
satellites N1, N2, and N3 at t1, t2, and t3 respectively. It is assumed that each connection lasts for 1 minutes.
The changes of topology are shown in {{ex-inf10}}.

~~~
    +------+          +------+
t1  |  N2  |----------|  N1  |
    +------+          +---+--+
                          |
                         /|\
                        \___/
                         / \
                       Vessel
------------------------------------------------------------------
    +------+          +------+          +------+
t2  |  N3  |----------|  N2  |----------|  N1  |
    +------+          +---+--+          +------+
                          |
                         /|\
                        \___/
                         / \
                       Vessel
------------------------------------------------------------------
                      +------+          +------+          +------+
t3                    |  N3  |----------|  N2  |----------|  N1  |
                      +---+--+          +------+          +------+
                          |
                         /|\
                        \___/
                         / \
                       Vessel
------------------------------------------------------------------
~~~
{: #ex-inf10 title="An example topology for Predictable Moving Vessels" artwork-align="center"}

This scenario is similar to the "Mobile Satellites" example, so the TVR node YANG module JSON example and topology YANG
JSON example of this scenario can refer to the JSON example of the "Mobile Satellites" example.
