---
title: Simulating the Internet
author: mehul
date: 2024-05-31 09:03:00
categories: [Blogging, Opinion]
tags: [BGP, BGP Poisoning, Autonomous Systems, Internet Simulation, Network Topology, AS Level Connections, Internet Hierarchy, P2C Connections, P2P Connections, Internet Exchange Points (IXPs), Network Protocols, Internet Mapping, Tier-1 ASes, Transit ASes, Stub ASes, Internet Dynamics, Network Evolution, Internet Measurements, Internet Datasets, Cybersecurity, Network Security]
image:
  path: '/assets/img/headers/internet.jpg'
  alt: Image of Internet created using small geometric shapes in a monochrome design on a black background.
---
## Introduction

Simulating the Internet involves creating realistic network topologies and using simulation environments to study various network behaviors and protocols. The Internet is a vast and complicated mess of interconnections. To successfully simulate it, we require two things:

- **Topology Generator**: We need a way to effectively randomize and generate the topology based on what the Internet looks like. We could do this process manually, however considering that we might need our topology to be massive to get valuable outcomes, having a topology generator that can design the topology based on a few parameters would be key.
- **Simulation Environment**: With a generated topology, next we need a way to simulate it. These simulations could be as detailed and specific to certain protocols as needed, or as general as possible to assess Internet trends.

## Topology Generation

A significant challenge in studying BGP dynamics is creating a realistic simulation environment that accurately reflects the Internet's topology ([read more about the shape of the Internet using my previous blogpost](https://paradoxicalbit.com/posts/What-Internet-Looks-Like/)). I explored various network topology generators, each with its own strengths and limitations. Here’s a brief overview of the tools I examined and my custom algorithm developed for this purpose.

### **BRITE (Boston university Representative Internet Topology gEnerator) by Medina et al.**:

This tool utilizes two main methods to generate the network topology.

- **Waxman**: The connectivity probability between two nodes is based on a mathematical formula considering their distance and parameters α and β.
- **Barabási**: This method involves incremental node addition, preferring connections to already well-connected nodes.

It has adjustable parameters such as the plane's size, node count, and growth type. It also supports random and heavy-tailed node placements.

- *Pros*:
	- Models complex relationships and can generate top-down/bottom-up architectures.
	- Can create various topology architectures (flat AS level, router level, hierarchical).
- *Cons*:
	- Does not consider different AS types and does not control inter-AS connections.
	- Does not represent the true Internet as it uses randomness and user-specified parameters.

![BRITE - Network Topology Generator](/assets/img/posts/BRITE1.png){: width="972" height="589" } *BRITE Network Topology Generator*

### **INET-3.0 by Winick and Jamin**:

The original tool built in 2002 was not available (https://topology.eecs.umich.edu/inet/) but found reprinted code on GitHub (https://github.com/isliusc/NS3-Inet-3.0).

- *Pros*:
	- Customizable nodes by choosing the proportion of degree 1 nodes.
	- Uses a plane to plot nodes first, then establishes connections.
	- Establishes weights for links.
- *Cons*:
	- No consideration for p2p/p2c.
	- Only works for networks with 3037+ nodes.
	- Cannot create custom AS categories.

![INET-3.0 - Network Topology Generator](/assets/img/posts/INET3.0.png){: width="972" height="589" } *INET-3.0 Network Topology Generator*

### **ASHIIP by Tomasik and Weisser**:

ASHIIP was intended as an enhancement over BRITE, focusing on large-scale topologies. Unfortunately, their server was down (http://wwwdi.supelec.fr/software/ashiip), and I couldn't access it online.

### **My Custom Algorithm**:

Given the specific requirements of simulating the Internet, I developed a custom algorithm that offers a more streamlined approach:

- **P2P and P2C Connections**: Supports both types of connections in a specific order.
- **AS Categories**: Supports and can expand on AS categories (Tier, Transit, Stub).
- **Node Generation and Connection**: Generates nodes first and connects them based on predefined parameters, making it more representative of the actual Internet than purely random values.

Considering the need for p2p and p2c connections, specific AS categories, and a more accurate representation of the Internet, my algorithm stands out as the best choice for this simulation. By generating nodes first and connecting them based on constraints, it aligns closer to real-world Internet structures compared to other tools that rely heavily on randomness.

Simulating the Internet is a complex task, but with the right tools and approaches, we can create more accurate models to study and understand the vast network that connects us all. This is crucial for testing and improving protocols like BGP, which is fundamental to the Internet's operation.

## Simulation Environment

I used the [Mini-Internet Project](https://github.com/nsg-ethz/mini_internet_project) by Holterbach et al. due to its high customizability and open-source nature. This project utilizes docker containers for ASes and employs Open vSwitches and FRRouting to deploy switches and routers within the topology. It also has a looking-glass service that provides access to updated routing tables across the topology, which can be used to automate data collection.
Hardware used was a Virtual Machine running an Ubuntu 20.04 instance with 16GB RAM, 4 CPU cores, and 40GB storage.
