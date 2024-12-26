---
title: Hybrid SDN Management System
summary: "A network management system to manage hybrid SDN
networks (consisting of SDN and traditional non-SDN
devices) allowing users to configure various policies using a unified
GUI environment."
description: "A network management system to manage hybrid SDN
networks (consisting of SDN and traditional non-SDN
devices) allowing users to configure various policies using a unified
GUI environment."
technologies: [SDN, OpenFlow, Ryu Framework, NETCONF, Python, React]
date: 2024-12-20
draft: false
showauthor: false
---

{{< github repo="AzzamAlsharafi/FYP-hSDN" >}}

A network management system to manage hybrid [SDN]
networks (consisting of [SDN] and traditional non-SDN
devices) allowing users to configure various policies using a unified
GUI environment.

{{< youtubeLite id="8IWqjOU-V7w" label="Demo" >}}

<figcaption>This video was originally part of a recorded presentation. Presentation audio has been removed.</figcaption>

## Overview

The Hybrid [SDN] Network Management System is designed to manage hybrid networks that include both [SDN] *(Software-Defined Networking)* and traditional non-SDN devices. The system provides a unified, user-friendly GUI that enables configuration, and monitoring of network devices.

At the heart of the system is a modular controller built with the [Ryu framework], which allows communication with [SDN] devices using [OpenFlow], while also incorporating [NETCONF] support for managing traditional network devices. This dual compatibility allows management of hybrid network infrastructures from a single platform.

The system implements policy management, allowing users to define and enforce network policies for flow management, routing, blocking, and more. Policies can be created, modified, or deleted through the GUI, offering flexibility in network management.

With its real-time network visualization, the system allows users to monitor network performance, identify congestion or underutilized resources, and make informed decisions about their network configurations. The system’s interactive network graph also gives better user experience.

## Usage Guide

This section provides a general overview of the features offered by the system and how use them.

### Initial Setup

* Networking devices must be accessible to management server (where the system is running).
* Traditional devices must be pre-configured with a management address and have [NETCONF] enabled.
* Traditional devices must be configured with the same [NETCONF] username and password.

### Adding devices

* To add new traditional devices, users can either use the GUI or edit `config/netconf.txt` configuration file. Example:
```bash
# Example of a configuration file for classic devices (Non-SDN)
# Empty lines, and lines beginning with '#' are ignored.

# First two valid lines set NETCONF username and password for all devices
user = USERNAME
pass = PASSWORD

# Each line represents a single device.
# IPv4 address followed by hostname. Separated by a single space.
192.168.1.11 R1
192.168.1.12 R2
192.168.1.21 R3
192.168.1.22 R4
```

* To add new [SDN] devices, simply configure the management server as the controller on the [SDN] device itself.

### Network View

![System GUI][img-gui]


Users can monitor their network using the GUI. It displays a network summary panel on the side, and a visualization of active devices and their connections.
The network graph is interactive allowing users to freely move nodes and organize the view as they see fit.

Only active links are displayed on the graph; any disabled links or links blocked by policies are hidden. Links are color-coded based on utilization:
* **Red:** high load.
* **Green:** low load.
* **Gray:** unutilized.

Additionally, available subnets are represented as green boxes, connected to their gateway devices.

### Policy Management

All configurations are managed using policies, which can be created, modified, or deleted via the side panel.

The system currently supports six types of policies:
* **Address Policy:** Defines a host subnet in the network. When used, the system configures the appropriate gateway interface and updates routing on 
all devices in the network to ensure accessibility to the new subnet.
* **Flow Policy:** Specifies a flow, which is a set of source and destination IPs, protocol, and port numbers. Defined flows can be used in other policies.
* **Block Policy:** Blocks traffic matching a flow.
* **Route Policy:** Controls the routing of traffic that matches a specified flow.
* **Zone Policy:** Assigns a device as a member to a zone. Zones can be used with Block Policy.
* **Disable Policy:** Disables an interface on a device.

#### Device Multi-selection

Route Policy and Zone Policy can be added to multiple devices in one action by using **Device Multi-Selection**.

To select multiple devices:
* Hold the `Shift` key and draw a selection box around the targeted devices.
* Hold the `Ctrl` key and select multiple devices one by one.

