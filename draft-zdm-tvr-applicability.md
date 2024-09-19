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

Brief description on tidal network and associated devices.

# Applicability of TVR Yang Model in Tidal Network

## Yang Model for Tidal Network

What portions of the YANG model do we use for the Tidal example?


## Interaction Between Devices

TODO

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
