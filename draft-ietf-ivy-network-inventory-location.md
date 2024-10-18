---
title: "A YANG Data Model for Network Inventory Location"
abbrev: "Network Inventory Location"
category: std

docname: draft-ietf-ivy-network-inventory-location-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Network Inventory YANG"
keyword:

 - Automation
 - Network Digital Map
 - Network Inventory
 - Network Operation
 - Topology

author:

-
    name: Bo Wu
    org: Huawei
    email: lana.wubo@huawei.com
-
    name: Sergio Belotti
    org: Nokia
    email: sergio.belotti@nokia.com
-
    ins: J-F. Bouquier
    name: Jean-Francois Bouquier
    org: Vodafone
    email: jeff.bouquier@vodafone.com
-
    name: Fabio Peruzzini
    org: TIM
    email: fabio.peruzzini@telecomitalia.it
-
    name: Phil Bedard
    org: Cisco
    email: phbedard@cisco.com

contributor:

-
    name: Italo Busi
    org: Huawei Technologies
    email: italo.busi@huawei.com
-
   fullname: Chaode Yu
   organization: Huawei
   email: yuchaode@huawei.com

normative:

informative:

--- abstract

This document defines a YANG data model for Network Inventory
location (e.g., site, room, rack, geo-location data), which provides
location information with different granularity levels for
inventoried network elements.

Accurate location information is useful for network planning,
deployment, and maintenance.  However, such information cannot be
obtained or verified from the Network Elements themselves.  This
document defines a location model for network inventory that extends
the base inventory with comprehensive location data.

--- middle

# Introduction

NEs can be grouped by location to provide more information for
network planning, deployment, and maintenance (e.g., easily locate
problematic NEs, optimize network resources, or help planning
forecasts).  The location can reflect outdoor or indoor information.
An indoor location may be represented as a building, room, or other
similar organizational structures.  Outdoor locations can be walls,
poles, or other mount places.

The information about sites, equipment rooms, and other more precise
locations is critical, but it cannot be automatically populated and
retrieved from network elements (NEs).  Instead, it is usually
configured manually.

The Network Inventory location model is to record physical locations,
such as sites, building, equipment rooms, racks, and so on.
Additionally, it includes provisions for physical addresses or geo-
location data (geographic coordinates).  The location model can
extend the base network inventory 

{{!I-D.ietf-ivy-network-inventory-yang}} to enrich NEs with location
information.

The Network Inventory location model is classified as a network
configuration model (Section 4 of  {{?RFC8309}}).

The YANG data model in this document conforms to the Network
Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

* XXXX --> the assigned RFC number for this I-D
* AAAA --> the assigned RFC number for {{!I-D.ietf-ivy-network-inventory-yang}}

## Terminology and Notations

The following terms are defined in {{!RFC7950}} and are not
redefined here:

*  client
*  server
*  augment
*  data model
*  data node
The following terms are defined in {{!RFC6241}} and are not redefined
here:
*  configuration data
*  state data
The tree diagram used in this document follows the notation defined
in {{?RFC8340}}..

 Also, this document uses terms defined in {{!I-D.ietf-ivy-network-inventory-yang}}.

# Hierarchical Locations of Network Inventory

The "location" list is generalized to support a variety of geographic
location, such as sites, rooms, buildings.

A site represents a general geographic location to group a set of NEs
and corresponding inventory components.  NEs, racks, equipment rooms,
and buildings can be grouped within a site.

A room is a facility, a space for network elements and other
equipment (such as servers, storage) with power supply systems, air
conditioning system, etc.

Locations can be nested to form a hierarchy.  For example, buildings
may be within a site, and a room may be within a building.

The "location-type" is defined as a YANG identity to identify the
type of an inventory location, which may be site, equipment room,
building, etc.

~~~~ ascii-art
     +--rw locations
        +--rw location* [id]
        |  +--rw id                  string
        |  +--ro uuid?               yang:uuid
        |  +--rw name?               string
        |  +--rw description?        string
        |  +--rw alias?              string
        |  +--rw type?               identityref
        |  +--rw parent?             -> ../../location/id
        |  +--rw child*              -> ../../location/id
        |  +--rw physical-address
        |  |     ...
        |  +--rw geo-location
        |        ...
