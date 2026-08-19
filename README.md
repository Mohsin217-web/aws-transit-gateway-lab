# AWS Transit Gateway Lab

Hands-on AWS Cloud Networking project demonstrating how to connect multiple VPCs using an AWS Transit Gateway with a hub-and-spoke architecture.

## 📌 Project Overview

This project demonstrates private network connectivity between three separate Amazon VPCs using AWS Transit Gateway.

The lab was designed and implemented from scratch to understand:

- AWS Transit Gateway
- Hub-and-spoke networking
- VPC attachments
- Transit Gateway route tables
- Route propagation
- VPC route tables
- Static routing
- Security Groups
- Private IP connectivity
- Network troubleshooting
- Connectivity validation
- AWS resource cleanup and cost awareness

---

## 🏗️ Architecture

The environment consists of three VPCs connected through a centralized Transit Gateway.

```text
                    AWS Transit Gateway
                         TGW-HUB
                            |
             +--------------+--------------+
             |              |              |
             |              |              |
           VPC-A          VPC-B          VPC-C
        10.0.0.0/16    20.0.0.0/16    30.0.0.0/16
             |              |              |
          EC2-A           EC2-B          EC2-C
        10.0.1.65        20.0.1.8       30.0.1.168
