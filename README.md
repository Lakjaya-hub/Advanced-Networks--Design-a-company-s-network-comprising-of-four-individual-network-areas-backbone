# Advanced-Networks--Design-a-company-s-network-comprising-of-four-individual-network-areas-backbone

I designed company’s network comprising of six individual network areas and a backbone area. 

Two of the areas will be using OSPF as the Interior Routing Protocol, one area will be running RIP and One area will be using EIGRP. Other two remote branch networks will be running OSPF as the Interior Routing Protocol. The backbone area (area 0) is also using OSPF as the backbone area routing protocol. Additionally, we developed the 3 GRE tunnels. One GRE tunnel was placed between backbone area and remote branch, another GRE tunnel was placed between OSPF area and remote branch, finally GRE tunnel was placed between Remote branch one and remote branch two.


We have considered the following features in our design:
 
-	Fully functional routing between all networks/areas of the company.
-	Fully functional routing between Company internal network and Remote branches.
-	Inclusion of a ASBR router that connects the area 0 (backbone) to the Internet and used to direct all the organization’s traffic to the Internet.
-	Redistribution of routes between OSPF and RIP.
-	Redistribution of routes between OSPF and EIGRP.
-	The use of OSPF interface authentication for security.
-	Establishment a GRE tunnel between remote branch office, located outside the company network and one of the OSPF branch networks.
-	Use of ACLs to restrict access to certain services from branch networks in the company.

1.1) Introduction to Routing Protocols

Routing Protocol Basics
A routing protocol is the language a router speaks with other routers in order to share information about the reachability and status of network. It includes a procedure to select the best path based on the reachability information it has and for recording this information in a route table. Regarding to select the best path, a routing metric will be applied, and it is computed by a routing algorithm.
Routing Metric Basics
A metric is a variable assigned to routers as a means of ranking them from the best to worst or from most preferred to least preferred. (1) Different routing protocols have different metrics. When there is more than one route between two nodes, a router must determine method of metrics by choose the routing protocol to calculate the best path.
Static Routing Dynamic Routing
Protocols can fall into two groups: static routing and dynamic routing. Static routing is simply the process of manually entering routes into a device’s routing table via a configuration file that is loaded when the routing device starts up. In static routing, all the changes in the logical network layout need to be manually done by the system administrator. However, dynamic routing allows routers to select the best path when there is a real time logical network layout change. In our project, we will discuss the difference between the EIGRP, RIP and OSPF. All of them are belong to dynamic routing protocols.
Distance Vector and Link State
In addition, most routing protocols can be classified into two classes: distance vector and link state. Distance vector routing protocol is based on Bellman – Ford algorithm and Ford Fulkerson algorithm to calculate paths. A distance vector routing protocol uses a distance calculation and a vector direction of next hop router as reported by neighboring routers to choose the best path. It requires that a router informs its neighbors of topology changes periodically.
Link state routing protocols build a complete topology of the entire network are and then calculating the best path from this topology of all the interconnected networks. It requires more processing power and memory because it has a complete picture of the network.








1.2) Routing Information Protocol (RIP)