~~~~
{: #fig-ni-location-tree title="YANG Subtree of Location"}



# Rack

"racks" represent physical equipment racks in which NEs can be
installed, which facilitate device maintenance.  Through "rack-
location", each rack can be assigned to a site or a specific location
within a site, such as an equipment room.

Each rack is assigned a unique ID and a name in the context of a
facility, e.g. a site.  A rack may have some specific attributes,
such as appearance-related attributes and electricity-related
attributes.  The height, depth and width are described by Figure 2
(please consider that the door of the rack is facing the user).

Note: Further discussion is needed to decide whether to separate
"racks" from the list of "location".

~~~~ ascii-art
                          ----------------      ---
                          /|              /|      |
                         / |             / |      |
                        /  |            /  |      |
                       ----|-----------|   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |    height
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   | Door    Q |   |      |
                       |   |         Q |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   |           |   |      |
                       |   /-----------|----     ---
                       |  /            |   /      /
                       | /             |  /      depth
                       |/              | /      /
                       -----------------      ---
                       |______width____|
                       |               |
~~~~
{: #fig-rack title="Height, Width and Depth of Rack" artwork-align="center"}

The rack attributes include:

~~~~~~~~~~
        +--rw racks
           +--rw rack* [id]
              +--rw id                     string
              |     ...
              +--rw height?                uint16
              +--rw width?                 uint16
              +--rw depth?                 uint16
              +--rw max-voltage?           uint16
              +--rw max-allocated-power?   uint16
              +--rw contained-chassis* [relative-position]
                    ...
~~~~~~~~~~
{: #tree title="YANG Subtree of Rack" artwork-align="center"}

Max-voltage: the maximum voltage supported by the rack.

# Network Inventory Location Tree {#sec-module}

{{full-tree}} provides an overview of the data model for "ietf-ni-location" module.

~~~~~~~~~~
{::include ./ietf-ni-location-tree.txt}
~~~~~~~~~~
{: #full-tree title="Network Inventory Location Tree Structure" artwork-align="center"}

# YANG Data model for Network Inventory Location

The "ietf-ni-location" module uses types defined in {{!RFC9179}},
   {{!I-D.ietf-ivy-network-inventory-yang}}.

~~~~~~~~~~
<CODE BEGINS> file "ietf-ni-location@2024-08-19.yang"
{::include-fold ./ietf-ni-location.yang}
<CODE ENDS>
~~~~~~~~~~

# Security Considerations

This section uses the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

The YANG module specified in this document defines schema for data
that is designed to be accessed via network management protocols such
 as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
is the secure transport layer, and the mandatory-to-implement secure
transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
is HTTPS, and the mandatory-to-implement secure transport is TLS
 {{!RFC8446}}.

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are
writable/creatable/deletable (i.e., config true, which is the
default).  These data nodes may be considered sensitive or vulnerable
in some network environments.  Write operations (e.g., edit-config)
and delete operations to these data nodes without proper protection
or authentication can have a negative effect on network operations.
Specifically, the following subtrees and data nodes have particular
sensitivities/vulnerabilities:

Some of the readable data nodes in this YANG module may be considered
sensitive or vulnerable in some network environments.  It is thus
important to control read access (e.g., via get, get-config, or
notification) to these data nodes.  Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

'locations':
The list may be used to track the set of network elements.

# IANA Considerations

IANA is requested to register the following URI in the "ns" subregistry within
the "IETF XML Registry" {{!RFC3688}}:

~~~~
URI:  urn:ietf:params:xml:ns:yang:ietf-ni-location
Registrant Contact:  The IESG.
XML:  N/A; the requested URI is an XML namespace.
~~~~

IANA is requested to register the following YANG module in the "YANG Module
Names" registry {{!RFC6020}} within the "YANG Parameters" registry group:

~~~~
Name:  ietf-network-inventory-topology
Namespace:  urn:ietf:params:xml:ns:yang:ietf-ni-location
Prefix:  nil
Maintained by IANA?  N
Reference:  RFC XXXX
~~~~

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to thank the authors and contributors of
{{?I-D.ietf-ccamp-network-inventory-yang}} to trigger this work.
During the discussion of base Network Inventory (NI) model, it is agreed
that the definition of the equipment room and rack can be a separate
location model and support manual configuration, while the NI model
aggregates the inventory data of the Network Elements (NEs) on the
network. Usually the information about sites or equipment rooms is
not detectable by network controller and configured manually.

The authors wish to thank Mohamed Boucadair and many others for their helpful comments and suggestions. 