When multiple devices are selected, the following actions will appear in the side panel:
* **Add Zone:** Adds all selected devices to the specified zone.
* **Add Route:** Configures a new route for the specified flow that passes through the selected devices in the order they were selected in. 
If multiple paths are possible, the system will present a list of the best routes, allowing the user to choose from.

## System Design

The system consists of three primary components, each of which is built using a different framework:
* **Controller:** Manages communication with networking devices. Built using [Ryu framework].
* **GUI Application:** The Graphical User Interface. Built using [React].
* **API:** Connection link between the Controller and the GUI. Built using [FastAPI].

![System Architecture Diagram][img-architecture]

### Controller

The Controller is the heart of the system. Built using [Ryu framework], it is responsible for managing and controlling networking devices through nine different specialized modules, as shown in the above diagram.

#### Ryu Framework
[Ryu] is a **Python**-based framework for developing [SDN] systems. The framework offers two main capabilities:
* A modular architecture, where each module runs as a '*Ryu Application*'. [Ryu] runs each application in its own thread, and allows inter-module communication
through message-passing.
* Built-in [OpenFlow] support, enabling applications 
to recieve and send [OpenFlow] messages to OpenFlow-enabled switches.

#### Traditional Devices Management
While [SDN] devices can be managed using the built-in [OpenFlow] support, traditional devices require [NETCONF] protocol for their communication. 
The **NETCONF Controller** module achieves that by establishing and maintaining [NETCONF] connections with the registered traditional devices. 
Other modules utilize this module to perform any function on traditional devices.

#### Topology Discovery
Topology discovery is achieved using **Topology Discovery Unit**, which manages the network topology using [LLDP]. 
This is done by: 
* Detecting and registering new devices. 
* Keeping track of devices status and availability.
* Mapping the links between the devices. 
* Monitoring for any topology changes.

The unit comprises the following three modules:
* **Classic Topology Discovery:** Perform topology discovery on traditional devices, making use of the **NETCONF Controller** module for
its communication with the traditional devices.

* **SDN Topology Discovery:** Handles topology discovery on [SDN] devices. It communicates with the devices
directly through [Ryu framework] native [OpenFlow] support.

* **Topology Manager:** Collects discovery data from both discovery modules, combining the data to generate a uniform topology for the whole network.

![Topology Discoevry Process][img-topology]

#### Policies Management
The **Policy Manager** module provides management of user-defined policies. While policies are defined via the GUI, the **Policy Manager** handles:
* Storage of policies.
* Validation of new policies.
* Exposing the policies to other parts of the system.

#### Device Configuration
Configurations are specified using high-level user-defined policies, 
which are taken by the **Configuration Unit** to be translated to appropriate per-device configurations. 

The **Configuration Unit**, similarly to **Topology Discovery Unit**, comprises three modules:
* **Configuration Generator:** Combines user-defined policies with the current network topology to generate per-device configurations. New configurations are
generated whenver the policies or the topology changes.
* **Classic Configurator:** Handles traditional devices configuration by using **NETCONF Controller** to send appropriate [NETCONF] commands to
each traditional devices to apply its generated configurations.
* **SDN Configurator:** Manages configurations for [SDN] devices by sending [OpenFlow] messages to each device to apply the configurations.

### GUI
The GUI is developed using [React] Framework. The network visualization is implemented using the [ReactFlow] library.

### API
The system includes a simple API developed using [FastAPI]. The API is used to faciliate communication between the Controller and the GUI. The Controller uses it to expose active topology
and policies, while the GUI uses it to send instructions to the Controller to create new policies or register new traditional devices.

[SDN]: https://en.wikipedia.org/wiki/Software-defined_networking
[Ryu framework]: https://github.com/faucetsdn/ryu
[Ryu]: https://github.com/faucetsdn/ryu
[OpenFlow]: https://en.wikipedia.org/wiki/OpenFlow
[NETCONF]: https://en.wikipedia.org/wiki/NETCONF
[React]: https://react.dev/
[ReactFlow]: https://reactflow.dev/
[FastAPI]: https://fastapi.tiangolo.com/
[LLDP]: https://en.wikipedia.org/wiki/Link_Layer_Discovery_Protocol

[img-architecture]: ./architecture.png "Diagram of system architecture"
[img-gui]: ./featured_gui.png "System GUI"
[img-topology]: ./topology-discovery.png "Topology discovery visualization showing how each device is only aware of directly connected devices"