RIP is a standardized vector distance routing protocol and uses a form of distance as hop count metric. It is a distance vector. Through limiting the number of hop counts allowed in paths between sources and destinations, RIP prevents routing loops. Typically, the maximum number of hops allowed for RIP is 15. However, by achieving this routing loop prevention, the size of supporting networks is sacrificed. Since the maximum number of hop counts allowed for RIP is 15, as long as the number goes beyond 15, the route will be considered as unreachable.
When first developed, RIP only transmitted full updates every 30 seconds. In the early distributions, traffic was not important because the routing tables were small enough. As networks become larger, massive traffic burst becomes more likely during the 30 seconds period, even if the routers had been initialized at different times. Because of this random initialization, it is commonly understood that the routing updates would spread out in time, but that is not the case in real practice.
RIP has four basic timers:
Update Timer (default 30 seconds): defines how often the router will send out a routing table update.
Invalid Timer (default 180 seconds): indicates how long a route will remain in a routing table before being marked as invalid if no new updates are heard about this route. The invalid timer will be reset if an update is received for that route before the timer expires. A route marked as invalid is not immediately removed from the routing table. Instead, the route is marked with a metric of 16,
which means the route is unreachable and will be placed in a hold-down state. Hold-down Timer (default 180 seconds): specifies how long RIP will keep a route from receiving updates when it is in a hold-down state. In a hold-down state, RIP will not receive any new updates for routes until the hold-down timer expires. A route will go into a hold-down state for the following
reasons:
• The invalid timer has expired
• An update has been received from another router; route goes into a 16 metric (or unreachable).
• An update has been received from another router; route goes into a higher metric than what it is currently using.
Flush Timer (default 240 seconds): When no new updates are received about this route, flush timer indicates how long a route can remain in a routing table before getting flushed out. The flush timers operate simultaneously with the invalid timer, so every 60 seconds, after it has been marked invalid, the route will get flushed out. When RIP timer is not in sync with all routers on the RIP network, system instability occurs. This timer must be set to a higher value than the invalid timer.




1.3) Open Shortest Path First (OSPF)

OSPF is defined in RFC 2328 which is an interior Gateway Protocol used to distribute routing information within an AS (Autonomous System). Among all the three chosen samples, OSPF is the most widely used routing protocol in large enterprise networks. OSPF is based on link-state technology by using SPF algorithm which calculates the shortest path.
SPF calculation
Before running the calculation, it is required that all routers in the network to know about all the other routers in the same network and the links among them. The next step is to calculate the shortest path between each single router. For all the routers they exchange link-states which would be stored in the link-state database. Every time a router receives a link-state update, the information stores into the database and this router propagate the updated information to all the other routers. Below is a simple model of how the SPF algorithm works.
A simple network formed by five routers; all the routers know about all the other routers and links. After all the paths are figured out, the path information is stored in the link database.









The link database for the above model is: [A, B, 3], [A, C, 6], [B, A, 3], [B, D, 3], [B, E, 5], [C, A, 6], [C, D, 9], [D, C, 9], [D, B, 3], [D, E, 3] , [E, B, 5] and [E, D, 3]. Each term is referred to the originating router, the router connected to and the cost of the link between the two routers. Once the database of each router is finished, the router determines the Shortest Path Tree to all the destinations. (The shortest path in the SPF algorithm is called the Shortest Path Tree). The Dijkstra Shortest Path First is then running to determine the shortest path from a specific router to all the other routers in the network. Each router is put at the root of the Shortest Path Tree and then the shortest path to each destination is calculated. The accumulated cost to reach the destination would be the shortest path.




The cost (metric) of OSPF is the cost of sending packets across a certain interface. The formula to calcite the cost is: cost= 10000 0000 /bandwidth in bps. If the bandwidth is wider, the cost would be lower. Below is a diagram of the structure used to calculate the Shortest Path Tree.
 

When the Shortest Path Tree is completed, the router will work on the routing table. 

Areas and Border Routers

In OSPF protocol, an Autonomous System can be divided into sections. A section and a nearby router can dorm an AREA. Since each section calculate the Shortest Path using the same algorithm as above, each section has its own database and path tree, and the information are invisible outside this section. By doing this, the size of the database can be dramatically reduced.
 

In an autonomous, there is a backbone called Area 0 which is the pivot of this autonomous. Area 0 connected to other sections with ABR (Area Border Router) to exchange link-state. Stub Area, Not-So-Stubby Area, Totally Stubby Areas and Totally NSSA are other types of sections with specific functions.

