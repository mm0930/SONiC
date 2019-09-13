# PortChannel
Openconfig support for PortChannel interface via openconfig-interfaces.yang
# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 09/09/2019  |   Tejaswi Goel      | Initial version                   |

# About this Manual
This document provides information about the PortChannel configuration in SONiC using management framework container

# Scope
Covers Northbound interface for the PortChannel feature, as well as Unit Test cases.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
|          LAG             | Link aggregation                    |
|          LACP            | Link Aggregation Control Protocol   |

# 1 Feature Overview
Add support for PortChannel create/set/get via CLI, REST and GNMI using  openconfig-interfaces.yang data model and sonic-mgmt-framework container

## 1.1 Requirements
Provide management framework capabilities to handle:
- PortChannel creation and deletion
- Addition of ports to PortChannel
- Removal of ports from PortChannel
- Configure min-links, mtu, admin-status and ip address
- Show PortChannel details

### 1.1.1 Functional Requirements
Provide management framework support to existing SONiC capabilities with respect to PortChannel

### 1.1.2 Configuration and Management Requirements
- IS-CLI style configuration and show commands
- REST API support
- gNMI Support

Details described in Section 3.

### 1.1.3 Scalability Requirements
key scaling factors -N/A
### 1.1.4 Warm Boot Requirements
N/A

## 1.2 Design Overview
### 1.2.1 Basic Approach
Provide transformer methods in sonic-mgmt-framework container for PortChannel handling

### 1.2.2 Container
All code changes will be done in management-framework container

### 1.2.3 SAI Overview
N/A

# 2 Functionality
## 2.1 Target Deployment Use Cases
Manage/configure PortChannel interface via GNMI, REST and CLI interfaces
## 2.2 Functional Description
Provide CLI, GNMI and REST support PortChannel related commands handling

# 3 Design
## 3.1 Overview
Enhancing the management framework backed code and transformer methods to add support for PortChannel interface Handling

## 3.2 DB Changes
N/A
### 3.2.1 CONFIG DB
No changes to database schema's just populate Config DB
### 3.2.2 APP DB
Read data
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
### 3.3.2 Other Process
N/A

## 3.4 SyncD
N/A

## 3.5 SAI
N/A

## 3.6 User Interface
### 3.6.1 Data Models
List of yang models required for PortChannel interface management.
1. **openconfig-if-aggregate.yang** 
2. **openconfig-interfaces.yang**

