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
    email: sergio.belotti@nokia.com
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

Accurate location information is useful for network planning,
deployment, and maintenance. However, such information cannot be
obtained or verified from the Network Elements themselves. This
document defines a location model for network inventory that extends
the base inventory with comprehensive location data.

--- middle

# Introduction

Network Elements (NEs) can be grouped by location to provide more information for
network planning, deployment, and maintenance (e.g., easily locate
problematic NEs, optimize network resources, or help planning
forecasts). The location can reflect outdoor or indoor information.
An indoor location may be represented as a building, room, or other
similar organizational structures. Outdoor locations can be walls,
poles, or other mount places.

The information about sites, equipment rooms, and other more precise
locations is critical, but it cannot be automatically populated and
retrieved from NEs. Instead, it is usually configured manually.

The Network Inventory location model is to record physical locations,
such as sites, building, equipment rooms, racks, and so on.
Additionally, it includes provisions for physical addresses or geo-
location data (geographic coordinates). The location model augments the base network inventory {{!I-D.ietf-ivy-network-inventory-yang}} to enrich NEs with location information.

The Network Inventory location model is classified as a network model (Section 3.5.1 of {{?I-D.ietf-netmod-rfc8407bis}}).

The YANG data model in this document conforms to the Network
Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

* XXXX --> the assigned RFC number for this document
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
in {{?RFC8340}}.

 Also, this document uses terms defined in {{!I-D.ietf-ivy-network-inventory-yang}}.

# Hierarchical Locations of Network Inventory

The "location" list is generalized to support a variety of geographic
location, such as sites, rooms, buildings.

A site represents a general geographic location to group a set of NEs
and corresponding inventory components. NEs, racks, equipment rooms,
and buildings can be grouped within a site.

A room is a facility, a space for network elements and other
equipment (such as servers, storage) with power supply systems, air
conditioning system, etc.

Locations can be nested to form a hierarchy. For example, buildings
may be within a site, and a room may be within a building.

The "location-type" is defined as a YANG identity to identify the
type of an inventory location, which may be site, equipment room,
building, etc.

~~~~ ascii-art
+--ro locations
   +--ro location* [id]
   |  +--ro id                   string
   |  +--ro uuid?                yang:uuid
   |  +--ro name?                string
   |  +--ro alias?               string
   |  +--ro description?         string
   |  +--ro type?                string
   |  +--ro parent?              -> ../../location/id
   |  +--ro timestamp?           yang:date-and-time
   |  +--ro valid-until?         yang:date-and-time
   |  +--ro physical-address
   |  |     ...
   |  +--ro geo-location
   |  |     ...
   |  +--ro contained-chassis* [chassis-id]
   |        ...
