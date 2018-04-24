# Thread-Zigbee

## This repo contains an introduction to Thread and Zigbee

### Introduction to Thread

Links to [Thread Group](http://www.threadgroup.org) and [OpenThread](https://openthread.io/).

The main purpose of Thread is to extend the range of short range wireless devices and to insure simple access to the internet through IPv6 addressable nodes, in a low power fashion.
Thread is a routed mesh with built in features to prevent any single point of failure. Its innate self-healing properties makes Thread a very robust and reliable meshed network. 

OpenThread is an open source implementation of the Thread specification

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









