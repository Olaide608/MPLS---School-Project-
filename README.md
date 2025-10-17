
# MPLS PRACTICE - SCHOOL PROJECT

This project demonstrates the operation of a basic MPLS network within an ISP core, utilizing OSPF as the IGP, an IBGP full-mesh setup between the Provider Edge (PE) routers, and EBGP connections between the Customer Edge (CE) routers. The topology consists of four routers (R1–R4) representing the ISP core (Label Switch Routers) and two routers (R5 and R6) representing customers in different autonomous systems. Follow along as I walk you through how it all works.



## WORKING MECHANISM

1. When Provider Edge 1 (PE1/R1) receives an unlabeled IP packet from Customer Edge 1 (CE1/R5) destined for Customer Edge 2 (CE2/R6), it performs a lookup in the Cisco Express Forwarding (CEF) table. R1 then imposes two labels: an inner VPN label (104, check the MPLS Configuration section to confirm) and an outer Transport label (203). R1 functions as the Ingress Label Switched Router (LSR)
2. Upon arrival of the labeled packet (outer label 203) at R2's incoming interface, the Label Forwarding Information Base (LFIB) is consulted. The LFIB dictates a Label Swap operation, providing the outgoing interface (eth0/1) and a new outgoing label (302) for the packet.
3. R3, acting as the penultimate hop, receives the packet (outer label 302). It then performs Penultimate Hop Popping (PHP) by removing the outer Transport label (302) and transmits the packet with only the inner VPN label remaining. The final router, the Egress LSR (R4), receives the packet, removes the inner label, and transmits the original unlabeled IP packet to Customer Edge 2 (CE2/R6).
## TOPOLOGY DIAGRAM


![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/MPLS%20Topology%20Diagram.png)
## IP ADDRESSING SCHEME

| Routers | Interfaces | Addresses |
| :-----: | :------: | ----: |
| R1 | lo0 | 150.1.1.1/32 |
|    |  Eth0/0      |  155.1.12.1/24 |
|    |  Eth0/1      |  155.1.15.1/24 |
| R2 | lo0 | 150.1.2.2/32 |
|    |  Eth0/0      |  155.1.12.2/24 |
|    |  Eth0/1      |  155.1.23.2/24 |
| R3 | lo0 | 150.1.3.3/32 |
|    |  Eth0/0      |  155.1.34.3/24 |
|    |  Eth0/1      |  155.1.23.3/24 |
| R4 | lo0 | 150.1.4.4/32 |
|    |  Eth0/0      |  155.1.34.4/24 |
|    |  Eth0/1      |  155.1.46.4/24 |
| R5 | lo0 | 150.1.5.5/32 |
|    |  Eth0/1      |  155.1.15.5/24 |
| R6 | lo0 | 150.1.6.6/32 |
|    |  Eth0/1      |  155.1.46.6/24 |


## IGP (OSPF) CONFIGURATION

The provided OSPF configuration details how the ISP's core routers (Label Switch Routers) are set up to ensure inter-router connectivity and facilitate MPLS label switching operations.

```bash
 R1
 router ospf 1
 router-id 150.1.1.1
 network 150.1.1.1 0.0.0.0 area 0
 network 155.1.12.0 0.0.0.255 area 0
 redistribute ospf 1
 ```

 ```bash
 R2
 router ospf 1
 router-id 150.1.2.2
 network 150.1.2.2 0.0.0.0 area 0
 network 155.1.12.0 0.0.0.255 area 0
 network 155.1.23.0 0.0.0.255 area 0
 ```
 
  ```bash
 R3
 router ospf 1
 router-id 150.1.3.3
 network 150.1.3.3 0.0.0.0 area 0
 network 155.1.23.0 0.0.0.255 area 0
 network 155.1.34.0 0.0.0.255 area 0
 ```

  
  ```bash
 R4
router ospf 1
router-id 150.1.4.4
network 150.1.4.4 0.0.0.0 area 0
network 155.1.34.0 0.0.0.255 area 0
 ```


## IBGP (FULL MESH) CONFIGURATION

Below shows the IBGP full-mesh configuration used among the Provider Edge (PE) routers to facilitate the exchange of routes learned from customer eBGP peers;

 ```bash
 R1
router bgp 1
 bgp router-id 150.1.1.1
 neighbor 150.1.4.4 remote-as 1
 neighbor 150.1.4.4 update-source Loopback0
 neighbor 150.1.4.4 next-hop-self
 ```

  ```bash
R4
router bgp 1
 bgp router-id 150.1.4.4
 neighbor 150.1.1.1 remote-as 1
 neighbor 150.1.1.1 update-source Loopback0
 neighbor 150.1.1.1 next-hop-self
 ```

 
## EBGP CONFIGURATION

The following configuration establishes eBGP peering between the PE and CE routers to exchange customer routing information, ensuring the PE routers have the necessary external routes. 

Additionally, it shows the advertisement of the R5 and R6 Loopback interfaces to guarantee inter-CE connectivity and enable the PE routers to encapsulate and forward traffic across the MPLS label-switched network.

  ```bash
R1
router bgp 1
  neighbor 155.1.15.5 remote-as 10
 ```

   ```bash
R4
router bgp 1
  neighbor 155.1.46.6 remote-as 20
 ```

   ```bash
R5
router bgp 10
 bgp router-id 150.1.5.5
 network 150.1.5.5 mask 255.255.255.255
 neighbor 155.1.15.1 remote-as 1
 ```

   ```bash
R6
router bgp 20
 bgp router-id 150.1.6.6
 network 150.1.6.6 mask 255.255.255.255
 neighbor 155.1.46.4 remote-as 1
 ```
## MPLS CONFIGURATION

Below details the MPLS label ranges configured on each ISP core Label Switched Routers (LSRs), along with screenshots of their corresponding MPLS Forwarding Tables (LFIBs). This configuration establishes and verifies the network's ability to label-switch and encapsulate customer traffic across the ISP network.

   ```bash
R1(config)#mpls label range 100 199
R2(config)#mpls label range 200 299
R3(config)#mpls label range 300 399
R4(config)#mpls label range 400 499
 ```

![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/R1%20MPLS%20Forwarding%20table.png)

![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/R2%20MPLS%20Forwarding%20table.png)

![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/R3%20MPLS%20Forwarding%20table.png)

![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/R4%20MPLS%20Forwarding%20table.png)




## PING CE1/R5 & CE2/R6

![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/PING%20FROM%20CE1%20to%20CE2.png)





## PING CE2/R6 & CE1/R5

![](https://github.com/Olaide608/MPLS---School-Project-/blob/main/PING%20FROM%20CE2%20to%20CE1.png)