~~~~
{: #fig-ni-location-tree title="YANG Subtree of Location"}



# Rack

"racks" represent physical equipment racks in which NEs can be
installed, which facilitate device maintenance. Through "rack-
location", each rack can be assigned to a site or a specific location
within a site, such as an equipment room.

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
       +--ro height?                uint16
       +--ro width?                 uint16
       +--ro depth?                 uint16
       +--ro max-voltage?           uint16
       +--ro max-allocated-power?   uint16
       +--ro contained-chassis* [relative-position]
       |     ...
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
{: sourcecode-markers="true" sourcecode-name="ietf-ni-location@2026-02-28.yang"}

# Operational Considerations

This model serves as a complement to the base inventory, providing
 a read-only perspective of network inventory location information
 known to the controller. It reports the physical locations of network
 elements and components installed in the network, enabling queries
 for site, rack, and other location-related information associated
 with network elements and components.

When used in brownfield scenarios, it is worth noting that existing
 deployments are based on proprietary inventory OSS solutions, and
 the migration path is highly dependent on the specific proprietary
 implementation.

The model is designed based on the controller maintaining authoritative
 location data through automated tooling, while OSS systems consume
 this data as read-only operational state. Sources of controller location
 data may include RFID (Radio Frequency IDentification) tooling, geolocation services, as well as manual
 entry via controller interfaces.

As this data is read-only, the controller does not support OSS modification
 of controller location records.

OSS systems and other management applications obtain location information
 via standard YANG retrieval operations (NETCONF, RESTCONF), such as
 querying network elements associated with a specific site or rack.

In large-scale inventories containing numerous network elements and
 components, querying location associations can impose a load on the
 server. To optimize retrieval and avoid overwhelming the server, mechanisms
 such as RESTCONF or NETCONF pagination should be utilized for queries
 involving large result sets.

Data quality is indicated through timestamps recording the last update
 time, as well as an optional expiration time for location validity.

Before using a location for field dispatch or planning, verification
 is required to ensure at least one of physical-address or geo-location
 is present, and that the valid-until leaf is either not present or indicates
 a future time. Once the valid-until time has passed, the location MUST be
 considered stale and MUST NOT be used for operational purposes.


A parallel "location-planning" container (read-write) may be introduced
 in future revisions to support intent-based configuration, where OSS provides
 planning-level location targets. This is outside the scope of the current document.

# Security Considerations

This section is modeled after the template described in {{Section 3.7.1 of ?RFC9907}}.

The "ietf-ni-location" YANG module defines a data model that is
designed to be accessed via YANG-based management protocols, such as
Network Configuration Protocol (NETCONF) {{!RFC6241}}
and RESTCONF {{!RFC8040}}.  These YANG-based management protocols (1) have to
use a secure transport layer (e.g., SSH {{!RFC6242}}, TLS {{!RFC8446}}, and
QUIC {{!RFC9000}}) and (2) have to use mutual authentication.

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

Some of the readable data nodes in this YANG module may be considered
sensitive or vulnerable in some network environments. It is thus
important to control read access (e.g., via get, get-config, or
notification) to these data nodes. Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

'locations': This list reveals physical deployment positions,
      facility structures, and geographic distribution of
      network elements.  Uncontrolled disclosure may enable
      mapping of critical infrastructure, leading to physical
      security or operational risks.

"rack-class": This leaf indicates the security classification of a rack.
Unauthorized disclosure of rack security classification can reveal physical security
      characteristics of critical infrastructure and network equipment
      deployments.

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
Prefix:  nil
Reference:  RFC XXXX
~~~~

--- back

# Examples
This section provides example usages of the Network Inventory Location
 model for two common deployment scenarios: a non-rack-mounted Access Point
 and a distributed multi-chassis network element.

## Non-Rack Deployment: Access Point
This example illustrates a typical edge deployment scenario where a Wi-Fi
 Access Point (AP) is mounted directly to a ceiling without a rack enclosure.

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
{
  "ietf-ni-location:locations": {
    "location": [
      {
        "id": "Foo-Enterprise-Campus",
        "uuid": "550e8400-e29b-41d4-a716-446655440000",
        "name": "Foo Enterprise Campus",
        "type": "site",
        "timestamp": "2026-01-15T08:30:00Z"
      },
      {
        "id": "Building-A",
        "uuid": "550e8400-e29b-41d4-a716-446655440001",
        "name": "Building A",
        "type": "building",
        "parent": "Foo-Enterprise-Campus",
        "timestamp": "2026-01-15T08:30:00Z"
      },
      {
        "id": "Floor-2",
        "uuid": "550e8400-e29b-41d4-a716-446655440002",
        "name": "Floor 2",
        "type": "floor",
        "parent": "Building-A",
        "timestamp": "2026-01-15T08:30:00Z"
      },
      {
        "id": "Corridor-East",
        "uuid": "550e8400-e29b-41d4-a716-446655440003",
        "name": "East Corridor",
        "alias": "Corridor-2F-East",
        "description": "East corridor on Floor 2, AP deployment area",
        "type": "corridor",
        "parent": "Floor-2",
        "timestamp": "2026-01-15T08:30:00Z",
        "valid-until": "2030-12-31T23:59:59Z",
        "physical-address": {
          "address": "123 Foo Street, Floor 2 East Corridor",
          "postal-code": "12345",
          "state": "Foo-State",
          "city": "Foo-City",
          "country-code": "ZZ"
        },
        "geo-location": {
          "reference-frame": {
            "astronomical-body": "earth",
            "geodetic-system": {
              "geodetic-datum": "WGS-84",
              "coord-accuracy": 5.0,
              "height-accuracy": 10.0
            }
          },
          "ellipsoid": {
            "latitude": 40.7128,
            "longitude": -74.0060,
            "height": 15.0
          },
          "velocity": {
            "v-north": 0.0,
            "v-east": 0.0,
            "v-up": 0.0
          },
          "timestamp": "2026-01-15T08:30:00Z",
          "valid-until": "2030-12-31T23:59:59Z"
        },
        "contained-chassis": [
          {
            "chassis-id": 1,
            "ne-ref": "AP-Corridor-East-01",
            "component-ref": "chassis-1"
          }
        ]
      }
    ],
    "racks": []
  }
}
~~~~

## Distributed Multi-Chassis Network Element

This example illustrates a distributed deployment where a single
 logical network element (NE-1, a stack switch) spans multiple
 physical locations. The three chassis of the stack switch
 are located in separate telecommunications rooms on different
 floors, interconnected via stacking cables.
The location hierarchy is as follows:

~~~~ aasvg
Site: Foo-DC
├── Location: Room-101 (First Floor Telecom Room)
│   └── Rack: Rack-101-A
│       └── U10: NE-1 chassis-1 (Master switch)
│
├── Location: Room-201 (Second Floor Telecom Room)
│   └── Rack: Rack-201-B
│       └── U15: NE-1 chassis-2 (Stack member)
│
└── Location: Room-301 (Third Floor Telecom Room)
    └── Rack: Rack-301-C
        └── U20: NE-1 chassis-3 (Stack member)
~~~~

The following shows the location data instance:

~~~~ json
{
  "ietf-ni-location:locations": {
    "location": [
      {
        "id": "Foo-DC",
        "name": "Foo Data Center",
        "type": "site",
        "timestamp": "2026-01-15T08:00:00Z"
      },
      {
        "id": "Room-101",
        "name": "First Floor Telecom Room",
        "type": "room",
        "parent": "Foo-DC",
        "timestamp": "2026-01-15T08:00:00Z",
        "contained-chassis": []
      },
      {
        "id": "Room-201",
        "name": "Second Floor Telecom Room",
        "type": "room",
        "parent": "Foo-DC",
        "timestamp": "2026-01-15T08:00:00Z",
        "contained-chassis": []
      },
      {
        "id": "Room-301",
        "name": "Third Floor Telecom Room",
        "type": "room",
        "parent": "Foo-DC",
        "timestamp": "2026-01-15T08:00:00Z",
        "contained-chassis": []
      }
    ],
    "racks": {
      "rack": [
        {
          "id": "Rack-101-A",
          "uuid": "660e8400-e29b-41d4-a716-446655440010",
          "name": "Rack A Room 101",
          "rack-location": {
            "location-ref": "Room-101",
            "row-number": 1,
            "column-number": 1
          },
          "height": 2200,
          "width": 600,
          "depth": 1200,
          "max-voltage": 240,
          "max-allocated-power": 8000,
          "contained-chassis": [
            {
              "relative-position": 10,
              "ne-ref": "NE-1",
              "component-ref": "chassis-1"
            }
          ],
          "timestamp": "2026-01-15T10:00:00Z",
          "valid-until": "2028-01-15T10:00:00Z"
        },
        {
          "id": "Rack-201-B",
          "uuid": "660e8400-e29b-41d4-a716-446655440011",
          "name": "Rack B Room 201",
          "rack-location": {
            "location-ref": "Room-201",
            "row-number": 2,
            "column-number": 1
          },
          "height": 2200,
          "width": 600,
          "depth": 1200,
          "max-voltage": 240,
          "max-allocated-power": 8000,
          "contained-chassis": [
            {
              "relative-position": 15,
              "ne-ref": "NE-1",
              "component-ref": "chassis-2"
            }
          ],
          "timestamp": "2026-01-15T10:00:00Z",
          "valid-until": "2028-01-15T10:00:00Z"
        },
        {
          "id": "Rack-301-C",
          "uuid": "660e8400-e29b-41d4-a716-446655440012",
          "name": "Rack C Room 301",
          "rack-location": {
            "location-ref": "Room-301",
            "row-number": 3,
            "column-number": 1
          },
          "height": 2200,
          "width": 600,
          "depth": 1200,
          "max-voltage": 240,
          "max-allocated-power": 8000,
          "contained-chassis": [
            {
              "relative-position": 20,
              "ne-ref": "NE-1",
              "component-ref": "chassis-3"
            }
          ],
          "timestamp": "2026-01-15T10:00:00Z",
          "valid-until": "2028-01-15T10:00:00Z"
        }
      ]
    }
  }
}

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
network. Usually the information about sites or equipment rooms is
not detectable by network controller and configured manually.

The authors wish to thank Mohamed Boucadair and many others for their helpful comments and suggestions.