Supported yang objects and attributes:
```diff

module: openconfig-interfaces
+   +--rw interfaces
+      +--rw interface* [name]
+         +--rw name                   -> ../config/name
+         +--rw config
          |  +--rw name?            string
          |  +--rw type             identityref
+         |  +--rw mtu?             uint16
          |  +--rw loopback-mode?   boolean
          |  +--rw description?     string
+         |  +--rw enabled?         boolean
          |  +--rw oc-vlan:tpid?    identityref
+         +--ro state
+         |  +--ro name?            string
          |  +--ro type             identityref
+         |  +--ro mtu?             uint16
          |  +--ro loopback-mode?   boolean
          |  +--ro description?     string
          |  +--ro enabled?         boolean
          |  +--ro ifindex?         uint32
+         |  +--ro admin-status     enumeration
+         |  +--ro oper-status      enumeration
          |  +--ro last-change?     oc-types:timeticks64
          |  +--ro logical?         boolean
+         +--rw subinterfaces
+         |  +--rw subinterface* [index]
+         |     +--rw index           -> ../config/index
          |     +--rw config
          |     |  +--rw index?         uint32
          |     |  +--rw description?   string
          |     |  +--rw enabled?       boolean
          |     +--ro state
          |     |  +--ro index?          uint32
          |     |  +--ro description?    string
          |     |  +--ro enabled?        boolean
          |     |  +--ro name?           string
          |     |  +--ro ifindex?        uint32
          |     |  +--ro admin-status    enumeration
          |     |  +--ro oper-status     enumeration
          |     |  +--ro last-change?    oc-types:timeticks64
          |     |  +--ro logical?        boolean
          |     |  +--ro counters
          |     |     +--ro in-octets?             oc-yang:counter64
          |     |     +--ro in-pkts?               oc-yang:counter64
          |     |     +--ro in-unicast-pkts?       oc-yang:counter64
          |     |     +--ro in-broadcast-pkts?     oc-yang:counter64
          |     |     +--ro in-multicast-pkts?     oc-yang:counter64
          |     |     +--ro in-discards?           oc-yang:counter64
          |     |     +--ro in-errors?             oc-yang:counter64
          |     |     +--ro in-unknown-protos?     oc-yang:counter64
          |     |     +--ro in-fcs-errors?         oc-yang:counter64
          |     |     +--ro out-octets?            oc-yang:counter64
          |     |     +--ro out-pkts?              oc-yang:counter64
          |     |     +--ro out-unicast-pkts?      oc-yang:counter64
          |     |     +--ro out-broadcast-pkts?    oc-yang:counter64
          |     |     +--ro out-multicast-pkts?    oc-yang:counter64
          |     |     +--ro out-discards?          oc-yang:counter64
          |     |     +--ro out-errors?            oc-yang:counter64
          |     |     +--ro carrier-transitions?   oc-yang:counter64
          |     |     +--ro last-clear?            oc-types:timeticks64
          |     +--rw oc-vlan:vlan
          |     |  +--rw oc-vlan:config
          |     |  |  +--rw oc-vlan:vlan-id?   union
          |     |  +--ro oc-vlan:state
          |     |     +--ro oc-vlan:vlan-id?   union
+         |     +--rw oc-ip:ipv4
+         |     |  +--rw oc-ip:addresses
+         |     |  |  +--rw oc-ip:address* [ip]
+         |     |  |     +--rw oc-ip:ip        -> ../config/ip
+         |     |  |     +--rw oc-ip:config
+         |     |  |     |  +--rw oc-ip:ip?              oc-inet:ipv4-address
+         |     |  |     |  +--rw oc-ip:prefix-length?   uint8
+         |     |  |     +--ro oc-ip:state
+         |     |  |     |  +--ro oc-ip:ip?              oc-inet:ipv4-address
+         |     |  |     |  +--ro oc-ip:prefix-length?   uint8
          |     |  |     |  +--ro oc-ip:origin?          ip-address-origin
+         |     +--rw oc-ip:ipv6
+         |        +--rw oc-ip:addresses
+         |        |  +--rw oc-ip:address* [ip]
+         |        |     +--rw oc-ip:ip        -> ../config/ip
+         |        |     +--rw oc-ip:config
+         |        |     |  +--rw oc-ip:ip?              oc-inet:ipv6-address
+         |        |     |  +--rw oc-ip:prefix-length    uint8
+         |        |     +--ro oc-ip:state
+         |        |     |  +--ro oc-ip:ip?              oc-inet:ipv6-address
+         |        |     |  +--ro oc-ip:prefix-length    uint8
          |        |     |  +--ro oc-ip:origin?          ip-address-origin
          |        |     |  +--ro oc-ip:status?          enumeration
+         +--rw oc-eth:ethernet
+         |  +--rw oc-eth:config
          |  |  +--rw oc-eth:mac-address?           oc-yang:mac-address
          |  |  +--rw oc-eth:auto-negotiate?        boolean
          |  |  +--rw oc-eth:duplex-mode?           enumeration
          |  |  +--rw oc-eth:port-speed?            identityref
          |  |  +--rw oc-eth:enable-flow-control?   boolean
+         |  |  +--rw oc-lag:aggregate-id?          -> /oc-if:interfaces/interface/name
+         |  +--ro oc-eth:state
          |  |  +--ro oc-eth:mac-address?              oc-yang:mac-address
          |  |  +--ro oc-eth:auto-negotiate?           boolean
          |  |  +--ro oc-eth:duplex-mode?              enumeration
          |  |  +--ro oc-eth:port-speed?               identityref
          |  |  +--ro oc-eth:enable-flow-control?      boolean
          |  |  +--ro oc-eth:hw-mac-address?           oc-yang:mac-address
          |  |  +--ro oc-eth:negotiated-duplex-mode?   enumeration
          |  |  +--ro oc-eth:negotiated-port-speed?    identityref
          |  |  +--ro oc-eth:counters
          |  |  |  +--ro oc-eth:in-mac-control-frames?    oc-yang:counter64
          |  |  |  +--ro oc-eth:in-mac-pause-frames?      oc-yang:counter64
          |  |  |  +--ro oc-eth:in-oversize-frames?       oc-yang:counter64
          |  |  |  +--ro oc-eth:in-undersize-frames?      oc-yang:counter64
          |  |  |  +--ro oc-eth:in-jabber-frames?         oc-yang:counter64
          |  |  |  +--ro oc-eth:in-fragment-frames?       oc-yang:counter64
          |  |  |  +--ro oc-eth:in-8021q-frames?          oc-yang:counter64
          |  |  |  +--ro oc-eth:in-crc-errors?            oc-yang:counter64
          |  |  |  +--ro oc-eth:in-block-errors?          oc-yang:counter64
          |  |  |  +--ro oc-eth:out-mac-control-frames?   oc-yang:counter64
          |  |  |  +--ro oc-eth:out-mac-pause-frames?     oc-yang:counter64
          |  |  |  +--ro oc-eth:out-8021q-frames?         oc-yang:counter64
+         |  |  +--ro oc-lag:aggregate-id?             -> /oc-if:interfaces/interface/name
+         +--rw oc-lag:aggregation
+         |  +--rw oc-lag:config
          |  |  +--rw oc-lag:lag-type?    aggregation-type
+         |  |  +--rw oc-lag:min-links?   uint16
+         |  +--ro oc-lag:state
          |  |  +--ro oc-lag:lag-type?    aggregation-type
+         |  |  +--ro oc-lag:min-links?   uint16
          |  |  +--ro oc-lag:lag-speed?   uint32
+         |  |  +--ro oc-lag:member*      oc-if:base-interface-ref
```

**Note:** Currently LACP fallback not supported, so will be augmenting openconfig-if-aggregate.yang data model. 

