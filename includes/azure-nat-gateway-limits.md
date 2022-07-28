---
 title: include file
 description: include file
 services: networking
 author: johndowns
 ms.service: NAT Gateway
 ms.topic: include
 ms.date: 09/24/2020
 ms.author: jodowns
 ms.custom: include file

---
The following limits apply to NAT gateway resources managed through Azure Resource Manager per region per subscription. Learn how to [view your current resource usage against your subscription limits](../articles/networking/check-usage-against-limits.md).


| Resource            | Limit              |
|---------------------|--------------------|
| Public IP addresses | 16 per NAT gateway |
| NAT gateways        | 1,000 per subscription per region |
| Packets processed   | 1M - 5M packets / second |
| Simultaneous connections | 50,000 connections to the same destination / public IP |
