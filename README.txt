AWS TRANSIT GATEWAY LAB
=======================

Hands-on AWS Cloud Networking Project

PROJECT OVERVIEW
----------------

This project demonstrates how to connect multiple AWS VPCs using
AWS Transit Gateway with a centralized hub-and-spoke architecture.

Three separate VPCs were created and connected through an AWS
Transit Gateway. Private connectivity was then tested between
EC2 instances located in each VPC.

The complete environment was tested, documented, and cleaned up
after the lab to minimize unnecessary AWS costs.


ARCHITECTURE
------------

The lab uses a hub-and-spoke network architecture.

                         AWS TRANSIT GATEWAY
                              TGW-HUB
                           /     |     \
                          /      |      \
                         /       |       \
                      VPC-A    VPC-B    VPC-C
                    10.0.0/16 20.0.0/16 30.0.0/16
                       |         |         |
                     EC2-A     EC2-B     EC2-C
                  10.0.1.65  20.0.1.8  30.0.1.168


NETWORK DESIGN
--------------

VPC-A
CIDR: 10.0.0.0/16
Subnet: 10.0.1.0/24
EC2-A: 10.0.1.65

VPC-B
CIDR: 20.0.0.0/16
Subnet: 20.0.1.0/24
EC2-B: 20.0.1.8

VPC-C
CIDR: 30.0.0.0/16
Subnet: 30.0.1.0/24
EC2-C: 30.0.1.168

The VPC CIDR ranges were intentionally selected to be
non-overlapping so that routing between the VPCs would be
possible.


PROJECT OBJECTIVES
------------------

The main objectives of this project were:

- Understand AWS Transit Gateway
- Understand hub-and-spoke networking
- Create multiple VPCs
- Plan non-overlapping CIDR ranges
- Create Transit Gateway VPC attachments
- Configure Transit Gateway route tables
- Understand route propagation
- Configure VPC route tables
- Configure Security Groups
- Test private connectivity
- Troubleshoot AWS networking
- Understand Transit Gateway versus VPC Peering
- Practice AWS cost control
- Practice AWS resource cleanup


TRANSIT GATEWAY
---------------

AWS Transit Gateway acts as a centralized network hub.

Instead of creating direct connections between every VPC,
each VPC connects to the Transit Gateway through a VPC
attachment.

Architecture:

                         TGW
                       /  |  \
                      /   |   \
                   VPC-A VPC-B VPC-C

This design is called a hub-and-spoke architecture.


VPC ATTACHMENTS
---------------

Three VPC attachments were created:

VPC-A -> TGW Attachment A
VPC-B -> TGW Attachment B
VPC-C -> TGW Attachment C

Each attachment provides connectivity between the VPC and
the Transit Gateway.


TRANSIT GATEWAY ROUTING
-----------------------

The Transit Gateway route table contained routes for the
three VPC networks:

10.0.0.0/16 -> VPC-A Attachment
20.0.0.0/16 -> VPC-B Attachment
30.0.0.0/16 -> VPC-C Attachment

When the Transit Gateway receives traffic, it uses the
destination network to determine which attachment should
receive the packet.


ROUTE PROPAGATION
-----------------

Route propagation was used to allow the VPC attachments to
populate the Transit Gateway route table with their VPC
CIDR ranges.

Resulting TGW routes:

10.0.0.0/16 -> VPC-A
20.0.0.0/16 -> VPC-B
30.0.0.0/16 -> VPC-C


VPC ROUTE TABLES
----------------

VPC-A:

20.0.0.0/16 -> Transit Gateway
30.0.0.0/16 -> Transit Gateway

VPC-B:

10.0.0.0/16 -> Transit Gateway
30.0.0.0/16 -> Transit Gateway

VPC-C:

10.0.0.0/16 -> Transit Gateway
20.0.0.0/16 -> Transit Gateway


SECURITY GROUPS
---------------

Security Groups were configured to allow ICMP traffic between
the required VPC CIDR ranges.

Routing alone does not guarantee connectivity.

Traffic must successfully pass through the network routing
and security controls before reaching the destination EC2.


TRAFFIC FLOW
------------

Example: EC2-A to EC2-B

EC2-A
10.0.1.65
   |
   v
VPC-A Route Table
20.0.0.0/16 -> TGW
   |
   v
Transit Gateway
   |
   v
TGW Route Table
20.0.0.0/16 -> VPC-B Attachment
   |
   v
VPC-B
   |
   v
EC2-B
20.0.1.8


Example: EC2-A to EC2-C

EC2-A
10.0.1.65
   |
   v
