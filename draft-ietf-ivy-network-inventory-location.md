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
    email: s.belotti.ietf@gmail.com
-
    ins: J-F. Bouquier
    name: Jean-Francois Bouquier
    org: Vodafone
    email: jeff.bouquier@vodafone.com
-
    name: Fabio Peruzzini
    org: FiberCop
    email: fabio.peruzzini@fibercop.com
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

--- middle

# Introduction

Network Elements (NEs) can be grouped by location to provide more information for
network planning, deployment, and maintenance. For example, location information
enables operators to rapidly locate problematic NEs, optimize resource usage,
and perform capacity forecasting. The location may describe outdoor or indoor
placements: indoor hierarchies include sites, buildings, equipment rooms.
Racks are separate physical mounting containers that are associated with a location.
Outdoor locations include mounting infrastructure such as poles and walls.

This document defines a YANG data model for physical location records in the network inventory.
The model captures containment hierarchy information, along with optional
postal addresses and geo-location data (geographic coordinates).
It extends the base network inventory model in {{!I-D.ietf-ivy-network-inventory-yang}}
to attach physical placement context to managed inventory items.

The Network Inventory location model is classified as a network model (Section 3.5.1 of {{?RFC9907}}).

The model provides a read-only view of location data maintained by an authoritative management system,
covering physical location entities (sites, rooms, racks), NEs, and hardware components physically deployed in the live network.
Warehouse spares, inactive assets, and planned but undeployed sites are out of scope.
Details on the source and update semantics of location records are provided in {{sec-operation}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

* XXXX --> the assigned RFC number for this document
* AAAA --> the assigned RFC number for {{!I-D.ietf-ivy-network-inventory-yang}}

## Terminology and Notations

The following terms are defined in {{!RFC7950}} and are not
redefined here:

*  server
*  augment
*  data model
*  data node

Also, this document uses terms defined in Section 2.2 of {{!I-D.ietf-ivy-network-inventory-yang}}, specifically:

*  Network Inventory
*  Network Element
*  Component
*  Chassis

The tree diagram used in this document follows the notation defined
in {{?RFC8340}}.

Some examples in this document contain long lines that may be folded, as described in {{?RFC8792}}.

# Hierarchical Locations of Network Inventory

The "location" list is generalized to support a variety of geographic
location types, such as sites, buildings, and equipment rooms.

A site represents a general geographic location to group a set of NEs,
inventory components, racks, and buildings. A room is a facility,
a space for network elements and other equipment (such as compute
servers, storage) with power supply systems, air conditioning system, etc.

Locations can be nested to form a hierarchy. For example, buildings
may be within a site, and a room may be within a building.
The "type" leaf within "location" list is a string used to identify the
type of an inventory location, which may be site, equipment room,
building, or pole.

The location list includes a contained‑chassis list. This list references
chassis hardware instances that are deployed directly within this spatial
location without being mounted inside a rack (for example wall‑mounted access
points or outdoor optical devices). Each contained‑chassis entry uses ne‑ref and
component‑ref leafrefs pointing to network‑element and component records from
{{!I-D.ietf-ivy-network-inventory-yang}}.

~~~~ ascii-art
+--ro locations
   +--ro location* [id]
      +--ro id                   string
      +--ro uuid?                yang:uuid
      +--ro name?                string
      +--ro alias?               string
      +--ro description?         string
      +--ro type?                string
      +--ro parent?              -> ../../location/id
      +--ro contained-chassis* [chassis-id]
      |     ...
      +--ro timestamp?           yang:date-and-time
      +--ro valid-until?         yang:date-and-time
      +--ro physical-address
      |     ...
      +--ro geo-location
            ...
~~~~
{: #fig-ni-location-tree title="YANG Subtree of Location"}



# Rack

"racks" represent physical equipment racks in which NEs can be
installed, which facilitate device maintenance.
Through "rack-location", each rack can be assigned to a site
or a specific location within a site, such as an equipment room.

A rack is modelled as an independent inventory asset with a reference
to a location. With this design, a rack retains its stable inventory
identity when physically moved to another location.

"racks" defines its own contained‑chassis list. This list documents
chassis hardware installed in specific U‑slot positions inside this rack.
Each entry uses ne‑ref and component‑ref to reference corresponding records
in {{!I-D.ietf-ivy-network-inventory-yang}}. The key relative‑position expresses the U‑slot
position inside the rack.

Each rack is assigned a unique ID and a name in the context of a
facility, e.g. a site. A rack may have some specific attributes,
such as appearance-related attributes and electricity-related
attributes. The height, depth and width are described by Figure 2
(please consider that the door of the rack is facing the user).

Note: Further discussion is needed to decide whether to separate
"racks" from the list of "location".

~~~~ aasvg
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

~~~~~~~~~~ascii-art
+--ro racks
   +--ro rack* [id]
      +--ro id                     string
      +--ro rack-class?            identityref
      +--ro uuid?                  yang:uuid
      +--ro name?                  string
      +--ro alias?                 string
      +--ro description?           string
      +--ro rack-location
      |     ...
      +--ro contained-chassis* [relative-position]
      |     ...
      +--ro height?                uint16
      +--ro width?                 uint16
      +--ro depth?                 uint16
      +--ro max-voltage?           uint16
      +--ro max-allocated-power?   uint32
      +--ro timestamp?             yang:date-and-time
      +--ro valid-until?           yang:date-and-time
~~~~~~~~~~
{: #tree title="YANG Subtree of Rack" artwork-align="center"}

Max-voltage: the maximum voltage supported by the rack.

# Network Inventory Location Tree {#sec-module}

{{full-tree}} provides an overview of the data model for "ietf-ni-location" module.

~~~~~~~~~~
{::include-fold ./ietf-ni-location-tree.txt}
~~~~~~~~~~
{: #full-tree title="Network Inventory Location Tree Structure" artwork-align="center"}

# YANG Data model for Network Inventory Location

The "ietf-ni-location" module uses types defined in {{!RFC9911}}, {{!RFC9179}}, and {{!I-D.ietf-ivy-network-inventory-yang}}.

~~~~~~~~~~ yang
{::include ./ietf-ni-location.yang}
~~~~~~~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-ni-location@2026-07-06.yang"}

# Operational Considerations {#sec-operation}

This model complements {{!I-D.ietf-ivy-network-inventory-yang}} to
enable operators to associate inventory items with physical location
context. It supports operational workflows such as fault localization,
site capacity planning, and physical asset verification by querying
location-to-asset and asset-to-location relationships.

For a given location data, the authoritative management system (e.g., a network controller)
maintains the data and serves as its source of truth. Location records can be populated
by automated tooling including RFID and geolocation services, or via manual
entry through the authoritative system’s interfaces. External management
applications consume this data as operational state using YANG-based
management protocols, such as NETCONF or RESTCONF, for retrieval operations only.
The mechanisms for manual configuration of these location records within
the authoritative system are outside the scope of this document.

In large-scale inventories containing numerous network elements and
components, querying location associations can impose a load on the server.
Mechanisms such as RESTCONF or NETCONF pagination should be utilized for
queries involving large result sets.

Each location record includes a timestamp for the last update, plus an optional
valid-until leaf that sets an expiration for the record’s validity. Before
relying on a location record for field dispatch or network planning, consumers
SHOULD confirm that either a physical address or geo-location is provided,
and that valid-until is either omitted or holds a future time. Once valid-until
has elapsed, the location record MUST be considered stale and MUST NOT be used
for operational decisions.

Intent-based configuration of location information is outside the scope of this document.

# Security Considerations

This section is modeled after the template described in {{Section 3.7.1 of ?RFC9907}}.

The "ietf-ni-location" YANG module defines a data model that is
designed to be accessed via YANG-based management protocols, such as
Network Configuration Protocol (NETCONF) {{!RFC6241}}
and RESTCONF {{!RFC8040}}.  These YANG-based management protocols (1) have to
use a secure transport layer (e.g., Secure Shell (SSH) {{?RFC6242}}, TLS {{?RFC9846}}, and
QUIC {{?RFC9000}}) and (2) have to use mutual authentication
(e.g., for SSH, the SSH Authentication Protocol {{?RFC4252}}).

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

Some of the readable data nodes in this YANG module may be considered
sensitive or vulnerable in some network environments. It is thus
important to control read access (e.g., via get, get-config, or
notification) to these data nodes. Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

"/nwi:network-inventory/nilo:locations": This subtree reports physical deployment
information including facility structures (sites, buildings, rooms),
and optionally geographic coordinates.

"/nwi:network-inventory/nilo:racks": This subtree reports rack physical attributes
(dimensions, power capacity, security classification).

Both subtrees reference network elements and components by their inventory
identifiers (ne-id, component-id). Uncontrolled disclosure may:
*  Enable association of inventory identifiers with physical facility
   structures and geographic coordinates
*  Reveal facility layouts, equipment density, and rack physical
   security characteristics when combined with other network data
*  Expose precise geographic coordinates (when configured), which may
   facilitate physical location identification
*  Indicate physical protection levels through rack security
   classifications

The "ietf-ni-location" YANG module augments the base network inventory
model defined in {{!I-D.ietf-ivy-network-inventory-yang}}. All security
considerations specified in the base model apply to this extension module.

# IANA Considerations

IANA is requested to register the following URI in the "ns" registry within
the "IETF XML Registry" group {{!RFC3688}}:

~~~~
URI:  urn:ietf:params:xml:ns:yang:ietf-ni-location
Registrant Contact:  The IESG.
XML:  N/A; the requested URI is an XML namespace.
~~~~

IANA is requested to register the following YANG module in the "YANG Module
Names" registry {{!RFC6020}} within the "YANG Parameters" registry group.

~~~~
Name:  ietf-ni-location
Maintained by IANA?  N
Namespace:  urn:ietf:params:xml:ns:yang:ietf-ni-location
Prefix:  nilo
Reference:  RFC XXXX
~~~~

--- back

# Examples
This section provides example usages of the Network Inventory Location
 model for two common deployment scenarios: a non-rack-mounted Access Point
 and a distributed multi-chassis network element.

## Non-Rack Deployment: Access Point
This example illustrates a campus deployment scenario where a WLAN
Access Point (AP) is mounted directly to a ceiling without a rack enclosure.
 The AP is modeled as a chassis directly contained by
the location, using the location's `contained-chassis` list. Its
`chassis-id` is a location-local key,

The location hierarchy is as follows:

~~~~ aasvg
Site: Foo-Enterprise-Campus
└── Building: Building-A
    └── Floor: Floor-2
        └── Room: Corridor-East (corridor area)
            └── AP directly ceiling-mounted (no rack)
~~~~

The following shows the location data instance:

~~~~ json
{::include-fold ./non-rack-example.json}
~~~~

## Distributed Multi-Chassis Network Element

This example illustrates a distributed deployment where a single
 logical network element (NE-1, a stack switch) spans multiple
 racks. The three chassis of the stack switch are located in
 adjacent racks within the same telecommunications room,
 interconnected via stacking cables.
The location hierarchy is as follows:

~~~~ aasvg
Site: Foo-DC
└── Location: Room-101 (Telecom Room 101)
    ├── Rack: Rack-101-A
    │   └── U10: NE-1 chassis-1 (Master switch)
    ├── Rack: Rack-101-B
    │   └── U15: NE-1 chassis-2 (Stack member)
    └── Rack: Rack-101-C
        └── U20: NE-1 chassis-3 (Stack member)
~~~~

The following shows the location data instance:

~~~~ json
{::include-fold ./rack-example.json}
~~~~

{: numbered="false"}

# Acknowledgments
{:numbered="false"}

The authors would like to thank the authors and contributors of
{{?I-D.ietf-ccamp-network-inventory-yang}} to trigger this work.
During the discussion of base Network Inventory (NI) model, it is agreed
that the definition of the equipment room and rack can be a separate
location model and support manual configuration, while the NI model
aggregates the inventory data of the Network Elements (NEs) on the
network.

The authors wish to thank Mohamed Boucadair and many others for their helpful comments and suggestions.
