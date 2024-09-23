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

What portions of the YANG model do we use for the Tidal example?

## Encoding of Yang Model

How/what do we encode from YANG to the messenger protocol, which protocol, and why?

## NetConf Example

TODO

## RESTConf Example

TODO

# Time Synchronization

Which time synchronization protocol are recommended, why?

## NTP Example

TODO

## SNTP Example

TODO

# Schedule Database

## Data Structure

The fileds of schedule data entry.

## Operations

The add, delete and sanity check operations.

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