VPC-A Route Table
30.0.0.0/16 -> TGW
   |
   v
Transit Gateway
   |
   v
TGW Route Table
30.0.0.0/16 -> VPC-C Attachment
   |
   v
VPC-C
   |
   v
EC2-C
30.0.1.168


CONNECTIVITY TESTING
--------------------

Private connectivity was tested between all three EC2
instances.

Test results:

EC2-A -> EC2-B    PASS
EC2-A -> EC2-C    PASS
EC2-B -> EC2-A    PASS
EC2-B -> EC2-C    PASS
EC2-C -> EC2-A    PASS
EC2-C -> EC2-B    PASS

Each successful test returned:

4 packets transmitted
4 packets received
0% packet loss

This confirmed bidirectional private connectivity between
all three VPCs.


PRIVATE IP CONNECTIVITY
-----------------------

The connectivity tests used private IP addresses:

EC2-A: 10.0.1.65
EC2-B: 20.0.1.8
EC2-C: 30.0.1.168

The public IP addresses were used only for SSH management
access from the Windows workstation.

The Transit Gateway handled the private VPC-to-VPC traffic.


SSH MANAGEMENT PATH
-------------------

Windows PC
    |
    v
Internet
    |
    v
Internet Gateway
    |
    v
EC2 Instance


PRIVATE VPC-TO-VPC PATH
-----------------------

EC2-A
    |
    v
VPC-A
    |
    v
Transit Gateway
    |
    +------> VPC-B -> EC2-B
    |
    +------> VPC-C -> EC2-C


TRANSIT GATEWAY VS VPC PEERING
------------------------------

VPC PEERING

VPC Peering creates a direct connection between two VPCs.

Example:

VPC-A <-> VPC-B

With multiple VPCs, several individual peering connections
may be required.

Example:

VPC-A <-> VPC-B
VPC-A <-> VPC-C
VPC-B <-> VPC-C


TRANSIT GATEWAY

Transit Gateway provides a centralized hub.

Example:

             TGW
           /  |  \
          /   |   \
       VPC-A VPC-B VPC-C

This architecture is easier to manage and scale when many
VPCs and networks need connectivity.


TROUBLESHOOTING METHODOLOGY
---------------------------

When troubleshooting AWS network connectivity, check each
layer in order:

1. Source EC2
2. Source Security Group
3. Source VPC Route Table
4. TGW Attachment
5. TGW Route Table
6. Route Propagation or Static Routes
7. Destination VPC Route Table
8. Destination Security Group
9. Destination EC2
10. Connectivity test

This provides a structured approach to identifying where
network traffic is being blocked.


AWS COST AWARENESS
------------------

Potential billable resources in this lab included:

- Transit Gateway
- Transit Gateway VPC attachments
- EC2 instances
- EBS storage
- Public IPv4 addresses
- Network data processing

The lab was designed as a temporary hands-on environment.

After testing was completed, the AWS resources were deleted
to minimize unnecessary costs.


CLEANUP PERFORMED
-----------------

EC2-A -> Terminated
EC2-B -> Terminated
EC2-C -> Terminated

TGW Attachment A -> Deleted
TGW Attachment B -> Deleted
TGW Attachment C -> Deleted

Transit Gateway -> Deleted

Lab VPC resources -> Deleted
Subnets -> Deleted
Route Tables -> Deleted


PROJECT EVIDENCE
----------------

The GitHub repository contains:

- Architecture diagram
- Editable Draw.io architecture source
- AWS configuration screenshots
- EC2 screenshots
- Connectivity test screenshots
- Project documentation


REPOSITORY STRUCTURE
--------------------

aws-transit-gateway-lab/

    README.md
    README.txt
    architecture-diagram.png
    architecture-diagram.drawio

    screenshots/
        TGW configuration screenshots
        VPC routing screenshots
        EC2 screenshots
        Connectivity test screenshots


KEY LEARNING OUTCOMES
---------------------

Through this project, I gained practical hands-on experience
with:

- AWS Transit Gateway
- Hub-and-spoke architecture
- VPC networking
- CIDR planning
- VPC attachments
- TGW route tables
- Route propagation
- Static routing
- VPC route tables
- Security Groups
- EC2 private connectivity
- Network troubleshooting
- AWS cost awareness
- AWS resource cleanup


PROJECT STATUS
--------------

COMPLETED

Three AWS VPCs were successfully connected through an AWS
Transit Gateway.

Private connectivity was successfully verified between all
three VPCs in both directions.

The AWS environment was cleaned up after testing to minimize
unnecessary costs.