Advantages
Compared to RIP, OSPF has no limitation due to hops (RIP has a limit of 15 hops so any network with more than 15 hops cannot be achieved by RIP. 
OSPF can handle Variable Length Subnet Masks (VLSM) but RIP cannot. 
The most important is that OSPF converges much faster than RIP due to its calculation algorithm. This might not be significant in a small size network but in large enterprise networks, this will be a time out.


Open Shortest Path First (OSPF) Areas

In OSPF, a single autonomous system (AS) can be divided into smaller groups called areas. This reduces the number of link-state advertisements (LSAs) and other OSPF overhead traffic sent on the network, and it reduces the size of the topology database that each router must maintain. The routing devices that participate in OSPF routing perform one or more functions based on their location in the network.
Areas
An area is a set of networks and hosts within an AS that have been administratively grouped together. We recommend that you configure an area as a collection of contiguous IP subnetted networks. Routing devices that are wholly within an area are called internal routers. All interfaces on internal routers are directly connected to networks within the area.
The topology of an area is hidden from the rest of the AS, thus significantly reducing routing traffic in the AS. Also, routing within the area is determined only by the area’s topology, providing the area with some protection from bad routing data. All routing devices within an area have identical topology databases.
Area Border Routers
Routing devices that belong to more than one area and connect one or more OSPF areas to the backbone area are called area border routers (ABRs). At least one interface is within the backbone while another interface is in another area. ABRs also maintain a separate topological database for each area to which they are connected.
Backbone Areas
An OSPF backbone area consists of all networks in area ID 0.0.0.0, their attached routing devices, and all ABRs. The backbone itself does not have any ABRs. The backbone distributes routing information between areas. The backbone is simply another area, so the terminology and rules of areas apply: a routing device that is directly connected to the backbone is an internal router on the backbone, and the backbone’s topology is hidden from the other areas in the AS.
The routing devices that make up the backbone must be physically contiguous. If they are not, you must configure virtual links to create the appearance of backbone connectivity. You can create virtual links between any two ABRs that have an interface to a common nonbackbone area. OSPF treats two routing devices joined by a virtual link as if they were connected to an unnumbered point-to-point network.
AS Boundary Routers
Routing devices that exchange routing information with routing devices in non-OSPF networks are called AS boundary routers. They advertise externally learned routes throughout the OSPF AS. Depending on the location of the AS boundary router in the network, it can be an ABR, a backbone router, or an internal router (with the exception of stub areas). Internal routers within a stub area cannot be an AS boundary router because stub areas cannot contain any Type 5 LSAs.
Routing devices within the area where the AS boundary router resides know the path to that AS boundary router. Any routing device outside the area only knows the path to the nearest ABR that is in the same area where the AS boundary router resides.
Backbone Router
Backbone routers are routing devices that have one or more interfaces connected to the OSPF backbone area (area ID 0.0.0.0).
Internal Router
Routing devices that connect to only one OSPF area are called internal routers. All interfaces on internal routers are directly connected to networks within a single area.
Stub Areas
Stub areas are areas through which or into which AS external advertisements are not flooded. You might want to create stub areas when much of the topological database consists of AS external advertisements. Doing so reduces the size of the topological databases and therefore the amount of memory required on the internal routers in the stub area.
Routing devices within a stub area rely on the default routes originated by the area’s ABR to reach external AS destinations. You must configure the default-metric option on the ABR before it advertises a default route. Once configured, the ABR advertises a default route in place of the external routes that are not being advertised within the stub area, so that routing devices in the stub area can reach destinations outside the area.
The following restrictions apply to stub areas: you cannot create a virtual link through a stub area, a stub area cannot contain an AS boundary router, the backbone cannot be a stub area, and you cannot configure an area as both a stub area and a not-so-stubby area.

Not-So-Stubby Areas
An OSPF stub area has no external routes in it, so you cannot redistribute from another protocol into a stub area. A not-so-stubby area (NSSA) allows external routes to be flooded within the area. These routes are then leaked into other areas. However, external routes from other areas still do not enter the NSSA.
The following restriction applies to NSSAs: you cannot configure an area as both a stub area and an NSSA.
Transit Areas
Transit areas are used to pass traffic from one adjacent area to the backbone (or to another area if the backbone is more than two hops away from an area). The traffic does not originate in, nor is it destined for, the transit area.
OSPF Area Types and Accepted LSAs
The following table gives details about OSPF area types and accepted LSAs:
 



1.4) Enhanced Interior Gateway Routing Protocol (EIGRP)

