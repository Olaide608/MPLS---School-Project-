
# MPLS PRACTICE - SCHOOL PROJECT

This project demonstrates the operation of a basic MPLS network within an ISP core, utilizing OSPF as the IGP, an IBGP full-mesh setup between the Provider Edge (PE) routers, and EBGP connections between the Customer Edge (CE) routers. The topology consists of four routers (R1–R4) representing the ISP core and two routers (R5 and R6) representing customers in different autonomous systems. Follow along as I walk you through how it all works.



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


## IGP (OSPF) Configurtion

The provided OSPF configuration details how the ISP's core routers are set up to ensure inter-router connectivity and facilitate MPLS label switching operations.

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