### 3.6.2 CLI
#### 3.6.2.1 Configuration Commands
| Command description | CLI command |
| :------ | :----- |
| Create a PortChannel  | sonic(config)# interface PortChannel \<channel-number>  |
| Configure min-links |  sonic(conf-if-poX)# minimum-links \<number> |
| Configure MTU |  sonic(conf-if-poX)#  mtu \<number> |
| Configure default MTU |  sonic(conf-if-poX)# no mtu |
| Disable interface |  sonic(conf-if-poX)# shutdown |
| Enable interface |  sonic(conf-if-poX)# no shutdown|
| Configure IP address |  sonic(conf-if-poX)# ip address \<ip-address with mask> |
| Remove IP address |  sonic(conf-if-poX)# no ip address \<ip-address> |
| Add port member | sonic(config)# interface EthernetX \<id> <br><br> sonic(conf-if-EthernetX)# channel-group \<channel-number>  |
| Remove a port member | sonic(config)# interface EthernetX <br><br> sonic(conf-if-EthernetX)# no channel-group|
| Delete a PortChannel | sonic(config)# no interface PortChannel \<channel-number> |

#### 3.6.2.2 Show Commands

| Command description | CLI command |
| :------ | :----- |
| Display summary information about PortChannels| sonic# show PortChannel summary |

Example output:
````
Flags:  D - Down
        U - Up (PortChannel)
--------------------------------------------------------------------------------
Group Port-Channel           Type     Protocol  Member Ports
--------------------------------------------------------------------------------
1    PortChannel1    (D)     Eth      LACP    Ethernet56, Ethernet60
10   PortChannel10   (D)     Eth      LACP
111  PortChannel111  (D)     Eth      LACP

````

| Command description | CLI command |
| :------ | :----- |
| Show Interface status and configuration | sonic# show interface PortChannel 1 |

Example output:
````
PortChannel 1 is up, line protocol is down
Interface index is 49
MTU 1532 bytes, IP MTU 1500 bytes
Minimum number of links to bring Port-channel up is 1
Members in this channel: Ethernet56
Input statistics:
     0 packets, 0 octets
     0 64-byte pkts, 0 over 64-byte pkts, 0 over 127-byte pkts
     0 over 255-byte pkts, 0 over 511-byte pkts, 0 over 1023-byte pkts
     0 Multicasts, 0 Broadcasts, 0 Unicasts
     0 runts, 0 giants, 0 throttles
     0 CRC, 0 overrun, 0 discarded
Output statistics:
     0 packets, 0 octets
     0 64-byte pkts, 0 over 64-byte pkts, 0 over 127-byte pkts
     0 over 255-byte pkts, 0 over 511-byte pkts, 0 over 1023-byte pkts
     0 Multicasts, 0 Broadcasts, 0 Unicasts
     0 throttles, 0 discarded, 0 Collisions,  wred drops
Rate Info(interval  seconds):
     Input 0 Mbits/sec, 0 packets/sec, 0% of line rate
     Output 0 Mbits/sec, 0 packets/sec, 0% of line rate
Time since last interface status change: 1 day 21:33:17

````

#### 3.6.2.3 Debug Commands
N/A

#### 3.6.2.4 IS-CLI Compliance
N/A

### 3.6.3 REST API Support
| Command description | OpenConfig Command Path |
| :------ | :----- |
| Create/Delete a PortChannel | /openconfig-interfaces:interfaces/interface={name} |
| Set min-links  | /openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/config/min-links |
| Set MTU/admin-status  | /openconfig-interfaces:interfaces/interface={name}/config/[admin-status|mtu] |
| Set IP | /openconfig-interfaces:interfaces/interface={name}/subinterfaces/subinterface[index=0]/openconfig-if-ip:ipv4/addresses/address={ip}/config |
| Add/Remove port member | /openconfig-interfaces:interfaces/interface={name}/openconfig-if-ethernet:ethernet/config/openconfig-if-aggregate:aggregate-id  |
| Get PortChannel details | /openconfig-interfaces:interfaces/interface={name}/openconfig-if-aggregate:aggregation/state <br><br> /openconfig-interfaces:interfaces/interface={name}/state/[admin-status | mtu]|

# 4 Flow Diagrams
N/A

# 5 Error Handling
TBD

# 6 Serviceability and Debug
TBD

# 7 Warm Boot Support
N/A

# 8 Scalability
N/A

# 9 Unit Test
- Validate PortChannel creation via CLI, GNMI and REST
    - Verify error returned if PortChannel ID out of supported range
- Validate min links, mtu and admin-status config for PortChannel via CLI, GNMI and REST
    - Verify error returned if min-links value out of supported range
- Validate addition of ports to PortChannel via CLI, GNMI and REST
    - Verify error returned if port already part of other PortChannel
    - Validate MTU, speed and list of Vlans permitted on each member link are same as PortChannel
- Validate removal of ports from PortChannel via CLI, GNMI and REST
    - Verify error returned if PortChannel does not exist
    - Verify error returned if invalid interface given
- Validate PortChannel deletion via CLI, GNMI and REST
    - Verify error returned if PortChannel does not exist

# 10 Internal Design Information
