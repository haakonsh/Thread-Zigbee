# Thread-Zigbee

## This repo contains an Tutorial to Thread and Zigbee
The goal of this repo is to teach how one can set up Thread and ZigBee networks and access them using Nordic Examples.

### TODO: 
* Write an introduction to Thread and Zigbee from the notes.  
-explain the goal of the Thread Group and OpenThread project.  
-explain the network topology and the various types of roles(Leader, Comissioner, router, REED, MED, SED, parent, child, etc).  
-explain the OSI model, PHY-->MAC-->Network-->Application, and how a Thread network can interface wth the cloud through MQTT and CoAP.  
-Provide some graphics to aid the introduction (OSI model, network topology, etc)  .
* Give detailed examples with code snippets and message sequence charts on:  
-Starting a network(REED->Leader).  
-Joining a network(REED, MED, and SED).  
-Management of the number of Routers and REEDs.  
-partioning and merging of a partitioned network.  
-Sending and receiving data between routers, REEDS, MED, and SEDs. 
-Sending and receiving data between a cloud service and a SED over the application layer.   

* Create a demo that controls the nordic "smart house"(OpenThread network) via the nordic cloud service and an OpenThread network.   

***  




### Introduction to Thread

Links to [Thread Group](http://www.threadgroup.org) and [OpenThread](https://openthread.io/).

The main purpose of Thread is to extend the range of short range wireless devices and to insure simple access to the internet through IPv6 addressable nodes in a low power fashion.
Thread is a routed mesh with built in features to prevent any single point of failure, its innate self-healing properties makes Thread a very robust and reliable meshed network. 

OpenThread is an open source implementation of the Thread specification...

The minimum memory requirement for the OpenThread stack is 250kB Flash and 30kB Ram.

-Sleepy nodes allows for Low Power. Not required to check-in on a periodic basis. Parent nodes holds messages for sleepy devices.
"Available" devices: Nest and Legrand. Nest uses Thread OpenLeaf. Own application layer, planned to be open source. Waiting for this.

Product certification:
-You need to buy a Thread membership to certify a product.
-You also need to pay to certify each product.

-Our chip is certified, but that does not mean that the customers product can use this. We only certified to give the customer "confidence" that they are using already certified components.

FFD - Full-function device. 3 possible roles: PAN coordinator, coordinator or a device.
RFD - Reduced-function device. 1 possible role: device. (Leaf nodes)

RFDs can only talk to FFDs. FFDs can talk to other FFDs or RFDs.

### Zigbee:

sub-GHz problem: Have looked into it, but since there are different frequency bands around the world, this is not available at this point in time.

proprietary solutions on 802.15.4 is most commonly used for backwards compatibility. Not recommended for new products.

Not as frequent channel hopping as in regular BLE. Can be done in application layer.

LQI (Link quality Indication)
CCA (Clear channel assessment) used to measure the energy on a channel to see if it is busy or not.
PDDU format (PHY protocol data unit)
PHY limit of packet: 127 Bytes.

MAC layer: One layer above PHY. Big set of available functionalities.

IEEE 802.15.4 MAC
	- Beacon Enabled Frame
		-a lot of things.
		-NOT the same as BLE Beacon.
		-Uses a beacon to synchronize communication
		-not suitable for bigger networks. Difficult to synchronize several beacons.
	-Non Beacon Enabled Frame
		-used in Thread and Zigbee
		-unsynchronized communication.
		-Listen before talk.

		
Transmission options 
	- direct transmission
	- indirect transmission
		- used to communicate with sleepy-nodes.
		- stores up a queue of messages in FFDs waiting to transfer to sleepy-nodes/end nodes.
	-peer to peer data transfers.
		-coordinator - network device (end_node)
		-coordinator is always listening (when not transmitting).
		-coordinator stores up messages for end devices.
		-end device has to ask coordinator if they have any packs when it wakes up.

		
Security - basics:
	-Symmetric key cryptography (AES).
	
	
	
Nordic's SW Solutions:
	-802.15.4 is available in the SDK.
		-limited. It does have full MAC implementation. Can be useful.
	-Other stack availabla on GitHub.
		-https://github.com/NordicSemiconductor/nRF-IEEE-802.15.4-radio-driver
		-https://github.com/NordicSemiconductor/nRF-IEEE-802.15.4-radio-driver/wiki			 "infocenter" for the radio driver.
	

-----------------------------------------------------------------
nRF IEEE 802.15.4 radio driver features:
-Automatic sending/receiving of ACK frames.
radio periph. uses:
	-RADIO
	-2x Timer
	-1 EGU
	-7 PPI channels
	-1 PPI group
	-2 GPIOTE channels - if FEM support is enabled.
	
FEM: Front End Module is configurable. Select witch pins are used for PA and LNA (Power amplifier and Low Noise Amplifier) and which state is active (High/Low). FEM is disabled by default.

Debugging: A couple of defines: ENABLE_DEBUG_GPIO, ENABLE_DEBUG_ASSERT, ENABLE_DEBUG_LOG. GCC compiler only. (IAR available, but not the debug defines).

---------------------------------------------------------------------

IPV6 related protocols:

--


---------------------------------------------------------------------
Thread Protocol:

Two types of devices:
	-Full thread device - FTD
	-Minimal thread device - MTD (used for sleepy end devices (SED)) Also used if you have a chip with small flash/ram.
	
	-Max 32 active routers in a Thread network.
	-no single point of failure. A network has capability of electing a new leader.
	-You can have 32 routers, which each can have 511 child nodes. You can connect several thread networks wich has their own boarder router.
	
	-MED Minimal End Device
		-not power optimized.
		-good solution for cheap chips that has unlimited power.
	-SED Thread Sleepy End Device.
		-Same functionality as MED, but is low power.
	-REED Router Eligible End Device.
		-Starts off as an End Device. (All nodes in Thread does this). Has capability of becoming a Router.
	
	LEADER - REED - ROUTER are the same type of roles.
	SED - MED are also the same type.
		-these groups can change roles within the group.
		
Thread Commissioning:
	-off network commissioner: e.g. phone with commissioning app.
	-on-mesh commissioner: e.g. Border Router with NFC
	
	
Thread Routing:
	-Uses two-way RSSI to describe the quality of a link.
		-Routers periodically advertise their routing values, so that other the network can optimize the routing table.
		-This is the reason for the 32 routers. To prevent too large routing tables.
		-maximum advertisement interval is 64 seconds (network is in a stable state).
		-adverrisements are used to detect missing nodes.
			-if a router does not receive 3 consecutive ACKs, it will be removed from the routing table.
			
	EID (Endpoint Identifier) is an IPv6 address that uniquely identifies a Thread interface within a Thread Network.
	
	
	
-------------------------------------------------------------

Open Thread:
	-Certified Thread Stack.
	-https://github.com/openthread/openthread






Configuration for hands on is found on infocenter.

Thread Training
----
Day 2
----

Mesh: Joining a network
	- MLE attachment is a process of joining a Thread Network.
	- Every device has to attach to existing network as a Child (Routers as REEDs).
	- If no parent is found, REED can establish new partition and become its leader.
	- During attachment frame counters are synchronized (to prevent replay attacks).
	- Routers (and REEDs) within range may reply with MLE parent Response.
		- child chooses the best parent:
			- partition priority (randomized 32 bit number), link quality, number of parents neighbors are considered, routers are preferred over REEDs.
	
	- MLE Advertisement
		-serves as a keep-alive message for Routers.
		-message frequency is driven by trickle algorithm - up to 64s between messages.
	
	- Routing table
		- Routers have to be up-to-date with topology changes.
		- Check if Leader is present in the mesh.
			- if not the Routers will take over as leader, or look for another network.
		
		
	Thread Management Framework (TMF)
	-TMF address solicitation
		-Whenever REED wants to become a Router it must request Router ID from the Leader.
		Two basic triggers for becoming a Router:
			- Too few Router in the network. - By default REEDs will promote if there is less than 16 Routers.
			- REED was chosen to become a parent for attaching device - it must promote before accepting a child.
			
	Thread Partitions
		- Autonomous. Quite slick. (see slides)
		
	Thread Self Healing
		- Leader Loss:
			- Timeout of 120s
			- A new partition is created
			- (it is possible that all nodes will create their own partition) 
			- The partition with the highest ID will be leader
		
	Persistent Data
		-
		
		
		
		
Thread and Zigbee concurrently:
	-radio parts are the same (modulation e.g.)
	-May use different channels.
		-If they use the same channel?:
			-Then Possible for small networks
			- may be a bad user experience for the user.
			- is it possible to change channels very fast? (difficult because you need to listen all the time)
				- This is possible because of preamble etc. 4B of preamble and 1B of SFD (preamble stop/package start message).
				- The nRF radio is not capable of this.
				- All in all very difficult. Maybe possible in the future.
				- Recommend that customers use two chips for this (2 x radio)
	
Support for NCP:
	- available from github pull request.
	- Used Raspberry Pi 3B for reference.
	- Legacy Border Router Solution:
		- available from Github
		- 	








Raspberry demo:

Flash sd-card with image
connect via serial (putty) baud: 115200
command: 
	- sudo wpanctl status
	- wifi_connect NORDIC-IPV4/IPv6 BillionBluetooth
	               name              password
	- sudo nmoli o
	- ping 8.8.8.8
	- ip.route
	

	
	Zigbee Training
----
Day 3
----

The Renaissance of Zigbee - Why?
	- Zigbee is old and "well known". More than 15 years old.
	
	
Thread vs. Zigbee:
	- Zigbee defines the application layer.
	- Both run on IEEE 802.15.4

Zigbee before Zigbee 3.0
	- Zigbee application layers
		- products from different vendors should work together
		- Problem was that they used different "dialects".
		- Not one Zigbee, but many. Gained bad reputation. This is why we thought Zigbee was dying.

Zigbee 3.0:
	- At stack level, a Zigbee 3.0 device must conform to revision 21 of the Zigbee PRO stack specification (usually called the R21 stack).
	- All nodes should support the same commissioning process/level.
	- Not mandatory to support Green Power.
		- mandatory to support proxy requirement for Green Power.
			- Green Power is a low energy Zigbee. Relaxed req. of security.
	- Combine the different profiles.
		- Already use the same commissioning method
	- Profiles are gone. Cluster 
		
		
Thread vs. Zigbee:
	- Similar in many aspects. Mesh protocol for PAN (personal area network).
	- Target similar markets (Smart Home)
	- Zigbee can also run Sub 1MHz.
	- Run on identical silicon. (802.15.4).
	- Important differences:
		- App layer defined in Zigbee. Not in Thread.
			- Zigbee Cluster Library (ZCL)
		- Thread nodes have an IP address.
			-Zigbee has a binary 16.bit address that must be translated into the external IP world.
		- If you want to support NEST, go Thread.
		- If you want to support Amazon, go Zigbee.
		- We have a lot of memory on 840. Can support several protocols at the same time on the chip.
		- For Zigbee, the gateway is proprietary. May be different in different vendors.
		- Deep dive for Thread/Zigbee based on White Paper from Texas Instruments.

		
Zigbee Alliance:
	- Established in 2002.
	- You do not need to certify to create a Zigbee product. You can not use Zigbee Logo/TM .
	- If you want the Logo/TM, you must join the Zigbee Alliance at any level (cheaper than Thread. 4000$ vs. 15000$ for Thread).
	- You must remove the logo if you no longer are a member.



Zigbee Architecture.
	- Zigbee PRO standard defines:
		- Network layer (NWK)
		- Application support layer (APS)
		- Zigbee Device Object (ZDO)
	- Zigbee Cluster Library (ZCL)
		- defines Zigbee application layer.
	- Mainly 2.4GHz but sub 1GHz PHY is used for smart energu applications.
		- Currently you have to pick one, but soon you can run concurrently.
		- Sub 1GHz has some disadvantages. Only one channel, and duty cycle limitations. Max 20kbps.
		
Zigbee roles:
	- Zigbee Coordinator.
		- Responsible for forming a network.
			- Selects channel and PAN ID.
		- Acts as a Router
		- May ave added responsibilities
			- Trust center - manages security settings and network authorization.
			- Network manager - Manages PAN ID conflicts and channel change/switching.
			
	Zigbee Router:
		- Creates the core of mesh topology.
		- No limitation on number of Routers.
			- Networks of many thousand devices.
			- If there are many routers in a dense area, it is difficult to join.
			
	Zigbee End Device (ZED)
		- End device is a leaf node.
		- Only has one parent.
		- Similar to Thread.
		- Low power devices.
		- Both sleepy devices and non sleepy devices.
		
Network Addressing:
	- IEEE MAC address
		- 64-bit address - "extended address".
		- In Zigbee we should recommend to buy an organization MAC block.
		- Not needed in Thread.
	- Network address
		- 16-bit short address. 
		- Random. Assigned when joining the network.
		- Coordinator always has network address of 0x00.
		- if two devices has the same short address, you can differenciate them on the PAN ID.
		- No way to resolve extended PAN ID conflict (64-bit PAN ID).
		
		
Routing Concepts:
	- Unicast (table) Routing
		- Table holds the next step/hop for a given destination.
		- Route discovery used for building up the table.
		- May need large amounts of RAM
		- Acknowledged.
	- Broadcast routing
		- Different broadcast domains. (0xffff - all nodes, 0xfffd - all non sleeping nodes, 0xfffc - all routers, including coordinator).
		- Limited number of broadcast packets at a given time (often 9).
		- Retransmitted (default 3).
		- Not acknowledged.
	- Multicast Routing (depricated)
	- Many-to-one/Source Routing (not supported)
	
Route discovery:
	- Zigbee uses Ad-hoc On Demand Vector (AODV) routing protocol.
	- Route discovery is reactive rather than pro-active
	- Pros: small number of destinations -> small routing table
	- Cons: Large number of destinations -> large routing table
	- Algorithm in a nutshell:
		- broadcast a route request with intended destination
		- destination unicasts a ewplay to the source of route request.
	- Routes are aged.
	
Hop-by-hop reliability
	- Acknowledgments provided by 802.15.4 MAC
	- The MAC layer attempts transmission five 5  times
	

Network Formation:
	- Opening network:
		- Up to application to open the network, e.g. button press.
		- Network is opened for a period of time.
		- The weak point of the Zigbee spec is this procedure. Able to sniff secrets of the network if you sniff at this time. "ZigbeeAlliance09".
		- Joiner and Trust Center has a tunnel, where they exchange keys. The problem is that the key to this tunnel is publicly known ---^
		
		
Grouping:
	- Allows multiple endpoints from different devices into a group.
		- You can give this group an address.
		- group messages is broadcasted. If an device is part of this group, take action (e.g. turn on/off light).
		
		
		
Service Discovery:
	- Services for querying the application within a node.
	- Most important:
		- Active_EP_req:
			- Get a list of endpoints on a given device.
			- ... see slides.

Binding:
	- Network management:
		- Allows for getting diagnostic information
			- Neighbor table (Lqi_req)
			- Routing table (Rtg_req)
			- Binding table (Bind_rq)
		- Open network for joining
			- Permit_Joining_req
		- Change network parameters
			- NWK_Update_Req
	
	
	
Zigbee Security:
	- IEEE MAC level security is NOT used.
		- Two levels of security. Network (mandatory) and application.
	- CCM* with AES 127 for encryption and authentication.
	- Nodes are authenticated through Trust Center (TC)
	- All nodes within the network use the same key.
	- The key CAN BE (optional) rotated.
	- Frame counters are used for replay protection.
	- Hop-by-hop security.
	- Security is MANDATORY.
	- Everything except MAC header is authenticated (can't tamper the MIC (CRC ish)
		- Payload is encrypted.
	- APS layer security:
		- A "link key" is shared only between the two devices.
	- Trust Center Networks
		- One single entity authenticates devices on the network (often Coordinator).
			- What happens if the coordinator runs out of power?
				- The network will still operate, but it is not possible to authenticate new devices.
	- Distributed Trust Center Networks.
		- Any router can authenticate new devices on the network
		- The network key can not be rotated.
	- Decision on the security type on network forming.
	- Nodes which join a network inherit security type.
	- In Zigbee 3.0 every node will have an APS secure link with the Trust Center.
	- Trust center link keys
	- A separate key between the trust center and each device, used e.g. to send new network keys after frame counter rolls over.
	- New in Zigbee 3.0 security commissioning:
		- Using an OOB key (install code) to generate a Trust Center Link Key.
		- hash(OOB code)
		- e.g. hash(0xcafe)
		- Typically QR code, printed code, NFC, 
	

	
	
	
ZBOSS 3.9 stack
	- Stack overview
		- supported Roles: Coordinator, router, End Device
		- unsupported roles: Sleepy End Device and Green Power device.
		- Probably will not support Green power proxy.
	- Device declaration
		- 
	- Application flow
	- Joining a network
	- Debugging the stack
		- They have some different debugging tools in Krakow. Ask them if nescessary.
		- No debugging tools are included inside the release package.
		- API:
			- Selectively enable logs:
				- ZB_SET_TRASE_MASK(mask)
			- Adjust trace level:
				- 0: Trace log disabled
				- 1: error and warning levels
				- 2: info level
				- 3: debug level
			- All available masks can be found inside zb_trace.h (debug libs required).
	

	
Zigbee Certification:
	-Process of certification:
		- Testing performed by Alliance authorized service providers using test plans developed by the Alliance.
	-Certification by similarity:
		- If you only have small changes that doesn't affect the stack, it can be certified by similarity.
			- they then judge whether this may affect the certification.
			- Change in platform, or adding of functionality will not be certified by similarity.
			- Change in language (speaking) is an example of approved certification by similarity.
	-Spot check:
		-if we only change HW, but no change in the stack, a spot test can be performed.
	-Test is not required with e.g. product packaging changes
	-A product can have it's certification revoked.


Bluetooth Mesh Training
----
Day 4
----
1            -> 2
Provisioning -> Configuration


Node types:
	- N: Node
	- LPN: Low power node
	- RN: Relay node
	- FN: Friend node (stores messages for LPN)
	

Message relay and managed flooding
	- Resending received packets
	- BLE advertising and scanning
	- Allows range extension
	- Limited by TTL
	- Message chache
	- Optional relay
	- Managed Flooding:
		- sends an amount of time.
		- speed of sound.
	
	
	













	
	
	


	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	