The Enhanced Interior Gateway Routing Protocol (EIGRP) is a hybrid routing protocol which provides significant improvements on IGRP. EIGRP replaced IGRP in 1993 since Internet Protocol is designed to support IPv4 addresses that IGRP could not support. Hybrid routing protocol incorporates advantages of both Link-state and Distance-Vector routing protocols, it was based on Distance-Vector protocol but contains more features of Link-State protocol. EIGRP saves all routes rather than the best route to ensure the faster convergence.
EIGRP keeps neighboring routing tables and it only exchange information that it neighbor would not contain. EIGRP is commonly used in large networks, and it updates only when a topology changes but not periodically unlike old Distance-Vector protocols such as RIP. Metric is used to determine whether the chosen route is optimized. EIGRP metric is based on its bandwidth, delay, reliability, load and MTU. A default expression for EIGRP metric is 𝑀𝑒𝑡𝑟𝑖𝑐 = 𝐵𝑎𝑛𝑑𝑊𝑖𝑑𝑡ℎ + 𝐷𝑒𝑙𝑎𝑦 ∗ 256.
There are four basic components to operate EIGRP, which are
• Neighbour Discovery/Recovery
• Reliable Transport Protocol
• DUAL Finite State Machine
• Protocol Dependent Module

Since EIGRP updates are triggered when there is a change, it is important to have a process that routers dynamically learn of other routers on directly connected network. A router should discover once a neighboring router is unreachable of inoperative. Neighbor Discovery and Recovery is accomplished by sending small Hello packets periodically at low cost. Once the hello packets are received, whether this neighbor is alive can be determined.The neighboring router will start exchanging information when routers are functioning. The default EIGRP algorithm DUAL requires guaranteed and ordered delivery of packets for transmission which reliable transport protocol manages. EIGRP sends a unicast data receiver indicating the hello packet does not require the confirmation of the notification packet. When there is a packet sending, a sequence number is assigned which is incremented by the router sending a new packet. Reliable Transport Protocol ensures fast transport when there are unacknowledged packets pending. That guarantees the low convergence time.

DUAL, the Diffusing Update Algorithm is the default convergence algorithm which is used in EIGRP to prevent routing loops from recalculating routes. DUAL tracks all routes and detect the optimal path in terms of efficiency and cost which will be added in the routing table. There also exist backup routes that can be used in case the optimal route is dropped. Protocol Dependent Modules are used to encapsulate the IP packets for network layer. It determines if an additional route is necessary from sources such as routing table. PDMs make sure that EIGRP provides support for various routed protocols.


 

1.5) Route Redistribution
Route redistribution runs on the router that connects two networks. It's really the main "shared" location between the two networks that translates protocols and routes for seamless integration.When working with routing protocols and redistribution, you might hear it referred to as mutual redistribution. Route redistribution is the process in which one shared resource maps and translates each route — regardless of the protocol used on different network segments.
Typically, route redistribution is only needed on larger networks. But even small office networks can grow into massive segments that need route redistribution. Think about your own network and its connection to the internet.  




2) Network design    


Our task was to design an enterprise network. Our enterprise network design has a head office and a branch office. The head office network is consisting of six individual network areas and a backbone area (OSPF area 0). The two remote branch office is consisting of one individual network which is also using OSPF.
As the interior routing protocols OSPF, RIP and EIGRP is used. The head office consists of two areas which is running OSPF, where one area is running RIP and two areas are running EIGRP. The branch office is running on another OSPF network area. OSPF is being used as the Exterior / Backbone Routing Protocol in the backbone area (area 0) that connects the branch offices. The head office network is connected to the internet and the branch office also connected to the internet using Autonomous System Boundary Routers (ASBR).









